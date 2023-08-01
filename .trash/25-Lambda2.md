>[!note] Lambda expressions (since Cpp 11)
>Constructs a closure: an unnamed function object capable of capturing variables in scope.
>
>A lambda function, or simply “lambda”, is an anonymous (unnamed) function that is defined in place, within your source code, and with a concise syntax. Lambda functions were introduced in C++11 and have since become a widely used feature, especially in combination with the Standard Library algorithms.

## Syntax

<table><tbody><tr><td colspan="10"></td></tr><tr><td><code><b>[</b></code> captures <code><b>] (</b></code> params <code><b>)</b></code> specs requires ﻿(optional) <code><b>{</b></code> body <code><b>}</b></code></td><td>(1)</td><td></td></tr><tr><td colspan="10"></td></tr><tr><td><code><b>[</b></code> captures <code><b>]</b></code> attr <code><b>(</b></code> params <code><b>)</b></code> specs requires ﻿(optional) <code><b>{</b></code> body <code><b>}</b></code></td><td>(1)</td><td>(since C++23)</td></tr><tr><td colspan="10"></td></tr><tr><td><code><b>[</b></code> captures <code><b>] {</b></code> body <code><b>}</b></code></td><td>(2)</td><td></td></tr><tr><td colspan="10"></td></tr><tr><td><code><b>[</b></code> captures <code><b>]</b></code> attr specs <code><b>{</b></code> body <code><b>}</b></code></td><td>(2)</td><td>(since C++23)</td></tr><tr><td colspan="10"></td></tr><tr><td><code><b>[</b></code> captures <code><b>] &lt;</b></code> tparams <code><b>&gt;</b></code> requires ﻿(optional) <code><b>(</b></code> params <code><b>)</b></code> specs requires ﻿(optional) <code><b>{</b></code> body <code><b>}</b></code></td><td>(3)</td><td>(since C++20)</td></tr><tr><td colspan="10"></td></tr><tr><td><code><b>[</b></code> captures <code><b>] &lt;</b></code> tparams <code><b>&gt;</b></code> requires ﻿(optional) attr <code><b>(</b></code> params <code><b>)</b></code> specs requires ﻿(optional) <code><b>{</b></code> body <code><b>}</b></code></td><td>(3)</td><td>(since C++23)</td></tr><tr><td colspan="10"></td></tr><tr><td><code><b>[</b></code> captures <code><b>] &lt;</b></code> tparams <code><b>&gt;</b></code> requires ﻿(optional) <code><b>{</b></code> body <code><b>}</b></code></td><td>(4)</td><td>(since C++20)</td></tr><tr><td colspan="10"></td></tr><tr><td><code><b>[</b></code> captures <code><b>] &lt;</b></code> tparams <code><b>&gt;</b></code> requires ﻿(optional) attr specs <code><b>{</b></code> body <code><b>}</b></code></td><td>(4)</td><td>(since C++23)</td></tr><tr><td colspan="10"></td></tr></tbody></table>

1) Full form.
2) Omitted parameter list: function takes no arguments, as if the parameter list were `()`.
3) Same as (1), but specifies a generic lambda and explicitly provides a list of template parameters.
4) Same as (2), but specifies a generic lambda and explicitly provides a list of template parameters.

## Explanation

<table><tbody><tr><td>captures</td><td>-</td><td>a comma-separated list of zero or more <a href="#Lambda_capture">captures</a>, optionally beginning with a capture-default.<p>See <a href="#Lambda_capture">below</a> for the detailed description of captures.</p><p>A lambda expression can use a variable without capturing it if the variable</p><ul><li>is a non-local variable or has static or thread local <a href="https://en.cppreference.com/w/cpp/language/storage_duration" title="cpp/language/storage duration">storage duration</a> (in which case the variable cannot be captured), or</li><li>is a reference that has been initialized with a <a href="https://en.cppreference.com/w/cpp/language/constant_expression#Constant_expression" title="cpp/language/constant expression">constant expression</a>.</li></ul><p>A lambda expression can read the value of a variable without capturing it if the variable</p><ul><li>has const non-volatile integral or enumeration type and has been initialized with a <a href="https://en.cppreference.com/w/cpp/language/constant_expression#Constant_expression" title="cpp/language/constant expression">constant expression</a>, or</li><li>is <code>constexpr</code> and has no mutable members.</li></ul></td></tr><tr><td>tparams</td><td>-</td><td>a non-empty comma-separated list of <a href="https://en.cppreference.com/w/cpp/language/template_parameters" title="cpp/language/template parameters">template parameters</a>, used to provide names to the template parameters of a generic lambda (see <code>ClosureType:: operator ()</code> below).</td></tr><tr><td>params</td><td>-</td><td>The list of parameters, as in <a href="https://en.cppreference.com/w/cpp/language/function" title="cpp/language/function">named functions</a>.</td></tr><tr><td>specs</td><td>-</td><td>consists of specifiers, exception, attr and trailing-return-type in that order; each of these components is optional</td></tr><tr><td>specifiers</td><td>-</td><td>Optional sequence of specifiers. If not provided, the objects captured by copy are const in the lambda body. The following specifiers are allowed at most once in each sequence:<ul><li><code><b>mutable</b></code>: allows body to modify the objects captured by copy, and to call their non-const member functions</li></ul><table><tbody><tr><td><ul><li><code><b>constexpr</b></code>: explicitly specifies that the function call operator or any given operator template specialization is a <a href="https://en.cppreference.com/w/cpp/language/constexpr#constexpr_function" title="cpp/language/constexpr">constexpr function</a>. When this specifier is not present, the function call operator or any given operator template specialization will be <code>constexpr</code> anyway, if it happens to satisfy all constexpr function requirements</li></ul></td><td>(since C++17)</td></tr></tbody></table><table><tbody><tr><td><ul><li><code><b>consteval</b></code>: specifies that the function call operator or any given operator template specialization is an <a href="https://en.cppreference.com/w/cpp/language/consteval" title="cpp/language/consteval">immediate function</a>. consteval and constexpr cannot be used at the same time.</li></ul></td><td>(since C++20)</td></tr></tbody></table><table><tbody><tr><td><ul><li><code><b>static</b></code>: specifies that the function call operator or any given operator template specialization is a <a href="https://en.cppreference.com/w/cpp/language/static#Static_member_functions" title="cpp/language/static">static member function</a>. mutable and static cannot be used at the same time, and captures must be empty if static is present.</li></ul></td><td>(since C++23)</td></tr></tbody></table></td></tr><tr><td>exception</td><td>-</td><td>provides the <a href="https://en.cppreference.com/w/cpp/language/except_spec" title="cpp/language/except spec">dynamic exception specification</a> or (until C++20) the <a href="https://en.cppreference.com/w/cpp/language/noexcept_spec" title="cpp/language/noexcept spec">noexcept specifier</a> for operator () of the closure type</td></tr><tr><td>attr</td><td>-</td><td>optional <a href="https://en.cppreference.com/w/cpp/language/attributes" title="cpp/language/attributes">attribute specifier sequence</a>.<table><tbody><tr><td><p>An attribute specifier sequence applies to the type of the function call operator or operator template of the closure type. Any attribute so specified does not appertain to the function call operator or operator template itself, but its type. (For example, the <code> [[<a href="https://en.cppreference.com/w/cpp/language/attributes/noreturn" title="cpp/language/attributes/noreturn">noreturn</a>]] </code> attribute cannot be used.)</p></td><td>(until C++23)</td></tr><tr><td><p>If an attribute specifier sequence appears before the parameter list, lambda specifiers, or noexcept specifier, it applies to the function call operator or operator template of the closure type (and thus the <code> [[<a href="https://en.cppreference.com/w/cpp/language/attributes/noreturn" title="cpp/language/attributes/noreturn">noreturn</a>]] </code> attribute can be used). Otherwise, it applies to the type of the function call operator or operator template.</p></td><td>(since C++23)</td></tr></tbody></table></td></tr><tr><td>trailing-return-type</td><td>-</td><td><code><b>-&gt;</b></code> ret, where ret specifies the return type. If trailing-return-type is not present, the return type of the closure's operator () is <a href="https://en.cppreference.com/w/cpp/language/template_argument_deduction" title="cpp/language/template argument deduction">deduced</a> from <a href="https://en.cppreference.com/w/cpp/language/return" title="cpp/language/return">return</a> statements as if for a function whose <a href="https://en.cppreference.com/w/cpp/language/function#Return_type_deduction" title="cpp/language/function">return type is declared auto</a>.</td></tr><tr><td>requires</td><td>-</td><td>(since C++20) adds <a href="https://en.cppreference.com/w/cpp/language/constraints" title="cpp/language/constraints">constraints</a> to operator () of the closure type</td></tr><tr><td>body</td><td>-</td><td>Function body</td></tr></tbody></table>

<table><tbody><tr><td><p>If <a href="https://en.cppreference.com/w/cpp/language/auto" title="cpp/language/auto"><code>auto</code></a> is used as a type of a parameter or an explicit template parameter list is provided (since C++20), the lambda is a <i>generic lambda</i>.</p></td><td>(since C++14)</td></tr></tbody></table>

A variable __func__ is implicitly defined at the beginning of body, with semantics as described [here]( https://en.cppreference.com/w/cpp/language/function#func "cpp/language/function").

The lambda expression is a prvalue expression of unique unnamed non-union non-aggregate class type, known as _closure type_, which is declared (for the purposes of [ADL]( https://en.cppreference.com/w/cpp/language/adl "cpp/language/adl")) in the smallest block scope, class scope, or namespace scope that contains the lambda expression. The closure type has the following members, they cannot be [explicitly instantiated]( https://en.cppreference.com/w/cpp/language/function_template#Explicit_instantiation "cpp/language/function template"), [explicitly specialized]( https://en.cppreference.com/w/cpp/language/template_specialization "cpp/language/template specialization"), or (since C++14) named in a [friend declaration]( https://en.cppreference.com/w/cpp/language/friend "cpp/language/friend"):

ClosureType:: operator ()(params)
-------------------------------

<table><tbody><tr><td><p>ret operator ()(params) { body }</p></td><td></td><td>(static and const may be present, see below)</td></tr><tr><td><p>template&lt; template-params&gt;<br>ret operator ()(params) { body }</p></td><td></td><td>(since C++14)<br>(generic lambda, static and const may be present, see below)</td></tr><tr><td></td><td></td><td></td></tr></tbody></table>

Executes the body of the lambda-expression, when invoked. When accessing a variable, accesses its captured copy (for the entities captured by copy), or the original object (for the entities captured by reference).

Unless the keyword mutable was used in the lambda specifiers, the cv-qualifier of the function-call operator or operator template is const and the objects that were captured by copy are non-modifiable from inside this operator (). Explicit const qualifier is not allowed. The function-call operator or operator template is never virtual and cannot have the volatile qualifier.

<table><tbody><tr><td><p>The function-call operator or any given operator template specialization is always <code>constexpr</code> if it satisfies the requirements of a <a href="https://en.cppreference.com/w/cpp/language/constexpr" title="cpp/language/constexpr">constexpr function</a>. It is also constexpr if the keyword constexpr was used in the lambda specifiers.</p></td><td>(since C++17)</td></tr></tbody></table><table><tbody><tr><td><p>The function-call operator or any given operator template specialization is an <a href="https://en.cppreference.com/w/cpp/language/consteval" title="cpp/language/consteval">immediate function</a> if the keyword consteval was used in the lambda specifiers.</p></td><td>(since C++20)</td></tr></tbody></table><table><tbody><tr><td><p>The function-call operator or any given operator template specialization is a <a href="https://en.cppreference.com/w/cpp/language/static#Static_member_functions" title="cpp/language/static">static member function</a> if the keyword static was used in the lambda specifiers.</p></td><td>(since C++23)</td></tr></tbody></table><table><tbody><tr><td><p>For each parameter in params whose type is specified as auto, an invented template parameter is added to template-params, in order of appearance. The invented template parameter may be a <a href="https://en.cppreference.com/w/cpp/language/parameter_pack" title="cpp/language/parameter pack">parameter pack</a> if the corresponding function member of params is a function parameter pack.</p><pre class="hljs cpp">// generic lambda, operator () is a template with two parameters
auto glambda = [](auto a, auto&amp;&amp; b) { return a &lt; b; };
bool b = glambda (3, 3.14); // OK
&nbsp;
// generic lambda, operator () is a template with one parameter
auto vglambda = [](auto printer)
{
    return [=](auto&amp;&amp;... ts) // generic lambda, ts is a parameter pack
    { 
        printer (std::forward&lt; decltype (ts)&gt;(ts)...);
        // nullary lambda (takes no parameters):
        return [=] { printer (ts...); };
    };
};
&nbsp;
auto p = vglambda ([](auto v 1, auto v 2, auto v 3)
{
    std:: cout &lt;&lt; v 1 &lt;&lt; v 2 &lt;&lt; v 3;
});
&nbsp;
auto q = p (1, 'a', 3.14); // outputs 1 a 3.14
q ();                      // outputs 1 a 3.14</pre></td><td>(since C++14)</td></tr></tbody></table><table><tbody><tr><td><p>If the lambda definition uses an explicit template parameter list, that template parameter list is used with operator (). For each parameter in params whose type is specified as <code>auto</code>, an additional invented template parameter is appended to the end of that template parameter list:</p><pre class="hljs cpp">// generic lambda, operator () is a template with two parameters
auto glambda = []&lt; class T&gt;(T a, auto&amp;&amp; b) { return a &lt; b; };
&nbsp;
// generic lambda, operator () is a template with one parameter pack
auto f = []&lt; typename... Ts&gt;(Ts&amp;&amp;... ts)
{
    return foo (std::forward&lt; Ts&gt;(ts)...);
};</pre></td><td>(since C++20)</td></tr></tbody></table>

The exception specification exception on the lambda-expression applies to the function-call operator or operator template.

For the purpose of [name lookup]( https://en.cppreference.com/w/cpp/language/lookup "cpp/language/lookup"), determining the type and value of the [`this` pointer]( https://en.cppreference.com/w/cpp/language/this "cpp/language/this") and for accessing non-static class members, the body of the closure type's function call operator or operator template is considered in the context of the lambda-expression.

```
struct X
{
    int x, y;
    int operator ()(int);
    void f ()
    {
        // the context of the following lambda is the member function X::f
        [=]() -> int
        {
            return operator ()(this->x + y); // X:: operator ()(this->x + (*this). y)
                                            // this has type X*
        };
    }
};
```

### Dangling references

If a non-reference entity is captured by reference, implicitly or explicitly, and the function call operator or a specialization of the function call operator template of the closure object is invoked after the entity's lifetime has ended, undefined behavior occurs. The C++ closures do not extend the lifetimes of objects captured by reference.

Same applies to the lifetime of the current *this object captured via `this`.

ClosureType:: operator ret (*)(params)()
--------------------------------------

<table><tbody><tr><td><p>capture-less non-generic lambda</p></td><td></td><td></td></tr></tbody><tbody><tr><td><p>using F = ret (*)(params);<br>operator F () const noexcept;</p></td><td></td><td>(until C++17)</td></tr><tr><td><p>using F = ret (*)(params);<br>constexpr operator F () const noexcept;</p></td><td></td><td>(since C++17)</td></tr></tbody><tbody><tr><td><p>capture-less generic lambda</p></td><td></td><td></td></tr></tbody><tbody><tr><td><p>template&lt; template-params&gt; using fptr_t = /*see below*/;</p><p>template&lt; template-params&gt;<br></p>operator fptr_t&lt; template-params&gt;() const noexcept;<p></p></td><td></td><td>(since C++14)<br>(until C++17)</td></tr><tr><td><p>template&lt; template-params&gt; using fptr_t = /*see below*/;</p><p>template&lt; template-params&gt;<br></p>constexpr operator fptr_t&lt; template-params&gt;() const noexcept;<p></p></td><td></td><td>(since C++17)</td></tr></tbody><tbody><tr><td></td><td></td><td></td></tr></tbody></table>

This [user-defined conversion function]( https://en.cppreference.com/w/cpp/language/cast_operator "cpp/language/cast operator") is only defined if the capture list of the lambda-expression is empty. It is a public, constexpr, (since C++17) non-virtual, non-explicit, const noexcept member function of the closure object.

<table><tbody><tr><td><p>This function is an <a href="https://en.cppreference.com/w/cpp/language/consteval" title="cpp/language/consteval">immediate function</a> if the function call operator (or specialization, for generic lambdas) is an immediate function.</p></td><td>(since C++20)</td></tr></tbody></table><table><tbody><tr><td><p>A generic captureless lambda has a user-defined conversion function template with the same invented template parameter list as the function-call operator template. If the return type is empty or auto, it is obtained by return type deduction on the function template specialization, which, in turn, is obtained by <a href="https://en.cppreference.com/w/cpp/language/template_argument_deduction" title="cpp/language/template argument deduction">template argument deduction</a> for conversion function templates.</p><pre class="hljs cpp">void f 1 (int (*)(int)) {}
void f 2 (char (*)(int)) {}
void h (int (*)(int)) {}  // #1
void h (char (*)(int)) {} // #2
&nbsp;
auto glambda = [](auto a) { return a; };
f 1 (glambda); // OK
f 2 (glambda); // error: not convertible
h (glambda);  // OK: calls #1 since #2 is not convertible
&nbsp;
int&amp; (*fpi)(int*) = [](auto* a) -&gt; auto&amp; { return *a; }; // OK</pre></td><td>(since C++14)</td></tr></tbody></table><table><tbody><tr><td><p>The value returned by the conversion function is a pointer to a function with C++ <a href="https://en.cppreference.com/w/cpp/language/language_linkage" title="cpp/language/language linkage">language linkage</a> that, when invoked, has the same effect as invoking the closure type's function call operator on a default-constructed instance of the closure type.</p></td><td>(until C++14)</td></tr><tr><td><p>The value returned by the conversion function (template) is a pointer to a function with C++ <a href="https://en.cppreference.com/w/cpp/language/language_linkage" title="cpp/language/language linkage">language linkage</a> that, when invoked, has the same effect as:</p><ul><li>for non-generic lambdas, invoking the closure type's function call operator on a default-constructed instance of the closure type.</li><li>for generic lambdas, invoking the generic lambda's corresponding function call operator template specialization on a default-constructed instance of the closure type.</li></ul></td><td>(since C++14)<br>(until C++23)</td></tr><tr><td><p>The value returned by the conversion function (template) is</p><ul><li>if operator () is static, a pointer to that operator () (specialization) with C++ <a href="https://en.cppreference.com/w/cpp/language/language_linkage" title="cpp/language/language linkage">language linkage</a>,</li><li>otherwise, a pointer to a function with C++ <a href="https://en.cppreference.com/w/cpp/language/language_linkage" title="cpp/language/language linkage">language linkage</a> that, when invoked, has the same effect as:<ul><li>for non-generic lambdas, invoking the closure type's function call operator on a default-constructed instance of the closure type.</li><li>for generic lambdas, invoking the generic lambda's corresponding function call operator template specialization on a default-constructed instance of the closure type.</li></ul></li></ul></td><td>(since C++23)</td></tr></tbody></table><table><tbody><tr><td><p>This function is constexpr if the function call operator (or specialization, for generic lambdas) is constexpr.</p><pre class="hljs cpp">auto Fwd = [](int (*fp)(int), auto a) { return fp (a); };
auto C = [](auto a) { return a; };
static_assert (Fwd (C, 3) == 3);  // OK
&nbsp;
auto NC = [](auto a) { static int s; return a; };
static_assert (Fwd (NC, 3) == 3); // error: no specialization can be
                                // constexpr because of static s</pre><p>If the closure object's operator () has a non-throwing exception specification, then the pointer returned by this function has the type pointer to noexcept function.</p></td><td>(since C++17)</td></tr></tbody></table>

ClosureType:: ClosureType ()
--------------------------

<table><tbody><tr><td><p>ClosureType () = default;</p></td><td></td><td>(since C++20)<br>(only if no captures are specified)</td></tr><tr><td><p>ClosureType (const ClosureType&amp;) = default;</p></td><td></td><td></td></tr><tr><td><p>ClosureType (ClosureType&amp;&amp;) = default;</p></td><td></td><td></td></tr><tr><td></td><td></td><td></td></tr></tbody></table><table><tbody><tr><td><p>Closure types are not <a href="https://en.cppreference.com/w/cpp/named_req/DefaultConstructible" title="cpp/named req/DefaultConstructible">DefaultConstructible</a>. Closure types have no default constructor.</p></td><td>(until C++20)</td></tr><tr><td><p>If no captures are specified, the closure type has a defaulted default constructor. Otherwise, it has no default constructor (this includes the case when there is a capture-default, even if it does not actually capture anything).</p></td><td>(since C++20)</td></tr></tbody></table>

The copy constructor and the move constructor are declared as defaulted and may be implicitly-defined according to the usual rules for [copy constructors]( https://en.cppreference.com/w/cpp/language/copy_constructor "cpp/language/copy constructor") and [move constructors]( https://en.cppreference.com/w/cpp/language/move_constructor "cpp/language/move constructor").

ClosureType::operator=(const ClosureType&)
------------------------------------------

<table><tbody><tr><td><p>ClosureType&amp; operator=(const ClosureType&amp;) = delete;</p></td><td></td><td>(until C++20)</td></tr><tr><td><p>ClosureType&amp; operator=(const ClosureType&amp;) = default;<br>ClosureType&amp; operator=(ClosureType&amp;&amp;) = default;</p></td><td></td><td>(since C++20)<br>(only if no captures are specified)</td></tr><tr><td><p>ClosureType&amp; operator=(const ClosureType&amp;) = delete;</p></td><td></td><td>(since C++20)<br>(otherwise)</td></tr><tr><td></td><td></td><td></td></tr></tbody></table><table><tbody><tr><td><p>The copy assignment operator is defined as deleted (and the move assignment operator is not declared). Closure types are not <a href="https://en.cppreference.com/w/cpp/named_req/CopyAssignable" title="cpp/named req/CopyAssignable">CopyAssignable</a>.</p></td><td>(until C++20)</td></tr><tr><td><p>If no captures are specified, the closure type has a defaulted copy assignment operator and a defaulted move assignment operator. Otherwise, it has a deleted copy assignment operator (this includes the case when there is a capture-default, even if it does not actually capture anything).</p></td><td>(since C++20)</td></tr></tbody></table>

ClosureType::~ClosureType ()
---------------------------

<table><tbody><tr><td><p>~ClosureType () = default;</p></td><td></td><td></td></tr><tr><td></td><td></td><td></td></tr></tbody></table>

The destructor is implicitly-declared.

ClosureType::Captures
---------------------

If the lambda-expression captures anything by copy (either implicitly with capture clause `**[=]**` or explicitly with a capture that does not include the character &, e.g. `**[a, b, c]**`), the closure type includes unnamed non-static data members, declared in unspecified order, that hold copies of all entities that were so captured.

Those data members that correspond to captures without initializers are [direct-initialized]( https://en.cppreference.com/w/cpp/language/direct_initialization "cpp/language/direct initialization") when the lambda-expression is evaluated. Those that correspond to captures with initializers are initialized as the initializer requires (could be copy- or direct-initialization). If an array is captured, array elements are direct-initialized in increasing index order. The order in which the data members are initialized is the order in which they are declared (which is unspecified).

The type of each data member is the type of the corresponding captured entity, except if the entity has reference type (in that case, references to functions are captured as lvalue references to the referenced functions, and references to objects are captured as copies of the referenced objects).

For the entities that are captured by reference (with the capture-default `**[&]**` or when using the character &, e.g. `**[&a, &b, &c]**`), it is unspecified if additional data members are declared in the closure type, but any such additional members must satisfy [LiteralType]( https://en.cppreference.com/w/cpp/named_req/LiteralType "cpp/named req/LiteralType") (since C++17).

### [[edit]( https://en.cppreference.com/mwiki/index.php?title=cpp/language/lambda&action=edit&section=4 "Edit section: Lambda capture")] Lambda capture

The captures is a comma-separated list of zero or more _captures_, optionally beginning with the capture-default. The capture list defines the outside variables that are accessible from within the lambda function body. The only capture defaults are

*   `**&**` (implicitly capture the used automatic variables by reference) and
*   `**=**` (implicitly capture the used automatic variables by copy).

The current object (*this) can be implicitly captured if either capture default is present. If implicitly captured, it is always captured by reference, even if the capture default is `=`. The implicit capture of *this when the capture default is `=` is deprecated. (since C++20)

The syntax of an individual capture in captures is

<table><tbody><tr><td colspan="10"></td></tr><tr><td>identifier</td><td>(1)</td><td></td></tr><tr><td colspan="10"></td></tr><tr><td>identifier <code><b>...</b></code></td><td>(2)</td><td></td></tr><tr><td colspan="10"></td></tr><tr><td>identifier initializer</td><td>(3)</td><td>(since C++14)</td></tr><tr><td colspan="10"></td></tr><tr><td><code><b>&amp;</b></code> identifier</td><td>(4)</td><td></td></tr><tr><td colspan="10"></td></tr><tr><td><code><b>&amp;</b></code> identifier <code><b>...</b></code></td><td>(5)</td><td></td></tr><tr><td colspan="10"></td></tr><tr><td><code><b>&amp;</b></code> identifier initializer</td><td>(6)</td><td>(since C++14)</td></tr><tr><td colspan="10"></td></tr><tr><td><code><b>this</b></code></td><td>(7)</td><td></td></tr><tr><td colspan="10"></td></tr><tr><td><code><b>*</b></code> <code><b>this</b></code></td><td>(8)</td><td>(since C++17)</td></tr><tr><td colspan="10"></td></tr><tr><td><code><b>...</b></code> identifier initializer</td><td>(9)</td><td>(since C++20)</td></tr><tr><td colspan="10"></td></tr><tr><td><code><b>&amp;</b></code> <code><b>...</b></code> identifier initializer</td><td>(10)</td><td>(since C++20)</td></tr><tr><td colspan="10"></td></tr></tbody></table>

1) simple by-copy capture

4) simple by-reference capture

6) by-reference capture with an initializer

7) simple by-reference capture of the current object

8) simple by-copy capture of the current object

9) by-copy capture with an initializer that is a pack expansion

10) by-reference capture with an initializer that is a pack expansion

If the capture-default is `&`, subsequent simple captures must not begin with `&`.

```
struct S 2 { void f (int i); };
void S 2:: f (int i)
{
    [&] {};          // OK: by-reference capture default
    [&, i] {};       // OK: by-reference capture, except i is captured by copy
    [&, &i] {};      // Error: by-reference capture when by-reference is the default
    [&, this] {};    // OK, equivalent to [&]
    [&, this, i] {}; // OK, equivalent to [&, i]
}
```

If the capture-default is `=`, subsequent simple captures must begin with `&` or be *this (since C++17) or `this` (since C++20).

```
struct S 2 { void f (int i); };
void S 2:: f (int i)
{
    [=] {};        // OK: by-copy capture default
    [=, &i] {};    // OK: by-copy capture, except i is captured by reference
    [=, *this] {}; // until C++17: Error: invalid syntax
                   // since C++17: OK: captures the enclosing S 2 by copy
    [=, this] {};  // until C++20: Error: this when = is the default
                   // since C++20: OK, same as [=]
}
```

Any capture may appear only once, and its name must be different from any parameter name:

```
struct S 2 { void f (int i); };
void S 2:: f (int i)
{
    [i, i] {};        // Error: i repeated
    [this, *this] {}; // Error: "this" repeated (C++17)
 
    [i] (int i) {};   // Error: parameter and capture have the same name
}
```

Only lambda-expressions defined at block scope or in a [default member initializer]( https://en.cppreference.com/w/cpp/language/data_members#Member_initialization "cpp/language/data members") may have a capture-default or captures without initializers. For such lambda-expression, the _reaching scope_ is defined as the set of enclosing scopes up to and including the innermost enclosing function (and its parameters). This includes nested block scopes and the scopes of enclosing lambdas if this lambda is nested.

The identifier in any capture without an initializer (other than the `this`-capture) is looked up using usual [unqualified name lookup]( https://en.cppreference.com/w/cpp/language/lookup "cpp/language/lookup") in the _reaching scope_ of the lambda. The result of the lookup must be a [variable]( https://en.cppreference.com/w/cpp/language/object "cpp/language/object") with automatic storage duration declared in the reaching scope, or a [structured binding]( https://en.cppreference.com/w/cpp/language/structured_binding "cpp/language/structured binding") whose corresponding variable satisfies such requirements (since C++20). The entity is _explicitly captured_.

<table><tbody><tr><td><p>A capture with an initializer acts as if it declares and explicitly captures a variable declared with type <a href="https://en.cppreference.com/w/cpp/language/auto" title="cpp/language/auto"><code>auto</code></a>, whose declarative region is the body of the lambda expression (that is, it is not in scope within its initializer), except that:</p><ul><li>if the capture is by-copy, the non-static data member of the closure object is another way to refer to that auto variable.</li><li>if the capture is by-reference, the reference variable's lifetime ends when the lifetime of the closure object ends.</li></ul><p>This is used to capture move-only types with a capture such as x = std:: move (x).</p><p>This also makes it possible to capture by const reference, with &amp; cr = <a href="http://en.cppreference.com/w/cpp/utility/as_const">std::as_const</a>(x) or similar.</p><pre class="hljs markdown">int x = 4;
&nbsp;
auto y = [&amp;r = x, x = x + 1]() -&gt; int
{
    r += 2;
    return x * x;
}(); // updates&nbsp;:: x to 6 and initializes y to 25.</pre></td><td>(since C++14)</td></tr></tbody></table>

If a capture list has a capture-default and does not explicitly capture the enclosing object (as this or *this), or an automatic variable that is [odr-usable]( https://en.cppreference.com/w/cpp/language/definition#ODR-use "cpp/language/definition") in the lambda body, or a [structured binding]( https://en.cppreference.com/w/cpp/language/structured_binding "cpp/language/structured binding") whose corresponding variable has atomic storage duration (since C++20), it captures it _implicitly_ if

*   the body of the lambda [odr-uses]( https://en.cppreference.com/w/cpp/language/definition#ODR-use "cpp/language/definition") the entity

<table><tbody><tr><td><ul><li>or the entity is named in a <a href="https://en.cppreference.com/w/cpp/language/expressions#Potentially-evaluated_expressions" title="cpp/language/expressions">potentially-evaluated</a> expression within an expression that depends on a generic lambda parameter (until C++17) (including when the implicit this-&gt; is added before a use of non-static class member). For this purpose, the operand of <a href="https://en.cppreference.com/w/cpp/language/typeid" title="cpp/language/typeid"><code>typeid</code></a> is always considered potentially-evaluated. Entities might be implicitly captured even if they are only named within a <a href="https://en.cppreference.com/w/cpp/language/if#Constexpr_if" title="cpp/language/if">discarded statement</a>. (since C++17)</li></ul><pre class="hljs cpp">void f (int, const int (&amp;)[2] = {}) {}   // #1
void f (const int&amp;, const int (&amp;)[1]) {} // #2
&nbsp;
void test ()
{
    const int x = 17;
&nbsp;
    auto g 0 = [](auto a) { f (x); };  // OK: calls #1 , does not capture x
&nbsp;
    auto g 1 = [=](auto a) { f (x); }; // does not capture x in C++14, captures x in C++17
                                     // the capture can be optimized away
    auto g 2 = [=](auto a)
    {
        int selector[sizeof (a) == 1 ? 1 : 2] = {};
        f (x, selector); // OK: is a dependent expression, so captures x
    };
&nbsp;
    auto g 3 = [=](auto a)
    {
        typeid (a + x);  // captures x regardless of
                        // whether a + x is an unevaluated operand
    };
}</pre></td><td>(since C++14)</td></tr></tbody></table>

If the body of a lambda [odr-uses]( https://en.cppreference.com/w/cpp/language/definition#ODR-use "cpp/language/definition") an entity captured by copy, the member of the closure type is accessed. If it is not odr-using the entity, the access is to the original object:

```
void f (const int*);
void g ()
{
    const int N = 10;
    [=]
    { 
        int arr[N]; // not an odr-use: refers to g's const int N
        f (&N); // odr-use: causes N to be captured (by copy)
               // &N is the address of the closure object's member N, not g's N
    }();
}
```

If a lambda odr-uses a reference that is captured by reference, it is using the object referred-to by the original reference, not the captured reference itself:

```
#include <iostream>
 
auto make_function (int& x)
{
    return [&] { std:: cout << x << '\n'; };
}
 
int main ()
{
    int i = 3;
    auto f = make_function (i); // the use of x in f binds directly to i
    i = 5;
    f (); // OK: prints 5
}
```

Within the body of a lambda with capture default `=`, the type of any capturable entity is as if it were captured (and thus const-qualification is often added if the lambda is not `mutable`), even though the entity is in an unevaluated operand and not captured (e.g. in [`decltype`]( https://en.cppreference.com/w/cpp/language/decltype "cpp/language/decltype")):

```
void f 3 ()
{
    float x, &r = x;
    [=]
    { // x and r are not captured (appearance in a decltype operand is not an odr-use)
        decltype (x) y 1;        // y 1 has type float
        decltype ((x)) y 2 = y 1; // y 2 has type float const& because this lambda
                               // is not mutable and x is an lvalue
        decltype (r) r 1 = y 1;   // r 1 has type float& (transformation not considered)
        decltype ((r)) r 2 = y 2; // r 2 has type float const&
    };
}
```

Any entity captured by a lambda (implicitly or explicitly) is odr-used by the lambda-expression (therefore, implicit capture by a nested lambda triggers implicit capture in the enclosing lambda).

All implicitly-captured variables must be declared within the _reaching scope_ of the lambda.

If a lambda captures the enclosing object (as this or *this), either the nearest enclosing function must be a non-static member function or the lambda must be in a [default member initializer]( https://en.cppreference.com/w/cpp/language/data_members#Member_initialization "cpp/language/data members"):

```
struct s 2
{
    double ohseven = .007;
    auto f () // nearest enclosing function for the following two lambdas
    {
        return [this]      // capture the enclosing s 2 by reference
        {
            return [*this] // capture the enclosing s 2 by copy (C++17)
            {
                return ohseven; // OK
            }
        }();
    }
 
    auto g ()
    {
        return [] // capture nothing
        { 
            return [*this] {}; // error: *this not captured by outer lambda-expression
        }();
    }
};
```

If a lambda expression (or an instantiation of a generic lambda's function call operator) (since C++14) ODR-uses *this or any variable with automatic storage duration, it must be captured by the lambda expression.

```
void f 1 (int i)
{
    int const N = 20;
    auto m 1 = [=]
    {
        int const M = 30;
        auto m 2 = [i]
        {
            int x[N][M]; // N and M are not odr-used 
                         // (ok that they are not captured)
            x[0][0] = i; // i is explicitly captured by m 2
                         // and implicitly captured by m 1
        };
    };
 
    struct s 1 // local class within f 1 ()
    {
        int f;
        void work (int n) // non-static member function
        {
            int m = n * n;
            int j = 40;
            auto m 3 = [this, m]
            {
                auto m 4 = [&, j] // error: j is not captured by m 3
                {
                    int x = n; // error: n is implicitly captured by m 4
                               // but not captured by m 3
                    x += m;    // OK: m is implicitly captured by m 4
                               // and explicitly captured by m 3
                    x += i;    // error: i is outside of the reaching scope
                               // (which ends at work ())
                    x += f;    // OK: this is captured implicitly by m 4
                               // and explicitly captured by m 3
                };
            };
        }
    };
}
```

Class members cannot be captured explicitly by a capture without initializer (as mentioned above, only [variables]( https://en.cppreference.com/w/cpp/language/object "cpp/language/object") are permitted in the capture list):

```
class S
{
    int x = 0;
 
    void f ()
    {
        int i = 0;
    //  auto l 1 = [i, x] { use (i, x); };      // error: x is not a variable
        auto l 2 = [i, x = x] { use (i, x); };  // OK, copy capture
        i = 1; x = 1; l 2 (); // calls use (0,0)
        auto l 3 = [i, &x = x] { use (i, x); }; // OK, reference capture
        i = 2; x = 2; l 3 (); // calls use (1,2)
    }
};
```

When a lambda captures a member using implicit by-copy capture, it does not make a copy of that member variable: the use of a member variable `m` is treated as an expression (*this). m, and *this is always implicitly captured by reference:

```
class S
{
    int x = 0;
 
    void f ()
    {
        int i = 0;
 
        auto l 1 = [=] { use (i, x); }; // captures a copy of i and
                                      // a copy of the this pointer
        i = 1; x = 1; l 1 ();           // calls use (0, 1), as if
                                      // i by copy and x by reference
 
        auto l 2 = [i, this] { use (i, x); }; // same as above, made explicit
        i = 2; x = 2; l 2 ();           // calls use (1, 2), as if
                                      // i by copy and x by reference
 
        auto l 3 = [&] { use (i, x); }; // captures i by reference and
                                      // a copy of the this pointer
        i = 3; x = 2; l 3 ();           // calls use (3, 2), as if
                                      // i and x are both by reference
 
        auto l 4 = [i, *this] { use (i, x); }; // makes a copy of *this,
                                             // including a copy of x
        i = 4; x = 4; l 4 ();           // calls use (3, 2), as if
                                      // i and x are both by copy
    }
};
```

<table><tbody><tr><td><p>If a lambda expression appears in a <a href="https://en.cppreference.com/w/cpp/language/default_arguments" title="cpp/language/default arguments">default argument</a>, it cannot explicitly or implicitly capture anything, unless all captures have initializers which satisfy the constraints of an expression appearing in a default argument (since C++14):</p><pre class="hljs cpp">void f 2 ()
{
    int i = 1;
&nbsp;
    void g 1 (int = ([i] { return i; })()); // error: captures something
    void g 2 (int = ([i] { return 0; })()); // error: captures something
    void g 3 (int = ([=] { return i; })()); // error: captures something
&nbsp;
    void g 4 (int = ([=] { return 0; })());       // OK: capture-less
    void g 5 (int = ([] { return sizeof i; })()); // OK: capture-less
&nbsp;
    // C++14
    void g 6 (int = ([x = 1] { return x; }))(); // OK: 1 can appear
                                              //     in a default argument
    void g 7 (int = ([x = i] { return x; }))(); // error: i cannot appear
                                              //        in a default argument
}</pre></td><td>(since C++11)</td></tr></tbody></table>

Members of [anonymous unions]( https://en.cppreference.com/w/cpp/language/union "cpp/language/union") members cannot be captured. [Bit-fields]( https://en.cppreference.com/w/cpp/language/bit_field "cpp/language/bit field") can only be captured by copy.

If a nested lambda `m 2` captures something that is also captured by the immediately enclosing lambda `m 1`, then `m 2`'s capture is transformed as follows:

*   if the enclosing lambda `m 1` captures by copy, `m 2` is capturing the non-static member of `m 1`'s closure type, not the original variable or *this; if `m 1` is not mutable, the non-static data member is considered to be const-qualified.
*   if the enclosing lambda `m 1` captures by reference, `m 2` is capturing the original variable or *this.

```
#include <iostream>
 
int main ()
{
    int a = 1, b = 1, c = 1;
 
    auto m 1 = [a, &b, &c]() mutable
    {
        auto m 2 = [a, b, &c]() mutable
        {
            std:: cout << a << b << c << '\n';
            a = 4; b = 4; c = 4;
        };
        a = 3; b = 3; c = 3;
        m 2 ();
    };
 
    a = 2; b = 2; c = 2;
 
    m 1 ();                             // calls m 2 () and prints 123
    std:: cout << a << b << c << '\n'; // prints 234
}
```

### [[edit]( https://en.cppreference.com/mwiki/index.php?title=cpp/language/lambda&action=edit&section=5 "Edit section: Notes")] Notes

<table><tbody><tr><th>Feature-test macro</th><th>Value</th><th>Std</th><th>Comment</th></tr><tr><td><a href="https://en.cppreference.com/w/cpp/feature_test#Language_features" title="cpp/feature test"><code>__cpp_lambdas</code></a></td><td>200907 L</td><td>(C++11)</td><td>Lambda expressions</td></tr><tr><td rowspan="2"><a href="https://en.cppreference.com/w/cpp/feature_test#Language_features" title="cpp/feature test"><code>__cpp_generic_lambdas</code></a></td><td>201304 L</td><td>(C++14)</td><td>Generic lambda expressions</td></tr><tr><td>201707 L</td><td>(C++20)</td><td>Explicit template parameter list for generic lambdas</td></tr><tr><td rowspan="2"><a href="https://en.cppreference.com/w/cpp/feature_test#Language_features" title="cpp/feature test"><code>__cpp_init_captures</code></a></td><td>201304 L</td><td>(C++14)</td><td>Lambda init-capture</td></tr><tr><td>201803 L</td><td>(C++20)</td><td>Allow pack expansion in lambda init-capture</td></tr><tr><td><a href="https://en.cppreference.com/w/cpp/feature_test#Language_features" title="cpp/feature test"><code>__cpp_capture_star_this</code></a></td><td>201603 L</td><td>(C++17)</td><td>Lambda capture of *this by value as [=, *this]</td></tr><tr><td><a href="https://en.cppreference.com/w/cpp/feature_test#Language_features" title="cpp/feature test"><code>__cpp_constexpr</code></a></td><td>201603 L</td><td>(C++17)</td><td>constexpr lambda</td></tr><tr><td><a href="https://en.cppreference.com/w/cpp/feature_test#Language_features" title="cpp/feature test"><code>__cpp_static_call_operator</code></a></td><td>202207 L</td><td>(C++23)</td><td>static operator () for captureless lambdas</td></tr></tbody></table>

### [[edit]( https://en.cppreference.com/mwiki/index.php?title=cpp/language/lambda&action=edit&section=6 "Edit section: Example")] Example

This example shows how to pass a lambda to a generic algorithm and how objects resulting from a lambda expression can be stored in [std:: function]( https://en.cppreference.com/w/cpp/utility/functional/function "cpp/utility/functional/function") objects.

```
#include <algorithm>
#include <functional>
#include <iostream>
#include <vector>
 
int main ()
{
    std::vector<int> c{1, 2, 3, 4, 5, 6, 7};
    int x = 5;
    c.erase (std:: remove_if (c.begin (), c.end (), [x](int n) { return n < x; }), c.end ());
 
    std:: cout << "c: ";
    std:: for_each (c.begin (), c.end (), [](int i) { std:: cout << i << ' '; });
    std:: cout << '\n';
 
    // the type of a closure cannot be named, but can be inferred with auto
    // since C++14, lambda could own default arguments
    auto func 1 = [](int i = 6) { return i + 4; };
    std:: cout << "func 1: " << func 1 () << '\n';
 
    // like all callable objects, closures can be captured in std::function
    // (this may incur unnecessary overhead)
    std::function<int(int)> func 2 = [](int i) { return i + 4; };
    std:: cout << "func 2: " << func 2 (6) << '\n';
 
    constexpr int fib_max {8};
    std:: cout << "Emulate `recursive lambda` calls:\nFibonacci numbers: ";
    auto nth_fibonacci = [](int n)
    {
        std::function<int(int, int, int)> fib = [&](int n, int a, int b)
        {
            return n ? fib (n - 1, a + b, a) : b;
        };
        return fib (n, 0, 1);
    };
 
    for (int i{1}; i <= fib_max; ++i)
        std:: cout << nth_fibonacci (i) << (i < fib_max ? ", " : "\n");
 
    std:: cout << "Alternative approach to lambda recursion:\nFibonacci numbers: ";
    auto nth_fibonacci 2 = [](auto self, int n, int a = 0, int b = 1) -> int
    {
        return n ? self (self, n - 1, a + b, a) : b;
    };
 
    for (int i{1}; i <= fib_max; ++i)
        std:: cout << nth_fibonacci 2 (nth_fibonacci 2, i) << (i < fib_max ? ", " : "\n");
 
#ifdef __cpp_explicit_this_parameter
    std:: cout << "C++23 approach to lambda recursion:\n";
    auto nth_fibonacci 3 = [](this auto self, int n, int a = 0, int b = 1) -> int
    {
         return n ? self (n - 1, a + b, a) : b;
    };
 
    for (int i{1}; i <= fib_max; ++i)
        std:: cout << nth_fibonacci 3 (i) << (i < fib_max ? ", " : "\n");
#endif
}
```

Possible output:

```
c: 5 6 7
func 1: 10
func 2: 10
Emulate `recursive lambda` calls:
Fibonacci numbers: 0, 1, 1, 2, 3, 5, 8, 13
Alternative approach to lambda recursion:
Fibonacci numbers: 0, 1, 1, 2, 3, 5, 8, 13
```

### [[edit]( https://en.cppreference.com/mwiki/index.php?title=cpp/language/lambda&action=edit&section=7 "Edit section: Defect reports")] Defect reports

The following behavior-changing defect reports were applied retroactively to previously published C++ standards.

<table><tbody><tr><th>DR</th><th>Applied to</th><th>Behavior as published</th><th>Correct behavior</th></tr><tr><td><a rel="nofollow" href="https://cplusplus.github.io/CWG/issues/974.html">CWG 974</a></td><td>C++11</td><td>default argument was not allowed in the<br>parameter list of a lambda expression</td><td>allowed</td></tr><tr><td><a rel="nofollow" href="https://cplusplus.github.io/CWG/issues/975.html">CWG 975</a></td><td>C++11</td><td>the return type of closure's operator () was only<br>deduced if lambda body contains a single return</td><td>deduced as if for C++14<br>auto-returning function</td></tr><tr><td><a rel="nofollow" href="https://cplusplus.github.io/CWG/issues/1249.html">CWG 1249</a></td><td>C++11</td><td>it is not clear that whether the captured member of the<br>enclosing non-mutable lambda is considered <code>const</code> or not</td><td>considered <code>const</code></td></tr><tr><td><a rel="nofollow" href="https://cplusplus.github.io/CWG/issues/1557.html">CWG 1557</a></td><td>C++11</td><td>the language linkage of the returned function type of<br>the closure type's conversion function was not specified</td><td>it has C++<br>language linkage</td></tr><tr><td><a rel="nofollow" href="https://cplusplus.github.io/CWG/issues/1607.html">CWG 1607</a></td><td>C++11</td><td>lambda expressions could appear in<br>function and function template signatures</td><td>not allowed</td></tr><tr><td><a rel="nofollow" href="https://cplusplus.github.io/CWG/issues/1612.html">CWG 1612</a></td><td>C++11</td><td>members of anonymous unions could be captured</td><td>not allowed</td></tr><tr><td><a rel="nofollow" href="https://cplusplus.github.io/CWG/issues/1722.html">CWG 1722</a></td><td>C++11</td><td>the conversion function for captureless lambdas<br>had unspecified exception specification</td><td>conversion function<br>is noexcept</td></tr><tr><td><a rel="nofollow" href="https://cplusplus.github.io/CWG/issues/1772.html">CWG 1772</a></td><td>C++11</td><td>the semantic of __func__ in lambda body was not clear</td><td>it refers to the closure<br>class's operator ()</td></tr><tr><td><a rel="nofollow" href="https://cplusplus.github.io/CWG/issues/1780.html">CWG 1780</a></td><td>C++14</td><td>it was unclear whether the members of the closure types of generic<br>lambdas can be explicitly instantiated or explicitly specialized</td><td>neither is allowed</td></tr><tr><td><a rel="nofollow" href="https://cplusplus.github.io/CWG/issues/1891.html">CWG 1891</a></td><td>C++11</td><td>closure had a deleted default constructor<br>and implicit copy/move constructors</td><td>no default and defaulted<br>copy/move constructors</td></tr><tr><td><a rel="nofollow" href="https://cplusplus.github.io/CWG/issues/1937.html">CWG 1937</a></td><td>C++11</td><td>as for the effect of invoking the result of the conversion function, it was<br>unspecified on which object calling its operator () has the same effect</td><td>on a default-constructed<br>instance of the closure type</td></tr><tr><td><a rel="nofollow" href="https://cplusplus.github.io/CWG/issues/2011.html">CWG 2011</a></td><td>C++11</td><td>for a reference captured by reference, it was unspecified<br>which entity the identifier of the capture refers to</td><td>it refers to the originally<br>referenced entity</td></tr><tr><td><a rel="nofollow" href="https://cplusplus.github.io/CWG/issues/2095.html">CWG 2095</a></td><td>C++11</td><td>the behavior of capturing rvalue references<br>to functions by copy was not clear</td><td>made clear</td></tr><tr><td><a rel="nofollow" href="https://cplusplus.github.io/CWG/issues/2211.html">CWG 2211</a></td><td>C++11</td><td>the behavior was unspecified if a capture<br>has the same name as a parameter</td><td>the program is ill-<br>formed in this case</td></tr><tr><td><a rel="nofollow" href="https://cplusplus.github.io/CWG/issues/2358.html">CWG 2358</a></td><td>C++14</td><td>lambda expressions appearing in default arguments had<br>to be capture-less even if all captures are initialized with<br>expressions which can appear in default arguments</td><td>allow such lambda<br>expressions with captures</td></tr><tr><td><a rel="nofollow" href="https://cplusplus.github.io/CWG/issues/2509.html">CWG 2509</a></td><td>C++17</td><td>each specifier could have multiple<br>occurrences in the specifier sequence</td><td>each specifier can only<br>appear at most once in<br>the specifier sequence</td></tr></tbody></table>

### [[edit]( https://en.cppreference.com/mwiki/index.php?title=cpp/language/lambda&action=edit&section=8 "Edit section: See also")] See also

<table><tbody><tr><td><a href="https://en.cppreference.com/w/cpp/language/auto" title="cpp/language/auto"><code>auto</code> specifier </a>(C++11)</td><td>specifies a type deduced from an expression <a rel="nofollow" href="https://en.cppreference.com/mwiki/index.php?title=Template:cpp/language/dsc_auto&amp;action=edit">[edit]</a></td></tr><tr><td></td><td>wraps callable object of any copy constructible type with specified function call signature<br>(class template) <a rel="nofollow" href="https://en.cppreference.com/mwiki/index.php?title=Template:cpp/utility/functional/dsc_function&amp;action=edit">[edit]</a></td></tr><tr><td></td><td>wraps callable object of any type with specified function call signature<br>(class template) <a rel="nofollow" href="https://en.cppreference.com/mwiki/index.php?title=Template:cpp/utility/functional/dsc_move_only_function&amp;action=edit">[edit]</a></td></tr></tbody></table>

### [[edit]( https://en.cppreference.com/mwiki/index.php?title=cpp/language/lambda&action=edit&section=9 "Edit section: External links")] External links

[Nested function]( https://en.wikipedia.org/wiki/Nested_function "enwiki: Nested function") - a function which is defined within another (_enclosing_) function.

NewPP limit report Preprocessor visited node count: 13146/1000000 Preprocessor generated node count: 25795/1000000 Post‐expand include size: 300641/2097152 bytes Template argument size: 105598/2097152 bytes Highest expansion depth: 20/40 Expensive parser function count: 0/100