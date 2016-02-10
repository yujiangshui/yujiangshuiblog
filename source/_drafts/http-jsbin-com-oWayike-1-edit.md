title: "http://jsbin.com/oWayike/1/edit"
id: 103
categories:
  - Uncategorized
tags:
---

You can easily get the current element that is 'selected' by using a little-known DOM method called document.activeElement. Let's see this in action.

Suppose you have an HTML form with three form fields and a submit button. We'll also add a generic 'output' element to the page, to display the results. In addition, I've added unique data-* attribute values to each element, so we can use that for the output. I've also set up a few random box elements with tabindex attributes, so they too can be 'selected' when clicked.

Here's our JavaScript:

var doc = document,
output = doc.getElementById('op'),
activeEl;

doc.addEventListener('click', function () {
activeEl = doc.activeElement;
output.innerHTML = activeEl.getAttribute('data-input');
}, false);

And here's a demo on JS Bin. Click any element to see the active element's info displayed on the page.

Things to note:
MDN explains that the 'active' element is the element that is focused and ready to receive text input. As you can see from my demo, any element can be 'active', not just those with text input capabilities.
If you want to be able to track elements that receive focus by tabbing, you'd have to write a different script that doesn't use 'click' as the event.
Notice my demo includes two generic boxes and a heading that can also be clicked, which also display their data-* attribute values when 'active'.
If we don't use the data-* attributes, then we can output a string showing the element object itself, so the output might say something like [object HTMLInputElement].

MDN's article has its own demo to view, and you can also view the WHATWG spec on the subject.

According to a few sources, this feature works everywhere, even back to IE4! Not sure if all versions of IE have the same behaviour, but the feature is present everywhere.