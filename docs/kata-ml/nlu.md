---
id: kata-ml-nlu
title: NLU
prev: kata-ml-data-structure
next: kata-ml-method
---

## Definition

A NLU consists of following definitions:

- `type: string` - NLU Type
- `method : method` - method name
- `options : JsonObject` - Override NLU options
- `process : method | method[]` - Postprocess intent using methods

Example:

```yaml
nlus:
  genericNer:
    type: verstandTagger
    options:
      model: <id>
```

## Keyword NLU

```yaml
myNLU:
  type: keyword
  options:
    case: boolean # true for case sensitive
    exact: boolean # true to match only exact message
    default: string # default key if nothing match
    keywords: # <key> : [<match values>]
      <key>: string[]
```

Output:

```
string
```

Example:

```yaml
options:
  keywords:
    'American Thin Crust':
      - thin crust
      - american thin crust
      - american
    'Hawaiian':
      - hawai
      - hawaiian
```

## Regex NLU

```yaml
myRegex:
  type: regex
  options:
    regex: <regex as string>
    index: number # optional
```

or

```yaml
myRegex:
  type: regex
  options:
    regex: <regex as string>
    index:
      <key>: number
```

or

```yaml
myRegex:
  type: regex
  options:
    regex:
      <key>: <regex as string>
```

Example with index:

```yaml
nlus:
  nameNLU:
    type: regex
    options:
      regex: 'nama saya (.+)'
      index: 1

## usage in intent
intents:
  giveName:
    attributes:
      name:
        nlu: nameNLU
```

Example with multiple index:

```yaml
nlus:
  nameNLU:
    type: regex
    options:
      regex: 'nama (saya|kamu) (.+)'
      index:
        who: 1
        name: 2

## usage in intent
intents:
  giveName:
    attributes:
      name:
        nlu: nameNLU
        path: who
      who:
        nlu: nameNLU
        path: name
```

Example with multiple regex:

```yaml
nlus:
  regexNlu:
    type: regex
    options:
      regex:
        name: 'nama saya (.+)'
        email: 'w+@w+.w+'

## usage in intent
intents:
  giveName:
    attributes:
      name:
        nlu: regexNlu
        path: name
      who:
        nlu: regexNLU
        path: email
```

## Verstand Tagger NLU

```yaml
myNLU:
  type: verstandTagger
  options:
    model: string # verstand model id
    labels: boolean # for use as classifier
```

Output:

```
# labels=false
{ [entity:string] : string }
# labels=true
string[]
```

Example:

```
# labels=false
{
    brand: "simpati",
    brand2: "xl",
    nominal: "5000k"
}
# labels=true
["brand", "brand2", "nominal"]
```

## Verstand Classifier NLU

```yaml
myNLU:
  type: verstandClassifier
  options:
    model: string # verstand model id
    threshold: number # match threshold
    asAttribute: boolean # for use as attribute (path = score/label)
```

Output:

```
# asAttribute=false
string[]
# asAttribute=true
{label:string, score:number}
```

Example:

```
# asAttribute=false
["order_gojek"]
# asAttribute=true
{label:"order_gojek", score:0.75}
```

## Verstand Retrieval NLU

```yaml
myNLU:
  type: verstandRetrieval
  options:
    model: string # verstand model id
```

Output:

```
{score:number, answer:string}[]
```

Example:

```
[
    {score: 0.8, answer:"Simpati adalah salah satu brand Telkomsel",
    {score: 0.3, answer:"Anda bisa membeli kartu simpati di graPARI terdekat"}
]
```

## Wit Intent NLU

```yaml
myNLU:
  type: witIntent
  options:
    apitoken: string # wit api token
    threshold: number # match threshold
```

Output:

```
string []
```

Example:

```
["order", "order_pulsa"]
```

## Wit Entities NLU

```yaml
myNLU:
  type: witEntity
  options:
    apitoken: string # wit api token
    threshold: number # match threshold
```

Output:

```
{ [entity: string] : string | number}
```

Example:

```
{
    brand: "simpati",
    conf_brand: 0.8,
    brand2: "xl",
    conf_brand2: 0.7,
    nominal: "5000k",
    conf_nominal: 0.9
}
```

## Custom NLU

You can create custom NLU using method.

Example:

```yaml
nlus:
    custom:
        type: method
        method: customNLU

methods:
    customNLU(msg,opt,cfg): "
        return msg.content == opt.match
    "
```
