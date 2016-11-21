---
layout: post
title: "Easy localization management with Google spreadsheets"
date: 2016-11-19 16:50:18 +0000
comments: true
categories: tips tools swift localization
---

Last thursday I gave a talk at [NSLondon meetup][NSLondon] called 'Minimum Viable Tooling'. The topic was examples of approaching tooling and automation for small teams. I showed real examples of what we do at [Peak][Peak], where I currently work. One of the things people showed interest in after the talk was how we deal with localizations.

In this post I will replicate the simple solution from scratch, so you can follow along and get started with your own projects. And we will be using Swift for the scripting, just because we can!

<!-- more -->

# The minimal CMS

In the talk I showed what we currently use to automate generation of strings at [Peak][Peak]. The goal of the solution is twofold: The first is to remove manual manipulation of local strings files by developers. The second is to raise visibility of any translation across the company and enable easier collaboration with translators. This is generally achieved by using a CMS, either a built solution or your own. We'll build our own by leveraging Google spreadsheets as a database and web interface.

So we'll need two simple parts: One one side, the strings are hosted in a Google spreasheet. Then on the other side, we'll run a script locally or on our CI every time we want to update strings in the application. The script will load the spreasheet contents, and generate the appropiate `.strings` file contents.


# The spreadsheet

I've created an example spreadsheet [here][spreadsheet]. It contains rows with keys, and columns with the supported language keys in your app:

![spreadsheet1](/images/posts/spreadsheet-1.png)

Here you can take two approaches for your script; you can either use any Google spreadsheet library for a popular scripting language like Ruby or Python, or you can leverage yet another service on top of spreasheets: [Sheetsu][Sheetsu]. Using the service means you don't need any library because it converts the content to a JSON API that can be easily parsed with traditional code. We'll use this as it's the simplest solution. Note that we would need to pay to have more flexibility as the free plan only allows one spreadsheet without any additional sheets, but this is fine for our example.

## Sheetsu

Sign up to [Sheetsu][Sheetsu], and give them the spreadsheet you want to use. Once you do this, they'll automatically create an API endpoint:

![sheetsu1](/images/posts/sheetsu-1.png)

# The script

We'll need to setup Swift for scripting. Given that we don't use any libraries, we can develop the code in a playground, and when we're done we can move the file to our repository. Here's an example playground code:

```swift
import Foundation
import PlaygroundSupport

PlaygroundPage.current.needsIndefiniteExecution = true

typealias RawLocalizationData = [[String:String]]

struct Entry {
    var key: String
    var value: String
}

struct Localization {
    var languageKey: String
    var entries: [Entry]
}

extension Localization {
    func export() -> [String] {
        return entries.map { entry in
            let value = entry.value.characters.count > 0 ? entry.value : "NOT_TRANSLATED"
            return "\"\(entry.key)\" = \"\(value)\""
        }
    }
}

func findLanguages(in data: RawLocalizationData) -> [String] {
    guard let firstEntry = data.first else { return [] }
    assert(firstEntry["key"] != nil)
    let languages = firstEntry.keys.filter { $0 != "key" }
    return languages.map { $0 }
}

func parse(_ data: RawLocalizationData) -> [Localization] {
    let languages = findLanguages(in: data)
    var localizations = [String:Localization]()

    for entry in data {
        let key = entry["key"] ?? "__ERROR__NO_KEY__"
        let values = languages.map { (language: $0, entry: Entry(key: key, value: entry[$0]!)) }
        for value in values {
            var localization = localizations[value.language] ?? Localization(languageKey: value.language, entries: [])
            localization.entries.append(value.entry)
            localizations[value.language] = localization
        }
    }

    return localizations.values.map { $0 }
}

func write(_ localizations: [Localization]) {
    for localization in localizations {
        print(localization.export().joined(separator: "\n"))
    }
}

let spreadsheetURL = URL(string: "https://sheetsu.com/apis/v1.0/a96943d37c32", relativeTo: nil)!

let session = URLSession.shared
let task = session.dataTask(with: spreadsheetURL) { data, response, error in
    guard let data = data else { print("Error"); return }

    let json = try? JSONSerialization.jsonObject(with: data, options: []) as! RawLocalizationData
    let localizations = parse(json ?? [])
    write(localizations)

    PlaygroundPage.current.finishExecution()
}

task.resume()
```

The only thing worthy of note is using `PlaygroundSupport` to enable asynchrous execution. All the other code is straightforward use of `URLSesssion` to parse JSON. All that is left is converting the playground to a script. The script doesn't save contents to any file, because when running in playground we're constrained to the sandbox. When moving to a script we can generate the folders and files as appropiate for our application. We're not going to do all these finishing touches now, but it's very simple to continue from here.

It only took me 1 hours to setup, and considering I had to look up how to use `PlaygroundSupport`, I would say it's very quick to implement!

# Caveats

With this approach we can quickly build automation around localizations, and we can share the spreadsheet with our translators. This example spreadsheet does not contain strings with placeholders (for example: `"WELCOME_MESSAGE" = "Hello %@!"`). This will require handling validation, because the translators can - and will - make mistakes when writing their new strings. In a later post I will elaborate the required validation of strings with placeholders, and how we can reduce errors with human-readable tokens.

I hope this is useful to you.

[NSLondon]: http://www.meetup.com/NSLondon/
[Peak]: http://www.peak.net
[Sheetsu]: https://sheetsu.com
[spreadsheet]: https://docs.google.com/spreadsheets/d/1pduU_ZiMNjPmQVl5_dDjK2XcgacfJkARsA567ddOrGg/edit?usp=sharing
