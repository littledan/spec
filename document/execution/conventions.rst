.. index:: ! execution, stack, store

Conventions
-----------

WebAssembly code is run by :ref:`instantiating <instantiation>` a module and :ref:`invoking <exec-invocation>` one of its exported functions.

The behavior of executing WebAssembly code is defined in terms of an *abstract machine*.
For each instruction, there is a rule that specifies the effect of its execution.
Furthermore, there are rules describing the instantiation of a module.
As with :ref:`validation <validation>`, all rules are given in two *equivalent* forms:

1. In *prose*, describing the execution in intuitive form.
2. In *formal notation*, describing the rule in mathematical form.

.. note::
   As with validation, the prose and formal rules are equivalent,
   so that understanding of the formal notation is *not* required to read this specification.
   The formalism offers a more concise description in notation that is used widely in programming languages semantics and is readily amenable to mathematical proof.

In both cases, the rules operate on both an abstract *stack* that records operand values and control constructs and an abstract *store* containing global mutable state.
These and other runtime structures are made precise in terms of abstract syntax.


.. _syntax-val:
.. index:: ! value, constant
   pair: abstract syntax; value

Values
~~~~~~

WebAssembly computations manipulate *values* of the four basic :ref:`value types <syntax-valtype>`: :ref:`uninterpreted integers <syntax-int>` and :ref:`floating-point data <syntax-float>` of 32 or 64 bit width, respectively.

In most places of the semantics, values of different types can occur.
In order to avoid ambiguities, values are therefor represented with an abstract syntax that makes their type explicit.
It is convenient to reuse the same notation as for the instructions producing them:

.. math::
   \begin{array}{llll}
   \production{(value)} & \val &::=&
     \I32.\CONST~\i32 ~|~
     \I64.\CONST~\i64 ~|~
     \F32.\CONST~\f32 ~|~
     \F64.\CONST~\f64
   \end{array}


.. _store:
.. _syntax-store:
.. index:: ! store, table instance, memory instance, global instance, module
   pair: abstract syntax; store

Store
~~~~~

The *store* represents all *mutable* global state that can be manipulated by WebAssembly programs.
It consists of the runtime representation of all *instances* of :ref:`tables <syntax-tableinst>`, :ref:`memories <syntax-meminst>`, and :ref:`globals <syntax-globalinst>` that have been *allocated* during the life time of the execution engine. [#gc]_

Syntactically, the store is defined as a :ref:`record <syntax-record>` of respective lists.

.. math::
   \begin{array}{llll}
   \production{(store)} & \store &::=& \{~
     \begin{array}[t]{l@{~}ll}
     \TABLES & \tableinst^\ast, \\
     \MEMS & \meminst^\ast, \\
     \GLOBALS & \globalinst^\ast ~\} \\
     \end{array}
   \end{array}

.. note::
   Since the store holds the state of arbitrary many, possibly interacting, :ref:`module instances <syntax-moduleinst>`, it can host multiple tables and memories.

   :ref:`Function instances <syntax-funcinst>` and :ref:`module instances <syntax-moduleinst>` are not part of the store because they are not mutable.
   In particular, there is no requirement that function instances have a unique identity, since it is not observable.
   :ref:`Embedders <embedder>` that make the identity of function instances observable need to provide a suitable definition.

.. [#gc]
   In practice, implementations may apply techniques like garbage collection to remove objects from the store that are no longer referenced.
   However, such techniques are not semantically observable,
   and hence outside the scope of this specification.


Convention
..........

* The meta variable :math:`S` ranges over stores where clear from context.


.. _syntax-addr:
.. _syntax-tableaddr:
.. _syntax-memaddr:
.. _syntax-globaladdr:
.. index:: ! address, store, table instance, memory instance, global instance
   pair: abstract syntax; table address
   pair: abstract syntax; memory address
   pair: abstract syntax; global address
   pair: table; address
   pair: memory; address
   pair: global; address

Addresses
~~~~~~~~~

:ref:`Table instances <syntax-tableinst>`, :ref:`memory instances <syntax-meminst>`, and :ref:`global instances <syntax-globalinst>` in the :ref:`store <syntax-store>` are referenced with abstract *addresses*.
These are simply indices into the respective store component.

.. math::
   \begin{array}{llll}
   \production{(address)} & \addr &::=&
     0 ~|~ 1 ~|~ 2 ~|~ \dots \\
   \production{(table address)} & \tableaddr &::=&
     \addr \\
   \production{(memory address)} & \memaddr &::=&
     \addr \\
   \production{(global address)} & \globaladdr &::=&
     \addr \\
   \end{array}

.. note::
   There is no specific limit on the number of allocations of store objects,
   hence addresses can be arbitrarily large natural numbers.

   A *memory address* |memaddr| denotes the abstract address of a memory *instance* in the store,
   not an offset *inside* a memory instance.


.. _syntax-moduleinst:
.. index:: ! instance, function type, function instance, table instance, memory instance, global instance, export instance, table address, memory address, global address, index
   pair: abstract syntax; module instance
   pair: module; instance

Module Instances
~~~~~~~~~~~~~~~~

A *module instance* is the runtime representation of a :ref:`module <syntax-module>`.
It is created by :ref:`instantiating <instantiation>` a module,
and collects runtime representations of all entities that are imported, defined, or exported by the module.

.. math::
   \begin{array}{llll}
   \production{(module instance)} & \moduleinst &::=& \{
     \begin{array}[t]{l@{~}ll}
     \TYPES & \functype^\ast, \\
     \FUNCS & \funcinst^\ast, \\
     \TABLES & \tableaddr^\ast, \\
     \MEMS & \memaddr^\ast, \\
     \GLOBALS & \globaladdr^\ast \\
     \EXPORTS & \exportinst^\ast ~\} \\
     \end{array}
   \end{array}

Each component contains the runtime instances of the respective entities from the original module -- whether imported or defined -- in the order of their static :ref:`indices <syntax-index>`.
:ref:`Table instances <syntax-tableinst>`, :ref:`memory instances <syntax-meminst>`, and :ref:`global instances <syntax-globalinst>` are referenced with an indirection through their respective :ref:`addresses <syntax-addr>` in the :ref:`store <syntax-store>`.

It is an invariant of the semantics that all :ref:`export instances <syntax-exportinst>` in a given module instance have different :ref:`names <syntax-name>`.


.. _syntax-funcinst:
.. index:: ! function instance, module instance, function, closure
   pair: abstract syntax; function instance
   pair: function; instance

Function Instances
~~~~~~~~~~~~~~~~~~

A *function instance* is the runtime representation of a :ref:`function <syntax-func>`.
It is effectively a *closure* of the original function over the runtime :ref:`module instance <syntax-moduleinst>` of its own :ref:`module <syntax-module>`.
The module instance is used to resolve references to other non-local definitions during execution of the function.

.. math::
   \begin{array}{llll}
   \production{(function instance)} & \funcinst &::=&
     \{ \MODULE~\moduleinst, \FUNC~\func \} \\
   \end{array}


.. _syntax-tableinst:
.. _syntax-funcelem:
.. index:: ! table instance, table, function instance
   pair: abstract syntax; table instance
   pair: table; instance

Table Instances
~~~~~~~~~~~~~~~

A *table instance* is the runtime representation of a :ref:`table <syntax-table>`.
It holds a vector of *function elements* and an optional maximum size, if one was specified at the definition site of the table.

Each function element is either empty, representing an uninitialized table entry, or a :ref:`function instance <syntax-funcinst>`.
Function elements can be mutated through the execution of an :ref:`element segment <syntax-elem>` or by other means provided by the :ref:`embedder <embedder>`.

.. math::
   \begin{array}{llll}
   \production{(table instance)} & \tableinst &::=&
     \{ \ELEM~\vec(\funcelem), \MAX~\u32^? \} \\
   \production{(function element)} & \funcelem &::=&
     \funcinst^? \\
   \end{array}

It is an invariant of the semantics that the length of the element vector never exceeds the maximum size, if present.

.. note::
   Other table elements may be added in future versions of WebAssembly.


.. _syntax-meminst:
.. index:: ! memory instance, memory, byte, ! page size, memory type
   pair: abstract syntax; memory instance
   pair: memory; instance

Memory Instances
~~~~~~~~~~~~~~~~

A *memory instance* is the runtime representation of a linear :ref:`memory <syntax-memory>`.
It holds a vector of bytes and an optional maximum size, if one was specified at the definition site of the table.

The length of the vector always is a multiple of the *page size*, which is defined to be the constant :math:`65536` -- abbreviated to :math:`64\,\F{Ki}`.
Like in a :ref:`memory type <syntax-memtype>`, the maximum size is given in units this page size.

The bytes can be mutated through specific instructions, the execution of an :ref:`data segment <data-elem>`, or by other means provided by the :ref:`embedder <embedder>`.

.. math::
   \begin{array}{llll}
   \production{(memory instance)} & \meminst &::=&
     \{ \DATA~\vec(\by), \MAX~\u32^? \} \\
   \end{array}

It is an invariant of the semantics that the length of the byte vector, divided by page size, never exceeds the maximum size, if present.


.. _syntax-globalinst:
.. index:: ! global instance, value
   pair: abstract syntax; global instance
   pair: global; instance

Global Instances
~~~~~~~~~~~~~~~~

A *global instance* is the runtime representation of a :ref:`global variable <syntax-global>`.
It holds an individual :ref:`value <syntax-val>` and a flag indicating whether it is mutable.

The value of mutable globals can be mutated through specific instructions or by other means provided by the :ref:`embedder <embedder>`.

.. math::
   \begin{array}{llll}
   \production{(global instance)} & \globalinst &::=&
     \{ \VALUE~\val, \MUT~\mut \} \\
   \end{array}


.. _syntax-exportinst:
.. index:: ! export instance, name, external value
   pair: abstract syntax; export instance
   pair: export; instance

Export Instances
~~~~~~~~~~~~~~~~

An *export instance* is the runtime representation of an :ref:`export <syntax-export>`.
It defines the export's :ref:`name <syntax-name>` and the :ref:`external value <syntax-externval>` being exported.

.. math::
   \begin{array}{llll}
   \production{(export instance)} & \exportinst &::=&
     \{ \NAME~\name, \VALUE~\externval \} \\
   \end{array}


.. _syntax-externval:
.. index:: ! external value, function instance, table address, memory address, global address
   pair: abstract syntax; external value
   pair: external; value

External Values
~~~~~~~~~~~~~~~

An *external value* is the runtime representation of an entity that can be imported or exported.
It is either a :ref:`function instance <syntax-funcinst>`, or an :ref:`address <syntax-addr>` denoting a :ref:`table instance <syntax-tableinst>`, :ref:`memory instance <syntax-meminst>`, and :ref:`global instances <syntax-globalinst>` in the shared :ref:`store <syntax-store>`.

.. math::
   \begin{array}{llll}
   \production{(external value)} & \externval &::=&
     \FUNC~\funcinst ~|~
     \TABLE~\tableaddr ~|~
     \MEM~\memaddr ~|~
     \GLOBAL~\globaladdr \\
   \end{array}


Conventions
...........

The following auxiliary notation is defined for sequences of external values, filtering out entries of a specific kind in an order-preserving fashion:

.. math::
   \begin{array}{lcl}
   \funcs(\externval^\ast) &=& [\funcinst ~|~ \FUNC~\funcinst \in \externval^\ast] \\
   \tables(\externval^\ast) &=& [\tableaddr ~|~ \TABLE~\tableaddr \in \externval^\ast] \\
   \mems(\externval^\ast) &=& [\memaddr ~|~ \MEM~\memaddr \in \externval^\ast] \\
   \globals(\externval^\ast) &=& [\globaladdr ~|~ \GLOBAL~\globaladdr \in \externval^\ast] \\
   \end{array}


.. _syntax-externtype:
.. index:: ! external type, function type, table type, memory type, global type
   pair: abstract syntax; external type
   pair: external; type

External Types
~~~~~~~~~~~~~~

*External types* classify :ref:`external values <syntax-externval>`, and thereby imports and exports, with respective types.

.. math::
   \begin{array}{llll}
   \production{external types} & \externtype &::=&
     \FUNC~\functype ~|~
     \TABLE~\tabletype ~|~
     \MEM~\memtype ~|~
     \GLOBAL~\globaltype \\
   \end{array}

These types are used in the definition of :ref:`instantiation <instantiation>`.


Conventions
...........

The following auxiliary notation is defined for sequences of external types, filtering out entries of a specific kind in an order-preserving fashion:

.. math::
   \begin{array}{lcl}
   \funcs(\externtype^\ast) &=& [\functype ~|~ \FUNC~\functype \in \externtype^\ast] \\
   \tables(\externtype^\ast) &=& [\tabletype ~|~ \TABLE~\tabletype \in \externtype^\ast] \\
   \mems(\externtype^\ast) &=& [\memtype ~|~ \MEM~\memtype \in \externtype^\ast] \\
   \globals(\externtype^\ast) &=& [\globaltype ~|~ \GLOBAL~\globaltype \in \externtype^\ast] \\
   \end{array}


.. _stack:
.. _frame:
.. _label:
.. _syntax-frame:
.. _syntax-label:
.. index:: ! stack, ! frame, ! label
   pair: abstract syntax; frame
   pair: abstract syntax; label

Stack
~~~~~

Besides the :ref:`store <store>`, most :ref:`instructions <syntax-instr>` interact with an implicit *stack*.
The stack contains three kinds of entries:

* *Values*: communicating the *operands* (arguments and results) of instructions.

* *Labels*: recording information about the active :ref:`structured control instructions <syntax-instr-control>` that can be targeted by branches.

* *Locals*: representing the *call frames* of active :ref:`function <syntax-func>` calls.

All these entries can occur on the stack in almost any order during the execution of a program.

.. note::
   It is possible to model the semantics using two or even three separate stacks for operands, control constructs, and calls.
   However, they are interdependent, so that additional book keeping about associated stack heights would be required.
   For the purpose of this specification, an interleaved representation is simpler.

Abstract syntax is adopted for stack entries as follows.

:ref:`Values <syntax-val>` are represented by themselves.

Labels carry an argument arity :math:`n` and the branch *target*, which is expressed syntactically as an :ref:`instruction <syntax-instr>` sequence:

.. math::
   \begin{array}{llll}
   \production{(label)} & \label &::=&
     \LABEL_n\{\instr^\ast\} \\
   \end{array}

Intuitively, :math:`\instr^\ast` is the *continuation* to execute when the branch is taken, "replacing" the original control construct.

.. note::
   For example, a loop label has the form

   .. math::
      \LABEL_n\{\LOOP~[t^?]~\instr~\dots~\END\}

   When performing a branch to this label, this restarts the loop from the beginning.
   Conversely, a simple block label has the form

   .. math::
      \LABEL_n\{\epsilon\}

   When branching, the original block is replaced by an empty continuation, thereby ending it and proceeding with consecutive instructions.

Activation frames carry the return arity of the respective function, and hold the values of its locals (including arguments) in the order corresponding to their static :ref:`local indices <syntax-localidx>`, as well as a reference to the function's own :ref:`module instance <syntax-moduleinst>`:

.. math::
   \begin{array}{llll}
   \production{(activation)} & \X{activation} &::=&
     \FRAME_n\{\frame\} \\
   \production{(frame)} & \frame &::=&
     \{ \LOCALS~\val^\ast, \MODULE~\moduleinst\} \\
   \end{array}

The values of the locals are mutated by respective instructions.

.. note::
   In the current version of WebAssembly, the arities of labels and activations cannot be larger than :math:`1`.
   This may be generalized in future versions.


Conventions
...........

* The meta variable :math:`L` ranges over labels where clear from context.

* The meta variable :math:`F` ranges over frames where clear from context.


Textual Notation
~~~~~~~~~~~~~~~~

Execution is specified by stylised, step-wise rules for each :ref:`instruction <syntax-instr>` of the :ref:`abstract syntax <syntax>`.

The rules implicitly assume a given :ref:`store <store>` :math:`S`.
This store is mutated by *replacing* some of its components.
Such replacement is assumed to apply globally.

The rules also assume the presence of a :ref:`stack <stack>`.
:ref:`Values <syntax-value>`, :ref:`labels <syntax-label>`, and :ref:`frames <syntax-frame>` are pushed to and popped from the stack.
Various rules require the stack to contain at least one frame,
which is referred to as the *current* frame.

In various places the rules contain *assertions* that express crucial invariants about the state of execution and explain why these are known to hold.

The execution of an instruction may *trap*, in which case the entire computation is aborted and no further modifications to the store are performed by it.
The execution of an instruction may also end in a *jump* to a designated target, which defines the next instruction to execute.
In all other cases, :ref:`instruction sequences <syntax-instr-seq>` are implicitly executed in order.


Formal Notation
~~~~~~~~~~~~~~~

.. note::
   This section gives a brief explanation of the notation for specifying execution formally.
   For the interested reader, a more thorough introduction can be found in respective text books. [#tapl]_

.. todo::
   Describe


.. [#tapl]
   For example: Benjamin Pierce. `Types and Programming Languages <https://www.cis.upenn.edu/~bcpierce/tapl/>`_. The MIT Press 2002
