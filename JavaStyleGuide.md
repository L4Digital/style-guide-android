# Java Style Guidelines for Android
New projects should follow the Android Gradle project structure that is defined on the [Android Gradle plugin user guide](http://tools.android.com/tech-docs/new-build-system/user-guide#TOC-Project-Structure).

## Class Files
Class names are written in `UpperCamelCase`.

For classes that extend an Android component, the name of the class should end with the name of the component; for example: `SignInActivity`, `SignInFragment`, `ImageUploaderService`, `ChangePasswordDialog`.

## Java Language Rules
We follow standard Java coding conventions with the addition of a few rules:

### Don’t Ignore Exceptions
Sometimes it is tempting to write code that completely ignores an exception like this:

~~~java
void setServerPort(String value) {
    try {
        serverPort = Integer.parseInt(value);
    } catch (NumberFormatException e) { }
}
~~~

You should never do this. While you may think that your code will never encounter this error condition or that it is not important to handle it, ignoring exceptions like above creates mines in your code for someone else to trip over some day. You should handle every Exception in your code in some principled way. The specific handling varies depending on the case.

*Anytime somebody has an empty catch clause they should have a creepy feeling. There are definitely times when it is actually the correct thing to do, but at least you have to think about it. In Java you can’t escape the creepy feeling.* — [James Gosling](http://www.artima.com/intv/solid4.html)

Acceptable alternatives (in order of preference) are:

* Throw the exception up to the caller of your method.

~~~java
void setServerPort(String value) throws NumberFormatException {
    serverPort = Integer.parseInt(value);
}
~~~

* Throw a new exception that’s appropriate to your level of abstraction.

~~~java
void setServerPort(String value) throws ConfigurationException {
    try {
        serverPort = Integer.parseInt(value);
    } catch (NumberFormatException e) {
        throw new ConfigurationException("Port " + value + " is not valid.");
    }
}
~~~

* Handle the error gracefully and substitute an appropriate value in the catch { } block.

~~~java
/** Set port. If value is not a valid number, 80 is substituted. */

void setServerPort(String value) {
    try {
        serverPort = Integer.parseInt(value);
    } catch (NumberFormatException e) {
        serverPort = 80;  // default port for server
    }
}
~~~

* Catch the Exception and throw a new RuntimeException. This is dangerous: only do it if you are positive that if this error occurs, the appropriate thing to do is crash.

~~~java
/** Set port. If value is not a valid number, die. */

void setServerPort(String value) {
    try {
        serverPort = Integer.parseInt(value);
    } catch (NumberFormatException e) {
        throw new RuntimeException("port " + value " is invalid, ", e);
    }
}
~~~

*Note that the original exception is passed to the constructor for RuntimeException.*

* Last resort: if you are confident that actually ignoring the exception is appropriate then you may ignore it, but you must also comment why with a good reason:

~~~java
/** If value is not a valid number, original port number is used. */
void setServerPort(String value) {
    try {
        serverPort = Integer.parseInt(value);
    } catch (NumberFormatException e) {
        // Method is documented to just ignore invalid user input.
        // serverPort will just be unchanged.
    }
}
~~~

### Don’t Catch Generic Exception
Sometimes it is tempting to be lazy when catching exceptions and do something like this:

~~~java
try {
    someComplicatedIOFunction();        // may throw IOException
    someComplicatedParsingFunction();   // may throw ParsingException
    someComplicatedSecurityFunction();  // may throw SecurityException
    // phew, made it all the way
} catch (Exception e) {                 // I’ll just catch all exceptions
    handleError();                      // with one generic handler!
}
~~~

You should not do this. In almost all cases it is inappropriate to catch generic Exception or Throwable, preferably not Throwable, because it includes Error exceptions as well. It is very dangerous. It means that Exceptions you never expected (including RuntimeExceptions like ClassCastException) end up getting caught in application-level error handling. It obscures the failure handling properties of your code. It means if someone adds a new type of Exception in the code you’re calling, the compiler won’t help you realize you need to handle that error differently. And in most cases you shouldn’t be handling different types of exception the same way, anyway.

There are rare exceptions to this rule: certain test code and top-level code where you want to catch all kinds of errors (to prevent them from showing up in a UI, or to keep a batch job running). In that case you may catch generic Exception (or Throwable) and handle the error appropriately. You should think very carefully before doing this, though, and put in comments explaining why it is safe in this place.

Alternatives to catching generic Exception:

* Catch each exception separately as separate catch blocks after a single try. This can be awkward but is still preferable to catching all Exceptions. Beware repeating too much code in the catch blocks.

* Refactor your code to have more fine-grained error handling, with multiple try blocks. Split up the IO from the parsing, handle errors separately in each case.

* Rethrow the exception. Many times you don’t need to catch the exception at this level anyway, just let the method throw it.

Remember: exceptions are your friend! When the compiler complains you’re not catching an exception, don’t scowl. Smile: the compiler just made it easier for you to catch runtime problems in your code.

### Don’t Use Finalizers
Finalizers are a way to have a chunk of code executed when an object is garbage collected.

Pros: can be handy for doing cleanup, particularly of external resources.

Cons: there are no guarantees as to when a finalizer will be called, or even that it will be called at all.

Decision: we don’t use finalizers. In most cases, you can do what you need from a finalizer with good exception handling. If you absolutely need it, define a close() method (or the like) and document exactly when that method needs to be called. See InputStream for an example. In this case it is appropriate but not required to print a short log message from the finalizer, as long as it is not expected to flood the logs.

### Fully Qualify Imports
When you want to use class Bar from package foo,there are two possible ways to import it:

1. `import foo.*;`

Pros: Potentially reduces the number of import statements.

1. `import foo.Bar;`

Pros: Makes it obvious what classes are actually used. Makes code more readable for maintainers.

Decision: Use the latter for importing all Android code. An explicit exception is made for java standard libraries (`java.util.*`, `java.io.*`, etc.) and unit test code (`junit.framework.*`)

## Java Library Rules
There are conventions for using Android’s Java libraries and tools. In some cases, the convention has changed in important ways and older code might use a deprecated pattern or library. When working with such code, it’s okay to continue the existing style. When creating new components never use deprecated libraries.

## Java Style Rules

### Use Javadoc Standard Comments
Every class and nontrivial public method you write should contain a Javadoc comment with at least one sentence describing what the class or method does. This sentence should start with a 3rd person descriptive verb.

Examples:

~~~java
/** Returns the correctly rounded positive square root of a double value. */
static double sqrt(double a) {
    ...
}
~~~

or

~~~java
/**
 * Constructs a new String by converting the specified array of
 * bytes using the platform’s default character encoding.
 */
public String(byte[] bytes) {
    ...
}
~~~

You do not need to write Javadoc for trivial get and set methods such as `setFoo()` if all your Javadoc would say is “sets Foo”. If the method does something more complex (such as enforcing a constraint or having an important side effect), then you should document it. And if it’s not obvious what the property “Foo” means, you should document it.

Every method you write, whether public or otherwise, would benefit from Javadoc. Public methods are part of an API and therefore should include Javadoc.

Android does not currently enforce a specific style for writing Javadoc comments, but you should follow the instructions [How to Write Doc Comments for the Javadoc Tool](http://www.oracle.com/technetwork/java/javase/documentation/index-137868.html).

### Write Short Methods
To the extent that it is feasible, methods should be kept small and focused. It is, however, recognized that long methods are sometimes appropriate, so no hard limit is placed on method length. If a method exceeds 40 lines or so, think about whether it can be broken up without harming the structure of the program.

### Define Fields in Standard Places
Fields should be defined either at the top of the file, or immediately before the methods that use them.

### Limit Variable Scope
The scope of local variables should be kept to a minimum. By doing so, you increase the readability and maintainability of your code and reduce the likelihood of error. Each variable should be declared in the innermost block that encloses all uses of the variable.

Local variables should be declared at the point they are first used. Nearly every local variable declaration should contain an initializer. If you don’t yet have enough information to initialize a variable sensibly, you should postpone the declaration until you do.

One exception to this rule concerns try-catch statements. If a variable is initialized with the return value of a method that throws a checked exception, it must be initialized inside a try block. If the value must be used outside of the try block, then it must be declared before the try block, where it cannot yet be sensibly initialized:

~~~java
// Instantiate class cl, which represents some sort of Set
Set s = null;

try {
    s = (Set) cl.newInstance();
} catch(IllegalAccessException e) {
    throw new IllegalArgumentException(cl + " not accessible");
} catch(InstantiationException e) {
    throw new IllegalArgumentException(cl + " not instantiable");
}

// Exercise the set
s.addAll(Arrays.asList(args));
~~~

But even this case can be avoided by encapsulating the try-catch block in a method:

~~~java
Set createSet(Class cl) {
    // Instantiate class cl, which represents some sort of Set
    try {
        return (Set) cl.newInstance();
    } catch(IllegalAccessException e) {
        throw new IllegalArgumentException(cl + " not accessible");
    } catch(InstantiationException e) {
        throw new IllegalArgumentException(cl + " not instantiable");
    }
}

...

// Exercise the set
Set s = createSet(cl);
s.addAll(Arrays.asList(args));
~~~

Loop variables should be declared in the for statement itself unless there is a compelling reason to do otherwise:

~~~java
for (int i = 0; i < n; i++) {
    doSomething(i);
}
~~~

and

~~~java
for (Iterator i = c.iterator(); i.hasNext(); ) {
    doSomethingElse(i.next());
}
~~~

### Order Import Statements
The ordering of import statements should be:

1. Android imports
1. Imports from third parties (`com`, `junit`, `net`, `org`)
1. `java` and `javax`

To exactly match the IDE settings, the imports should be:

* Alphabetical within each grouping, with capital letters before lowercase letters (e.g. Z before a).
* There should be a blank line between each major grouping (`android`, `com`, `junit`, `net`, `org`, `java`, `javax`).

Originally there was no style requirement on the ordering. This meant that the IDE’s were either always changing the ordering, or IDE developers had to disable the automatic import management features and maintain the imports by hand. This was deemed bad. When java-style was asked, the preferred styles were all over the map. It pretty much came down to our needing to “pick an ordering and be consistent.” So we chose a style, updated the style guide, and made the IDEs obey it. We expect that as IDE users work on the code, the imports in all of the packages will end up matching this pattern without any extra engineering effort.

This style was chosen such that:

* The imports people want to look at first tend to be at the top (`android`)
* The imports people want to look at least tend to be at the bottom (`java`)
* Humans can easily follow the style
* IDEs can follow the style

The use and location of static imports have been mildly controversial issues. Some people would prefer static imports to be interspersed with the remaining imports, some would prefer them reside above or below all other imports. Additionally, we have not yet come up with a way to make all IDEs use the same ordering.

Since most people consider this a low priority issue, just use your judgement and please **be consistent**.

### Order Class Members and Methods
There is no single correct solution for this but using a logical and consistent order will improve code learnability and readability. It is recommendable to use the following order:

1. Interfaces
1. Constants
1. Fields
1. Constructors
1. Override methods and callbacks (public or private)
1. Public methods
1. Private methods
1. Inner classes

Example:

~~~java
public class MainActivity extends Activity {

    public static final String EXTRA_TITLE = "extra_title";

    private TextView titleTextView;

    @Override
    public void onCreate() {
        ...
    }

    public void setTitle(String text) {
        titleTextView.setText(text);
    }

    private void setUpView() {
        ...
    }

    static class AnInnerClass {
        ...
    }
}
~~~

If your class is extending an Android component such as an Activity or Fragment, it is good practice to order the override methods so that they match the component's lifecycle. For example, if you have an Activity that implements onCreate(), onDestroy(), onPause() and onResume(), then the correct order is:

~~~java
public class MainActivity extends Activity {

    @Override
    public void onCreate() {}

    @Override
    public void onResume() {}

    @Override
    public void onPause() {}

    @Override
    public void onDestroy() {}
}
~~~

### Order Method Parameters
The Context parameter should always be first for methods that require it, whereas callback interfaces should always be last.

Examples:

~~~java
// Context always goes first
User loadUser(Context context, int userId);

// Callbacks always go last
void loadUserAsync(Context context, int userId, UserCallback callback);
~~~

### Use Spaces for Indentation
We use 4 space indents for blocks. We never use tabs. When in doubt, **be consistent** with code around you.

We use 8 space indents for line wraps, including function calls and assignments. For example, this is correct:

~~~java
Instrument i =
        someLongExpression(that, wouldNotFit, on, one, line);
~~~

and this is not correct:

~~~java
Instrument i =
    someLongExpression(that, wouldNotFit, on, one, line);
~~~

### Follow Field Naming Conventions
* Field names are written in `lowerCamelCase`
* Static final fields (constants) are ALL\_CAPS\_WITH\_UNDERSCORES.

For example:

~~~java
public class MyClass {
    public static final int SOME_CONSTANT = 42;
    private static final String ARGUMENT_NAME = "arg_name";
    public int publicField;
    int packagePrivateField;
    protected int protectedField;
    private static MyClass singleton;
    private int privateField;
}
~~~

### Use Standard Brace Style
Braces do not go on their own line; they go on the same line as the code before them. So:

~~~java
class MyClass {
    int func() {
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

We require braces around the statements for a conditional. Except, if the body of the conditional is a simple return that fits on one line, you may (but are not obligated to) put it all on one line. That is, this is legal:

~~~java
if (condition) {
    return;
}
~~~

and this is legal:

~~~java
if (condition) return;
~~~

but this is still illegal:

~~~java
if (condition)
    return;  // bad!
~~~

### Limit Line Length
Each line of text in your code should be at most 120 characters long.

Exception: if a comment line contains an example command or a literal URL longer than 120 characters, that line may be longer than 120 characters for ease of cut and paste.

Exception: import lines can go over the limit because humans rarely see them. This also simplifies tool writing.

### Wrap Chained Methods
When multiple methods are chained (such as when using Builders), each method call should be on a new line, breaking before the `.`

~~~java
Glide.with(context)
        .load(imageUrl)
        .into(imageView);
~~~

This also applies to RxJava where every operator must be on a new line and the line should break before the `.`

~~~java
public Observable<Long> getTimer() {
    return Observable.timer(60, TimeUnit.SECONDS)
            .observeOn(AndroidSchedulers.mainThread())
            .onErrorReturn(new Function<Throwable, Long>() {

                @Override
                public Long apply(Throwable throwable) throws Exception {
                    return -1L;
                }
            });
}
~~~

### Use Standard Java Annotations
#### Annotation Practices
Annotations should precede other modifiers for the same language element. Simple marker annotations (e.g. @Override) can be listed on the same line with the language element. If there are multiple annotations, or parameterized annotations, they should each be listed one-per-line in alphabetical order.

Android standard practices for the three predefined annotations in Java are:

* `@Deprecated`: The @Deprecated annotation must be used whenever the use of the annotated element is discouraged. If you use the @Deprecated annotation, you must also have a @deprecated Javadoc tag and it should name an alternate implementation. In addition, remember that a @Deprecated method *is still supposed to work*.
<br><br>
If you see old code that has a @deprecated Javadoc tag, please add the @Deprecated annotation.

* `@Override`: The @Override annotation must be used whenever a method overrides the declaration or implementation from a super-class.
<br><br>
For example, if you use the @inheritdocs Javadoc tag, and derive from a class (not an interface), you must also annotate that the method @Overrides the parent class’s method.

* `@SuppressWarnings`: The @SuppressWarnings annotation should only be used under circumstances where it is impossible to eliminate a warning. If a warning passes this “impossible to eliminate” test, the @SuppressWarnings annotation must be used, so as to ensure that all warnings reflect actual problems in the code.
<br><br>
When a @SuppressWarnings annotation is required, the code should be refactored to isolate the software elements where the annotation applies.
<br><br>
When a @SuppressWarnings annotation is necessary, it must be prefixed with a TODO comment that explains the “impossible to eliminate” condition. This will normally identify an offending class that has an awkward interface. 

~~~java
// TODO: The third-party class com.third.useful.Utility.rotate() needs generics
@SuppressWarnings("generic-cast")
List<String> blix = Utility.rotate(blax);
~~~

#### Annotation Style
When annotations are applied to a class, method, or constructor, they are listed after the documentation block and should appear as one annotation per line.

~~~java
/** This is the documentation block about the method */
@AnnotationA
@AnnotationB
public void myMethod() { }
~~~

Annotations applying to fields should be listed on the same line, unless there is more than one annotation.

~~~java
@Nullable String firstName;

@AnnotationA
@AnnotationB
ContactInfo contactInfo;
~~~

### Treat Acronyms as Words
Treat acronyms and abbreviations as words in naming variables, methods, and classes. The names are much more readable:

Good                | Bad
------------------- | -------------------
`XmlHttpRequest`    | `XMLHTTPRequest`
`getCustomerId`     | `getCustomerID`
`class Html`        | `class HTML`
`String url`        | `String URL`
`long id`           | `long ID`

### Use TODO Comments
Use TODO comments for code that is temporary, a short-term solution, or good-enough but not perfect.

TODOs should include the string TODO in all caps, followed by a colon, and reference the associated JIRA ticket:

~~~java
// TODO: JIRA-123 - Remove this code after the UrlTable2 has been checked in.
~~~

and

~~~java
// TODO: JIRA-124 - Change this to use a flag instead of a constant.
~~~

If your TODO is of the form “At a future date do something” make sure that you either include a very specific date (“Fix by November 2005”) or a very specific event (“Remove this code after all production mixers understand protocol V7.”).

## Javatests Style Rules

### Follow Test Method Naming Conventions
When naming test methods, you can use an underscore to separate what is being tested from the specific case being tested. This style makes it easier to see exactly what cases are being tested.

For example:

~~~java
testMethod_specificCase1 testMethod_specificCase2

void testIsDistinguishable_protanopia() {
    ColorMatcher colorMatcher = new ColorMatcher(PROTANOPIA)
    assertFalse(colorMatcher.isDistinguishable(Color.RED, Color.BLACK))
    assertTrue(colorMatcher.isDistinguishable(Color.X, Color.Y))
}
~~~
