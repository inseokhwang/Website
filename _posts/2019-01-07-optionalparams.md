---
layout: post
title: Optional Parameters in C#
---

I'm a big fan of optional parameters (or default parameters in other languages), but my coworker strongly advised against using optional parameters in any enterprise code written in C# unless well justified. I was skeptical at first. Why would you not want optional parameters to avoid having to write so many overloaded functions? Add a new parameter here, add a new parameter there, and next thing you know, you've gotten yourself pages of formatted code to achieve the same effect with slight variations.

His advice comes from a different angle: debuggability when using DLL's. C# doesn't actually compile different overloaded versions of the function, it simply copies the default value at compile time. If you're hotswapping the DLL's to apply code fixes and patches, this pitfall will eventually stab you in the back. Below is few code snippets for demonstration.

Compile a DLL with a static function that take in a default value:
```csharp
namespace LibraryWithDefaultParam
{
    using System;

    public class NumberPrinter
    {
        public static void EchoNumber(int x = 5)
        {
            Console.WriteLine("The number is: " + x);
        }
    }
}
```

Now, create a console application with the above function:
```csharp
namespace CallerConsole
{
    using LibraryWithDefaultParam;

    class Program
    {
        static void Main(string[] args)
        {
            NumberPrinter.EchoNumber();
        }
    }
}
```

Copy the DLL and the executable file over to a separate folder and run:
![Console output](/assets/images/posts/2019-01-07/optionalparameters/Before.PNG)

Now, rename the DLL to something else, and create/copy over a new DLL with the following changes:
```csharp
namespace LibraryWithDefaultParam
{
    using System;

    public class NumberPrinter
    {
        /// Update the 5 to a 4
        public static void EchoNumber(int x = 4)
        {
            Console.WriteLine("The number is: " + x);
        }
    }
}
```

And voila, we still get 5.
![Console output with new DLL](/assets/images/posts/2019-01-07/optionalparameters/After.PNG)