### Gradle Implementation vs API configuration

I'm trying to figure it out what is the difference between api and implementation configuration while building my dependencies.
In the documentation, it says that implementation has better build time, but, seeing this comment in a similar question I got to wonder if is it true.
Since I'm no expert in gradle, I hope someone can help. I've read the documentation already but I was wondering about a easy-to-understand explanation.


Gradle compile keyword was deprecated in favor of the api and implementation keywords to configure dependencies.

Using api is the equivalent of using the deprecated compile, so if you replace all compile with api everything will works as always.

To understand the implementation keyword consider the following example.

EXAMPLE

Suppose you have a library called MyLibrary that internally uses another library called InternalLibrary. Something like this:
```
    // 'InternalLibrary' module
    public class InternalLibrary {
        public static String giveMeAString(){
            return "hello";
        }
    }
```
```
    // 'MyLibrary' module
    public class MyLibrary {
        public String myString(){
            return InternalLibrary.giveMeAString();
        }
    }
```
Suppose the MyLibrary build.gradle uses api configuration in dependencies{} like this:

```
dependencies {
    api project(':InternalLibrary')
}
```
You want to use MyLibrary in your code so in your app's build.gradle you add this dependency:
```
dependencies {
    api project(':MyLibrary')
}
```
Using the api configuration (or deprecated compile) you can access both MyLibrary and InternalLibrary in your application code:
```
// Access 'MyLibrary' (as desired and expected)
MyLibrary myLib = new MyLibrary();
System.out.println(myLib.myString());
```
```
// Can ALSO access the internal library too (and you shouldn't)
System.out.println(InternalLibrary.giveMeAString());
```
In this way the module MyLibrary is potentially "leaking" the internal implementation of something. You shouldn't (be able to) use that because it's not directly imported by you.

The implementation configuration was introduced to prevent this. So now if you use implementation instead of api in MyLibrary:

```
dependencies {
    implementation project(':InternalLibrary')
}
```
And in your app's build.gradle:

```
dependencies {
    implementation project(':MyLibrary')
}
```
you won't be able to call InternalLibrary.giveMeAString() in your app code anymore.

Note that if MyLibrary uses api to import InternalLibrary, your app WILL be able to call InternalLibrary.giveMeAString() without problems, independent of using api or implementation to add MyLibrary to your app.

This sort of boxing strategy allows Android Gradle plugin to know that if you edit something in InternalLibrary, it must only trigger the recompilation of MyLibrary and not the recompilation of your entire app, because you don't have access to InternalLibrary.

When you have a lot of nested dependencies this mechanism can speed up the build a lot. (Watch the video linked at the end for a full understanding of this)

CONCLUSIONS

When you switch to the new Android Gradle plugin 3.X.X, you should replace all your compile with the implementation keyword (1*). Then try to compile and test your app. If everything it's ok leave the code as is, if you have problems you probably have something wrong with your dependencies or you used something that now is private and not more accessible. Suggestion by Android Gradle plugin engineer Jerome Dochez (1)*)

If you are a library mantainer you should use api for every dependency which is needed for the public API of your library, while use implementation for test dependencies or dependencies which must not be used by the final users.

Useful article Showcasing the difference between implementation and api

REFERENCES (This is the same video splitted up for time saving)

Google I/O 2017 - How speed up Gradle builds (FULL VIDEO)

Google I/O 2017 - How speed up Gradle builds (NEW GRADLE PLUGIN 3.0.0 PART ONLY)

Google I/O 2017 - How speed up Gradle builds (reference to 1*)

Android documentation
