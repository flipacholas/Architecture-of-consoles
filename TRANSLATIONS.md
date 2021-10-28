[![Crowdin](https://badges.crowdin.net/architecture-of-consoles/localized.svg)](https://crowdin.com/project/architecture-of-consoles)

### Current State

Right now, all the articles are in English and there's a few translated to Spanish.

English writings end with `.md`, while translated articles end with `.(language code).md` (i.e. `.es.md` for Spanish). To start localising, you can either:
- Sign in to [Crowdin](https://crowdin.com/project/architecture-of-consoles), choose a language and an article; and let Crowdin guide you through the process.
- Clone the repo, grab any article markdown file, translate it and then submit a merge request.

For any language except Spanish, the following files need to be translated as well:
- `_index.md`
- The list of strings on 'resources' folder.

When you finish translating a document, please drop me an email (address in the website) or open an issue here so I can review it and deploy it. The same applies if you have any questions.

I hope I don't sound bossy with this, I'm actually very grateful to anyone that helps me improve the site!

### Guidelines

Translating can get messy, so these are the guidelines to help keep the content consistent:

- Common tech terminology is tricky to translate, sometimes it can be translated, sometimes not. If you doubt, just leave it untranslated.
- Some unique names may seem 'translatable' (i.e. the 'Scalar Unit' of the Nintendo 64) however it mustn't be translated, since it's a term used to identify that component and no other.
- There may be sentences constructed in a way that only in English they will sound 'natural', feel free to re-construct them if you find a better form in your language.
- Blocks of [Hugo](https://gohugo.io/templates/introduction/) syntax only require `name=""` and `alt=""` values to be translated, the rest must stay as it is.

Finally, don't forget to sign the changelog with your name!

Feel free to give me a shout if you need help.

### Keeping up-to-date

It's not very often, but I may add some new content to an existing article that has been translated already. In this case, I can only maintain English, Spanish and Catalan translations. For the rest of languages, crowdin can notify previous translators whenever there's new changes detected.

As a rule of thumb, please check beforehand that the article you are interested in translating doesn't have any issue logged, this way you'll see which ones are meant to stay as they are.

Thanks!
