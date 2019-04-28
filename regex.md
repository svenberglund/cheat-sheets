

# Table of Contents
1. [JavaScript REGEX examples](#js-regex-examples)


# JavaScript REGEX examples <a name="js-regex-examples" /> 


## Character matching
Javascript Regex to match alphanumeric charcters, hyphen and dash.

This regex should probably do the job of matching such a string:
```
var regex = new RegExp(/^[\w\-/]+$/);
```

Comments: Anchor the start and end via `^` and `$` respectively.
End the expression with `+` to indicate one or more occurances, if a zero lenght match is also ok use `*`.


## Trim chars
Trim all characters from string except whitelist (alphanumeric,_,-,/)


If we want to 'clean' a string we can use replace, hence we match characters one by one, skip the `+`\
(and for some reason the anchor `$` aswell )...

```
function reduceToWhiteList(inputString) {
	//return inputString.replace(/[^0-9a-zA-Z_/\-]/g, '')
	return inputString.replace(/[^\w/\-]/g, '')
}
```

Comments about the above:

`\w` is the same as `[A-Za-z0-9_]` (in javascript, not necessarily in other languages)\
`-` should be escaped as `\-` so that the engine can distinguish it from "interval hyphen" as in `A-Z`.






