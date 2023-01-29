# Translations    [![Badge Crowdin]][Crowdin]

Thanks for taking an interest in bringing these writings to a wider audience.

## Introduction

Within the `articles` folder you'll find the English writings, while translated articles are found in the `translations` subdirectory. To start localising, please go to [Crowdin], choose a language and an article; and then let Crowdin guide you through the process.

Apart from any article, `articles/_index.md`, `resources/strings.toml` and `resources/generations.yaml` need to be translated as well.

When you finish translating a document, please open an issue here so I can review it and deploy it. Feel free to ask me for help if you need.

## Guidelines

Translating can get messy, so these are the guidelines to help keep the content consistent:

- Common tech terminology is tricky to translate, sometimes it can be translated, sometimes not. If you doubt, just leave it untranslated.
- Some unique names may seem 'translatable' (i.e. the 'Scalar Unit' of the Nintendo 64) however it mustn't be translated, since it's a term used to identify that component and no other.
- There may be sentences constructed in a way that only in English they will sound 'natural', feel free to re-construct them if you find a better form in your language.

I hope I don't sound bossy with this, I'm actually very grateful to anyone that helps me improve the site!

Finally, don't forget to sign the [authors log] with your name and, again, feel free to give me a shout if you need help.

## Keeping up-to-date

It's not very often, but I may add some new content to an existing article that has been translated already. In this case, I can only maintain English, Spanish and Catalan translations. For the rest of languages, crowdin can notify previous translators whenever there's new changes detected.

As a rule of thumb, please check beforehand that the article you are interested in translating doesn't have many issues logged, this way you'll see which ones are meant to stay as they are.

Thanks!

<!----------------------------------------------------------------------------->

[Badge Crowdin]: https://badges.crowdin.net/architecture-of-consoles/localized.svg
[Crowdin]: https://crowdin.com/project/architecture-of-consoles
[Hugo]: https://gohugo.io/templates/introduction/

[authors log]: resources/translation_authors.yaml
