---

layout: post
title: "GSoC 2017: Symbolic Integration"
author: "Gaurav Dhingra"
excerpt: "Google Summer of Code 2017 with SymPy"
keywords: gsoc, sympy, 2017, integration

---

GSoC 2017: Symbolic Integration

I applied for Google Summer of Code again this year, and my project on symbolic integration has been accepted. Like last year, my mentors are [Aaron Meurer](https://github.com/asmeurer)(SymPy lead developer) and [Kalevi Suominen](https://github.com/jksuom). The proposal I submitted can be found here [GSoC 2017 proposal: Symbolic Integration](https://github.com/sympy/sympy/wiki/GSoC-2017-Application-Gaurav-Dhingra:-Risch-algorithm-for-symbolic-integration).

* Aaron himself worked on Symbolic Integration in GSoC 2010, and he started the implementation of transcendental functions integration and a good part of transcendental function integration was done (main resource being *Symbolic Integration I: Transcendental Function by Manuel Bronstein*).

* Then in the year 2013, Chetna Gupta continued work on transcendental function integration as a part of her GSoC project. And still some part of transcendental function integration remains.

* I have liked to mention one of 'things' with the current integration module is sometime people get deceived with the results. Consider the integration of function *1/(x**2 + a**2)*. Most of would expect the output to be *atan(x/a)/a*. But with SymPy we get:

Example:

```
In [11]: integrate(1/(x**2 + a**2), x)
Out[11]: (-I*log(-I*a + x)/2 + I*log(I*a + x)/2)/a
```

which for a moment looks wrong, but actually the answer returned by SymPy is
not wrong (or is actually right mathematically). Since the answer returned by
SymPy is logarithmic form of *atan*.


`$atan(x) = I*log((-I*x + 1)/(I*x + 1))/2$`


Now the comes the main part, what would I be working on? The first main thing on
which I will be working on is:

 1. Completing the work on transcendental function integration: which includes

  * writing a lot of tests for functions present in prde.py (Parametric Risch
     Differential Equation), rde.py (Risch differential equation).

  * working on trigonometric extensions.
     example:

```python

In [1]: from sympy import *
In [2]: from sympy.integrals.risch import risch_integrate
In [3]: risch_integrate(sin(x), x)
Traceback (most recent call last):
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/home/gxyd/foss/sympy/sympy/integrals/risch.py", line 1677, in risch_integrate
    dummy=True, rewrite_complex=rewrite_complex)
  File "/home/gxyd/foss/sympy/sympy/integrals/risch.py", line 234, in __init__
    raise NotImplementedError("Trigonometric extensions are not "
NotImplementedError: Trigonometric extensions are not supported (yet!)

```

as you can see it raises the **NotImplementedError**. It doesn't mean
that SymPy can't evaluate the integral of simple function like *sin(x)*.

     ```
     In 1: from sympy import *
     In [2]: integrate(sin(x), x)
     Out[2]: -cos(x)
     ```

Since I have started using PDb as a debugger (thanks to Kalevi) instead
of the primitive **print** command. I will now explain how a function like
 *sin(x)* is currently integrated.


> ### Pdb working with integration

* First go into SymPy's root directory and start the IPython or bin/isympy session.
* Then import pdb module into your session with *import pdb*.
* Import the function from the tests file which you want to debug/analyse with
command for example in my I will be importing test case from the integral module's
test directory with *from sympy.integrals.tests.test_integrals import test_halo*

PDb important commands: You can use as command the word outside the bracket or
including the one outside bracket with word inside it.

s(tep): You can either type *s* or *step* on PDb command prompt (i.e. (Pdb)),
        this command is often used to look into its execution. Whenever you find
        something interesting (function or class) use this command.

b(reak): setting a break point. Often executed in conjunction with *continue*, it
          sets a breakpoint (see wiki article for it). An example would be its usage
          in seeing the execution of <i>DifferentialExtension</i> object.
          Example: (Pdb) b sympy/integrals/risch.py:196
                   Breakpoint 1 at /home/gxyd/foss/sympy/sympy/integrals/risch.py:19

c(ont(inue)): Often used in conjunction with <i>break</i> command. It continues the
              execution i.e. doesn't show any command(or code) on Pdb until the Pdb
              hits the set breakpoint or the function returns something to the main
              IPython session as output.

n(ext): continue to the next line.
l(ist): shows a limited source code of current line execution.
p: prints the object. Used in printing of variable/object value.

There are many more commands like <i>j</i>, <i>w</i>, which I haven't used often.
(For broader and good understanding of these commands use the trivial ?x where <i>x</i>
is the command under consideration).

> #### An example session:

```
gxyd@stallman:~/foss/sympy$ bin/isympy
IPython console for SymPy 1.0.1.dev (Python 3.6.0-64-bit) (ground types: python)

These commands were executed:
>>> from __future__ import division
>>> from sympy import *
>>> x, y, z, t = symbols('x y z t')
>>> k, m, n = symbols('k m n', integer=True)
>>> f, g, h = symbols('f g h', cls=Function)
>>> init_printing()

Documentation can be found at http://docs.sympy.org/dev

In [1]: import pdb

In [2]: from sympy.integrals.tests.test_integrals import test_halo

In [3]: pdb.run('test_halo()')
> <string>(1)<module>()
(Pdb) s
--Call--
> /home/gxyd/foss/sympy/sympy/integrals/tests/test_integrals.py(1182)test_halo()
-> def test_halo():
(Pdb) s
> /home/gxyd/foss/sympy/sympy/integrals/tests/test_integrals.py(1183)test_halo()
-> from sympy.abc import x
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/tests/test_integrals.py(1184)test_halo()
-> assert integrate(sin(x), x) == -cos(x)
(Pdb) s
--Call--
> /home/gxyd/foss/sympy/sympy/core/basic.py(105)__hash__()
-> def __hash__(self):
(Pdb) b sympy/integrals/integrals.py:1287
Breakpoint 1 at /home/gxyd/foss/sympy/sympy/integrals/integrals.py:1287
(Pdb) c
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(1287)integrate()
-> meijerg = kwargs.pop('meijerg', None)
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(1288)integrate()
-> conds = kwargs.pop('conds', 'piecewise')
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(1289)integrate()
-> risch = kwargs.pop('risch', None)
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(1290)integrate()
-> manual = kwargs.pop('manual', None)
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(1291)integrate()
-> integral = Integral(*args, **kwargs)
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(1293)integrate()
-> if isinstance(integral, Integral):
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(1294)integrate()
-> return integral.doit(deep=False, meijerg=meijerg, conds=conds,
(Pdb) s
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(1295)integrate()
-> risch=risch, manual=manual)
(Pdb) s
--Call--
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(354)doit()
-> def doit(self, **hints):
(Pdb) s
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(374)doit()
-> if not hints.get('integrals', True):
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(377)doit()
-> deep = hints.get('deep', True)
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(378)doit()
-> meijerg = hints.get('meijerg', None)
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(379)doit()
-> conds = hints.get('conds', 'piecewise')
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(380)doit()
-> risch = hints.get('risch', None)
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(381)doit()
-> manual = hints.get('manual', None)
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(383)doit()
-> if conds not in ['separate', 'piecewise', 'none']:
(Pdb) p conds
'piecewise'
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(387)doit()
-> if risch and any(len(xab) > 1 for xab in self.limits):
(Pdb) p risch
None
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(391)doit()
-> if self.is_zero:
(Pdb) p self
Integral(sin(x), x)
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(395)doit()
-> function = self.function
(Pdb) p self.function
sin(x)
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(396)doit()
-> if deep:
(Pdb) p deep
False
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(398)doit()
-> if function.is_zero:
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(401)doit()
-> if isinstance(function, MatrixBase):
(Pdb) p function
sin(x)
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(406)doit()
-> undone_limits = []
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(408)doit()
-> ulj = set()
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(409)doit()
-> for xab in self.limits:
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(412)doit()
-> if len(xab) == 1:
(Pdb) p self.limits
((x,),)
(Pdb) p xab
(x,)
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(413)doit()
-> uli = set(xab[:1])
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(422)doit()
-> if xab[0] in ulj or any(v[0] in uli for v in undone_limits):
(Pdb) p uli
{x}
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(438)doit()
-> def try_meijerg(function, xab):
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(462)doit()
-> meijerg1 = meijerg
(Pdb) p meijerg
None
(Pdb) p xab
(x,)
(Pdb) l
457  	                            ret = f, cond
458  	                        else:
459  	                            ret = f
460  	                return ret
461  	
462  ->	            meijerg1 = meijerg
463  	            if len(xab) == 3 and xab[1].is_real and xab[2].is_real \
464  	                and not function.is_Poly and \
465  	                    (xab[1].has(oo, -oo) or xab[2].has(oo, -oo)):
466  	                ret = try_meijerg(function, xab)
467  	                if ret is not None:
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(463)doit()
-> if len(xab) == 3 and xab[1].is_real and xab[2].is_real \
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(480)doit()
-> if meijerg1 is False and meijerg is True:
(Pdb) p meijerg1
None
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(483)doit()
-> antideriv = self._eval_integral(
(Pdb) p self.
*** SyntaxError: unexpected EOF while parsing
(Pdb) p self
Integral(sin(x), x)
(Pdb) s
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(484)doit()
-> function, xab[0],
(Pdb) s
--Call--
> /home/gxyd/foss/sympy/sympy/core/containers.py(40)__getitem__()
-> def __getitem__(self, i):
(Pdb) s
> /home/gxyd/foss/sympy/sympy/core/containers.py(41)__getitem__()
-> if isinstance(i, slice):
(Pdb) r
--Return--
> /home/gxyd/foss/sympy/sympy/core/containers.py(44)__getitem__()->x
-> return self.args[i]
(Pdb) s
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(485)doit()
-> meijerg=meijerg1, risch=risch, manual=manual,
(Pdb) s
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(486)doit()
-> conds=conds)
(Pdb) s
--Call--
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(651)_eval_integral()
-> def _eval_integral(self, f, x, meijerg=None, risch=None, manual=None,
(Pdb) s
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(735)_eval_integral()
-> from sympy.integrals.deltafunctions import deltaintegrate
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(736)_eval_integral()
-> from sympy.integrals.singularityfunctions import singularityintegrate
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(737)_eval_integral()
-> from sympy.integrals.heurisch import heurisch, heurisch_wrapper
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(738)_eval_integral()
-> from sympy.integrals.rationaltools import ratint
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(739)_eval_integral()
-> from sympy.integrals.risch import risch_integrate
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(741)_eval_integral()
-> if risch:
(Pdb) p risch
None
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(747)_eval_integral()
-> if manual:
(Pdb) p manual
None
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(762)_eval_integral()
-> if isinstance(f, Poly) and not meijerg:
(Pdb) p f
sin(x)
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(766)_eval_integral()
-> if f.func is Piecewise:
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(770)_eval_integral()
-> if not f.has(x):
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(774)_eval_integral()
-> poly = f.as_poly(x)
(Pdb) p sin(x).as_poly(x)
*** NameError: name 'sin' is not defined
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(775)_eval_integral()
-> if poly is not None and not meijerg:
(Pdb) p poly
None
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(778)_eval_integral()
-> if risch is not False:
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(779)_eval_integral()
-> try:
(Pdb) p risch
None
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(780)_eval_integral()
-> result, i = risch_integrate(f, x, separate_integral=True, conds=conds)
(Pdb) s
--Call--
> /home/gxyd/foss/sympy/sympy/integrals/risch.py(1569)risch_integrate()
-> def risch_integrate(f, x, extension=None, handle_first='log',
(Pdb) s
> /home/gxyd/foss/sympy/sympy/integrals/risch.py(1674)risch_integrate()
-> f = S(f)
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/risch.py(1676)risch_integrate()
-> DE = extension or DifferentialExtension(f, x, handle_first=handle_first,
(Pdb) s
> /home/gxyd/foss/sympy/sympy/integrals/risch.py(1677)risch_integrate()
-> dummy=True, rewrite_complex=rewrite_complex)
(Pdb) s
--Call--
> /home/gxyd/foss/sympy/sympy/integrals/risch.py(165)__init__()
-> def __init__(self, f=None, x=None, handle_first='log', dummy=False, extension=None, rewrite_complex=False):
(Pdb) s
> /home/gxyd/foss/sympy/sympy/integrals/risch.py(196)__init__()
-> if extension:
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/risch.py(206)__init__()
-> elif f is None or x is None:
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/risch.py(210)__init__()
-> from sympy.integrals.prde import is_deriv_k
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/risch.py(212)__init__()
-> if handle_first not in ['log', 'exp']:
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/risch.py(218)__init__()
-> self.f = f
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/risch.py(219)__init__()
-> self.x = x
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/risch.py(221)__init__()
-> self.dummy = dummy
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/risch.py(222)__init__()
-> self.reset()
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/risch.py(223)__init__()
-> exp_new_extension, log_new_extension = True, True
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/risch.py(224)__init__()
-> if rewrite_complex:
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/risch.py(233)__init__()
-> if any(i.has(x) for i in self.f.atoms(sin, cos, tan, atan, asin, acos)):
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/risch.py(234)__init__()
-> raise NotImplementedError("Trigonometric extensions are not "
(Pdb) n
NotImplementedError: Trigonometric extensions are not supported (yet!)
> /home/gxyd/foss/sympy/sympy/integrals/risch.py(234)__init__()
-> raise NotImplementedError("Trigonometric extensions are not "
(Pdb) n
--Return--
> /home/gxyd/foss/sympy/sympy/integrals/risch.py(234)__init__()->None
-> raise NotImplementedError("Trigonometric extensions are not "
(Pdb) n
NotImplementedError: Trigonometric extensions are not supported (yet!)
> /home/gxyd/foss/sympy/sympy/integrals/risch.py(1677)risch_integrate()
-> dummy=True, rewrite_complex=rewrite_complex)
(Pdb) n
--Return--
> /home/gxyd/foss/sympy/sympy/integrals/risch.py(1677)risch_integrate()->None
-> dummy=True, rewrite_complex=rewrite_complex)
(Pdb) n
NotImplementedError: Trigonometric extensions are not supported (yet!)
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(780)_eval_integral()
-> result, i = risch_integrate(f, x, separate_integral=True, conds=conds)
(Pdb) n
> /home/gxyd/foss/sympy/sympy/integrals/integrals.py(781)_eval_integral()
-> except NotImplementedError:
```


      As you can see it raise *NotImplemenedError* for forming the *DifferentialExtension*
      object for function f = *sin(x)*.

      I will leave it here, I think it wouldn't be an issue to continue from here.

> ### TODO

I am still trying to make a call to *param_rischDE* in the function *limited_integration*. I will write more about this in my next blog, hopefully(a big hope here from me, I have been stuck here for quite sometime and frustrated about it) will have completed this task by then.
