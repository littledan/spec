.. _exec-instr:
.. index:: instruction, function type, store

Instructions
------------


.. _exec-instr-numeric:
.. index:: numeric instruction
   pair: execution; instruction
   single: abstract syntax; instruction

Numeric Instructions
~~~~~~~~~~~~~~~~~~~~

In this section, the following grammar shorthands are adopted:

.. math::
   \begin{array}{llll}
   \production{unary operators} & \unop &::=&
     \CLZ ~|~
     \CTZ ~|~
     \POPCNT ~|~
     \ABS ~|~
     \NEG ~|~
     \SQRT ~|~
     \CEIL ~|~
     \FLOOR ~|~
     \TRUNC ~|~
     \NEAREST \\
   \production{binary operators} & \binop &::=&
     \ADD ~|~
     \SUB ~|~
     \MUL ~|~
     \DIV ~|~
     \DIV\K{\_}\sx ~|~
     \REM\K{\_}\sx ~|~
     \FMIN ~|~
     \FMAX ~|~
     \COPYSIGN ~|~ \\&&&
     \AND ~|~
     \OR ~|~
     \XOR ~|~
     \SHL ~|~
     \SHR\K{\_}\sx ~|~
     \ROTL ~|~
     \ROTR \\
   \production{test operators} & \testop &::=&
     \EQZ \\
   \production{relational operators} & \relop &::=&
     \EQ ~|~
     \NE ~|~
     \LT ~|~
     \GT ~|~
     \LE ~|~
     \GE ~|~
     \LT\K{\_}\sx ~|~
     \GT\K{\_}\sx ~|~
     \LE\K{\_}\sx ~|~
     \GE\K{\_}\sx \\
   \production{conversion operators} & \cvtop &::=&
     \WRAP ~|~
     \EXTEND\K{\_}\sx ~|~
     \TRUNC\K{\_}\sx ~|~
     \CONVERT\K{\_}\sx ~|~
     \DEMOTE ~|~
     \PROMOTE ~|~
     \REINTERPRET \\
   \end{array}


.. _exec-const:

:math:`t\K{.}\CONST~c`
......................

1. Push the value :math:`(t.\CONST~c)` to the operand stack.

.. note::
   There is no formal reduction rule for this instruction, since constant instructions are already considered values.


.. _exec-unop:

:math:`t\K{.}\unop`
...................

1. Pop the value :math:`(t_1.\CONST~c_1)` from the operand stack.

2. Assert: due to :ref:`validation <valid-unop>`, :math:`t_1` is the same :ref:`value type <syntax-valtype>` as :math:`t`.

3. If :math:`\unop_t(c_1)` is defined, then:

   a. Let :math:`c` be the result of computing :math:`\unop_t(c_1)`.

   b. Push the value :math:`(t.\CONST~c)` to the operand stack.

4. Else:

   a. Trap.

.. math::
   \frac{
     \unop_t(c_1) = c
   }{
     (t\K{.}\CONST~c_1)~t\K{.}\unop \stepto (t\K{.}\CONST~c)
   }

.. math::
   \frac{
     \unop_t(c_1) = \bot
   }{
     (t\K{.}\CONST~c_1)~t\K{.}\unop \stepto \TRAP
   }


.. _exec-binop:

:math:`t\K{.}\binop`
....................

1. Pop the value :math:`(t_2.\CONST~c_2)` from the operand stack.

2. Assert: due to :ref:`validation <valid-binop>`, :math:`t_2` is the same :ref:`value type <syntax-valtype>` as :math:`t`.

3. Pop the value :math:`(t_1.\CONST~c_1)` from the operand stack.

4. Assert: due to :ref:`validation <valid-binop>`, :math:`t_1` is the same :ref:`value type <syntax-valtype>` as :math:`t`.

5. If :math:`\binop_t(c_1, c_2)` is defined, then:

   a. Let :math:`c` be the result of computing :math:`\binop_t(c_1, c_2)`.

   b. Push the value :math:`(t.\CONST~c)` to the operand stack.

6. Else:

   a. Trap.

.. math::
   \frac{
     \binop_t(c_1, c_2) = c
   }{
     (t\K{.}\CONST~c_1)~(t\K{.}\CONST~c_2)~t\K{.}\binop \stepto (t\K{.}\CONST~c)
   }

.. math::
   \frac{
     \binop_t(c_1, c_2) = \bot
   }{
     (t\K{.}\CONST~c_1)~(t\K{.}\CONST~c_2)~t\K{.}\binop \stepto \TRAP
   }


.. _exec-testop:

:math:`t\K{.}\testop`
.....................

1. Pop the value :math:`(t_1.\CONST~c_1)` from the operand stack.

2. Assert: due to :ref:`validation <valid-testop>`, :math:`t_1` is the same :ref:`value type <syntax-valtype>` as :math:`t`.

3. Let :math:`c` be the result of computing :math:`\testop_t(c_1)`.

4. Push the value :math:`(\I32.\CONST~c)` to the operand stack.

.. math::
   \frac{
     \testop_t(c_1) = c
   }{
     (t\K{.}\CONST~c_1)~t\K{.}\testop \stepto (\I32\K{.}\CONST~c)
   }


.. _exec-relop:

:math:`t\K{.}\relop`
....................

1. Pop the value :math:`(t_2.\CONST~c_2)` from the operand stack.

2. Assert: due to :ref:`validation <valid-relop>`, :math:`t_2` is the same :ref:`value type <syntax-valtype>` as :math:`t`.

3. Pop the value :math:`(t_1.\CONST~c_1)` from the operand stack.

4. Assert: due to :ref:`validation <valid-relop>`, :math:`t_1` is the same :ref:`value type <syntax-valtype>` as :math:`t`.

5. Let :math:`c` be the result of computing :math:`\relop_t(c_1, c_2)`.

6. Push the value :math:`(\I32.\CONST~c)` to the operand stack.

.. math::
   \frac{
     \relop_t(c_1, c_2) = c
   }{
     (t\K{.}\CONST~c_1)~(t\K{.}\CONST~c_2)~t\K{.}\relop \stepto (\I32\K{.}\CONST~c)
   }


.. _exec-cvtop:

:math:`t_2\K{.}\cvtop/t_1`
..........................

1. Pop the value :math:`(t.\CONST~c_1)` from the operand stack.

2. Assert: due to :ref:`validation <valid-cvtop>`, :math:`t` is the same :ref:`value type <syntax-valtype>` as :math:`t_1`.

3. If :math:`\cvtop_{t_1,t_2}(c_1)` is defined:

   a. Let :math:`c_2` be the result of computing :math:`\cvtop_{t_1,t_2}(c_1)`.

   b. Push the value :math:`(t_2.\CONST~c_2)` to the operand stack.

4. Else:

   a. Trap.

.. math::
   \frac{
     \cvtop_{t_1,t_2}(c_1) = c_2
   }{
     (t_1\K{.}\CONST~c_1)~t_2\K{.}\cvtop/t_1 \stepto (t_2\K{.}\CONST~c_2)
   }

.. math::
   \frac{
     \cvtop_{t_1,t_2}(c_1) = \bot
   }{
     (t_1\K{.}\CONST~c_1)~t_2\K{.}\cvtop/t_1 \stepto \TRAP
   }


.. _exec-instr-parametric:
.. index:: parametric instructions
   pair: execution; instruction
   single: abstract syntax; instruction

Parametric Instructions
~~~~~~~~~~~~~~~~~~~~~~~

.. _exec-drop:

:math:`\DROP`
.............

1. Pop the value :math:`v` from the operand stack.

.. math::
   \frac{
   }{
     v~\DROP \stepto \epsilon
   }


.. _exec-select:

:math:`\SELECT`
...............

1. Pop the value :math:`(t.\CONST~n)` from the operand stack.

2. Assert: due to :ref:`validation <valid-select>`, :math:`t` is the :ref:`value type <syntax-valtype>` |I32|.

3. Pop the value :math:`v_2` from the operand stack.

4. Pop the value :math:`v_1` from the operand stack.

5. Assert: due to :ref:`validation <valid-select>`, :math:`v_1` and :math:`v_2` have the same :ref:`value type <syntax-valtype>`.

6. If :math:`n` is not :math:`0`, then:

   a. Push the value :math:`v_1` to the operand stack.

7. Else:

   a. Push the value :math:`v_2` to the operand stack.

.. math::
   \frac{
     n \neq 0
   }{
     v_1~v_2~(\I32\K{.}\CONST~n)~\SELECT \stepto v_1
   }

.. math::
   \frac{
     n = 0
   }{
     v_1~v_2~(\I32\K{.}\CONST~n)~\SELECT \stepto v_2
   }


.. _exec-instr-variable:
.. index:: variable instructions, local index, global index, address, global address, global instance, store, frame
   pair: execution; instruction
   single: abstract syntax; instruction

Variable Instructions
~~~~~~~~~~~~~~~~~~~~~

.. _exec-get_local:

:math:`\GETLOCAL~x`
...................

1. Assert: due to :ref:`validation <valid-get_local>`, :math:`F.\LOCALS[x]` is defined.

2. Let :math:`v` be the value :math:`F.\LOCALS[x]`.

3. Push the value :math:`v` to the operand stack.

.. math::
   \frac{
     F.\LOCALS[x] = v
   }{
     F; (\GETLOCAL~x) \stepto F; v
   }


.. _exec-set_local:

:math:`\SETLOCAL~x`
...................

1. Pop the value :math:`(t.\CONST~c)` from the operand stack.

2. Assert: due to :ref:`validation <valid-set_local>`, :math:`F.\LOCALS[x]` is defined.

3. Assert: due to :ref:`validation <valid-set_local>`, :math:`F.\LOCALS[x]` has the :ref:`value type <syntax-valtype>` :math:`t`.

4. Replace :math:`F.\LOCALS[x]` with the value :math:`(t.\CONST~c)`.

.. math::
   \frac{
     F' = F~\mbox{with}~\LOCALS[x] = v
   }{
     F; v~(\SETLOCAL~x) \stepto F'; \epsilon
   }


.. _exec-tee_local:

:math:`\TEELOCAL~x`
...................

1. Pop the value :math:`v` from the operand stack.

2. Push the value :math:`v` to the operand stack.

3. Push the value :math:`v` to the operand stack.

4. Execute the instruction :math:`(\SETLOCAL~x)`.

.. math::
   \frac{
   }{
     F; v~(\TEELOCAL~x) \stepto F'; v~v~(\SETLOCAL~x)
   }


.. _exec-get_global:

:math:`\GETGLOBAL~x`
....................

1. Assert: due to :ref:`validation <valid-get_global>`, :math:`F.\INST.\GLOBALS[x]` is defined.

2. Let :math:`a` be the :ref:`global address <syntax-globaladdr>` :math:`F.\INST.\GLOBALS[x]`.

3. Assert: due to :ref:`validation <valid-get_global>`, :math:`S.\GLOBALS[a]` is defined.

4. Let :math:`\X{glob}` be the :ref:`global instance <syntax-globalinst>` :math:`S.\GLOBALS[a]`.

5. Let :math:`v` be the value :math:`\X{glob}.\VALUE`.

6. Push the value :math:`v` to the operand stack.

.. math::
   \frac{
     S.\GLOBALS[F.\INST.\GLOBALS[x]].\VALUE = v
   }{
     S; F; (\GETGLOBAL~x) \stepto S; F; v
   }


.. _exec-set_global:

:math:`\SETGLOBAL~x`
....................

1. Pop the value :math:`(t.\CONST~c)` from the operand stack.

2. Assert: due to :ref:`validation <valid-set_global>`, :math:`F.\INST.\GLOBALS[x]` is defined.

3. Let :math:`a` be the :ref:`global address <syntax-globaladdr>` :math:`F.\INST.\GLOBALS[x]`.

4. Assert: due to :ref:`validation <valid-set_global>`, :math:`S.\GLOBALS[a]` is defined.

5. Let :math:`\X{glob}` be the :ref:`global instance <syntax-globalinst>` :math:`S.\GLOBALS[a]`.

6. Assert: due to :ref:`validation <valid-set_global>`, :math:`\X{glob}.\VALUE` has the :ref:`value type <syntax-valtype>` :math:`t`.

6. Replace :math:`S.\GLOBALS[a]` with the value :math:`(t.\CONST~c)`.

.. math::
   \frac{
     S' = S~\mbox{with}~\GLOBALS[F.\INST.\GLOBALS[x]].\VALUE = v
   }{
     S; F; v~(\GETGLOBAL~x) \stepto S'; F; \epsilon
   }


.. _exec-instr-memory:
.. _exec-memarg:
.. index:: memory instruction, memory index, store, frame, address, memory address, memory instance, store, frame, value type, width
   pair: execution; instruction
   single: abstract syntax; instruction

Memory Instructions
~~~~~~~~~~~~~~~~~~~

.. _exec-load:

:math:`t\K{.}\LOAD~\memarg`
...........................

1. Pop the value :math:`(t_1.\CONST~i)` from the operand stack.

2. Assert: due to :ref:`validation <valid-load>`, :math:`t_1` is the :ref:`value type <syntax-valtype>` |I32|.

3. Let :math:`k` be the integer :math:`i` converted to an :ref:`unsigned integer <syntax-uint>`.

4. Assert: due to :ref:`validation <valid-load>`, :math:`F.\INST.\MEMS[0]` is defined.

5. Let :math:`a` be the :ref:`memory address <syntax-memaddr>` :math:`F.\INST.\MEMS[0]`.

6. Assert: due to :ref:`validation <valid-load>`, :math:`S.\MEMS[a]` is defined.

7. Let :math:`\X{mem}` be the :ref:`memory instance <syntax-meminst>` :math:`S.\MEMS[a]`.

8. Let :math:`\X{ea}` be :math:`k + \memarg.\OFFSET`.

9. Let :math:`w` be the :ref:`width <syntax-valtype>` :ref:`value type <syntax-valtype>` of :math:`t`.

10. If :math:`\X{ea} + w` is larger than the length of :math:`\X{mem}.\DATA`, then:

   a. Trap.

10. Let :math:`b^\ast` be the byte sequence :math:`\X{mem}.\DATA[\X{ea}:\X{ea}+w]`.

11. Let :math:`c` be the result of computing :math:`\ofbits_t(b^\ast)`.

12. Push the value :math:`(t.\CONST~c)` to the operand stack.

.. math::
   \frac{
     \X{ea} = \uint{k} + \memarg.\OFFSET
     \qquad
     \X{ea} + |t| > |S.\MEMS[F.\INST.\MEMS[0]].\DATA|
   }{
     S; F; (\I32.\CONST~k)~t.\LOAD~\memarg \stepto S; F; \TRAP
   }

.. math::
   \frac{
     \X{ea} = \uint{k} + \memarg.\OFFSET
     \qquad
     \X{ea} + |t| \leq |S.\MEMS[F.\INST.\MEMS[0]].\DATA|
     \qquad
     b^\ast = S.\MEMS[F.\INST.\MEMS[0]].\DATA[\X{ea}:\X{ea}+|t|]
   }{
     S; F; (\I32.\CONST~k)~t.\LOAD~\memarg \stepto S; F; (t.\CONST~\ofbits_t(b^\ast))
   }

.. note::
   The alignment :math:`\memarg.\ALIGN` does not affect the semantics.
   Unaligned access is supported for all types, and succeeds regardless of the annotation.
   The only purpose of the annotation is to provide optimizatons hints.


.. _exec-loadn:

:math:`t\K{.}\LOAD{N}\K{\_}\sx~\memarg`
.......................................

1. Pop the value :math:`(t_1.\CONST~i)` from the operand stack.

2. Assert: due to :ref:`validation <valid-loadn>`, :math:`t_1` is the :ref:`value type <syntax-valtype>` |I32|.

3. Let :math:`k` be the integer :math:`i` converted to an :ref:`unsigned integer <syntax-uint>`.

4. Assert: due to :ref:`validation <valid-loadn>`, :math:`F.\INST.\MEMS[0]` is defined.

5. Let :math:`a` be the :ref:`memory address <syntax-memaddr>` :math:`F.\INST.\MEMS[0]`.

6. Assert: due to :ref:`validation <valid-loadn>`, :math:`S.\MEMS[a]` is defined.

7. Let :math:`\X{mem}` be the :ref:`memory instance <syntax-meminst>` :math:`S.\MEMS[a]`.

8. Let :math:`\X{ea}` be :math:`k + \memarg.\OFFSET`.

9. Let :math:`w` be the :ref:`width <syntax-valtype>` :ref:`value type <syntax-valtype>` of :math:`t`.

10. If :math:`\X{ea} + N` is larger than the length of :math:`\X{mem}.\DATA`, then:

   a. Trap.

10. Let :math:`b^\ast` be the byte sequence :math:`\X{mem}.\DATA[\X{ea}:\X{ea}+N]`.

11. Let :math:`n` be the result of computing :math:`\ofbits_{\iX{N}}(b^\ast)`.

11. Let :math:`c` be the result of computing :math:`\extend_{N,w,\sx}(n)`.

12. Push the value :math:`(t.\CONST~c)` to the operand stack.

.. math::
   \frac{
     \X{ea} = \uint{k} + \memarg.\OFFSET
     \qquad
     \X{ea} + N > |S.\MEMS[F.\INST.\MEMS[0]].\DATA|
   }{
     S; F; (\I32.\CONST~k)~t.\LOAD~\memarg \stepto S; F; \TRAP
   }

.. math::
   \frac{
     \X{ea} = \uint{k} + \memarg.\OFFSET
     \qquad
     \X{ea} + N \leq |S.\MEMS[F.\INST.\MEMS[0]].\DATA|
     \qquad
     b^\ast = S.\MEMS[F.\INST.\MEMS[0]].\DATA[\X{ea}:\X{ea}+N]
   }{
     S; F; (\I32.\CONST~k)~t.\LOAD{N}\K{\_}\sx~\memarg \stepto S; F; (t.\CONST~\extend_{N,|t|,\sx}(\ofbits_t(b^\ast)))
   }


.. _exec-store:

:math:`t\K{.}\STORE~\memarg`
............................

1. Pop the value :math:`(t_2.\CONST~i)` from the operand stack.

2. Assert: due to :ref:`validation <valid-store>`, :math:`t_2` is the :ref:`value type <syntax-valtype>` |I32|.

3. Let :math:`k` be the integer :math:`i` converted to an :ref:`unsigned integer <syntax-uint>`.

4. Pop the value :math:`(t_1.\CONST~c)` from the operand stack.

5. Assert: due to :ref:`validation <valid-store>`, :math:`t_1` is the same :ref:`value type <syntax-valtype>` as :math:`t`.

4. Assert: due to :ref:`validation <valid-store>`, :math:`F.\INST.\MEMS[0]` is defined.

5. Let :math:`a` be the :ref:`memory address <syntax-memaddr>` :math:`F.\INST.\MEMS[0]`.

6. Assert: due to :ref:`validation <valid-store>`, :math:`S.\MEMS[a]` is defined.

7. Let :math:`\X{mem}` be the :ref:`memory instance <syntax-meminst>` :math:`S.\MEMS[a]`.

8. Let :math:`\X{ea}` be :math:`k + \memarg.\OFFSET`.

9. Let :math:`w` be the :ref:`width <syntax-valtype>` :ref:`value type <syntax-valtype>` of :math:`t`.

10. If :math:`\X{ea} + w` is larger than the length of :math:`\X{mem}.\DATA`, then:

   a. Trap.

11. Let :math:`b^\ast` be the byte sequence resulting from computing :math:`\tobits_t(c)`.

12. Replace the bytes :math:`\X{mem}.\DATA[\X{ea}:\X{ea}+w]` with :math:`b^\ast`.

.. math::
   \frac{
     \X{ea} = \uint{k} + \memarg.\OFFSET
     \qquad
     \X{ea} + |t| > |S.\MEMS[F.\INST.\MEMS[0]].\DATA|
   }{
     S; F; (t.\CONST~c)~(\I32.\CONST~k)~t.\STORE~\memarg \stepto S; F; \TRAP
   }

.. math::
   \frac{
     \X{ea} = \uint{k} + \memarg.\OFFSET
     \qquad
     \X{ea} + |t| \leq |S.\MEMS[F.\INST.\MEMS[0]].\DATA|
     \qquad
     S' = S~\mbox{with}~\MEMS[F.\INST.\MEMS[0]].\DATA[\X{ea}:\X{ea}+|t|] = \tobits_t(c)
   }{
     S; F; (t.\CONST~c)~(\I32.\CONST~k)~t.\STORE~\memarg \stepto S'; F; \epsilon
   }


.. _exec-storen:

:math:`t\K{.}\STORE{N}~\memarg`
...............................

1. Pop the value :math:`(t_2.\CONST~i)` from the operand stack.

2. Assert: due to :ref:`validation <valid-storen>`, :math:`t_2` is the :ref:`value type <syntax-valtype>` |I32|.

3. Let :math:`k` be the integer :math:`i` converted to an :ref:`unsigned integer <syntax-uint>`.

4. Pop the value :math:`(t_1.\CONST~c)` from the operand stack.

5. Assert: due to :ref:`validation <valid-storen>`, :math:`t_1` is the same :ref:`value type <syntax-valtype>` as :math:`t`.

4. Assert: due to :ref:`validation <valid-storen>`, :math:`F.\INST.\MEMS[0]` is defined.

5. Let :math:`a` be the :ref:`memory address <syntax-memaddr>` :math:`F.\INST.\MEMS[0]`.

6. Assert: due to :ref:`validation <valid-storen>`, :math:`S.\MEMS[a]` is defined.

7. Let :math:`\X{mem}` be the :ref:`memory instance <syntax-meminst>` :math:`S.\MEMS[a]`.

8. Let :math:`\X{ea}` be :math:`k + \memarg.\OFFSET`.

9. Let :math:`w` be the :ref:`width <syntax-valtype>` :ref:`value type <syntax-valtype>` of :math:`t`.

10. If :math:`\X{ea} + N` is larger than the length of :math:`\X{mem}.\DATA`, then:

   a. Trap.

11. Let :math:`n` be the result of computing :math:`\wrap_N(c)`.

12. Let :math:`b^\ast` be the byte sequence resulting from computing :math:`\tobits_t(n)`.

13. Replace the bytes :math:`\X{mem}.\DATA[\X{ea}:\X{ea}+N]` with :math:`b^\ast`.

.. math::
   \frac{
     \X{ea} = \uint{k} + \memarg.\OFFSET
     \qquad
     \X{ea} + N > |S.\MEMS[F.\INST.\MEMS[0]].\DATA|
   }{
     S; F; (t.\CONST~c)~(\I32.\CONST~k)~t.\STORE{N}~\memarg \stepto S; F; \TRAP
   }

.. math::
   \frac{
     \X{ea} = \uint{k} + \memarg.\OFFSET
     \qquad
     \X{ea} + N \leq |S.\MEMS[F.\INST.\MEMS[0]].\DATA|
     \qquad
     S' = S~\mbox{with}~\MEMS[F.\INST.\MEMS[0]].\DATA[\X{ea}:\X{ea}+N] = \tobits_t(\wrap_N(c))
   }{
     S; F; (t.\CONST~c)~(\I32.\CONST~k)~t.\STORE{N}~\memarg \stepto S'; F; \epsilon
   }


.. _exec-current_memory:

:math:`\CURRENTMEMORY`
......................

1. Assert: due to :ref:`validation <valid-current_memory>`, :math:`F.\INST.\MEMS[0]` is defined.

2. Let :math:`a` be the :ref:`memory address <syntax-memaddr>` :math:`F.\INST.\MEMS[0]`.

3. Assert: due to :ref:`validation <valid-current_memory>`, :math:`S.\MEMS[a]` is defined.

4. Let :math:`\X{mem}` be the :ref:`memory instance <syntax-meminst>` :math:`S.\MEMS[a]`.

5. Let :math:`\X{sz}` be the length of :math:`\X{mem}.\DATA` divided by the :ref:`page size <page-size>`.

6. Push the value :math:`(\I32.\CONST~\X{sz})` to the operand stack.

.. math::
   \frac{
     |S.\MEMS[F.\INST.\MEMS[0]].\DATA| = \X{sz}\cdot64\,\F{Ki}
   }{
     S; F; \CURRENTMEMORY \stepto S; F; (\I32.\CONST~\X{sz})
   }


.. _exec-grow_memory:

:math:`\GROWMEMORY`
...................

1. Pop the value :math:`(t.\CONST~c)` from the operand stack.

2. Assert: due to :ref:`validation <valid-grow_memory>`, :math:`t` is the :ref:`value type <syntax-valtype>` |I32|.

3. Let :math:`n` be the integer :math:`c` converted to an :ref:`unsigned integer <syntax-uint>`.

4. Assert: due to :ref:`validation <valid-grow_memory>`, :math:`F.\INST.\MEMS[0]` is defined.

5. Let :math:`a` be the :ref:`memory address <syntax-memaddr>` :math:`F.\INST.\MEMS[0]`.

6. Assert: due to :ref:`validation <valid-grow_memory>`, :math:`S.\MEMS[a]` is defined.

7. Let :math:`\X{mem}` be the :ref:`memory instance <syntax-meminst>` :math:`S.\MEMS[a]`.

8. Let :math:`\X{sz}` be the length of :math:`S.\MEMS[a]` divided by the :ref:`page size <page-size>`.

9. If :math:`X{mem}.\MAX` is not empty and :math:`\X{sz} + n` is larger than :math:`\X{mem}.\MAX`, then:

  a. Push the value :math:`(\I32.\CONST~{-1})` to the operand stack.

10. Either:

  a. Let :math:`\X{len}` be :math:`n` multiplied with the :ref:`page size <page-size>`.

  b. Append :math:`\X{len}` bytes with value :math:`\hex{00}` to :math:`S.\MEMS[a]`.

  c. Push the value :math:`(\I32.\CONST~\X{sz})` to the operand stack.

11. Or:

  a. Push the value :math:`(\I32.\CONST~{-1})` to the operand stack.

.. math::
   \frac{
     F.\INST.\MEMS[0] = a
     \qquad
     |S.\MEMS[a].\DATA| = \X{sz}\cdot64\,\F{Ki}
     \qquad
     S.\MEMS[a].\MAX = \epsilon \vee \X{sz} + \uint(n) \leq S.\MEMS[a].\MAX
     \qquad
     S' = S~\mbox{with}~\MEMS[a].\DATA = S.\MEMS[a].\DATA~(\hex{00})^{\uint(n)\cdot64\,\F{Ki}}
   }{
     S; F; (\I32.\CONST~n)~\GROWMEMORY \stepto S'; F; (\I32.\CONST~\X{sz})
   }

.. math::
   \frac{
     F.\INST.\MEMS[0] = a
     \qquad
     |S.\MEMS[a].\DATA| = \X{sz}\cdot64\,\F{Ki}
     \qquad
     \X{sz} + \uint(n) > S.\MEMS[a].\MAX
   }{
     S; F; (\I32.\CONST~n)~\GROWMEMORY \stepto S'; F; (\I32.\CONST~{-1})
   }

.. math::
   \frac{
   }{
     S; F; (\I32.\CONST~n)~\GROWMEMORY \stepto S'; F; (\I32.\CONST~{-1})
   }

.. note::
   The |GROWMEMORY| instruction is non-deterministic.
   It may either succeed, returning the old memory size :math:`\X{sz}`,
   or fail, returning :math:`{-1}`.
   Failure *must* occur if the referenced memory instance has a maximum size defined that would be exceeded.
   However, failure *can* occur in other cases as well.
   In practice, the choice depends on the resources available to the :ref:`embedder <embedder>`.


.. _exec-instr-control:
.. _exec-label:
.. index:: control instructions, structured control, label, block, branch, result type, label index, function index, type index, vector, address, table address, table instance, store, frame
   pair: execution; instruction
   single: abstract syntax; instruction

Control Instructions
~~~~~~~~~~~~~~~~~~~~

.. _exec-nop:

:math:`\NOP`
............

1. Do nothing.

.. math::
   \frac{
   }{
     \NOP \stepto \epsilon
   }


.. _exec-unreachable:

:math:`\UNREACHABLE`
....................

1. Trap.

.. math::
   \frac{
   }{
     \UNREACHABLE \stepto \TRAP
   }


.. _exec-block:

:math:`\BLOCK~[t^?]~\instr^\ast~\END`
.....................................

1. Let :math:`n` be the arity of the :ref:`result type <syntax-resulttype>` :math:`t^?`.

2. Let :math:`L` be a label whose arity is :math:`n` and whose target is the end of the block.

3. With an empty operand stack and :math:`L` pushed on the label stack do:

   a. Execute the instruction sequence :math:`\instr^\ast`.

.. math::
   \frac{
   }{
     \BLOCK~[t^n]~\instr^\ast~\END \stepto \LABEL_n\{\epsilon\}~\instr^\ast~\END
   }


.. _exec-loop:

:math:`\LOOP~[t^?]~\instr^\ast~\END`
....................................

1. Let :math:`n` be the arity of the :ref:`result type <syntax-resulttype>` :math:`t^?`.

2. Let :math:`L` be the label whose arity is :math:`0` and whose target is the start of the loop.

3. With an empty operand stack and :math:`L` pushed on the label stack do:

   a. Execute the instruction sequence :math:`\instr^\ast`.

.. math::
   \frac{
   }{
     \LOOP~[t^?]~\instr^\ast~\END \stepto \LABEL_0\{\LOOP~[t^?]~\instr^\ast~\END\}~\instr^\ast~\END
   }


.. _exec-if:

:math:`\IF~[t^?]~\instr_1^\ast~\ELSE~\instr_2^\ast~\END`
........................................................

1. Pop the value :math:`(t.\CONST~c)` from the operand stack.

2. Assert: due to :ref:`validation <valid-if>`, :math:`t` is the :ref:`value type <syntax-valtype>` |I32|.

3. If :math:`c` is not :math:`0`, then:

   a. Execute the instruction :math:`\BLOCK~[t^?]~\instr_1^\ast~\END`.

4. Else:

   a. Execute the instruction :math:`\BLOCK~[t^?]~\instr_2^\ast~\END`.

.. math::
   \frac{
     n \neq 0
   }{
     (\I32.\CONST~n)~\IF~[t^?]~\instr_1^\ast~\END \stepto \BLOCK~[t^?]~\instr_1^\ast~\END
   }

.. math::
   \frac{
     n = 0
   }{
     (\I32.\CONST~n)~\IF~[t^?]~\instr_2^\ast~\END \stepto \BLOCK~[t^?]~\instr_2^\ast~\END
   }


.. _exec-br:

:math:`\BR~l`
.............

.. todo::

1. Assert: due to :ref:`validation <valid-br>`, :math:`l` is defined in the label stack.

.. math::
   \frac{
     n \neq 0
   }{
     \LABEL_n\{e^\ast\}~L^j[v^n~(\BR~l)]~\END \stepto v^n~e^\ast
   }


.. _exec-br_if:

:math:`\BRIF~l`
...............

1. Pop the value :math:`(t.\CONST~c)` from the operand stack.

2. Assert: due to :ref:`validation <valid-br_if>`, :math:`t` is the :ref:`value type <syntax-valtype>` |I32|.

3. If :math:`c` is not :math:`0`, then:

   a. Execute the instruction :math:`(\BR~l)`.

4. Else:

   a. Do nothing.

.. math::
   \frac{
     n \neq 0
   }{
     (\I32.\CONST~n)~(\BRIF~l) \stepto (\BR~l)
   }

.. math::
   \frac{
     n = 0
   }{
     (\I32.\CONST~n)~(\BRIF~l) \stepto \epsilon
   }


.. _exec-br_table:

:math:`\BRTABLE~l^\ast~l_N`
...........................

1. Pop the value :math:`(t.\CONST~c)` from the operand stack.

2. Assert: due to :ref:`validation <valid-br_if>`, :math:`t` is the :ref:`value type <syntax-valtype>` |I32|.

3. Let :math:`i` be the integer :math:`c` converted to an :ref:`unsigned integer <syntax-uint>`.

4. If :math:`i` is smaller than the length of :math:`l^\ast`, then:

   a. Let :math:`l_i` be the label :math:`l^\ast[i]`.

   b. Execute the instruction :math:`(\BR~l_i)`.

5. Else:

   a. Execute the instruction :math:`(\BR~l_N)`.

.. math::
   \frac{
     l^\ast[\uint(i)] = l_i
   }{
     (\I32.\CONST~i)~(\BRTABLE~l^\ast~l_N) \stepto (\BR~l_i)
   }

.. math::
   \frac{
     |l^\ast| \leq \uint(i)
   }{
     (\I32.\CONST~i)~(\BRTABLE~l^\ast~l_N) \stepto (\BR~l_N)
   }


.. _exec-return:

:math:`\RETURN`
...............

1. Let :math:`n` be the length of the label stack.

2. Assert: due to :ref:`validation <valid-return>`, :math:`n` is larger than :math:`0`.

3. Execute the instruction :math:`(\BR~n-1)`.

.. todo::

.. math::
   \frac{
     C.\LABELS[|C.\LABELS|-1] = [t^?]
   }{
     \RETURN \stepto (\BR~)
   }

.. note::
   The |RETURN| instruction branches to the outermost label,
   which, if present, always is the label of the implicit block of the current function body.


.. _exec-call:

:math:`\CALL~x`
...............

1. Assert: due to :ref:`validation <valid-call>`, :math:`F.\INST.\FUNCS[x]` is defined.

2. Let :math:`f` be the :ref:`function <syntax-func>` :math:`F.\INST.\FUNCS[x]`.

.. todo::



.. _exec-call_indirect:

:math:`\CALLINDIRECT~x`
.......................

1. Pop the value :math:`(t.\CONST~c)` from the operand stack.

2. Assert: due to :ref:`validation <valid-callindirect>`, :math:`t` is the :ref:`value type <syntax-valtype>` |I32|.

3. Let :math:`i` be the integer :math:`c` converted to an :ref:`unsigned integer <syntax-uint>`.

4. Assert: due to :ref:`validation <valid-call_indirect>`, :math:`F.\INST.\TYPES[x]` is defined.

5. Let :math:`\X{ft}_{\F{expect}}` be the :ref:`function type <syntax-functype>` :math:`F.\INST.\TYPES[x]`.

6. Assert: due to :ref:`validation <valid-call_indirect>`, :math:`F.\INST.\TABLES[0]` is defined.

7. Let :math:`a` be the :ref:`table address <syntax-tableaddr>` :math:`F.\INST.\TABLES[x]`.

8. Let :math:`\X{tab}` be the :ref:`table instance <syntax-tableinst>` :math:`S.\TABLES[a]`.

9. If :math:`i` is not smaller than the length of :math:`\X{tab}.\ELEM`, then:

   a. Trap.

10. If :math:`\X{tab}.\ELEM[i]` is uninitialized, then:

   a. Trap.

11. Let :math:`\X{func}` be the :ref:`function <syntax-func>` :math:`\X{tab}.\ELEM[i]`.

12. Assert: due to :ref:`validation <valid-func>`, :math:`\X{func}.\INST.\TYPES[\X{func}.\TYPE]` is defined.

13. Let :math:`\X{ft}_{\F{actual}}` be the :ref:`function type <syntax-functype>` :math:`\X{func}.\INST.\TYPES[\X{func}.\TYPE]`.

14. If :math:`\X{ft}_{\F{actual}}` and :math:`\X{ft}_{\F{expect}}` differ, then:

   a. Trap.

15. Invoke the function :math:`\X{func}`.

.. math::
   \frac{
     |S.\TABLES[F.\INST.\TABLES[0]].\ELEM| \leq \uint(i)
   }{
     S; F; (\I32.\CONST~i)~\CALLINDIRECT~x \stepto S; F; \TRAP
   }

.. math::
   \frac{
     S.\TABLES[F.\INST.\TABLES[0]].\ELEM[\uint(i)] = \epsilon
   }{
     S; F; (\I32.\CONST~i)~\CALLINDIRECT~x \stepto S; F; \TRAP
   }

.. math::
   \frac{
     S.\TABLES[F.\INST.\TABLES[0]].\ELEM[\uint(i)] = f
     \qquad
     F.\INST.\TYPES[x] \neq f.\INST.\TYPES[f.\TYPE]
   }{
     S; F; (\I32.\CONST~i)~\CALLINDIRECT~x \stepto S; F; \TRAP
   }

.. math::
   \frac{
     S.\TABLES[F.\INST.\TABLES[0]].\ELEM[\uint(i)] = f
     \qquad
     F.\INST.\TYPES[x] = f.\INST.\TYPES[f.\TYPE]
   }{
     S; F; (\I32.\CONST~i)~\CALLINDIRECT~x \stepto S; F; (\INVOKE~f)
   }


.. _exec-instr-invoke:

Invocation
..........

.. todo::


.. _exec-instr-seq:
.. index:: instruction, instruction sequence

Instruction Sequences
~~~~~~~~~~~~~~~~~~~~~

1. If a label is provided, then:

   a. Push the label to the label stack.

2. With a new, empty operand stack, do:

   a. While no trap occurred and there are more instructions in the sequence, do:

      i. Let :math:`\instr` be the next instruction.

      ii. Execute :math:`\instr`.

      iii. Remove :math:`\instr` from the sequence.

   b. Pop the results :math:`v^n` from the operand stack.

3. If there is a label on the label stack, then:

   a. Assert: due to :ref:`validation <valid-instr-seq>`, the arity of the label is :math:`n`.

   b. Pop the label from the label stack.

4. If no trap occurred, then:

   a. Return :math:`v^n`.
      
      

1. If no instructions are left in the instruction sequence, then:

   a. If there is a label on the label stack:

      i. Pop all results :math:`v^n` from the operand stack.

      ii. Assert: due to :ref:`validation <valid-instr-seq>`, the arity of the label is :math:`n`.

      iii. Pop the label from the label stack.

      iv. Restore the previous operand stack.

      v. Push the results :math:`v^n` back to the operand stack.

2. Else, if a |TRAP| occurs in an instruction sequence, then:

   a. Pop all results :math:`v^n` from the operand stack.

   b. If there is a label on the label stack:

      i. Pop the label from the label stack.

   c. Trap.

3. Else:

   a. Let :math:`\instr` be the next instruction.

   b. Execute :math:`\instr`.

   c. Execute the remainder of the instruction sequence.

.. math::
   \frac{
   }{
     \LABEL_n\{e^\ast\}~v^n~\END \stepto v^n
   }

.. math::
   \frac{
   }{
     \LABEL_n\{e^\ast\}~\TRAP~\END \stepto \TRAP
   }

.. math::
   \frac{
     L^0 \neq [\_]
   }{
     L^0[\TRAP] \stepto \TRAP
   }


Non-empty Instruction Sequence: :math:`\instr^\ast~\instr_N`
............................................................

* The instruction sequence :math:`\instr^\ast` must be valid with type :math:`[t_1^\ast] \to [t_2^\ast]`,
  for some sequences of :ref:`value types <syntax-valtype>` :math:`t_1^\ast` and :math:`t_2^\ast`.

* The instruction :math:`\instr_N` must be valid with type :math:`[t^\ast] \to [t_3^\ast]`,
  for some sequences of :ref:`value types <syntax-valtype>` :math:`t^\ast` and :math:`t_3^\ast`.

* There must be a sequence of :ref:`value types <syntax-valtype>` :math:`t_0^\ast`,
  such that :math:`t_2^\ast = t_0^\ast~t^\ast`.

* Then the combined instruction sequence is valid with type :math:`[t_1^\ast] \to [t_0^\ast~t_3^\ast]`.

.. math::
   \frac{
     C \vdash \instr^\ast : [t_1^\ast] \to [t_0^\ast~t^\ast]
     \qquad
     C \vdash \instr_N : [t^\ast] \to [t_3^\ast]
   }{
     C \vdash \instr^\ast~\instr_N : [t_1^\ast] \to [t_0^\ast~t_3^\ast]
   }


.. _exec-expr:
.. index:: expression
   pair: execution; expression
   single: abstract syntax; expression
   single: expression; constant

Expressions
~~~~~~~~~~~

Expressions :math:`\expr` are classified by :ref:`result types <syntax-resulttype>` of the form :math:`[t^?]`.


:math:`\instr^\ast~\END`
........................

.. todo::

.. math::
   \frac{
     S; F; \instr^\ast \stepto^\ast S; F; v
   }{
     S; F; \instr^\ast~\END \stepto^\ast v
   }
