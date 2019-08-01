# U-Labs BBCode-Parser
A vBulletin 4 compatible BB-Code parser for .NET Standard 2 OSS under [GNU GPLv3](https://choosealicense.com/licenses/gpl-3.0/).

![Demo](https://u-img.net/img/5022Nk.png)

## Usage 
You need to inject an instance of `BBCodeToHtml` and pass the BBCode to the `Parse(string bbCode)` method like this: 
```cs
string html = BBCodeToHtml.Parse("This is [b]Bold[/b]");
```
The solution contains a simple Razor views based demo project with our markup from the demo screenshot above. See 
[Index.html](./ULabs.BBCodeParserDemo/Pages/Index.cshtml) with the sample code. A simple integration into other projects using a 
NuGet package is planned. 

### Dependency injection
We don't have interfaces yet for clean dependency injection, but a helper method is already avaliable:

```cs
using ULabs.BBCodeParser.Html;
namespace ULabs.BBCodeParserDemo {
    public class Startup {
        public void ConfigureServices(IServiceCollection services) {
            services.AddBBCodeParser();
            // ...
        }
    }
}

```
The `AddBBCodeParser()` method can register all services with their default configuration. Changes are only required if you would like to
modify or extend BBCodes. 

## Customizing BBCodes
Common BBCodes like formattings were already covered by this library. This happens in 
[BBCodeHtmlMapper](./ULabs.BBCodeParser/Html/BBCodeHtmlMapper.cs). However, you're able to customize them like removing some BBCodes or
add new ones. To do this, simple create a instance of `BBCodeHtmlMapper` and modify `Codes` as you want. Every BBCode is covered by 
an instance of the `BBCode` class. Simple ones like bold formattings only contains their corresponding HTML tag: 

```cs
var boldCode = new BBCode("[b]", "<strong>");
```

This simply replaces `[b]` with `<b>` and it's corresponding close tag. 

Fore more complex tags, the `BBCode` class has an overload for some delegate method that get all required information in a `Node` object:

```cs
var complexCustomCode = new BBCode("[shadow]", (node) => $"<span class='text-shadow'>{node.InnerHtml}</span>");
```

You can also fetch the argument, which may got passed to the BBCode. This is everything after `{tagName}=`. A good example to explain this
is `[font]`:

```cs
var fontCode = new BBCode("[font]", (node) => $"<span style='font-family: {node.Argument}'>{node.InnerHtml}</span>");
```

So `[font=Arial]Hi![/font]` would result in `Arial` as argument, where `InnerHtml` is `Hi!`. 

## Motivation
This project is part of my approach to develop on a modern .NET Core application stack for vBulletin. I did some POCs, also on the database.
But now it's time to create a better structure. Since I believe in open source and also use a lot of OSS, I'd also like to share my work to
give something back for the community. 

The motivation behind this parser was that I couldn't find an reasonably actively maintained project for .NET Core/Standard that is 
customizeable or at least supports vBulletin's BBCodes. And I also want to avoid parsing using Regular Expression. It seems not the right 
tool for structured BBCode. So I wrote my own, which also gave me some experience how parser work. 

## Stabilits and known limitations
Currently, this library is not considered as production ready. Some things doesn't work well yet. For example formatting in lists, 
handling multiple headlines. And also attachments from vBulletin aren't parsed yet. I had an approach that works, but since we need 
access to VBs database, this part should be kept seperately. 

There are some other bugs for sure. So feel free to create issues and even better: Try to help me fixing them with pull requests. 
Contributions are welcome! :)

## Contributions/Coding Conventions

As said above: Every help on this library is welcome! The code in this repository should fit to 
[the official C# Coding Conventions](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/inside-a-program/coding-conventions). 
My only intentionally deviation from this were the curly brackets, which are not placed in an extra line. So code should looke like 

```cs
if(true) {
	myClass.DoAction();
}
```

instead of 

```cs
if(true) 
{
	myClass.DoAction();
}
```

## Credits
This project itself uses the following external open source libraries to which I would like to express my gratitude:
* [HtmlSanitizer](https://github.com/mganss/HtmlSanitizer)
* [RazorLight](https://github.com/toddams/RazorLight)