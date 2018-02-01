# TextBox Input Masks

It is often desirable to limit input in textboxes to certain keystrokes only. For instance, a textbox may be designed to only accept numeric input, or only character input, and so forth. CODE Framework provides a standard mechanism for this by means of a TextBoxEx class and its attached properties.

## Standard Masked Input

CODE Framework provides a feature to create textboxes with masked input using standard input mask syntax. This can be used to create text fields for entering phone numbers. In addition, CODE Framework provides enhanced features for currency and decimal input.

The feature is similar to other masked textbox classes/controls that are available. However, there are some key differences:

1. In true CODE Framework fashion, we didn’t want to create a separate control, but instead have an attached property that can be applied to any textbox control. 
2. We wanted to support the standard MaskedTextProvider features .NET provides to make sure we support all those features… 
3. ...but also add some additional features that neither the default .NET functionality, nor any of the other controls handle well. In particular, this has to do with decimal entry and currency entry. 

So here is how it basically works. If you want to create a textbox for currency entry, you can simply do this:

```
<TextBox controls:TextBoxEx.InputMask="$"/>
```

Or, if you wanted to create a textbox for a US phone number, you can do this:

```
<TextBox controls:TextBoxEx.InputMask="(999) 999-9999" />
```

So the basic idea is pretty simple. The user can now only enter what you’d expect. The Text property contains the full value as displayed. So if you used the phone number input mask, the actual Text of the control would be something like “(832) 717-4445”. If that is what you want, you can simply bind the Text property. However, it is often also desirable to just get the value without any special mask characters. For that, we have a special property that can be used like this:

```
<TextBox controls:TextBoxEx.InputMask="(999) 999-9999" controls:TextBoxEx.TextUnmasked="{Binding TestValue}" />
```

The unmasked text property then would contain “8327174445”. Similarly, for the currency textbox, the text would be something like “$ 19.95” (a string), while the unmasked version would simply be 19.95 (which is suitable to bind to a decimal field.

We also support a few extra features for currencies. Here is a more complete example:

```
<TextBox controls:TextBoxEx.InputMask="$" controls:TextBoxEx.InputMaskCurrencySymbol="Z$" controls:TextBoxEx.TextUnmasked="{Binding Amount}" />
```

This uses a currency format, but it uses Zimbabwe $ as the currency. This prevents the problem that often makes currency formats useless, because they are tied to the Windows UI currency. So if you have a machine that is set to Euros, the display mask would actually switch to Euros, even though what you want to save may be US$. 

The currency handling (as well as generic decimal handling, which can be done by setting the mask to “d”) is quite special in our control. It handles a lot of special cases. For instance, many of the other masked textboxes do not handle correctly when the user hits “.” as well as many other scenarios. So that should be quite helpful overall.

So what are all the masks we support? Well, there are the “$” and the “d” masks that are special for CODE Framework. In addition, we support all standard mask strings as described in the MaskedTextProvider documentation: https://msdn.microsoft.com/en-us/library/system.windows.forms.maskedtextbox.mask(v=vs.110).aspx

The following table shows the supported mask characters:

| Masking element | Description |
| --- | --- |
| $ | Currency input (decimal, with configurable or default currency symbol based on current UI culture settings). See below for information about negative values. |
| d | Decimal input. See below for information about negative values and number of digits allowed. |
| % | Percentage input. Similar to decimal, but with a trailing % symbol. See below for information about negative values and number of digits allowed. |
| 0 | Digit, required. This element will accept any single digit between 0 and 9. |
| 9 | Digit or space, optional. |
| # | Digit or space, optional. If this position is blank in the mask, it will be rendered as a space in the Text property. Plus (+) and minus (-) signs are allowed. |
| L | Letter, required. Restricts input to the ASCII letters a-z and A-Z. This mask element is equivalent to [a-zA-Z] in regular expressions. |
| ? | Letter, optional. Restricts input to the ASCII letters a-z and A-Z. This mask element is equivalent to [a-zA-Z]? in regular expressions. |
| & | Character, required. If the AsciiOnly property is set to true, this element behaves like the "L" element. |
| C | Character, optional. Any non-control character. If the AsciiOnly property is set to true, this element behaves like the "?" element. |
| A | Alphanumeric, required. If the AsciiOnly property is set to true, the only characters it will accept are the ASCII letters a-z and A-Z. This mask element behaves like the "a" element. |
| a | Alphanumeric, optional. If the AsciiOnly property is set to true, the only characters it will accept are the ASCII letters a-z and A-Z. This mask element behaves like the "A" element. | 
| . | Decimal placeholder. The actual display character used will be the decimal symbol appropriate to the format provider, as determined by the control's FormatProvider property. |
| , | Thousands placeholder. The actual display character used will be the thousands placeholder appropriate to the format provider, as determined by the control's FormatProvider property. |
| : | Time separator. The actual display character used will be the time symbol appropriate to the format provider, as determined by the control's FormatProvider property. |
| / | Date separator. The actual display character used will be the date symbol appropriate to the format provider, as determined by the control's FormatProvider property. |
| < | Shift down. Converts all characters that follow to lowercase. |
| > | Shift up. Converts all characters that follow to uppercase. |
| &#124; | Disable a previous shift up or shift down. |
| \ | Escape. Escapes a mask character, turning it into a literal. "\\" is the escape sequence for a backslash. |

All other characters are literals. All non-mask elements will appear as themselves within MaskedTextBox. Literals always occupy a static position in the mask at run time, and cannot be moved or deleted by the user.
 
Decimal (d), currency ($), and percentage (%) formatted fields also support negative values. However, this can be disabled by setting TextBoxEx.InputMaskSupportsNegative to false.

The number of digits allowed to the left of the decimal point (the "characteristic" of the entered value) for decimal (d), currency ($), and percentage (%) formatted fields is defined through the TextBoxEx.InputMaskDecimalCharacteristicMask property. The default is "###.###.###.##0", which allows for 12 digits to the left of the decimal point.

The number of digits allowed to the right of the decimal point (known as the "mantissa") for decimal (d) and percentage (%) formatted fields is defined through the TextBoxEx.InputMaskDecimalFractionalMask property. The default is "00", which calls for 2 fractional/mantissa digits being entered. For currency ($) format, the number of mantissa digits are driven by currency system settings.

 
## Regular Expression Input Masks

Consider the following example:

```
<Window x:Class="CODE.Framework.Wpf.TestBench.TextBoxTest"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:controls="clr-namespace:CODE.Framework.Wpf.Controls;assembly=CODE.Framework.Wpf"
        Title="TextBoxTest" Height="300" Width="300">
    <StackPanel>
        <TextBox controls:TextBoxEx.InputMaskRegEx="^\p{L}*$" />
    </StackPanel>
</Window>
```

In this case, the input in the textbox is limited by means of a regular expression to accept only alpha characters. You can use different regular expressions to limit input to different keystrokes.

Note: This only works for regular expressions that are generic enough to match any keystroke. The expression used in the above example matches all alpha characters. Thus when the user presses a key such as “a”, the expression matches and thus the input is allowed. When the user then presses “b”, the complete text is now “ab”, which still matches the expression, and the input is allowed. Expressions that requires a specific combination of input elements are more problematic. For instance, if you use a regular expression that matches an email address, then that requires some text, followed by the @ symbol, followed by more text, a dot, and some more text. However, then the user starts out with an empty textbox and then presses “a”, then “a” does not match the regular expression since it is missing elements such as the @ symbol and so the input is considered invalid and the user can never really type in a complete email address. Using a RegEx input mask is not appropriate for such a scenario.
