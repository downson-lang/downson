# Announcing downson

Concluding nearly a month of hard work, I'm finally making my (actual) favorite pet project, downson open source. 

In this article, I'd like to give some insight behind the birth of the downson format, as well as a look into the future. Here I won't go into great detail regarding what downson is or how its syntax looks like. If you're interested in those matters, please see the [README](../README.md) or the [SPECIFICATION](../SPECIFICATION.md) itself (both includes lots of examples).

## What is downson?

Downson is a new configuration file format that lets you embed typed and structured data into Markdown files. Simply put, it's a format, that puts additional semantics on top of the Markdown syntax, making it possible to store data in Markdown files in a well-defined, machine readable manner. This makes mixing documentation and data a breeze! But why would you use such a beast?

## Motivation

It is a widely accepted fact among software developers that code is read more often than it's written, thus we should aim for code that is easy to read by humans. Although configuration is not strictly code but data, we should apply the same thinking. However, we keep using machine-optimized formats for configuration even though we are not forced to do so. There are a plethora of competing formats, that share this property in common – they claim to be human, but they put the machine first.

Another crucial thing to understand is that even behind the simplest key-value pair, there can be considerations enough to fill up a whole day with meetings or thinking. Leaving these undocumented or separating the value from its documentation is a maintenance catastrophe waiting to happen. The thoughts behind a particular value are just as important as the value itself.

Instead of trying to use clever comment structures in existing configuration formats, I chose the exact opposite – make the documentation the first class citizen. Let's create a format that is more like an article written in some natural language and extract the data from it using a well-defined set of rules.

Markdown was a perfect fit for this idea, as it's a well-established format that is easy to read, write and render. Using Markdown, you can even create websites or PDFs out of your configuration! Crazy!

## Road to 1.0.0

Although the specification can be considered stable (of course it's open to new suggestions and recommendations), the ecosystem around downson is basically nonexistent. Currently, there's a single JavaScript library and CLI that implements the downson specification. I hope that through community contributions, a lot more languages and platforms can be covered.

Thus, before releasing the first officially stable (ie. 1.0.0) version of the specification, I'd like to wait for more real life and implementation experience.

## Acknowledgments

I'd like to take the opportunity to say thanks to everyone, who helped me creating downson. Thanks!

Attila Bagossy ([@battila7](https://github.com/battila7/))
