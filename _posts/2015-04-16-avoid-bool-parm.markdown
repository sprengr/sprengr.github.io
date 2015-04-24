---
layout: page
title:  "Avoid boolean params"
date:   2015-04-16 18:54:32
categories: cleancode
tags: featured cleancode c# .net
---
I recently stumbled upon following .net API calls:
{% highlight csharp %}
public void LoadData() {
    //...
    personBindingSource.ResetBindings(true);
    positionBindingSource.ResetBindings(false);
}
{% endhighlight %}
By just reading the code you don't know what will happen with these bindings. Will `positionBindingSource` be reset? It doesn't read like it would. So what does the boolean parameter mean? 

After checking out the [docs][bindingSourceDocs] we find out it refers to `metadataChanged`:
>true if the data schema has changed; false if only values have changed.

 How could we avoid this extra step of checking the implementation/doc? 
 In C# (and in other languages) we have enums, they are pretty clear about what their values mean. So how about we refactor it like this:

{% highlight csharp %}
public void LoadData() {
    //...
    personBindingSource.ResetBindings(ResetMode.DataSchema);
    positionBindingSource.ResetBindings(ResetMode.Data);
}
{% endhighlight %}


But isn't reseting a data schema something different than resetting data? Why is this even in the same method? I find this even clearer:

{% highlight csharp %}
public void LoadData() {
    //...
    personBindingSource.ResetDataSchema();
    positionBindingSource.ResetData();
}
{% endhighlight %}

ResetBindings seems to be an improper name if it actually resets data schema or values. 

P.S. Yes, I currently have to deal with a WinForms Project 

P.P.S. Of course WinForms is an old API and probably wouldn't be designed the same way today. But this method ships unchanged from .net 2.0 in 4.5 without ever been refactored.

[bindingSourceDocs]: https://msdn.microsoft.com/en-us/library/system.windows.forms.bindingsource.resetbindings%28v=vs.110%29.aspx?cs-save-lang=1&cs-lang=csharp#code-snippet-1