---
layout: docpage
navgroup: docs
title: "Gendarme Rules: Maintainability"
---

[Gendarme]({{ site.github.url }}/old_site/Gendarme "Gendarme")'s maintainability rules are located in the **Gendarme.Rules.Maintainability.dll** assembly. Latest sources are available from [anonymous SVN](http://anonsvn.mono-project.com/viewcvs/trunk/mono-tools/gendarme/rules/Gendarme.Rules.Maintainability/).

<table>
<col width="100%" />
<tbody>
<tr class="odd">
<td align="left"><h2>Table of contents</h2>
<ul>
<li><a href="#rules">1 Rules</a>
<ul>
<li><a href="#avoidalwaysnullfieldrule">1.1 AvoidAlwaysNullFieldRule</a></li>
<li><a href="#avoidcomplexmethodsrule">1.2 AvoidComplexMethodsRule</a>
<ul>
<li><a href="#highthreshold">1.2.1 HighThreshold</a></li>
<li><a href="#lowthreshold">1.2.2 LowThreshold</a></li>
<li><a href="#mediumthreshold">1.2.3 MediumThreshold</a></li>
<li><a href="#successthreshold">1.2.4 SuccessThreshold</a></li>
</ul></li>
<li><a href="#avoiddeepinheritancetreerule">1.3 AvoidDeepInheritanceTreeRule</a>
<ul>
<li><a href="#countexternaldepth">1.3.1 CountExternalDepth</a></li>
<li><a href="#maximumdepth">1.3.2 MaximumDepth</a></li>
</ul></li>
<li><a href="#avoidlackofcohesionofmethodsrule">1.4 AvoidLackOfCohesionOfMethodsRule</a>
<ul>
<li><a href="#lowseveritylowerlimit">1.4.1 LowSeverityLowerLimit</a></li>
<li><a href="#mediumseveritylowerlimit">1.4.2 MediumSeverityLowerLimit</a></li>
<li><a href="#minimumfieldcount">1.4.3 MinimumFieldCount</a></li>
<li><a href="#minimummethodcount">1.4.4 MinimumMethodCount</a></li>
<li><a href="#successlowerlimit">1.4.5 SuccessLowerLimit</a></li>
</ul></li>
<li><a href="#avoidunnecessaryspecializationrule">1.5 AvoidUnnecessarySpecializationRule</a></li>
<li><a href="#considerusingstopwatchrule">1.6 ConsiderUsingStopwatchRule</a></li>
<li><a href="#preferstringisnulloremptyrule">1.7 PreferStringIsNullOrEmptyRule</a></li>
</ul></li>
<li><a href="#feedback">2 Feedback</a></li>
</ul></td>
</tr>
</tbody>
</table>

Rules
=====

### AvoidAlwaysNullFieldRule

A type has a private field whose value is always null.

**Bad** example:

``` csharp
internal sealed class Bad {
    private List<int> values;
 
    public List<int> Values {
        get {
            return values;
        }
    }
}
```

**Good** example:

``` csharp
internal sealed class Good {
    private List<int> values = new List<int>();
 
    public List<int> Values {
        get {
            return values;
        }
    }
}
```

**Notes**

-   This rule is available since Gendarme 2.4

### AvoidComplexMethodsRule

This rule computes the cyclomatic complexity (CC) for every method and reports any method with a CC over 25 (this limit is configurable). Large CC value often indicate complex code that is hard to understand and maintain. It's likely that breaking the method into several methods will help readability. This rule won't report any defects on code generated by the compiler or by tools.

**Notes**

-   This rule is available since Gendarme 2.0

**Configuration**

Some elements of this rule can be customized to better fit your needs.

#### HighThreshold

Methods with cyclomatic complexity less than this (but higher than MediumThreshold) will be reported as high severity.

#### LowThreshold

Methods with cyclomatic complexity less than this will be reported as low severity.

#### MediumThreshold

Methods with cyclomatic complexity less than this (but higher than LowThreshold) will be reported as medium severity.

#### SuccessThreshold

The cyclomatic complexity at which defects begin to be reported.

### AvoidDeepInheritanceTreeRule

This rule will fire if a type has (by default) more than four base classes defined within the assembly set being analyzed. Optionally it will also count base classes defined outside the assembly set being analyzed.

**Notes**

-   This rule is available since Gendarme 2.0

**Configuration**

Some elements of this rule can be customized to better fit your needs.

#### CountExternalDepth

If true the rule will count base classes defined outside the assemblies being analyzed.

#### MaximumDepth

Classes with more base classes than this will result in a defect.

### AvoidLackOfCohesionOfMethodsRule

This rule checks every type for lack of cohesion between the fields and the methods. Low cohesion is often a sign that a type is doing too many, different and unrelated things. The cohesion score is given for each defect (higher is better).

**Notes**

-   This rule is available since Gendarme 2.0

**Configuration**

Some elements of this rule can be customized to better fit your needs.

#### LowSeverityLowerLimit

Defects with cohesion values greater than this will be reported at low severity.

#### MediumSeverityLowerLimit

Defects with cohesion values greater than (but less than LowSeverityLowerLimit) this will be reported at medium severity.

#### MinimumFieldCount

The minimum number of fields a class must have to be checked.

#### MinimumMethodCount

The minimum number of methods a class must have to be checked.

#### SuccessLowerLimit

Cohesion values lower than this will result in a defect.

### AvoidUnnecessarySpecializationRule

This rule checks methods for over specialized parameters - i.e. parameter types that are unnecessarily specialized with respect to what the method needs to perform its job. This often impairs the reusability of the method. If a problem is found the rule will suggest the most general type, or interface, required for the method to work.

**Bad** example:

``` csharp
public class DefaultEqualityComparer : IEqualityComparer {
    public int GetHashCode (object obj)
    {
        return o.GetHashCode ();
    }
}
 
public int Bad (DefaultEqualityComparer ec, object o)
{
    return ec.GetHashCode (o);
}
```

**Good** example:

``` csharp
public class DefaultEqualityComparer : IEqualityComparer {
    public int GetHashCode (object obj)
    {
        return o.GetHashCode ();
    }
}
 
public int Good (IEqualityComparer ec, object o)
{
    return ec.GetHashCode (o);
}
```

**Notes**

-   This rule is available since Gendarme 2.0

### ConsiderUsingStopwatchRule

This rule checks methods for cases where a **System.Diagnostics.Stopwatch** could be used instead of using **System.DateTime** to compute the time required for an action. Stopwatch is preferred because it better expresses the intent of the code and because (on some platforms at least) StopWatch is accurate to roughly the microsecond whereas DateTime.Now is only accurate to 16 milliseconds or so. This rule only applies to assemblies compiled with the .NET framework version 2.0 (or later).

**Bad** example:

``` csharp
public TimeSpan DoLongOperation ()
{
    DateTime start = DateTime.Now;
    DownloadNewOpenSuseDvdIso ();
    return DateTime.Now - start;
}
```

**Good** example:

``` csharp
public TimeSpan DoLongOperation ()
{
    Stopwatch watch = Stopwatch.StartNew ();
    DownloadNewOpenSuseDvdIso ();
    return watch.Elapsed;
}
```

**Notes**

-   This rule is available since Gendarme 2.0

### PreferStringIsNullOrEmptyRule

This rule checks methods for cases where **String.IsNullOrEmpty** could be used instead of doing separate null and length checks. This does not affect execution nor performance (much) but it does improve source code readability. This rule only applies to assemblies compiled with .NET 2.0 (or later).

**Bad** example:

``` csharp
public bool SendMessage (string message)
{
    if ((message == null) || (message.Length == 0)) {
        return false;
    }
    return SendMessage (Encode (message));
}
```

**Good** example:

``` csharp
public bool SendMessage (string message)
{
    if (String.IsNullOrEmpty (message)) {
        return false;
    }
    return SendMessage (Encode (message));
}
```

**Notes**

-   This rule is available since Gendarme 2.0

Feedback
========

Please report any documentation errors, typos or suggestions to the [Gendarme Google Group](http://groups.google.com/group/gendarme). Thanks!
