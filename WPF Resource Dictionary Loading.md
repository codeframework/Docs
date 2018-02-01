# WPF Resource Dictionary Loading

_Excerpt from an internal email at EPS/CODE_:

We have added a few new small but interesting features to the theme resource dictionary loading mechanism in the WPF framework.

In the past, we would always load a theme specific resource dictionary with views. For instance, when you load a Customer.xaml view and the current theme is set to “Metro”, it also loads a Customer.Metro.xaml file. This has now been enhanced so you can have multiple theme resource files. So in addition to Customer.Metro.xaml, you can now have Customer.Metro.0.xaml and Customer.Metro.1.xaml and Customer.Metro.2.xaml and so forth. There is no limit to how many such files you can have, but they MUST be sequentially numbered. So if you have Customer.Metro.4.xaml and the next is Customer.Metro.6.xaml, it will not load #6 since no #5 was found and thus it will stop searching for more.

Another new feature is that we now also have an AllThemes resource file. So in addition to loading Customer.Metro.xaml, it now also loads a Customer.AllThemes.xaml, so this is another place you can put stuff you need to load for all themes. (It is thus similar to the already supported xxx.Layout.xaml resources but I guess that naming confused people). This also supports the numbered scheme, so you can have multiples of those.

So the overall theme loading sequence for views is now this:

```raw
View.xaml
View.AllThemes.xaml
View.AllThemes.[0].xaml
View.AllThemes.[1].xaml
View.AllThemes.[n].xaml
View.[Theme].xaml
View.[Theme].[0].xaml
View.[Theme].[1].xaml
View.[Theme].[n].xaml
View.Layout.xaml
View.Layout.[0].xaml
View.Layout.[1].xaml
View.Layout.[n].xaml
```

Note that the sequence can be significant in cases where you might have resources of the same name defined multiple times. Last one always wins in that case. So this is another reason why we added the AllThemes version, since that is loaded before the other themes, while Layout is loaded afterwards.
