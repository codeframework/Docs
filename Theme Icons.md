# CODE Framework Theme Icons

CODE Framework WPF has a special ```ThemeIcon``` control, which is kind of cool. In its simplest fashion, it just makes it easy to add an icon into a view and bind it to one of the default icons we support, by means of setting an enum. Like this:

```
<mvvm:ThemeIcon StandardIcon="Save" />
```

So that is kind of cool, because it makes it easier to use the default icons we provide. So instead of always looking the information up in the documentation (see also: [Standard Icons](Themes---Standard-Icon-Resources) ), the enum guides you to the icons that are available (with a simplified name... so ```CODE.Framework-Icon-Save``` is just called ```Save``` in the enum).

The ThemeIcon does a few things for you. For one, it loads the icon in question, and, as the name suggest, it does so in a theme-specific way. So it will always load the right icon for the current theme. But the thing that is really neat about it is that it is aware of themes changing on the fly. So if the user changes the theme, the ```ThemeIcon``` control realizes that the theme was switched, and it will unload the old icon, and reload the one for the newly selected theme. So that makes for a nice user experience.

Note that this also works for non-standard theme. The ```StandardIcon``` property is a convenient way to get at icons, but you can also do this:

```
<mvvm:ThemeIcon IconResourceKey="MySpecialIcon" />
```

This simply loads a brush resource called “MySpecialIcon” (the ```StandardIcon``` property is just a simplified way of setting the resource key). Even in this case, the theme icon control still listens to theme switching events (which are raised on the ```ApplicationEx``` object... so ```Application.Current``` has those events) and thus re-evaluates the resource whenever a new theme is loaded. So if you have created theme-specific icons using your own keys, then they will load correctly. (If the icon isn’t theme specific, it will just end up re-loading the same one… wastes a bit of processing power, but doesn’t have other ill effects). 

Side-note: Icon resources should be vector-based drawing brushes. Ours certainly all are. But you could use any kind of brush resource, such as image brushes (yuk!) or video brushes, and so forth. But vector brushes are best, because they are high quality and can be displayed at any size. (Really, you could use this control to display “icons” that are huge in size and perhaps will an entire 4K display or more).

Another cool feature of this control is that it has fallback icons. In other words: If the icon you reference doesn’t exist, it will load a different icon. By default, it will load an icon resource called ```CODE.Framework-Icon-IconMissing```, which is provided in every theme. But you can change that by means of a fallback resource property. Just make sure you point to a resource that is always there ;-). Also, you can turn the whole fallback behavior off through the ```UseFallbackIcon``` property. Oh, and of course the Fallback icon is also re-evaluated when themes switch.

A really nice side-effect of all this is that CODE Framework now properly switches all the standard icons it uses whenever the theme changes. For instance, the Ribbon in the Workplace theme, or the Metro Tiles, or the Universe Hamburger Menu, or all drop-down menus, toolbars, and even the standard view-model templates in lists, now switch icons properly when the theme switches. It is probably not a surprise that this was one of the main motivations to create this control in the first place :-).
