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


.. _exec-const:

:math:`t\K{.}\CONST~c`
......................

1. Push the value :math:`t.\CONST~c` to the stack.

.. note::
   There is no formal reduction rule for this instruction, since constant instructions are already considered values.


.. _exec-unop:

:math:`t\K{.}\unop`
...................

1. Assert: due to :ref:`validation <valid-unop>`, a value of :ref:`value type <syntax-valtype>` :math:`t` is on the top of the stack.

2. Pop the value :math:`t.\CONST~c_1` from the stack.

3. If :math:`\unop_t(c_1)` is defined, then:

   a. Let :math:`c` be the result of computing :math:`\unop_t(c_1)`.

   b. Push the value :math:`t.\CONST~c` to the stack.

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

1. Assert: due to :ref:`validation <valid-binop>`, two values of :ref:`value type <syntax-valtype>` :math:`t` are on the top of the stack.

2. Pop the value :math:`t.\CONST~c_2` from the stack.

3. Pop the value :math:`t.\CONST~c_1` from the stack.

4. If :math:`\binop_t(c_1, c_2)` is defined, then:

   a. Let :math:`c` be the result of computing :math:`\binop_t(c_1, c_2)`.

   b. Push the value :math:`t.\CONST~c` to the stack.

5. Else:

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

1. Assert: due to :ref:`validation <valid-testop>`, a value of :ref:`value type <syntax-valtype>` :math:`t` is on the top of the stack.

2. Pop the value :math:`t.\CONST~c_1` from the stack.

3. Let :math:`c` be the result of computing :math:`\testop_t(c_1)`.

4. Push the value :math:`\I32.\CONST~c` to the stack.

.. math::
   \frac{
     \testop_t(c_1) = c
   }{
     (t\K{.}\CONST~c_1)~t\K{.}\testop \stepto (\I32\K{.}\CONST~c)
   }


.. _exec-relop:

:math:`t\K{.}\relop`
....................

1. Assert: due to :ref:`validation <valid-relop>`, two values of :ref:`value type <syntax-valtype>` :math:`t` are on the top of the stack.

2. Pop the value :math:`t.\CONST~c_2` from the stack.

3. Pop the value :math:`t.\CONST~c_1` from the stack.

4. Let :math:`c` be the result of computing :math:`\relop_t(c_1, c_2)`.

5. Push the value :math:`\I32.\CONST~c` to the stack.

.. math::
   \frac{
     \relop_t(c_1, c_2) = c
   }{
     (t\K{.}\CONST~c_1)~(t\K{.}\CONST~c_2)~t\K{.}\relop \stepto (\I32\K{.}\CONST~c)
   }


.. _exec-cvtop:

:math:`t_2\K{.}\cvtop/t_1`
..........................

1. Assert: due to :ref:`validation <valid-cvtop>`, a value of :ref:`value type <syntax-valtype>` :math:`t_1` is on the top of the stack.

2. Pop the value :math:`t_1.\CONST~c_1` from the stack.

3. If :math:`\cvtop_{t_1,t_2}(c_1)` is defined:

   a. Let :math:`c_2` be the result of computing :math:`\cvtop_{t_1,t_2}(c_1)`.

   b. Push the value :math:`t_2.\CONST~c_2` to the stack.

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

1. Assert: due to :ref:`validation <valid-drop>`, a value is on the top of the stack.

2. Pop the value :math:`v` from the stack.

.. math::
   \frac{
   }{
     v~\DROP \stepto \epsilon
   }


.. _exec-select:

:math:`\SELECT`
...............

1. Assert: due to :ref:`validation <valid-select>`, a value :ref:`value type <syntax-valtype>` |I32| is on the top of the stack.

2. Pop the value :math:`\I32.\CONST~n` from the stack.

3. Assert: due to :ref:`validation <valid-select>`, two more values (of the same :ref:`value type <syntax-valtype>`) are on the top of the stack.

4. Pop the value :math:`v_2` from the stack.

5. Pop the value :math:`v_1` from the stack.

6. If :math:`n` is not :math:`0`, then:

   a. Push the value :math:`v_1` back to the stack.

7. Else:

   a. Push the value :math:`v_2` back to the stack.

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

1. Let :math:`F` be the current :ref:`frame <syntax-frame>`.

2. Assert: due to :ref:`validation <valid-get_local>`, :math:`F.\LOCALS[x]` is defined.

3. Let :math:`v` be the value :math:`F.\LOCALS[x]`.

4. Push the value :math:`v` to the stack.

.. math::
   \frac{
     F.\LOCALS[x] = v
   }{
     F; (\GETLOCAL~x) \stepto F; v
   }


.. _exec-set_local:

:math:`\SETLOCAL~x`
...................

1. Let :math:`F` be the current :ref:`frame <syntax-frame>`.

2. Assert: due to :ref:`validation <valid-set_local>`, :math:`F.\LOCALS[x]` is defined.

3. Assert: due to :ref:`validation <valid-set_local>`, a value is on the top of the stack.

4. Pop the value :math:`v` from the stack.

5. Replace :math:`F.\LOCALS[x]` with the value :math:`v`.

.. math::
   \frac{
     F' = F~\mbox{with}~\LOCALS[x] = v
   }{
     F; v~(\SETLOCAL~x) \stepto F'; \epsilon
   }


.. _exec-tee_local:

:math:`\TEELOCAL~x`
...................

1. Assert: due to :ref:`validation <valid-tee_local>`, a value is on the top of the stack.

2. Pop the value :math:`v` from the stack.

3. Push the value :math:`v` to the stack.

4. Push the value :math:`v` to the stack.

5. Execute the instruction :math:`(\SETLOCAL~x)`.

.. math::
   \frac{
   }{
     F; v~(\TEELOCAL~x) \stepto F'; v~v~(\SETLOCAL~x)
   }


.. _exec-get_global:

:math:`\GETGLOBAL~x`
....................

1. Let :math:`F` be the current :ref:`frame <syntax-frame>`.

2. Assert: due to :ref:`validation <valid-get_global>`, :math:`F.\MODULE.\GLOBALS[x]` is defined.

3. Let :math:`a` be the :ref:`global address <syntax-globaladdr>` :math:`F.\MODULE.\GLOBALS[x]`.

4. Assert: due to :ref:`validation <valid-get_global>`, :math:`S.\GLOBALS[a]` is defined.

5. Let :math:`\X{glob}` be the :ref:`global instance <syntax-globalinst>` :math:`S.\GLOBALS[a]`.

6. Let :math:`v` be the value :math:`\X{glob}.\VALUE`.

7. Push the value :math:`v` to the stack.

.. math::
   \frac{
     S.\GLOBALS[F.\MODULE.\GLOBALS[x]].\VALUE = v
   }{
     S; F; (\GETGLOBAL~x) \stepto S; F; v
   }


.. _exec-set_global:

:math:`\SETGLOBAL~x`
....................

1. Let :math:`F` be the current :ref:`frame <syntax-frame>`.

2. Assert: due to :ref:`validation <valid-set_global>`, :math:`F.\MODULE.\GLOBALS[x]` is defined.

3. Let :math:`a` be the :ref:`global address <syntax-globaladdr>` :math:`F.\MODULE.\GLOBALS[x]`.

4. Assert: due to :ref:`validation <valid-set_global>`, :math:`S.\GLOBALS[a]` is defined.

5. Let :math:`\X{glob}` be the :ref:`global instance <syntax-globalinst>` :math:`S.\GLOBALS[a]`.

6. Assert: due to :ref:`validation <valid-set_global>`, a value is on the top of the stack.

7. Pop the value :math:`v` from the stack.

8. Replace :math:`S.\GLOBALS[a]` with the value :math:`v`.

.. math::
   \frac{
     S' = S~\mbox{with}~\GLOBALS[F.\MODULE.\GLOBALS[x]].\VALUE = v
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

1. Let :math:`F` be the current :ref:`frame <syntax-frame>`.

2. Assert: due to :ref:`validation <valid-load>`, :math:`F.\MODULE.\MEMS[0]` is defined.

3. Let :math:`a` be the :ref:`memory address <syntax-memaddr>` :math:`F.\MODULE.\MEMS[0]`.

4. Assert: due to :ref:`validation <valid-load>`, :math:`S.\MEMS[a]` is defined.

5. Let :math:`\X{mem}` be the :ref:`memory instance <syntax-meminst>` :math:`S.\MEMS[a]`.

6. Assert: due to :ref:`validation <valid-load>`, a value :ref:`value type <syntax-valtype>` |I32| is on the top of the stack.

7. Pop the value :math:`\I32.\CONST~i` from the stack.

8. Let :math:`k` be the integer :math:`i` converted to an :ref:`unsigned integer <syntax-uint>`.

9. Let :math:`\X{ea}` be :math:`k + \memarg.\OFFSET`.

10. Let :math:`w` be the :ref:`width <syntax-valtype>` :ref:`value type <syntax-valtype>` of :math:`t`.

11. If :math:`\X{ea} + w` is larger than the length of :math:`\X{mem}.\DATA`, then:

   a. Trap.

12. Let :math:`b^\ast` be the byte sequence :math:`\X{mem}.\DATA[\X{ea}:\X{ea}+w]`.

13. Let :math:`c` be the result of computing :math:`\ofbits_t(b^\ast)`.

14. Push the value :math:`t.\CONST~c` to the stack.

.. math::
   \frac{
     \X{ea} = \uint{k} + \memarg.\OFFSET
     \qquad
     \X{ea} + |t| > |S.\MEMS[F.\MODULE.\MEMS[0]].\DATA|
   }{
     S; F; (\I32.\CONST~k)~(t.\LOAD~\memarg) \stepto S; F; \TRAP
   }

.. math::
   \frac{
     \begin{array}{@{}c@{}}
     \X{ea} = \uint{k} + \memarg.\OFFSET
     \qquad
     \X{ea} + |t| \leq |S.\MEMS[F.\MODULE.\MEMS[0]].\DATA|
     \\
     b^\ast = S.\MEMS[F.\MODULE.\MEMS[0]].\DATA[\X{ea}:\X{ea}+|t|]
     \end{array}
   }{
     S; F; (\I32.\CONST~k)~(t.\LOAD~\memarg) \stepto S; F; (t.\CONST~\ofbits_t(b^\ast))
   }

.. note::
   The alignment :math:`\memarg.\ALIGN` does not affect the semantics.
   Unaligned access is supported for all types, and succeeds regardless of the annotation.
   The only purpose of the annotation is to provide optimizatons hints.


.. _exec-loadn:

:math:`t\K{.}\LOAD{N}\K{\_}\sx~\memarg`
.......................................

1. Let :math:`F` be the current :ref:`frame <syntax-frame>`.

2. Assert: due to :ref:`validation <valid-loadn>`, :math:`F.\MODULE.\MEMS[0]` is defined.

3. Let :math:`a` be the :ref:`memory address <syntax-memaddr>` :math:`F.\MODULE.\MEMS[0]`.

4. Assert: due to :ref:`validation <valid-loadn>`, :math:`S.\MEMS[a]` is defined.

5. Let :math:`\X{mem}` be the :ref:`memory instance <syntax-meminst>` :math:`S.\MEMS[a]`.

6. Assert: due to :ref:`validation <valid-loadn>`, a value :ref:`value type <syntax-valtype>` |I32| is on the top of the stack.

7. Pop the value :math:`\I32.\CONST~i` from the stack.

8. Let :math:`k` be the integer :math:`i` converted to an :ref:`unsigned integer <syntax-uint>`.

9. Let :math:`\X{ea}` be :math:`k + \memarg.\OFFSET`.

10. Let :math:`w` be the :ref:`width <syntax-valtype>` :ref:`value type <syntax-valtype>` of :math:`t`.

11. If :math:`\X{ea} + N` is larger than the length of :math:`\X{mem}.\DATA`, then:

   a. Trap.

12. Let :math:`b^\ast` be the byte sequence :math:`\X{mem}.\DATA[\X{ea}:\X{ea}+N]`.

13. Let :math:`n` be the result of computing :math:`\ofbits_{\iX{N}}(b^\ast)`.

14. Let :math:`c` be the result of computing :math:`\extend_{N,w,\sx}(n)`.

15. Push the value :math:`t.\CONST~c` to the stack.

.. math::
   \frac{
     \X{ea} = \uint{k} + \memarg.\OFFSET
     \qquad
     \X{ea} + N > |S.\MEMS[F.\MODULE.\MEMS[0]].\DATA|
   }{
     S; F; (\I32.\CONST~k)~(t.\LOAD{N}\K{\_}\sx~\memarg) \stepto S; F; \TRAP
   }

.. math::
   \frac{
     \begin{array}{@{}c@{}}
     \X{ea} = \uint{k} + \memarg.\OFFSET
     \qquad
     \X{ea} + N \leq |S.\MEMS[F.\MODULE.\MEMS[0]].\DATA|
     \\
     b^\ast = S.\MEMS[F.\MODULE.\MEMS[0]].\DATA[\X{ea}:\X{ea}+N]
     \end{array}
   }{
     S; F; (\I32.\CONST~k)~(t.\LOAD{N}\K{\_}\sx~\memarg) \stepto S; F; (t.\CONST~\extend_{N,|t|,\sx}(\ofbits_t(b^\ast)))
   }


.. _exec-store:

:math:`t\K{.}\STORE~\memarg`
............................

1. Let :math:`F` be the current :ref:`frame <syntax-frame>`.

2. Assert: due to :ref:`validation <valid-store>`, :math:`F.\MODULE.\MEMS[0]` is defined.

3. Let :math:`a` be the :ref:`memory address <syntax-memaddr>` :math:`F.\MODULE.\MEMS[0]`.

4. Assert: due to :ref:`validation <valid-store>`, :math:`S.\MEMS[a]` is defined.

5. Let :math:`\X{mem}` be the :ref:`memory instance <syntax-meminst>` :math:`S.\MEMS[a]`.

6. Assert: due to :ref:`validation <valid-store>`, a value :ref:`value type <syntax-valtype>` |I32| is on the top of the stack.

7. Pop the value :math:`\I32.\CONST~i` from the stack.

8. Let :math:`k` be the integer :math:`i` converted to an :ref:`unsigned integer <syntax-uint>`.

9. Let :math:`\X{ea}` be :math:`k + \memarg.\OFFSET`.

10. Let :math:`w` be the :ref:`width <syntax-valtype>` :ref:`value type <syntax-valtype>` of :math:`t`.

11. If :math:`\X{ea} + w` is larger than the length of :math:`\X{mem}.\DATA`, then:

   a. Trap.

12. Assert: due to :ref:`validation <valid-store>`, a value of :ref:`value type <syntax-valtype>` :math:`t` is on the top of the stack.

13. Pop the value :math:`t.\CONST~c` from the stack.

14. Let :math:`b^\ast` be the byte sequence resulting from computing :math:`\tobits_t(c)`.

15. Replace the bytes :math:`\X{mem}.\DATA[\X{ea}:\X{ea}+w]` with :math:`b^\ast`.

.. math::
   \frac{
     \X{ea} = \uint{k} + \memarg.\OFFSET
     \qquad
     \X{ea} + |t| > |S.\MEMS[F.\MODULE.\MEMS[0]].\DATA|
   }{
     S; F; (t.\CONST~c)~(\I32.\CONST~k)~(t.\STORE~\memarg) \stepto S; F; \TRAP
   }

.. math::
   \frac{
     \begin{array}{@{}c@{}}
     \X{ea} = \uint{k} + \memarg.\OFFSET
     \qquad
     \X{ea} + |t| \leq |S.\MEMS[F.\MODULE.\MEMS[0]].\DATA|
     \\
     S' = S~\mbox{with}~\MEMS[F.\MODULE.\MEMS[0]].\DATA[\X{ea}:\X{ea}+|t|] = \tobits_t(c)
     \end{array}
   }{
     S; F; (t.\CONST~c)~(\I32.\CONST~k)~(t.\STORE~\memarg) \stepto S'; F; \epsilon
   }


.. _exec-storen:

:math:`t\K{.}\STORE{N}~\memarg`
...............................

1. Let :math:`F` be the current :ref:`frame <syntax-frame>`.

2. Assert: due to :ref:`validation <valid-storen>`, :math:`F.\MODULE.\MEMS[0]` is defined.

3. Let :math:`a` be the :ref:`memory address <syntax-memaddr>` :math:`F.\MODULE.\MEMS[0]`.

4. Assert: due to :ref:`validation <valid-storen>`, :math:`S.\MEMS[a]` is defined.

5. Let :math:`\X{mem}` be the :ref:`memory instance <syntax-meminst>` :math:`S.\MEMS[a]`.

6. Assert: due to :ref:`validation <valid-storen>`, a value :ref:`value type <syntax-valtype>` |I32| is on the top of the stack.

7. Pop the value :math:`\I32.\CONST~i` from the stack.

8. Let :math:`k` be the integer :math:`i` converted to an :ref:`unsigned integer <syntax-uint>`.

9. Let :math:`\X{ea}` be :math:`k + \memarg.\OFFSET`.

10. Let :math:`w` be the :ref:`width <syntax-valtype>` :ref:`value type <syntax-valtype>` of :math:`t`.

11. If :math:`\X{ea} + N` is larger than the length of :math:`\X{mem}.\DATA`, then:

   a. Trap.

12. Assert: due to :ref:`validation <valid-storen>`, a value of :ref:`value type <syntax-valtype>` :math:`t` is on the top of the stack.

13. Pop the value :math:`t.\CONST~c` from the stack.

14. Let :math:`n` be the result of computing :math:`\wrap_N(c)`.

15. Let :math:`b^\ast` be the byte sequence resulting from computing :math:`\tobits_t(n)`.

16. Replace the bytes :math:`\X{mem}.\DATA[\X{ea}:\X{ea}+N]` with :math:`b^\ast`.

.. math::
   \frac{
     \X{ea} = \uint{k} + \memarg.\OFFSET
     \qquad
     \X{ea} + N > |S.\MEMS[F.\MODULE.\MEMS[0]].\DATA|
   }{
     S; F; (t.\CONST~c)~(\I32.\CONST~k)~(t.\STORE{N}~\memarg) \stepto S; F; \TRAP
   }

.. math::
   \frac{
     \begin{array}{@{}c@{}}
     \X{ea} = \uint{k} + \memarg.\OFFSET
     \qquad
     \X{ea} + N \leq |S.\MEMS[F.\MODULE.\MEMS[0]].\DATA|
     \\
     S' = S~\mbox{with}~\MEMS[F.\MODULE.\MEMS[0]].\DATA[\X{ea}:\X{ea}+N] = \tobits_t(\wrap_N(c))
     \end{array}
   }{
     S; F; (t.\CONST~c)~(\I32.\CONST~k)~(t.\STORE{N}~\memarg) \stepto S'; F; \epsilon
   }


.. _exec-current_memory:

:math:`\CURRENTMEMORY`
......................

1. Let :math:`F` be the current :ref:`frame <syntax-frame>`.

2. Assert: due to :ref:`validation <valid-current_memory>`, :math:`F.\MODULE.\MEMS[0]` is defined.

3. Let :math:`a` be the :ref:`memory address <syntax-memaddr>` :math:`F.\MODULE.\MEMS[0]`.

4. Assert: due to :ref:`validation <valid-current_memory>`, :math:`S.\MEMS[a]` is defined.

5. Let :math:`\X{mem}` be the :ref:`memory instance <syntax-meminst>` :math:`S.\MEMS[a]`.

6. Let :math:`\X{sz}` be the length of :math:`\X{mem}.\DATA` divided by the :ref:`page size <page-size>`.

7. Push the value :math:`\I32.\CONST~\X{sz}` to the stack.

.. math::
   \frac{
     |S.\MEMS[F.\MODULE.\MEMS[0]].\DATA| = \X{sz}\cdot64\,\F{Ki}
   }{
     S; F; \CURRENTMEMORY \stepto S; F; (\I32.\CONST~\X{sz})
   }


.. _exec-grow_memory:

:math:`\GROWMEMORY`
...................

1. Let :math:`F` be the current :ref:`frame <syntax-frame>`.

2. Assert: due to :ref:`validation <valid-grow_memory>`, :math:`F.\MODULE.\MEMS[0]` is defined.

3. Let :math:`a` be the :ref:`memory address <syntax-memaddr>` :math:`F.\MODULE.\MEMS[0]`.

4. Assert: due to :ref:`validation <valid-grow_memory>`, :math:`S.\MEMS[a]` is defined.

5. Let :math:`\X{mem}` be the :ref:`memory instance <syntax-meminst>` :math:`S.\MEMS[a]`.

6. Let :math:`\X{sz}` be the length of :math:`S.\MEMS[a]` divided by the :ref:`page size <page-size>`.

7. Assert: due to :ref:`validation <valid-grow_memory>`, a value of :ref:`value type <syntax-valtype>` |I32| is on the top of the stack.

8. Pop the value :math:`\I32.\CONST~c` from the stack.

9. Let :math:`n` be the integer :math:`c` converted to an :ref:`unsigned integer <syntax-uint>`.

10. If :math:`X{mem}.\MAX` is not empty and :math:`\X{sz} + n` is larger than :math:`\X{mem}.\MAX`, then:

  a. Push the value :math:`\I32.\CONST~{-1}` to the stack.

11. Either:

  a. Let :math:`\X{len}` be :math:`n` multiplied with the :ref:`page size <page-size>`.

  b. Append :math:`\X{len}` bytes with value :math:`\hex{00}` to :math:`S.\MEMS[a]`.

  c. Push the value :math:`\I32.\CONST~\X{sz}` to the stack.

12. Or:

  a. Push the value :math:`\I32.\CONST~{-1}` to the stack.

.. math::
   \frac{
   }{
     S; F; (\I32.\CONST~n)~\GROWMEMORY \stepto S'; F; (\I32.\CONST~{-1})
   } \\
   ~ \\

.. math::
   \frac{
     \begin{array}{@{}c@{}}
     F.\MODULE.\MEMS[0] = a
     \qquad
     |S.\MEMS[a].\DATA| = \X{sz}\cdot64\,\F{Ki}
     \\
     \X{sz} + \uint(n) > S.\MEMS[a].\MAX
     \end{array}
   }{
     S; F; (\I32.\CONST~n)~\GROWMEMORY \stepto S'; F; (\I32.\CONST~{-1})
   } \\
   ~ \\

.. math::
   \frac{
     \begin{array}{@{}c@{}}
     F.\MODULE.\MEMS[0] = a
     \qquad
     |S.\MEMS[a].\DATA| = \X{sz}\cdot64\,\F{Ki}
     \\
     \X{sz} + \uint(n) \leq S.\MEMS[a].\MAX \vee S.\MEMS[a].\MAX = \epsilon
     \\
     S' = S~\mbox{with}~\MEMS[a].\DATA = S.\MEMS[a].\DATA~(\hex{00})^{\uint(n)\cdot64\,\F{Ki}}
     \end{array}
   }{
     S; F; (\I32.\CONST~n)~\GROWMEMORY \stepto S'; F; (\I32.\CONST~\X{sz})
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

3. Execute the :ref:`labelled instruction sequence <exec-instr-seq-labelled>`  :math:`\instr^\ast` with label :math:`L`.

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

3. Execute the :ref:`labelled instruction sequence <exec-instr-seq-labelled>`  :math:`\instr^\ast` with label :math:`L`.

.. math::
   \frac{
   }{
     \LOOP~[t^?]~\instr^\ast~\END \stepto \LABEL_0\{\LOOP~[t^?]~\instr^\ast~\END\}~\instr^\ast~\END
   }


.. _exec-if:

:math:`\IF~[t^?]~\instr_1^\ast~\ELSE~\instr_2^\ast~\END`
........................................................

1. Assert: due to :ref:`validation <valid-if>`, a value of :ref:`value type <syntax-valtype>` |I32| is on the top of the stack.

2. Pop the value :math:`\I32.\CONST~n` from the stack.

3. If :math:`n` is not :math:`0`, then:

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

1. Assert: due to :ref:`validation <valid-br>`, the stack contains at least :math:`l+1` labels.

2. Let :math:`L` be the :math:`(l+1)`-th label from the top of the stack.

3. Let :math:`n` be the arity of :math:`L`.

4. Assert: due to :ref:`validation <valid-br>`, there are at least :math:`n` values on the top of the stack.

5. Pop the values :math:`v^n` from the stack.

6. Repeat :math:`l+1` times:

   a. While the top of the stack is a value, do:

      i. Pop the value from the stack.

   b. Assert: due to validation, the top of the stack now is a label.

   c. Pop the label from the stack.

7. Push the values :math:`v^n` to the stack.

.. math::
   \frac{
     n \neq 0
   }{
     \LABEL_n\{e^\ast\}~L^j[v^n~(\BR~l)]~\END \stepto v^n~e^\ast
   }


.. _exec-br_if:

:math:`\BRIF~l`
...............

1. Assert: due to :ref:`validation <valid-br_if>`, a value of :ref:`value type <syntax-valtype>` |I32| is on the top of the stack.

2. Pop the value :math:`\I32.\CONST~n` from the stack.

3. If :math:`n` is not :math:`0`, then:

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

1. Assert: due to :ref:`validation <valid-if>`, a value of :ref:`value type <syntax-valtype>` |I32| is on the top of the stack.

2. Pop the value :math:`\I32.\CONST~c` from the stack.

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

1. Let :math:`F` be the current :ref:`frame <syntax-frame>`.

2. Let :math:`n` be the arity of :math:`F`.

3. Assert: due to :ref:`validation <valid-br>`, there are at least :math:`n` values on the top of the stack.

4. Pop the results :math:`v^n` from the stack.

5. Assert: due to :ref:`validation <valid-return>`, the stack contains at least one :ref:`frame <syntax-frame>`.

6. While the top of the stack is not a frame, do:

   a. Pop the top element from the stack.

7. Assert: the top of the stack is the frame :math:`F`.

8. Pop the frame from the stack.

9. Push :math:`v^n` to the stack.

.. math::
   \frac{
   }{
     \LOCAL_n\{F\}~L^k[v^n~\RETURN]~\END \stepto v^n
   }


.. _exec-call:

:math:`\CALL~x`
...............

1. Let :math:`F` be the current :ref:`frame <syntax-frame>`.

2. Assert: due to :ref:`validation <valid-call>`, :math:`F.\MODULE.\FUNCS[x]` is defined.

3. Let :math:`\X{f}` be the :ref:`function instance <syntax-funcinst>` :math:`F.\MODULE.\FUNCS[x]`.

4. Invoke the function instance :math:`\X{f}`.

.. math::
   \frac{
     F.\MODULE.\FUNCS[x] = \X{f}
   }{
     F; \CALL~x \stepto F; (\INVOKE~\X{f})
   }



.. _exec-call_indirect:

:math:`\CALLINDIRECT~x`
.......................

1. Let :math:`F` be the current :ref:`frame <syntax-frame>`.

2. Assert: due to :ref:`validation <valid-call_indirect>`, :math:`F.\MODULE.\TABLES[0]` is defined.

3. Let :math:`a` be the :ref:`table address <syntax-tableaddr>` :math:`F.\MODULE.\TABLES[0]`.

4. Let :math:`\X{tab}` be the :ref:`table instance <syntax-tableinst>` :math:`S.\TABLES[a]`.

5. Assert: due to :ref:`validation <valid-call_indirect>`, :math:`F.\MODULE.\TYPES[x]` is defined.

6. Let :math:`\X{ft}_{\F{expect}}` be the :ref:`function type <syntax-functype>` :math:`F.\MODULE.\TYPES[x]`.

7. Assert: due to :ref:`validation <valid-callindirect>`, a value with :ref:`value type <syntax-valtype>` |I32| is on the top of the stack.

8. Pop the value :math:`\I32.\CONST~c` from the stack.

9. Let :math:`i` be the integer :math:`c` converted to an :ref:`unsigned integer <syntax-uint>`.

10. If :math:`i` is not smaller than the length of :math:`\X{tab}.\ELEM`, then:

   a. Trap.

11. If :math:`\X{tab}.\ELEM[i]` is uninitialized, then:

   a. Trap.

12. Let :math:`\X{f}` be the :ref:`function instance <syntax-funcinst>` :math:`\X{tab}.\ELEM[i]`.

13. Assert: due to :ref:`validation <valid-func>`, :math:`\X{func}.\MODULE.\TYPES[\X{f}.\FUNC.\TYPE]` is defined.

14. Let :math:`\X{ft}_{\F{actual}}` be the :ref:`function type <syntax-functype>` :math:`\X{func}.\MODULE.\TYPES[\X{f}.\FUNC.\TYPE]`.

15. If :math:`\X{ft}_{\F{actual}}` and :math:`\X{ft}_{\F{expect}}` differ, then:

   a. Trap.

16. Invoke the function instance :math:`\X{f}`.

.. math::
   \frac{
     |S.\TABLES[F.\MODULE.\TABLES[0]].\ELEM| \leq \uint(i)
   }{
     S; F; (\I32.\CONST~i)~\CALLINDIRECT~x \stepto S; F; \TRAP
   }

.. math::
   \frac{
     S.\TABLES[F.\MODULE.\TABLES[0]].\ELEM[\uint(i)] = \epsilon
   }{
     S; F; (\I32.\CONST~i)~\CALLINDIRECT~x \stepto S; F; \TRAP
   }

.. math::
   \frac{
     S.\TABLES[F.\MODULE.\TABLES[0]].\ELEM[\uint(i)] = f
     \qquad
     F.\MODULE.\TYPES[x] \neq f.\MODULE.\TYPES[f.\FUNC.\TYPE]
   }{
     S; F; (\I32.\CONST~i)~\CALLINDIRECT~x \stepto S; F; \TRAP
   }

.. math::
   \frac{
     S.\TABLES[F.\MODULE.\TABLES[0]].\ELEM[\uint(i)] = f
     \qquad
     F.\MODULE.\TYPES[x] = f.\MODULE.\TYPES[f.\FUNC.\TYPE]
   }{
     S; F; (\I32.\CONST~i)~\CALLINDIRECT~x \stepto S; F; (\INVOKE~f)
   }


.. _exec-instr-invoke:

Invocation of :ref:`Function Instance <syntax-funcinst>` :math:`f`
..................................................................

1. Assert: due to :ref:`validation <valid-func>`, :math:`f.\MODULE.\TYPES[f.\FUNC.\TYPE]` is defined.

2. Let :math:`[t_1^n] \to [t_2^m]` be the `function type <syntax-functype>` :math:`f.\MODULE.\TYPES[f.\FUNC.\TYPE]`.

3. Let :math:`t^n` be the list of :ref:`value types <syntax-valtype>` :math:`f.\FUNC.\LOCALS`.

4. Let :math:`\instr^\ast~\END` be the :ref:`expression <syntax-expr>` :math:`f.\FUNC.\BODY`.

5. Assert: due to :ref:`validation <valid-call>`, :math:`n` values are on the top of the stack.

6. Pop the values :math:`v^n` from the stack.

7. Let :math:`v_0^\ast` be the list of zero values of types :math:`t^\ast`.

8. Let :math:`F` be the :ref:`frame <syntax-frame>` :math:`\{ \MODULE~f.\MODULE, \LOCALS~v^n~v_0^\ast \}`.

9. Push :math:`F` to the stack.

10. Execute the instruction :math:`\BLOCK~[t_2^m]~\instr^\ast~\END`.

.. todo::
   terminate

.. math::
   \frac{
     f.\FUNC = \{ \TYPE~x, \LOCALS~t^ast, \BODY~\instr^\ast~\END \}
     \qquad
     f.\MODULE.\TYPES[x] = [t_1^n] \to [t_2^m]
     \qquad
     F = \{ \MODULE~f.\MODULE, \LOCALS~v^n~(t.\CONST 0)^\ast \}
   }{
     v^n~(\INVOKE~f) \stepto \LOCAL_n\{F\}~\BLOCK~[t_2^m]~\instr^\ast~\END~\END
   }


.. _exec-instr-seq:
.. index:: instruction, instruction sequence

Instruction Sequences
~~~~~~~~~~~~~~~~~~~~~

Plain Sequences :math:`\instr^\ast`
...................................

1. If :math:`\instr^\ast` is not empty, then:

   a. Let :math:`\instr` be the first instruction of the sequence.

   b. Execute :math:`\instr`.

   c. If no trap occurred, then:

      i. Let :math:`{\instr'}^\ast` the remainder of the sequence.

      ii. Execute the instruction sequence :math:`{\instr'}^\ast`.

   d. Else:

      i. While the top of the stack is a value, do:

         - Pop the value from the stack.

      ii. Trap.

.. math::
   \frac{
   }{
     v~\TRAP \stepto \TRAP
   }

.. math::
   \frac{
   }{
     \TRAP~\instr \stepto \TRAP
   }

.. math::
   \frac{
     S; F; \instr^\ast \stepto S'; F'; {\instr'}^\ast
   }{
     S; F; \LABEL_n\{\instr_0^\ast\}~\instr^\ast~\END \stepto S'; F'; \LABEL_n\{\instr_0^\ast\}~{\instr'}^\ast~\END
   }

.. math::
   \frac{
     S; F; \instr^\ast \stepto S'; F'; {\instr'}^\ast
   }{
     S; F_0; \LOCAL_n\{\F\}~\instr^\ast~\END \stepto S'; F_0; \LOCAL_n\{\F'\}~{\instr'}^\ast~\END
   }


.. _exec-instr-seq-labelled:

Labelled Sequences :math:`\instr^\ast` with label :math:`L`
...........................................................

1. Push :math:`L` to the stack.

2. Execute the :ref:`instruction sequence <exec-instr-seq>` :math:`\instr^\ast`.

3. If no trap occurred, then:

   a. Let :math:`n` be the arity of :math:`L`.

   b. Assert: due to :ref:`validation <valid-instr-seq>`, there are :math:`n` values on the top of the stack.

   c. Pop the results :math:`v^n` from the stack.

   d. Assert: due to :ref:`validation <valid-instr-seq>`, the label :math:`L` is now on the top of the stack.

   e. Pop the label from the label stack.

   f. Push :math:`v^n` back to the stack.

4. Else:

   b. Assert: due to :ref:`validation <valid-instr-seq>`, the label :math:`L` is on the top of the stack.

   c. Pop the label from the label stack.

   d. Trap.


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


.. _exec-expr:
.. index:: expression
   pair: execution; expression
   single: abstract syntax; expression
   single: expression; constant

Expressions
~~~~~~~~~~~

:math:`\instr^\ast~\END`
........................

1. Execute the instruction sequence :math:`\instr^\ast`.

2. If no trap occurred, then:

   a. Assert: due to :ref:`validation <valid-expr>`, the stack contains only values.

   b. Pop all values :math:`v^\ast` from the stack.

   c. Return :math:`v^\ast`.

3. Else:

   a. Assert: due to :ref:`validation <valid-expr>`, the stack is empty.

.. todo::
   DO  

.. math::
   \frac{
     S; F; \instr^\ast \stepto^\ast S; F; v
   }{
     S; F; \instr^\ast~\END \stepto^\ast v
   }
