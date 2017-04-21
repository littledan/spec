.. index:: ! execution

Conventions
-----------

Values
~~~~~~

.. math::
   \begin{array}{llll}
   \production{(value)} & \val &::=&
     \valtype.\CONST~c \\
   \end{array}


.. _syntax-addr:
.. _syntax-tableaddr:
.. _syntax-memaddr:
.. _syntax-globaladdr:
.. index:: ! address

Addresses
~~~~~~~~~

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
   A *memory address* denotes the abstract address of a memory *instance* in the store,
   not an offset *in* a linear memory instance.


.. _syntax-moduleinst:
.. index:: ! instance

Module Instances
~~~~~~~~~~~~~~~~

.. math::
   \begin{array}{llll}
   \production{(module instance)} & \moduleinst &::=& \{
     \begin{array}[t]{l@{~}ll}
     \TYPES & \functype^\ast, \\
     \FUNCS & \funcinst^\ast, \\
     \TABLES & \tableaddr^\ast, \\
     \MEMS & \memaddr^\ast, \\
     \GLOBALS & \globaladdr^\ast \\
     \EXPORTS & \externval^\ast ~\} \\
     \end{array}
   \end{array}


.. _syntax-funcinst:
.. index:: ! table instance

Function Instances
~~~~~~~~~~~~~~~~~~

.. math::
   \begin{array}{llll}
   \production{(function instance)} & \funcinst &::=&
     \{ \MODULE~\moduleinst, \FUNC~\func \} \\
   \end{array}


.. _syntax-tableinst:
.. _syntax-funcelem:
.. index:: ! table instance

Table Instances
~~~~~~~~~~~~~~~

.. math::
   \begin{array}{llll}
   \production{(table instance)} & \tableinst &::=&
     \{ \ELEM~(\func^?)^\ast, \MAX~\u32^? \} \\
   \end{array}


.. _syntax-meminst:
.. index:: ! memory instance

Memory Instances
~~~~~~~~~~~~~~~~

.. math::
   \begin{array}{llll}
   \production{(memory instance)} & \meminst &::=&
     \{ \DATA~\by^\ast, \MAX~\u32^? \} \\
   \end{array}


.. _syntax-tableinst:
.. _syntax-funcelem:
.. index:: ! table instance

Global Instances
~~~~~~~~~~~~~~~~

.. math::
   \begin{array}{llll}
   \production{(global instance)} & \globalinst &::=&
     \{ \VALUE~\val, \MUT~\mut \} \\
   \end{array}


.. _syntax-exportinst:
.. index:: ! export instance, name, external value

Export Instances
~~~~~~~~~~~~~~~~

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

*External types* classify imports and exports and their respective types.

.. math::
   \begin{array}{llll}
   \production{external types} & \externtype &::=&
     \FUNC~\functype ~|~ \\&&&
     \TABLE~\tabletype ~|~ \\&&&
     \MEM~\memtype ~|~ \\&&&
     \GLOBAL~\globaltype \\
   \end{array}


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


.. _store:
.. _syntax-store:
.. index:: ! store

Store
~~~~~

.. math::
   \begin{array}{llll}
   \production{(store)} & \store &::=& \{~
     \begin{array}[t]{l@{~}ll}
     \TABLES & \tableinst^\ast, \\
     \MEMS & \meminst^\ast, \\
     \GLOBALS & \globalinst^\ast ~\} \\
     \end{array}
   \end{array}

Convention
..........

* The meta variable :math:`S` ranges over stores where clear from context.


.. _frame:
.. _syntax-frame:
.. index:: ! frame

Frame
~~~~~

.. math::
   \begin{array}{llll}
   \production{(store)} & \frame &::=&
     \{\MODULE~\moduleinst; \LOCALS~\val^\ast\} \\
   \end{array}


.. _label:
.. _syntax-label:
.. index:: ! label

Label
~~~~~

.. math::
   \begin{array}{llll}
   \production{(store)} & \label &::=& \{~
     \LABEL_n\{\instr^\ast\} \\
   \end{array}


Textual Notation
~~~~~~~~~~~~~~~~

Validation is specified by stylised rules for each relevant part of the :ref:`abstract syntax <syntax>`.
The rules not only state constraints defining when a phrase is valid,
they also classify it with a type.
A phrase :math:`A` is said to be "valid with type :math:`T`",
if all constraints expressed by the respective rules are met.
The form of :math:`T` depends on what :math:`A` is.

.. note::
   For example, if :math:`A` is a :ref:`function <syntax-func>`,
   then  :math:`T` is a :ref:`function type <syntax-functype>`;
   for an :math:`A` that is a :ref:`global <syntax-global>`,
   :math:`T` is a :ref:`global type <syntax-globaltype>`;
   and so on.

The rules implicitly assume a given :ref:`context <context>` :math:`C`.
In some places, this context is locally extended to a context :math:`C'` with additional entries.
The formulation "Under context :math:`C'`, ... *statement* ..." is adopted to express that the following statement must apply under the assumptions embodied in the extended context.


Formal Notation
~~~~~~~~~~~~~~~

.. note::
   This section gives a brief explanation of the notation for specifying typing rules formally.
   For the interested reader, a more thorough introduction can be found in respective text books. [#tapl]_

The proposition that a phrase :math:`A` has a respective type :math:`T` is written :math:`A : T`.
In general, however, typing is dependent on the context :math:`C`.
To express this explicitly, the complete form is a *judgement* :math:`C \vdash A : T`,
which says that :math:`A : T` holds under the assumptions encoded in :math:`C`.

The formal typing rules use a standard approach for specifying type systems, rendering them into *deduction rules*.
Every rule has the following general form:

.. math::
   \frac{
     \X{premise}_1 \qquad \X{premise}_2 \qquad \dots \qquad \X{premise}_n
   }{
     \X{conclusion}
   }

Such a rule is read as a big implication: if all premises hold, then the conclusion holds.
Some rules have no premises; they are *axioms* whose conclusion holds unconditionally.
The conclusion always is a judgment :math:`C \vdash A : T`,
and there is one respective rule for each relevant construct :math:`A` of the abstract syntax.

.. note::
   For example, the typing rule for the :ref:`instruction <syntax-instr-numeric>` :math:`\I32.\ADD` can be given as an axiom:

   .. math::
      \frac{
      }{
        C \vdash \I32.\ADD : [\I32~\I32] \to [\I32]
      }

   The instruction is always valid with type :math:`[\I32~\I32] \to [\I32`]
   (saying that it consumes two |I32| values and produces one),
   independent from any side conditions.

   An instruction like |GETLOCAL| can be typed as follows:

   .. math::
      \frac{
        C.\LOCAL[x] = t
      }{
        C \vdash \GETLOCAL~x : [] \to [t]
      }

   Here, the premise enforces that the immediate :ref:`local index <syntax-localidx>` :math:`x` exists in the context.
   The instruction produces a value of its respective type :math:`t`
   (and does not consume any values).
   If :math:`C.\LOCAL[x]` does not exist then the premise does not hold,
   and the instruction is ill-typed.

   Finally, a :ref:`structured <syntax-instr-control>` instruction requires
   a recursive rule, where the premise is itself a typing judgement:

   .. math::
      \frac{
        C,\LABEL\,[t^?] \vdash \instr^\ast : [] \to [t^?]
      }{
        C \vdash \BLOCK~[t^?]~\instr^\ast~\END : [] \to [t^?]
      }

   A |BLOCK| instruction is only valid when the instruction sequence in its body is.
   Moreover, the result type must match the block's annotation :math:`t^?`.
   If so, then the |BLOCK| instruction has the same type as the body.
   Inside the body an additional label of the same type is available,
   which is expressed by locally extending the context :math:`C` with the additional label information for the premise.


.. [#tapl]
   For example: Benjamin Pierce. `Types and Programming Languages <https://www.cis.upenn.edu/~bcpierce/tapl/>`_. The MIT Press 2002
