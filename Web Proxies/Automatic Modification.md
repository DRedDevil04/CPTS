
## Automatic Request Modification

#### Burp Match and Replace

We can go to (`Proxy>Options>Match and Replace`) and click on `Add` in Burp. As the below screenshot shows, we will set the following options:


|   |   |
|---|---|
|`Type`: `Request header`|Since the change we want to make will be in the request header and not in its body.|
|`Match`: `^User-Agent.*$`|The regex pattern that matches the entire line with `User-Agent` in it.|
|`Replace`: `User-Agent: HackTheBox Agent 1.0`|This is the value that will replace the line we matched above.|
|`Regex match`: True|We don't know the exact User-Agent string we want to replace, so we'll use regex to match any value that matches the pattern we specified above.|

#### ZAP Replacer

ZAP has a similar feature called `Replacer`, which we can access by pressing [`CTRL+R`] or clicking on `Replacer` in ZAP's options menu. It is fairly similar to what we did above, so we can click on `Add` and add the same options we user earlier:

ZAP also has the `Request Header String` that we can use with a Regex pattern. `Try using this option with the same values we used for Burp to see how it works.`


ZAP also provides the option to set the `Initiators`, which we can access by clicking on the other tab in the windows shown above. Initiators enable us to select where our `Replacer` option will be applied. We will keep the default option of `Apply to all HTTP(S) messages` to apply everywhere.

We can now enable request interception by pressing [`CTRL+B`]

---
## Automatic Response Modification

Let us go back to (`Proxy>Options>Match and Replace`) in Burp to add another rule. This time we will use the type of `Response body` since the change we want to make exists in the response's body and not in its headers. In this case, we do not have to use regex as we know the exact string we want to replace, though it is possible to use regex to do the same thing if we prefer.

- `Type`: `Response body`.
- `Match`: `type="number"`.
- `Replace`: `type="text"`.
- `Regex match`: False.

We can now click on `Ping`, and our command injection should work without intercepting and modifying the request.

