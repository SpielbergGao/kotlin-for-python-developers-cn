## 使用空值

没有引用任何内容的变量引用为 `null`（或者您可以说变量`为 null`）。 与Python中的 `None` 相反，`null` 不是对象-它只是一个关键字，用于使变量不指向任何内容或检查其是否起作用（必须使用 `==` 或 `！=`）。 由于空值是编程错误的常见来源，因此 Kotlin 鼓励尽可能避免它们-除非声明为允许空值，否则变量实际上不能为空值，您可以在类型名后加上 `？`。 例如：

```kotlin
fun test(a: String, b: String?) {
}
```

编译器将允许将此函数称为例如 `test("a"，"b")` 或 `test("a"，null)`，但不作为 `test(null，"b"）`或 `test(null，null)`。 仅当编译器可以证明 `a` 不可能为空时，才允许调用 `test(a，b)`。 在 `test` 内部，编译器将不允许您对 `b` 进行任何操作，否则如果 `b` 恰好为 null，则会导致异常-因此您可以执行 `a.length`，但不能执行 `b.length`。 但是，一旦您处于检查 `b` 不为 null 的条件中，就可以这样做：

```kotlin
if (b != null) {
    println(b.length)
}
```

Or:

```kotlin
if (b == null) {
    // Can't use members of b in here
} else {
    println(b.length)
}
```

频繁进行空值检查很烦人，因此，如果必须考虑可能存在空值，那么 Kotlin 中有几个非常有用的运算符可以简化使用可能为空值的操作，如下所述。


## 安全调用操作符

`x?.y` evaluates `x`, and if it is not null, it evaluates `x.y` (without reevaluating `x`), whose result becomes the result of the expression - otherwise, you get null. This also works for functions, and it can be chained - for example, `x?.y()?.z?.w()` will return null if any of `x`, `x.y()`, or `x.y().z` produce null; otherwise, it will return the result of `x.y().z.w()`.


## Elvis 操作符

`x ?: y` evaluates `x`, which becomes the result of the expression unless it's null, in which case you'll get `y` instead (which ought to be of a non-nullable type).  This is also known as the "Elvis operator". You can even use it to perform an early return in case of null:

```kotlin
val z = x ?: return y
```

This will assign `x` to `z` if `x` is non-null, but if it is null, the entire function that contains this expression will stop and return `y` (this works because `return` is also an expression, and if it is evaluated, it evaluates its argument and then makes the containing function return the result).


## 非空断言操作符

Sometimes, you're in a situation where you have a value `x` that you know is not null, but the compiler doesn't realize it. This can legitimately happen when you're interacting with Java code, but if it happens because your code's logic is more complicated than the compiler's ability to reason about it, you should probably restructure your code. If you can't convince the compiler, you can resort to saying `x!!` to form an expression that produces the value of `x`, but whose type is non-nullable:

```kotlin
val x: String? = javaFunctionThatYouKnowReturnsNonNull()
val y: String = x!!
```

It can of course be done as a single expression: `val x = javaFunctionThatYouKnowReturnsNonNull()!!`.

`!!` will will raise a `NullPointerException` if the value actually is null. So you could also use it if you really need to call a particular function and would rather have an exception if there's no object to call it on (`maybeNull!!.importantFunction()`), although a better solution (because an NPE isn't very informational) is this:

```kotlin
val y: String = x ?: throw SpecificException("Useful message")
y.importantFunction()
```

The above could also be a oneliner - and note that the compiler knows that because the `throw` will prevent `y` from coming into existence if `x` is null, `y` must be non-null if we reach the line below. Contrast this with `x?.importantFunction()`, which is a no-op if `x` is null.




---

[← 上一节：异常](exceptions.html) | [下一节：函数式编程 →](functional-programming.html)


---

*本资料英文原文的作者是 [Aasmund Eldhuset](https://eldhuset.net/)；其所有权属于[可汗学院（Khan Academy）](https://www.khanacademy.org/)，授权许可为 [CC BY-NC-SA 3.0 US（署名-非商业-相同方式共享）](https://creativecommons.org/licenses/by-nc-sa/3.0/us/)。请注意，这并不是可汗学院官方产品的一部分。中文版由[灰蓝天际](https://hltj.me/)、[Yue-plus](https://github.com/Yue-plus) 翻译，遵循相同授权方式。*