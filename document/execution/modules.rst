Modules
-------


.. _exec-module:
.. index:: ! instantiation, module, instance, store

Instantiation
~~~~~~~~~~~~~

Given a :ref:`store <syntax-store>` :math:`S` and a :ref:`module <syntax-module>` :math:`m` is instantiated with a list of :ref:`external values <syntax-externval>` :math:`\externval^\ast` supplying the required imports as follows.

.. math::
   \begin{array}{llll}
   \F{alloctable}(S, \table) &=& S', \tableaddr \\[1ex]
   \mbox{where:} \\[1ex]
   \tableinst &=& \{ \ELEM~(\epsilon)^{\limits.\MIN}, \MAX~\limits.\MAX \}
     & (\table.\TYPE = \limits~\elemtype) \\
   \tableaddr &=& |S.\TABLES| \\
   S' &=& S~\mbox{with}~\TABLES = S.\TABLES~\tableinst \\
   \end{array}

.. math::
   \begin{array}{llll}
   \F{allocmem}(S, \mem) &=& S', \memaddr \\[1ex]
   \mbox{where:} \\[1ex]
   \meminst &=& \{ \DATA~(\hex{00})^{\limits.\MIN}, \MAX~\limits.\MAX \}
     & (\mem.\TYPE = \limits) \\
   \memaddr &=& |S.\MEMS| \\
   S' &=& S~\mbox{with}~\MEMS = S.\MEMS~\meminst \\
   \end{array}

.. math::
   \begin{array}{llll}
   \F{allocglobal}(S, \global) &=& S', \globaladdr \\[1ex]
   \mbox{where:} \\[1ex]
   \globalinst &=& \{ \VALUE~(t.\CONST~0), \MUT~\mut \}
     & (\global.\TYPE = \mut~t) \\
   \globaladdr &=& |S.\GLOBALS| \\
   S' &=& S~\mbox{with}~\GLOBALS = S.\GLOBALS~\globalinst \\
   \end{array}

.. math::
   \begin{array}{llll}
   \F{alloc}X^\ast(S_0, X^n) &=& S_n, a^n \\[1ex]
   \mbox{where for all $i < n$:} \\[1ex]
   S_{i+1}, a^n[i] &=& \F{alloc}X(S_i, X^n[i])
   \end{array}

.. math::
   \begin{array}{llll}
   \F{allocmodule}(S, \module, \externval_{\F{im}}^\ast) &=& S', \moduleinst \\[1ex]
   \mbox{where:} \\[1ex]
   \moduleinst &=& \{~
     \begin{array}[t]{@{}l@{}}
     \TYPES~\module.\TYPES, \\
     \FUNCS~\funcs(\externval_{\F{im}}^\ast)~\funcinst^\ast, \\
     \TABLES~\tables(\externval_{\F{im}}^\ast)~\tableaddr^\ast, \\
     \MEMS~\mems(\externval_{\F{im}}^\ast)~\memaddr^\ast, \\
     \GLOBALS~\globals(\externval_{\F{im}}^\ast)~\globaladdr^\ast, \\
     \EXPORTS~\exportinst^\ast ~\}
     \end{array} \\[1ex]
   S_1, \tableaddr^\ast &=& \F{alloctable}^\ast(S, \module.\TABLES) \\
   S_2, \memaddr^\ast &=& \F{allocmem}^\ast(S_1, \module.\MEMS) \\
   S', \globaladdr^\ast &=& \F{allocglobal}^\ast(S_2, \module.\GLOBALS) \\[1ex]
   \funcinst^\ast &=& (\{ \MODULE~\moduleinst, \FUNC~\func \})^\ast
     & (\func^\ast = \module.\FUNCS) \\
   \exportinst^\ast &=& (\{ \NAME~(\export.\NAME), \VALUE~\externval_{\F{ex}} \})^\ast
     & (\export^\ast = \module.\EXPORTS) \\[1ex]
   \funcs(\externval_{\F{ex}}^\ast) &=& (\moduleinst.\FUNCS[x])^\ast
     & (x^\ast = \funcs(\module.\EXPORTS)) \\
   \tables(\externval_{\F{ex}}^\ast) &=& (\moduleinst.\TABLES[x])^\ast
     & (x^\ast = \tables(\module.\EXPORTS)) \\
   \mems(\externval_{\F{ex}}^\ast) &=& (\moduleinst.\MEMS[x])^\ast
     & (x^\ast = \mems(\module.\EXPORTS)) \\
   \globals(\externval_{\F{ex}}^\ast) &=& (\moduleinst.\GLOBALS[x])^\ast
     & (x^\ast = \globals(\module.\EXPORTS)) \\
   \end{array}

.. math::
   \frac{
     n_1 \geq n_2
     \qquad
     m_1 \leq m_2
   }{
     \vdash \{ \MIN~n_1, \MAX~m_1 \} \leq \{ \MIN~n_2, \MAX~m_2 \}
   }
   \quad
   \frac{
     n_1 \geq n_2
   }{
     \vdash \{ \MIN~n_1, \MAX~m_1^? \} \leq \{ \MIN~n_2, \MAX~\epsilon \}
   }

.. math::
   \frac{
     \funcinst.\MODULE.\TYPES[\funcinst.\FUNC.\TYPE] = \functype
   }{
     S \vdash \FUNC~\funcinst : \FUNC~\functype
   }

.. math::
   \frac{
     S.\TABLES[\tableaddr] = \{ \ELEM~(a^?)^n, \MAX~m^? \}
     \qquad
     \vdash \{ \MIN~n, \MAX~m^? \} \leq \limits
   }{
     S \vdash \TABLE~\tableaddr : \TABLE~(\limits~\ANYFUNC)
   }

.. math::
   \frac{
     S.\MEMS[\memaddr] = \{ \DATA~b^{n\cdot64\,\F{Ki}}, \MAX~m^? \}
     \qquad
     \vdash \{ \MIN~n, \MAX~m^? \} \leq \limits
   }{
     S \vdash \MEM~\tableaddr : \MEM~\limits
   }

.. math::
   \frac{
     S.\GLOBALS[\globaladdr] = \{ \VALUE~(t.\CONST~c), \MUT~\mut\}
   }{
     S \vdash \GLOBAL~\globaladdr : \GLOBAL~(\mut~t)
   }

.. commented out
   .. math::
   \frac{
     S; \moduleinst; \expr \stepto^\ast \I32.\CONST~o
     \qquad
     o + n \leq |S.\TABLES[\moduleinst.\TABLES[x]]|
   }{
     S \vdash \INIT~\moduleinst~\{ \TABLE~x, \OFFSET~\expr, \INIT~y^n \} ~\mbox{ok}
   }

   .. math::
   \frac{
     S; \moduleinst; \expr \stepto^\ast \I32.\CONST~o
     \qquad
     o + n \leq |S.\MEMS[\moduleinst.\MEMS[x]]|
   }{
     S \vdash \INIT~\moduleinst~\{ \MEM~x, \OFFSET~\expr, \INIT~b^n \} ~\mbox{ok}
   }

.. math::
   \frac{
   }{
     S; \INSTANTIATE~\module~\externval^n \stepto S; \TRAP
   }

.. math::
   \frac{
     \begin{array}{@{}c@{}}
     \module.\IMPORTS = \externtype^n
     \qquad
     \module.\GLOBALS = \global^\ast
     \qquad
     \module.\ELEM = \elem^\ast
     \qquad
     \module.\DATA = \data^\ast
     \qquad
     \module.\START = \start^?
     \\
     (\vdash \externval : \externtype)^n
     \qquad
     \F{allocmodule}(S, \module, \externval^n) = S', \moduleinst
     \qquad
     F = \{ \MODULE~\moduleinst, \LOCAL~\epsilon \}
     \\
     (S'; F; \global.\INIT \stepto^\ast v)^\ast
     \qquad
     (S'; F; \elem.\OFFSET \stepto^\ast \I32.\CONST~\X{eo})^\ast
     \qquad
     (S'; F; \data.\OFFSET \stepto^\ast \I32.\CONST~\X{do})^\ast
     \\
     (\globalidx^\ast = i + |\moduleinst.\GLOBALS| - |\module.\GLOBALS|)_i^\ast
     \qquad
     (\globaladdr = \moduleinst.\GLOBALS[\globalidx])^\ast
     \\
     (\tableaddr = \moduleinst.\TABLES[\elem.\TABLE])^\ast
     \qquad
     (\globaladdr = \moduleinst.\MEMS[\data.\MEM])^\ast
     \qquad
     (\funcinst = \moduleinst.\FUNCS[\start.\FUNC])^?
     \\
     (\X{eo} + |\elem.\INIT| \leq |S'.\TABLES[\tableaddr]|)^\ast
     \qquad
     (\X{do} + |\data.\INIT| \leq |S'.\MEMS[\memaddr]|)^\ast
     \end{array}
   }{
     S; \INSTANTIATE~\module~\externval^n \stepto S';
       \begin{array}[t]{@{}l@{}}
       (\INITGLOBAL~\globaladdr~v)^\ast \\
       (\INITTABLE~\tableaddr~\X{eo}~\moduleinst~\elem.\INIT)^\ast \\
       (\INITMEM~\memaddr~\X{do}~\data.\INIT)^\ast \\
       (\INVOKE~\funcinst)^? \\
       \moduleinst \\
       \end{array}
   }

.. math::
   \frac{
     S' = S~\mbox{with}~\GLOBALS[\globaladdr] = v
   }{
     S; \INITGLOBAL~\globaladdr~v \stepto S'; \epsilon
   }

.. math::
   \frac{
   }{
     S; \INITTABLE~\tableaddr~o~\moduleinst~\epsilon \stepto S; \epsilon
   }

.. math::
   \frac{
     S' = S~\mbox{with}~\TABLES[\tableaddr][o] = \moduleinst.\FUNCS[x_0]
   }{
     S; \INITTABLE~\tableaddr~o~\moduleinst~(x_0~x^\ast) \stepto S'; \INITTABLE~\tableaddr~(o+1)~\moduleinst~x^\ast
   }

.. math::
   \frac{
   }{
     S; \INITMEM~\memaddr~o~\epsilon \stepto S; \epsilon
   }

.. math::
   \frac{
     S' = S~\mbox{with}~\MEMS[\memaddr][o] = b_0
   }{
     S; \INITMEM~\memaddr~o~(b_0~b^\ast) \stepto S'; \INITMEM~\memaddr~(o+1)~b^\ast
   }
