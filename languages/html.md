### Barrel Development Best Practices

# HTML Best Practices

## Semantics and Readability
Be as semantic as possible when structuring the markup of a page. Semantic markup means that tags are chosen based on the type of content and their hierarchy. These are documents and as such should read like a document when the presentation layer is stripped away. Semantic markup also makes it easier to implment accessibility standards when required. 

## Understanding the HTML Specification
Understand that the HTML specification evolves. Use valid html5 elements, referring to the [HTML5 spec](https://www.w3.org/TR/html52/) provided by the W3C and ensuring broadest [browser compatibility](caniuse.com/) frequently. 

## Validate it!
Be sure to run all processed and final html through a validator to ensure clean and valid html5 markup. Use the [w3c validator](http://validator.w3.org/) to check your output either by direct copy and paste input (for local development) or by passing the url and any httpauth information.

## Attributes
When there are many attributes on a single html element, it can be hard to read. To make it easier to digest, try sticking with the following conventions:
1. Keep the first attribute inline with the tag open.
2. Keep consistent tab or space indentation
3. Keep the right closing carat of the opening element tag inline with the last attribute.