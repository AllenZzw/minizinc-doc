.. _sec-modelling:

Modelamiento básico en MiniZinc
===============================

.. highlight:: minizinc
  :linenothreshold: 5

En esta sección se presenta la estructura básica de un modelo MiniZinc utilizando dos ejemplos simples.

Nuestro primer ejemplo
----------------------

.. _fig-aust:

.. figure:: figures/aust.*

  Estados de Australia

Como nuestro primer ejemplo, imagine que se desea colorear el mapa de Australia, tal como se ve en la figura :numref:`fig-aust`.

Esta figura se compone de siete estados diferentes, cada uno de los estados debe de tener un color y cada región adyacentes tiene que poseer diferentes colores.

.. literalinclude:: examples/aust.mzn
  :language: minizinc
  :caption: Un modelo de MiniZinc :download:`aust.mzn <examples/aust.mzn>` para colorear los estados y territorios en Australia
  :name: ex-aust

Este problema se puede modelar muy fácil en MiniZinc. El modelo se muestra en :numref:`ex-aust`.

La primera línea del modelo corresponde a un comentario. Un comentario comienza con un ``%``, que indica que el resto de la línea es un comentario.

MiniZinc también tiene comentarios de bloque similares al estilo del lenguaje de programación C. Este tipo de comentario comienzan con ``/*`` y finalizan con un ``*/``.

La siguiente parte del modelo declara las variables en el modelo.

La línea

::

  int: nc = 3;

especifica un :index:`parameter` en el problema, el cual es el número de colores a ser utilizados.

Los parámetros son similares a las variables (constantes) de la mayoría de los lenguajes de programación.

Estos deben de ser declarados y se debe de especificar un :index:`type`.
En este caso, el tipo de dato es :mzn:`int`.

Además se le debe de dar un valor al parámetro mediante una :index:`assignment`.

MiniZinc permite incluir la asignación como parte de la declaración (como en la línea anterior) o ser una sentencia de asignación separada.
Así, la siguiente asignación de dos líneas, es equivalente a la asignación de una línea.

::

  int: nc;
  nc = 3;

A diferencia de las variables, en muchos lenguajes de programación a un parámetro sólo se le puede asignar un solo valor. En ese sentido, son denominadas constantes. Es un error que un parámetro posea más de una asignación.


Lo básico :index:`parameter types <single: type; parameter>`
son :index:`integers <integer>` (:mzn:`int`),
floating point numbers  (:mzn:`float`),
:index:`Booleans <Boolean>` (:mzn:`bool`) and
:index:`strings <string>` (:mzn:`string`).
Arrays and sets are also supported.

.. index::
  see: decision variable; variable

MiniZinc models can also contain another kind of variable called a
*decision variable*.
:index:`Decision variables <variable>` are variables in the sense of mathematical or logical
variables.
Unlike parameters and variables in a standard programming language, the
modeller does not need to give them a value.
Rather the value of a decision variable is unknown and it is only when the
MiniZinc model is executed that the solving system determines if the
decision variable can be assigned a value that satisfies the constraints in the
model and if so what this is.

In our example model  we associate a *decision variable* with each region,
``wa``, ``nt``, ``sa``, ``q``, ``nsw``, ``v`` and ``t``,
which stands for the (unknown) colour to be used to fill the region.

.. index::
  single: domain

For each decision variable we need to give the set of possible values the
variable can take. This is called the variable's
*domain*.
This can be given as part of the :index:`variable declaration <variable; declaration>` and the
:index:`type` of the decision variable is inferred from the type of the values in the domain.

In MiniZinc decision variables
can be Booleans, integers, floating point numbers,
or sets.
Also supported are arrays whose elements are
decision variables.
In our MiniZinc model we use integers to model the different colours. Thus each of our
decision variables is declared to have the domain :mzn:`1..nc`
which is an integer range expression
indicating the set :math:`\{ 1, 2, \dots, nc \}`
using the :mzn:`var` declaration.
The type of the values is integer so all of the variables in the model are integer decision variables.

.. defblock:: Identifiers

  Identifiers, which are used to name parameters and variables,
  are sequences of lower and uppercase
  alphabetic characters, digits and the underscore ``_`` character. They must
  start with an alphabetic character. Thus ``myName_2`` is a valid
  identifier.  MiniZinc
  *keywords* are not allowed to be
  used as identifier names, they are listed in :ref:`spec-identifiers`.
  Neither are MiniZinc *operators*
  allowed to be used as identifier names;
  they are listed in :ref:`spec-Operators`.

MiniZinc carefully
distinguishes between the two kinds of model variables:
parameters and decision variables. The kinds of expressions that can be
constructed using decision variables are more restricted than those that can
be built from parameters. However, in any place that a decision variable can be
used, so can a parameter of the same type.

.. defblock:: Integer Variable Declarations

  An :index:`integer parameter variable <variable; declaration; integer>` is declared as either:

  .. code-block:: minizincdef

    int : <var-name>
    <l> .. <u> : <var-name>

  where :mzndef:`<l>` and :mzndef:`<u>` are fixed integer expressions.

  An integer decision variable is declared
  as either:

  .. code-block:: minizincdef

    var int : <var-name>
    var <l>..<u> : <var-name>

  where :mzndef:`<l>` and :mzndef:`<u>` are fixed integer expressions.


Formally the distinction between parameters and decision variables is called
the *instantiation* of the variable.
The combination of variable instantiation and type is called a
:index:`type-inst`.
As you start to use MiniZinc you will undoubtedly see examples of
*type-inst* errors.

.. index::
  single: constraint

The next component of the model are the *constraints*.
These specify the Boolean expressions that the decision variables must satisfy
to be a valid solution to the model.
In this case we have a number of not equal constraints between the decision
variables enforcing that if two states are adjacent then they must have
different colours.

.. defblock:: Relational Operators

  MiniZinc provides the :index:`relational operators <operator; relational>`:

  .. index::
    single: =
    single: ==
    single: !=
    single: <
    single: <=
    single: >
    single: >=

  equal (``=`` or ``==``), not equal
  (``!=``),
  strictly less than (``<``),
  strictly greater than (``>``),
  less than or equal to (``<=``), and
  greater than or equal to (``>=``).


The next line in the model:

::

  solve satisfy;

indicates the kind of problem it is.
In this case it is a :index:`satisfaction` problem:
we wish to find a value for the
decision variables that satisfies the constraints but we do not care which one.

.. index::
  single: output

The final part of the model is the *output* statement. This tells MiniZinc what to
print when the model has been run and a :index:`solution` is found.

.. defblock:: Output and Strings

  .. index::
    single: output
    single: string
    single: show

  An output statement is followed by a *list* of strings. These are
  typically either :index:`string literals <string; literal>`
  which are written between double quotes and
  use a C like notation for special characters,
  or an expression of the form :mzn:`show(e)`
  where :mzn:`e` is a MiniZinc expression.
  In the example ``\n``
  represents the newline character and ``\t`` a
  tab.

  There are also formatted varieties of :mzn:`show` for numbers:
  :mzn:`show_int(n,X)`
  outputs the value of integer
  ``X`` in at least :math:`|n|` characters, right justified
  if :math:`n > 0` and left justified otherwise;
  :mzn:`show_float(n,d,X)`
  outputs the value of float ``X`` in at least :math:`|n|` characters, right justified
  if :math:`n > 0` and left justified otherwise, with :math`d` characters after the
  decimal point.

  :index:`String literals <string; literal>` must fit on a single line. Longer string literals can be
  split across multiple lines using the string concatenation operator
  ``++``.
  For example, the string literal

  ::

    "Invalid datafile: Amount of flour is non-negative"

  is equivalent to the string literal expression

  ::

    "Invalid datafile: " ++
    "Amount of flour is non-negative"

  MiniZinc supports
  :index:`interpolated strings <string; literal; interpolated>`.
  Expressions can be embedded directly in string literals,
  where a sub string of the form :mzn:`"\(e)"`
  is replaced by the result of :mzn:`show(e)`.
  For example :mzn:`"t=\(t)\n"` produces the same string as
  :mzn:`"t=" ++ show(t) ++ "\n"`.

  A model can contain multiple output statements. In that case, all outputs
  are concatenated in the order they appear in the model.

We can evaluate our model by typing

.. code-block:: bash

  $ mzn-gecode aust.mzn

where :download:`aust.mzn <examples/aust.mzn>`
is the name of the file containing our MiniZinc model.
We must use the file extension ``.mzn`` to indicate a MiniZinc model.
The command ``mzn-gecode`` uses the Gecode finite domain solver to evaluate
our model.

When we run this we obtain the result:

::

  wa=2	 nt=3	 sa=1
  q=2	 nsw=3	 v=2
  t=1
  ----------


.. index::
  single: solution; separator ----------

The line of 10 dashes ``----------`` is automatically added
by the MiniZinc output to indicate a solution has been found.

An Arithmetic Optimisation Example
----------------------------------

Our second example is motivated by the need to bake some cakes for a fete at
our local school.
We know how to make two sorts of cakes.\footnote{WARNING: please don't use
these recipes at home}
A banana cake which takes 250g of self-raising flour, 2 mashed bananas, 75g
sugar and 100g of butter, and a chocolate cake which takes 200g of self-raising
flour, 75g of cocoa, 150g sugar and 150g of butter.
We can sell a chocolate cake for $4.50 and a banana cake for $4.00.  And we
have 4kg self-raising flour, 6 bananas, 2kg of sugar, 500g of butter and 500g
of cocoa.
The question is how many of each sort of cake should we bake for the fete to
maximise the profit.
A possible
MiniZinc model is shown in :numref:`ex-cakes`.

.. literalinclude:: examples/cakes.mzn
  :language: minizinc
  :caption: Model for determining how many banana and chocolate cakes to bake for the school fete (:download:`cakes.mzn <examples/cakes.mzn>`)
  :name: ex-cakes

.. index::
  single: expression; arithmetic

The first new feature is the use of *arithmetic expressions*.

.. defblock:: Integer Arithmetic Operators

  .. index::
    single: operator; integer
    single: +
    single: -
    single: div
    single: *
    single: mod

  MiniZinc provides the standard integer arithmetic operators.
  Addition (``+``),
  subtraction (``-``),
  multiplication (``*``),
  integer division (:mzn:`div`)
  and
  integer modulus (:mzn:`mod`).
  It also provides ``+`` and ``-``
  as unary operators.

  Integer modulus is defined to give a result :math:`a` :mzn:`mod` :math:`b`
  that has the same sign as the
  dividend :math:`a`. Integer division is defined so that
  :math:`a = b ` ``*`` :math:`(a` :mzn:`div` :math:`b) + (a` :mzn:`mod` :math:`b)`.

  MiniZinc provides standard integer functions for
  absolute value (:mzn:`abs`) and power function (:mzn:`pow`).
  For example :mzn:`abs(-4)` and :mzn:`pow(2,5)` evaluate to
  ``4`` and ``32`` respectively.

  The syntax for arithmetic literals is reasonably standard. Integer literals
  can be decimal, hexadecimal or octal. For instance ``0``, ``5``,
  ``123``, ``0x1b7``, ``0o777``.

.. index::
  single: optimization
  single: objective
  single: maximize
  single: minimize

The second new feature shown in the example is optimisation. The line

::

  solve maximize 400 * b + 450 * c;

specifies that we want to find a solution that maximises the expression in
the solve statement called the
*objective*.
The objective can be any
kind of arithmetic expression.  One can replace the keyword
:mzn:`maximize`
by :mzn:`minimize` to specify a minimisation problem.

When we run this we obtain the result:

::

  no. of banana cakes = 2
  no. of chocolate cakes = 2
  ----------
  ==========

.. index::
  single: solution; end `==========`

The line ``==========``
is output automatically for optimisation problems when the system has proved
that a solution is optimal.


.. index::
  single: data file

Datafiles and Assertions
------------------------

A drawback of this model is that if we wish to solve a similar problem the
next time we need to bake cakes for the school (which is often) we need to
modify the constraints in the model to reflect the ingredients that we have
in the pantry. If we want to reuse the model then we would be better off to
make the amount of each ingredient a parameter of the model and then set
their values at the top of the model.

Even better would be to set the value of these parameters in a separate
*data file*.
MiniZinc (like most other modelling languages) allows the
use of data files to set the value of parameters declared in the original
model. This allows the same model to be easily used with different data by
running it with different data files.

Data files must have the file extension ``.dzn`` to indicate a MiniZinc data file
and a model can be run with any number of data files (though a variable/parameter can only be assigned a value in one file).

.. literalinclude:: examples/cakes2.mzn
  :language: minizinc
  :caption: Data-independent model for determining how many banana and chocolate cakes to bake for the school fete (:download:`cakes2.mzn <examples/cakes2.mzn>`)
  :name: ex-cakes2

Our new model is shown in :numref:`ex-cakes2`.
We can run it using the command

.. code-block: bash

  $ mzn-gecode cakes2.mzn pantry.dzn

where the data file :download:`pantry.dzn <examples/pantry.dzn>` is defined in
:numref:`fig-pantry1`. This gives the same result as :download:`cakes.mzn <examples/cakes.mzn>`.
The output from running the command

.. code-block:: bash

  $ mzn-gecode cakes2.mzn pantry2.dzn

with an alternate data set defined in
:numref:`fig-pantry2` is

::

  no. of banana cakes = 3
  no. of chocolate cakes = 8
  ----------
  ==========

If we remove the output statement from :download:`cakes.mzn <examples/cakes.mzn>` then
MiniZinc will use a default output. In this case the resulting
output  will be

::

  b = 3;
  c = 8;
  ----------
  ==========

.. defblock:: Default Output

  A MiniZinc model with no output will output a line for each
  decision variable with its value, unless it is assigned an expression
  on its declaration. Note how the output is in the form of a correct datafile.

.. literalinclude:: examples/pantry.dzn
  :language: minizinc
  :caption: Example data file for :download:`cakes2.mzn <examples/cakes2.mzn>` (:download:`pantry.dzn <examples/pantry.dzn>`)
  :name: fig-pantry1

.. literalinclude:: examples/pantry2.dzn
  :language: minizinc
  :caption: Example data file for :download:`cakes2.mzn <examples/cakes2.mzn>` (:download:`pantry2.dzn <examples/pantry2.dzn>`)
  :name: fig-pantry2

Small data files can be entered
without directly creating a ``.dzn``
file, using the :index:`command line flag <data file;command line>`
``-D`` *string*,
where *string* is the contents of the data
file. For example the command

.. code-block:: bash

  $ mzn-gecode cakes2.mzn -D \
       "flour=4000;banana=6;sugar=2000;butter=500;cocoa=500;"

will give identical results to

.. code-block:: bash

  $ mzn-g12fd cakes2.mzn pantry.dzn

Data files can only contain assignment statements for
decision variables and parameters in the model(s) for which they are intended.

.. index::
  single: assert

Defensive programming suggests that we should check that the values in the
data file are reasonable.  For our example it is sensible to check that the
quantity of all ingredients is non-negative and generate a run-time error if
this is not true. MiniZinc provides a built-in Boolean operator for checking
parameter values. The form is :mzn:`assert(B,S)`. The Boolean expression
``B`` is evaluated and if it is false execution aborts and the string
expression ``S`` is evaluated and printed as an error message. To check and
generate an appropriate error message if the amount of flour is negative we
can simply add the line

::

  constraint assert(flour >= 0,"Amount of flour is non-negative");

to our model. Notice that the :mzn:`assert` expression is a Boolean
expression and so is regarded as a type of constraint. We can add similar
lines to check that the quantity of the other ingredients is non-negative.

Real Number Solving
-------------------

MiniZinc also supports "real number" constraint solving using
floating point variables and constraints.  Consider a problem of taking out a short loan
for one year to be repaid in 4 quarterly instalments.
A model for this is shown in :numref:`ex-loan`. It uses a simple interest
calculation to calculate the balance after each quarter.

.. literalinclude:: examples/loan.mzn
  :language: minizinc
  :caption: Model for determining relationships between a 1 year loan repaying every quarter (:download:`loan.mzn <examples/loan.mzn>`)
  :name: ex-loan

Note that we declare a float variable ``f``
similar to an integer variable using the keyword :mzn:`float` instead of
:mzn:`int`.

We can use the same model to answer a number of different questions.
The first question is: if I borrow $1000 at 4% and repay $260 per
quarter, how much do I end up owing? This question is encoded by
the data file :download:`loan1.dzn <examples/loan1.dzn>`.

Since we wish to use real number variables and constraint we need to use a solver
that supports this type of problem.
A suitable solver would be one that supports mixed integer linear
programming.
The MiniZinc distribution contains such a solver.
We can invoke it using the command ``mzn-cbc``:

.. code-block:: bash

  $ mzn-cbc loan.mzn loan1.dzn

The output is

::

  Borrowing 1000.00 at 4.0% interest, and repaying 260.00
  per quarter for 1 year leaves 65.78 owing
  ----------

The second question is if I want to borrow $1000 at 4% and owe nothing at
the end, how much do I need to repay?
This question is encoded by
the data file :download:`loan2.dzn <examples/loan2.dzn>`.
The output from running the command

.. code-block:: bash

  $ mzn-cbc loan.mzn loan2.dzn

is

::

  Borrowing 1000.00 at 4.0% interest, and repaying 275.49
  per quarter for 1 year leaves 0.00 owing
  ----------

The third question is if I can repay $250 a quarter, how much can I borrow
at 4% to end up owing nothing?
This question is encoded by the data file :download:`loan3.dzn <examples/loan3.dzn>`.
The output from running the command

.. code-block:: bash

  $ mzn-g12mip loan.mzn loan3.dzn

is

::

  Borrowing 907.47 at 4.0% interest, and repaying 250.00
  per quarter for 1 year leaves 0.00 owing
  ----------

.. literalinclude:: examples/loan1.dzn
  :language: minizinc
  :caption: Example data file for :download:`loan.mzn <examples/loan.mzn>` (:download:`loan1.dzn <examples/loan1.dzn>`)

.. literalinclude:: examples/loan2.dzn
  :language: minizinc
  :caption: Example data file for :download:`loan.mzn <examples/loan.mzn>` (:download:`loan2.dzn <examples/loan2.dzn>`)

.. literalinclude:: examples/loan3.dzn
  :language: minizinc
  :caption: Example data file for :download:`loan.mzn <examples/loan.mzn>` (:download:`loan3.dzn <examples/loan3.dzn>`)


.. defblock:: Float Arithmetic Operators

  .. index:
    single: operator; float
    single: +
    single: -
    single: *
    single: /
    single: abs
    single: sqrt
    single: ln
    single: log2
    single: log10
    single: exp
    single: sin
    single: cos
    single: tan
    single: asin
    single: acos
    single: atan
    single: sinh
    single: cosh
    single: tanh
    single: asinh
    single: acosh
    single: atanh
    single: pow

  MiniZinc provides the standard floating point arithmetic operators:
  addition (``+``),
  subtraction (``-``),
  multiplication (``*``)
  and floating point division (``/``).
  It also provides ``+`` and ``-`` as unary operators.

  MiniZinc can automatically coerce integers to
  floating point numbers. But to make the coercion explicit, the built-in function
  :mzn:`int2float`
  can be used. Note that one consequence of the automatic coercion is that
  an expression :mzn:`a / b` is always considered a floating point division. If
  you need an integer division, make sure to use the :mzn:`div` operator!

  MiniZinc provides in addition the following floating point functions:
  absolute value (``abs``),
  square root (``sqrt``),
  natural logarithm (``ln``),
  logarithm base 2 (``log2``),
  logarithm base 10 (``log10``),
  exponentiation of $e$ (``exp``),
  sine (``sin``),
  cosine (``cos``),
  tangent (``tan``),
  arcsine (``asin``),
  arc\-cosine (``acos``),
  arctangent (``atan``),
  hyperbolic sine (``sinh``),
  hyperbolic cosine (``cosh``),
  hyperbolic tangent (``tanh``),
  hyperbolic arcsine (``asinh``),
  hyperbolic arccosine (``acosh``),
  hyperbolic arctangent (``atanh``),
  and power (``pow``) which is the only binary function, the rest are
  unary.

  The syntax for arithmetic literals is reasonably standard. Example float
  literals are ``1.05``, ``1.3e-5`` and ``1.3E+5``.

.. \pjs{Should do multiple solutions????}

Basic structure of a model
--------------------------

We are now in a position to summarise the basic structure of a MiniZinc model.
It consists of multiple *items* each of which has a
semicolon ``;`` at its end.
Items can occur in any order.
For example, identifiers need not be declared before they are
used.

There are 8 kinds of :index:`items <item>`.

- :index:`Include items <item; include>` allow the contents of another file to be inserted into the model.
  They have the form:

  .. code-block:: minizincdef

    include <filename>;

  where :mzndef:`<filename>` is a string literal.
  They allow large models to be split into smaller sub-models and also the
  inclusion of constraints defined in library files.
  We shall see an example in :numref:`ex-smm`.

- :index:`Variable declarations <item; variable declaration>` declare new variables.
  Such variables are global variables and can be referred to from anywhere in the
  model.
  Variables come in two kinds.
  Parameters which are assigned a fixed value in the model or in a data file and
  decision variables whose value is found only when the model is solved.
  We say that parameters are :index:`fixed` and decision variables
  :index:`unfixed`.
  The variable can be optionally assigned a value as part of the declaration.
  The form is:

  .. index:
    single: expression; type-inst
    single: par
    single: var

  .. code-block:: minizincdef

    <type inst expr>: <variable> [ = ] <expression>;

  The :mzndef:`<type-inst expr>`
  gives the instantiation and type of the
  variable. These are one of the more complex aspects of MiniZinc.
  Instantiations are declared using :mzn:`par`
  for parameters and
  :mzn:`var` for decision variables. If there is no explicit instantiation
  declaration then the variable is a parameter.
  The type can be a base type,
  an :index:`integer or float range <range>`
  or an array or a set.
  The base types are :mzn:`float`,
  :mzn:`int`,
  :mzn:`string`,
  :mzn:`bool`,
  :mzn:`ann`
  of which only
  :mzn:`float`, :mzn:`int` and :mzn:`bool` can be used for decision
  variables.
  The base type :mzn:`ann` is an :index:`annotation` --
  we shall discuss
  annotations in :ref:`sec-search`.
  :index:`Integer range expressions <range; integer>` can be used
  instead of the type :mzn:`int`.
  Similarly :index:`float range expressions <range; float>`
  can be used instead of type :mzn:`float`.
  These are typically used to give the
  domain of a decision variable but can also be used to restrict the
  range of a parameter. Another use of variable declarations is to
  define :index:`enumerated types`, which we discuss in :ref:`sec-enum`.

- :index:`Assignment items <item; assignment>` assign a value to a variable. They have the form:

  .. code-block:: minizincdef

    <variable> = <expression>;

  Values can be assigned to decision variables in which case the assignment is
  equivalent to writing :mzndef:`constraint <variable> = <expression>`.

- :index:`Constraint items <item; constraint>` form the heart of the model. They have the form:

  .. code-block:: minizincdef

    constraint <Boolean expression>;

  We have already seen examples of simple constraints using arithmetic
  comparison and the built-in :mzn:`assert` operator. In the next section we
  shall see examples of more complex constraints.

- :index:`Solve items <item; solve>` specify exactly what kind of solution is being looked for.
  As we have seen they have one of three forms:

  .. code-block:: minizincdef

    solve satisfy;
    solve maximize <arithmetic expression>;
    solve minimize <arithmetic expression>;

  A model is required to have exactly one solve item.

- :index:`Output items <item; output>` are for nicely presenting the results of the model execution.
  They have the form:

  .. code-block:: minizincdef

    output [ <string expression>, ..., <string expression> ];

  If there is no output item, MiniZinc will by default print out the values of
  all the decision variables which are not optionally assigned a value in the
  format of assignment items.

- :index:`Enumerated type declarations <item; enum>`.
  We discuss these in :ref:`sec-arrayset` and :ref:`sec-enum`.

- :index:`Predicate, function and test items <item; predicate>` are for defining new constraints,
  functions and Boolean tests.
  We discuss these in :ref:`sec-predicates`.


- The :index:`annotation item <item; annotation>` is used to define a new annotation. We
  discuss these in :ref:`sec-search`.