# Kotlin Style Guidelines for Android
Kotlin is a statically-typed programming language that runs on the JVM and can also be compiled to JavaScript. It provides modern language features and has been designed to be completely interoperable with Java code.

All Kotlin code is expected to adhere to our [Java Style Guidelines](JavaStyleGuide.md) where applicable.

### Resources
* [Android Kotlin Guides](https://android.github.io/kotlin-guides/style.html)
* [Kotlin Coding Conventions](http://kotlinlang.org/docs/reference/coding-conventions.html)
* [Kotlin Language Reference](https://kotlinlang.org/docs/reference/)

## Kotlin Language Rules
We follow standard Kotlin coding conventions with the addition of a few rules:

### Naming
* Type names are written as `UpperCamelCase`
* Method and properties are written as `lowerCamelCase`
* Constants and immutable properties are written as `ALL_CAPS_WITH_UNDERSCORES`

For example:

~~~kotlin
class MyClass {
    
    private val field = 0

    fun getValue() = field

    companion object {
        const val LOG_TAG = "MyClass"
    }
}
~~~

For classes that extend an Android component, the name of the class should end with the name of the component; for example: `SignInActivity`, `SignInFragment`, `ImageUploaderService`, `ChangePasswordDialog`.

### Declarations
There should be a space before a colon when it separates type and supertype, but not when the colon separates instance and type:

~~~kotlin
interface Foo<out T : Any> : Bar {
    fun foo(a: Int): T
}
~~~

Types can be inferred by variable assignment in Kotlin and should be omitted unless necessary:

~~~kotlin
val bar = 10  // ": Int" is inferred
~~~

If a function returns Unit, the return type should be omitted:

~~~kotlin
fun foo() { // ": Unit" is omitted here
    // ...
}
~~~

### Brace Style
Braces do not go on their own line; they go on the same line as the code before them:

~~~kotlin
class MyClass {
    fun func(): Int {
        if (something) {
            // ...
        } else if (somethingElse) {
            // ...
        } else {
            // ...
        }
    }
}
~~~

We require the body of a statement to start on a new line with the following exceptions:

* The body is a lambda parameter with a single line:

~~~kotlin
observable?.let { subscribe(it) }
~~~

* The body is a single method call:

~~~kotlin
if (condition) doSomething()
~~~

* The body only contains the body of a keyword, then that keyword can be lifted:

~~~kotlin
if (condition) with(something) {
    setupSomethingAdapter(this)
    setupSomethingViews(this)
}

// ...

if (condition) try {
    doSomething()
} finally {
    logSomething()
}
~~~

* Ternary operations in Kotlin are represented as if/else statements and are allowed to be a single line:

~~~kotlin
val result = if (condition) 1 else 2
~~~

### Companion objects

Companion objects should always be declared at the bottom of the class. Fields within a companion object should follow our naming conventions:

~~~kotlin
class MainActivity : Activity() {

    // ...

    companion object {
        const val EXTRA_TITLE = "extra_title"
        const val EXTRA_URL = "extra_url"
    }
}
~~~

### Nullability
Objects and properties should not be nullable whenever possible. `!!` should rarely be used and `?` should be used sparingly. Limiting the number of nullable objects and properties will reduce the need for null checks and will provide for more stable code. 

### Strings
Always prefer string interpolation over concatenation:

~~~kotlin
val name = "${user.first} ${user.last}"
~~~

### When Statements
Case branches in when() statements should be a single line:

~~~kotlin
return when (field) {
    is String -> field
    is Int -> field.toString()
    else -> ""
}
~~~

### Extensions
Global extension functions should be used moderately and only when the extended class source code is not available or modifiable.
