### Barrel Development Best Practices

# SEO Best Practices
## What is the reason for search engine optimization?
Google accounts for about 65% of search market. 93% of visits to any site originate with a search. 75% of users do not go beyond the first page of search results. If the majority of visitors of any website start with a search, use google, and do not go beyond the first page of results, it's a website owners prerogative to make certain that their site is as optimized for those conditions as possible.

## Style-agnostic Tags
Start with semantic, meaningful HTML to ensure content is organized in a logical and hierarchical manner. This takes planning, communication, and improvisation. Use style-agnostic tags and typographic classes to apply visual hierarchy; that is, do not rely on simply styling a tag without a class or id attached to it. This doesn't mean you can't have a basic style for tags, but it means that you should have override classes that can immediately change the appearance without having negative cascading effects.

```
h1, h2, h3, h4, h5, h6 {
  /* browser resets go here */
}
h1 {
  font-weight: 500;
}
/* don't stop there^ */
.h1 {
  font-weight: 900;
}
.h2 {
  font-weight: 600;
}
```

## Basic Guidelines
### Meta Fields
Ensure there is a method to populate unique title and meta fields for any page. When `<meta/>` tags are the same across multiple pages, crawlers interpret this as duplicate content.

### Redirects
Ensure redirects for sub-directories without trailing slashes; that is, if a url does not end in a trailing slash (**/path**), a server-side redirect when possible should redirect the visitor to the same path with a trailing slash (**/path/**). This is because both paths could be treated as duplicate content without the redirect.

### Heading Tags
There are not hard and fast rules. They are guidelines. Be flexible.

- One H1 per [sectioning content](https://www.w3.org/TR/2010/WD-html5-20101019/content-models.html#sectioning-content) (`<section>`, `<article>`, `<nav>`, `<aside>`).
- Do not skip the next size in heading; i.e. do not place an `<h3>` if there is no preceding `<h2>`.
- Do not put lower heading tags above a higher heading tag.
- Home pages should implement a hidden H1 with the client’s brand name or site title. This is not the logo. Additionally, for scenarios with flexible content where the user may reorder blocks of content, ensure that there is a definitive `<h1>` on pages where this is not defined.

### Lists and Links
- Use unordered lists for collections.
- Links always need text, even if exclusively used with a styled user interface content.

### Image Lazy/Deferred Loading
Images can implement lazy load or deferred load as well as background images provided an img tag is available.

### Structured Data
- Structured data provides explicit clues about the meaning of a page to Google. Their essential for a website to provide informations to the customer and perform in the search results.
- Google recommends using JSON-LD for structured data whenever possible. Below an example:
```
<script type="application/ld+json">
	{
	  "@context": "http://schema.org",
	  "@type": "Organization",
	  "url": "http://www.example.com",
	  "name": "Unlimited Ball Bearings Corp.",
	  "contactPoint": {
	    "@type": "ContactPoint",
	    "telephone": "+1-401-555-1212",
	    "contactType": "Customer service"
	  }
	}
</script>
```
- Make sure that every important pages have their own JSON-L for structured data. (i.e. Homepage, Product pages, Collection pages, Article pages, ...).
- The [Google structured data Testing Tool](https://search.google.com/structured-data/testing-tool) is an easy and useful tool for validating structured data, and in some cases, previewing a feature in Google Search.

### Sitemap
Google is going to use what you submit in your XML sitemap as a clue to what's probably important on your site and should be indexed. Make sure to include for any Wordpress projects (Shopify handles generates it [automatically](https://help.shopify.com/en/manual/promoting-marketing/seo/find-site-map)).

### Hreflang tags
```
<link rel="alternate" href="https://www.example.fr" hreflang="fr-FR" />
<link rel="alternate" href="https://www.example.com" hreflang="en-US" />
<link rel="alternate" href="https://global.example.com" hreflang="x-default" />
```
- The `Hreflang` tags tell Google which language you are using on a specific page, so the search engine can serve that result to users searching in that language.
- The default `Hreflang` tag should default to the URL google should refer to when the website doesn’t have a specific URL for a specific language.

### No Index
```
<meta name="robots" content="noindex" />
```
Use the `noindex` meta tag to prevent Google to crawl any pages that shouldn't be indexed. (i.e. Thank You pages after email sign up, Landing Pages for Retargeting, ...).

### No Follow
```
<a href="http://www.example.com/" rel="nofollow">Anchor Text</a>
```
The `nofollow` rel attribute should be added to any external link that shouldn't be followed by Google and would potentially impact negatively the ranking of the page. A crawling tool should be used to validate the credibility of the link.

## Tools

- [browseo](http://www.browseo.net/)
- [site-analyzer](https://www.site-analyzer.com/)
- [raventools](https://raven-seo-tools.com/tools/m/login)
- [screamingfrog](https://www.screamingfrog.co.uk/seo-spider/)
