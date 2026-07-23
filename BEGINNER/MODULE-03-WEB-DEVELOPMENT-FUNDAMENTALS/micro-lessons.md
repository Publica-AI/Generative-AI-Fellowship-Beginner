# Module 3 - Web Development Fundamentals: Micro Lessons
> For use with AI practice question generation. Beginner level only.

---

## WEEK 4 — HTML & CSS

---

## Topic 1: Introduction to Web Development

**Title:** Introduction to Web Development

**Programming Language:** None

**Coding Exercise:** No

**Learning Objective:**
Students will understand how the web works — the client-server request-response cycle, the roles of HTML, CSS, and JavaScript, and how to set up VS Code and create their first HTML file.

**Description for AI:**
This topic introduces how the web works conceptually before students write any code. Students learn the client-server model: a browser (client) sends a request to a server, the server responds with HTML, CSS, and JavaScript files, and the browser renders them into a web page. The three technologies are introduced: HTML structures content, CSS styles it, JavaScript makes it interactive. Students also set up VS Code as their editor and create their first HTML file. Nigerian context: visiting a Nigerian e-commerce site like Jumia — the browser requests the page from Jumia's servers, and the server sends back files that the browser renders into what the user sees.

**Common Mistakes:**
- Confusing a web server with a web browser — the browser is the client that requests, the server is what responds
- Thinking CSS and JavaScript are part of HTML — they are separate technologies with separate files
- Not understanding that HTML, CSS, and JavaScript are all needed together for a complete web page

---

## Topic 2: HTML Document Structure

**Title:** HTML Document Structure

**Programming Language:** HTML

**Coding Exercise:** Yes

**Learning Objective:**
Students will write a correctly structured HTML document with DOCTYPE, html, head, and body elements, add meta tags and a title, write comments, and apply proper nesting and indentation.

**Description for AI:**
This topic covers the required structure of every HTML file. Students learn DOCTYPE (tells the browser this is HTML5), the html root element, head (metadata — not visible on page) and body (visible content) sections, meta charset and viewport tags, the title tag (shown in browser tab), HTML comments, and the rules of nesting (child elements must be fully inside parent elements) and indentation (2 or 4 spaces consistently). Nigerian context: building the skeleton of a Lagos startup website — the head contains the company name in the title tag.

**Common Mistakes:**
- Forgetting the DOCTYPE declaration at the top of the file
- Putting visible content inside the head instead of the body
- Incorrect nesting — closing a parent tag before closing child tags
- Inconsistent indentation making code hard to read

---

## Topic 3: Text & Content Elements

**Title:** Text & Content Elements

**Programming Language:** HTML

**Coding Exercise:** Yes

**Learning Objective:**
Students will structure text content using headings (h1–h6), paragraphs, line breaks, emphasis elements (strong, em), and ordered, unordered, and nested lists.

**Description for AI:**
This topic covers the HTML elements used to structure text. Headings h1 through h6 create a hierarchy — h1 is the main page title, only one per page. Paragraphs use the p tag. Line breaks use br (self-closing). Bold and italic are done semantically with strong and em rather than b and i. Lists: ul for unordered (bullet points), ol for ordered (numbered), li for each item, and nested lists by placing a ul or ol inside an li. Nigerian context: structuring a news article from Channels TV or a product listing from Konga using these elements.

**Common Mistakes:**
- Using multiple h1 tags on one page — only one h1 per page
- Using br tags for spacing instead of CSS margin/padding
- Using b and i instead of strong and em — the latter carry semantic meaning
- Not closing li tags properly in nested lists

---

## Topic 4: Links & Navigation

**Title:** Links & Navigation

**Programming Language:** HTML

**Coding Exercise:** Yes

**Learning Objective:**
Students will create internal and external links using anchor tags, open links in new tabs, and build a navigation menu linking to sections on the same page.

**Description for AI:**
This topic covers the anchor tag and its href attribute. External links point to other websites (full URL). Internal links point to sections on the same page using # and an id attribute on the target element. The target="_blank" attribute opens links in a new tab — add rel="noopener noreferrer" for security. Navigation menus use a nav element containing a ul with li items each containing an anchor. Nigerian context: building navigation for a Nigerian portfolio site that links to About, Skills, Projects, and Contact sections.

**Common Mistakes:**
- Forgetting the # prefix for internal anchor links
- Not adding rel="noopener noreferrer" with target="_blank"
- Wrapping block elements like headings inside anchor tags incorrectly
- Not giving target sections an id attribute for internal links to work

---

## Topic 5: Images & Media

**Title:** Images & Media

**Programming Language:** HTML

**Coding Exercise:** Yes

**Learning Objective:**
Students will add images, audio, video, and embedded content to web pages using the correct HTML elements and attributes including alt text for accessibility.

**Description for AI:**
This topic covers img (src, alt, width, height attributes), the difference between image formats (JPEG for photos, PNG for transparency, SVG for icons and logos), audio and video elements with controls, and embedding content using iframes (YouTube videos, Google Maps). The alt attribute is critical for accessibility — screen readers use it, and it appears when images fail to load. Nigerian context: adding a photo of the Lagos skyline to a portfolio, embedding a YouTube video from a Nigerian tech YouTube channel, or embedding a Google Maps view of an Abuja business location.

**Common Mistakes:**
- Leaving the alt attribute empty or missing entirely
- Using images that are too large — images should be optimised for the web
- Using absolute file paths that work on the developer's machine but break when deployed
- Forgetting the controls attribute on audio and video elements

---

## Topic 6: Semantic HTML

**Title:** Semantic HTML

**Programming Language:** HTML

**Coding Exercise:** Yes

**Learning Objective:**
Students will use semantic HTML5 elements — header, nav, main, section, article, aside, footer, figure, figcaption — correctly and explain why semantics matter for accessibility and SEO.

**Description for AI:**
Semantic HTML means using elements that describe the meaning of content, not just its appearance. header contains introductory content (logo, navigation). nav contains navigation links. main contains the primary content — one per page. section groups thematically related content with a heading. article is standalone content like a blog post. aside contains tangentially related content like a sidebar. footer contains closing information. div has no semantic meaning and should only be used for layout grouping when no semantic element fits. Nigerian context: marking up a Nigerian news website like Punch or Vanguard using the correct semantic structure.

**Common Mistakes:**
- Using div for everything instead of semantic elements
- Using multiple main elements on one page
- Putting nav outside of header when it belongs to the site header
- Using section without a heading inside it

---

## Topic 7: Forms & User Input

**Title:** Forms & User Input

**Programming Language:** HTML

**Coding Exercise:** Yes

**Learning Objective:**
Students will build HTML forms with input types, labels, buttons, and basic validation attributes — the foundation of every login page, contact form, and data collection interface.

**Description for AI:**
This topic covers the form element with action and method attributes, input types (text, email, password, number, date, checkbox, radio, file), the label element and its for attribute linking to input ids for accessibility, textarea, select dropdowns, submit buttons, and basic HTML validation attributes (required, minlength, pattern, type="email"). Students build a contact form for a Nigerian business. Nigerian context: a contact form for an Abuja consulting firm collecting name, email, phone number, and message.

**Common Mistakes:**
- Not linking labels to inputs using matching for and id attributes
- Forgetting the required attribute on mandatory fields
- Using input type="text" instead of type="email" or type="tel" — the browser validates specific types
- Not giving radio buttons and checkboxes the same name attribute for grouping

---

## Topic 8: Tables

**Title:** Tables

**Programming Language:** HTML

**Coding Exercise:** Yes

**Learning Objective:**
Students will structure tabular data using HTML table elements — table, tr, th, td — add captions, span rows and columns, and understand when tables are appropriate versus when CSS layout should be used instead.

**Description for AI:**
This topic covers table structure: table (container), thead, tbody, tfoot sections, tr (table row), th (table header cell — bold and centred by default), td (table data cell), caption (title above the table). colspan spans multiple columns, rowspan spans multiple rows. Tables are for tabular data only — never for page layout. Nigerian context: a pricing table for a Nigerian SaaS product showing plan names, features, and prices in Naira across columns.

**Common Mistakes:**
- Using tables for page layout instead of CSS Flexbox or Grid
- Forgetting thead and tbody sections — table still works but loses semantic structure
- Not using th for header cells — loses accessibility and semantic meaning
- Incorrect colspan or rowspan values causing cells to misalign

---

## Topic 9: Introduction to CSS

**Title:** Introduction to CSS

**Programming Language:** CSS

**Coding Exercise:** Yes

**Learning Objective:**
Students will understand what CSS does, link an external stylesheet to HTML, and write CSS rules using the selector, property, and value syntax.

**Description for AI:**
This topic introduces Cascading Style Sheets. CSS controls the visual presentation of HTML — colours, fonts, spacing, layout. Three ways to apply CSS: inline (style attribute on the element — avoid), internal (style tag in the head — for single pages), external (separate .css file linked with a link tag — the professional approach). CSS rule syntax: selector { property: value; }. Multiple declarations in one rule. The cascade — styles flow down from parent to child (inheritance). Nigerian context: applying a green and white colour scheme inspired by the Nigerian flag to a simple webpage.

**Common Mistakes:**
- Forgetting the semicolon at the end of each CSS declaration
- Using inline styles instead of external stylesheets
- Forgetting to link the CSS file to the HTML — the stylesheet has no effect if not linked
- Misspelling property names — CSS silently ignores unknown properties

---

## Topic 10: CSS Selectors

**Title:** CSS Selectors

**Programming Language:** CSS

**Coding Exercise:** Yes

**Learning Objective:**
Students will target HTML elements precisely using element, class, ID, grouping, descendant, and child selectors, and add interactive states using pseudo-classes.

**Description for AI:**
This topic covers selector types: element selector (targets all elements of a type), class selector (.classname — reusable, multiple elements), ID selector (#idname — unique, one element per page), grouping (comma-separated selectors share rules), descendant selector (space — targets elements anywhere inside a parent), child selector (> — targets only direct children), and pseudo-classes (:hover, :focus, :first-child, :nth-child). Specificity: ID > class > element. Nigerian context: styling a Nigerian e-commerce product card — class selector for all cards, ID selector for a featured card, hover state for the add-to-cart button.

**Common Mistakes:**
- Using IDs for styling when classes are more appropriate — IDs should be used sparingly
- Forgetting the dot prefix for class selectors or the hash for ID selectors
- Overusing descendant selectors creating overly specific rules that are hard to override
- Not understanding specificity — wondering why a style is not applying when a more specific rule overrides it

---

## Topic 11: Colors & Typography

**Title:** Colors & Typography

**Programming Language:** CSS

**Coding Exercise:** Yes

**Learning Objective:**
Students will apply colours using hex, RGB, and HSL values and control typography using font-family, Google Fonts, font-size, font-weight, line-height, and text-alignment.

**Description for AI:**
This topic covers CSS colour values: named colours (red, blue), hex codes (#387F7F), RGB (rgb(56, 127, 127)), HSL (hsl(180, 39%, 36%)). CSS custom properties (variables) for reusable colours: --primary-color: #387F7F. Typography: font-family with fallback stacks, importing Google Fonts with a link tag, font-size in pixels and rem units, font-weight (400 normal, 700 bold), font-style (italic), line-height for readable body text (1.5–1.6), letter-spacing, text-align, text-transform. Nigerian context: choosing a brand colour palette and typography for a Nigerian fintech startup website.

**Common Mistakes:**
- Not providing font fallbacks in font-family declarations
- Using pixels for font sizes instead of rem — rem respects browser accessibility settings
- Forgetting to import Google Fonts before using them in font-family
- Setting line-height too tight (below 1.4) making body text hard to read

---

## Topic 12: The Box Model

**Title:** The Box Model

**Programming Language:** CSS

**Coding Exercise:** Yes

**Learning Objective:**
Students will understand the CSS box model — content, padding, border, margin — apply box-sizing: border-box, and control width, height, and spacing using these properties.

**Description for AI:**
Every HTML element is a rectangular box with four layers: content (the actual text or image), padding (space between content and border), border (line around the element), margin (space outside the border separating elements). Box-sizing: content-box (default — width does not include padding and border, confusing), border-box (width includes padding and border — always use this). Margin collapsing: vertical margins between adjacent elements collapse to the larger value. Shorthand: margin: top right bottom left (clockwise), margin: vertical horizontal. Nigerian context: spacing the cards on a Nigerian bank's service offerings page.

**Common Mistakes:**
- Not applying box-sizing: border-box — causes width calculations to behave unexpectedly
- Confusing padding (inside the border) with margin (outside the border)
- Not understanding margin collapsing — surprised when vertical margins do not add up
- Using negative margins without understanding their effect on layout

---

## Topic 13: Display & Positioning

**Title:** Display & Positioning

**Programming Language:** CSS

**Coding Exercise:** Yes

**Learning Objective:**
Students will control element layout using display types and the position property — static, relative, absolute, fixed, and sticky — and centre elements reliably using CSS.

**Description for AI:**
Display types: block (full width, new line), inline (only as wide as content, no width/height), inline-block (inline but accepts width/height), none (hidden). Position values: static (default, normal flow), relative (offset from normal position), absolute (removed from flow, positioned relative to nearest positioned ancestor), fixed (relative to viewport — stays on screen when scrolling), sticky (relative until scroll threshold then fixed). Z-index controls stacking order. Centering: margin: 0 auto for block elements, flexbox for vertical centering. Nigerian context: positioning a fixed navigation bar that stays visible as users scroll a long Nigerian news article.

**Common Mistakes:**
- Using position: absolute without a positioned parent — element positions relative to the page unexpectedly
- Forgetting that position: absolute removes the element from the normal document flow
- Not setting a z-index when elements overlap unexpectedly
- Trying to use margin: auto for vertical centering — only works horizontally for block elements

---

## Topic 14: Flexbox Layout

**Title:** Flexbox Layout

**Programming Language:** CSS

**Coding Exercise:** Yes

**Learning Objective:**
Students will build one-dimensional flexible layouts using Flexbox — controlling alignment, direction, spacing, and sizing of elements along a single axis.

**Description for AI:**
Flexbox is applied by setting display: flex on a container. Main axis (default: horizontal) and cross axis (default: vertical). Key properties on the flex container: flex-direction (row, column), justify-content (main axis alignment: flex-start, center, space-between, space-around, space-evenly), align-items (cross axis alignment: flex-start, center, stretch), flex-wrap (allows items to wrap to new lines). Key properties on flex items: flex-grow (how much to grow), flex-shrink (how much to shrink), flex-basis (initial size), align-self (override container alignment for one item). Nigerian context: building a horizontal navigation bar and a row of product cards for a Konga-style e-commerce page.

**Common Mistakes:**
- Applying flex properties to flex items instead of the flex container (and vice versa)
- Confusing justify-content (main axis) with align-items (cross axis)
- Forgetting flex-wrap: wrap — items overflow the container instead of wrapping
- Not understanding that flex-direction: column switches which axis justify-content affects

---

## Topic 15: CSS Grid Layout

**Title:** CSS Grid Layout

**Programming Language:** CSS

**Coding Exercise:** Yes

**Learning Objective:**
Students will build two-dimensional layouts using CSS Grid — defining rows and columns, placing items in grid areas, controlling gaps, and knowing when to use Grid versus Flexbox.

**Description for AI:**
CSS Grid is for two-dimensional layout (rows AND columns simultaneously). Applied with display: grid. grid-template-columns defines column widths (px, %, fr units, repeat()). grid-template-rows defines row heights. gap controls space between cells. grid-template-areas creates named layout zones. Items can span multiple cells using grid-column and grid-row with span. Flexbox vs Grid: use Flexbox for one-dimensional layout (a row of items or a column of items), use Grid for two-dimensional layout (rows AND columns together, like a page layout or a card grid). Nigerian context: building a dashboard layout for a Nigerian fintech app with a sidebar, header, main content area, and footer using grid-template-areas.

**Common Mistakes:**
- Using Grid for simple one-dimensional layouts where Flexbox is more appropriate
- Forgetting fr units — using fixed pixel widths that break on different screen sizes
- Not understanding that grid-gap is now just gap (the old property still works but gap is current)
- Misaligning grid areas in grid-template-areas — each row must have the same number of columns

---

## Topic 16: Responsive Design

**Title:** Responsive Design

**Programming Language:** CSS

**Coding Exercise:** Yes

**Learning Objective:**
Students will make web pages fully responsive using the viewport meta tag, media queries, a mobile-first approach, responsive images, and a breakpoint strategy.

**Description for AI:**
Responsive design makes web pages work on all screen sizes. The viewport meta tag (name="viewport" content="width=device-width, initial-scale=1.0") must be in every HTML head — without it, mobile browsers zoom out to show the desktop version. Media queries (@media (max-width: 768px) { }) apply styles only when conditions are met. Mobile-first approach: write styles for mobile first, then override for larger screens with min-width queries. Common breakpoints: 375px (mobile), 768px (tablet), 1024px (desktop). Responsive images: max-width: 100%, width: 100%. Nigerian context: making a Nigerian business website usable on mobile — in Nigeria the majority of web traffic is from mobile devices.

**Common Mistakes:**
- Forgetting the viewport meta tag — the most common cause of mobile layout issues
- Writing desktop-first CSS and overriding for mobile — harder to maintain than mobile-first
- Using fixed pixel widths that overflow on small screens — use percentages, max-width, or fr units instead
- Not testing on actual mobile screen sizes — use Chrome DevTools device simulator

---

## WEEK 5 — JavaScript Part 1

---

## Topic 1 (Week 5): Introduction to the Web & JavaScript

**Title:** Introduction to the Web & JavaScript

**Programming Language:** JavaScript

**Coding Exercise:** Yes

**Learning Objective:**
Students will understand where JavaScript fits in the web stack, add a script tag to an HTML file, write their first JavaScript, and use the browser console to inspect output and debug.

**Description for AI:**
JavaScript is the programming language of the web — it runs in the browser and makes pages interactive. HTML structures content, CSS styles it, JavaScript controls behaviour. Adding JavaScript: external file linked with script src at the bottom of body, or inline script tag. console.log outputs to the browser developer console. The browser console (F12 or Cmd+Option+I) shows output, errors, and allows running JavaScript directly. Nigerian context: adding a greeting script to a Lagos developer portfolio — console.log("Welcome to Chidi's portfolio — Lagos, Nigeria").

**Common Mistakes:**
- Placing the script tag in the head without defer attribute — JavaScript runs before HTML is loaded
- Confusing the browser console with the terminal — they are different environments
- Not opening the browser console to see console.log output — the page looks blank but there is output
- Using var instead of let or const — var has function scope not block scope

---

## Topic 2 (Week 5): Variables, Data Types & Operators

**Title:** Variables, Data Types & Operators

**Programming Language:** JavaScript

**Coding Exercise:** Yes

**Learning Objective:**
Students will declare variables using let and const, work with primitive data types, and use arithmetic, comparison, and logical operators to write expressions.

**Description for AI:**
Variables declared with const (value cannot be reassigned — use by default), let (value can change), never var (outdated). Five primitive types: string ("hello"), number (42, 3.14), boolean (true/false), null (intentionally empty), undefined (declared but not assigned). Type conversion: Number("42") converts string to number, String(42) converts number to string. Operators: arithmetic (+, -, *, /, %, **), comparison (===, !==, >, <, >=, <= — always use === not ==), logical (&&, ||, !). Operator precedence follows BODMAS. Nigerian context: calculating a Naira amount after applying 7.5% VAT — const vatAmount = price * 0.075.

**Common Mistakes:**
- Using == instead of === — loose equality causes unexpected type coercion bugs
- Trying to reassign a const variable — use let when the value needs to change
- Confusing null (intentional absence) and undefined (not yet assigned)
- Not understanding that + with strings concatenates instead of adding — "10" + 5 = "105"

---

## Topic 3 (Week 5): Strings & String Methods

**Title:** Strings & String Methods

**Programming Language:** JavaScript

**Coding Exercise:** Yes

**Learning Objective:**
Students will create strings using quotes and template literals, and use the most important built-in string methods to clean, format, search, and transform text.

**Description for AI:**
Strings can use single quotes, double quotes, or backticks (template literals). Template literals use ${} to embed expressions: `Hello ${name}`. Key methods: trim() removes whitespace, toUpperCase()/toLowerCase() change case, replace(old, new) substitutes text, slice(start, end) extracts a portion, split(delimiter) turns a string into an array, includes(substring) returns boolean, indexOf(substring) returns position (-1 if not found), startsWith() and endsWith() check the beginning/end. String.length gives the character count. Strings are immutable — methods return new strings, they do not modify the original. Nigerian context: cleaning and formatting user input from a Paystack payment form — trimming whitespace from a name field, validating an email address contains @.

**Common Mistakes:**
- Expecting string methods to modify the original string — they always return a new string
- Using indexOf to check if a substring exists — use includes() which returns a boolean directly
- Forgetting that string indices start at 0 not 1
- Not using template literals for string interpolation — using + concatenation instead is harder to read

---

## Topic 4 (Week 5): Debugging & Developer Tools

**Title:** Debugging & Developer Tools

**Programming Language:** JavaScript

**Coding Exercise:** Yes

**Learning Objective:**
Students will use Chrome DevTools to debug JavaScript — reading error messages, using console methods, setting breakpoints, and stepping through code execution.

**Description for AI:**
Chrome DevTools (F12) is the primary debugging environment. Console methods: console.log (general output), console.warn (yellow warning), console.error (red error), console.table (display arrays/objects as table). Error types: SyntaxError (invalid code structure — caught before running), ReferenceError (variable not defined), TypeError (wrong data type operation). Error messages include the type, message, and line number. Breakpoints: click a line number in Sources tab to pause execution there. Step over (F10) runs the next line, step into (F11) goes inside a function call. Inspect variable values in the scope panel while paused. Nigerian context: debugging a broken form validation script for a Nigerian fintech signup page.

**Common Mistakes:**
- Ignoring the line number in the error message — always start debugging from the exact line reported
- Not using breakpoints — relying entirely on console.log to debug complex issues
- Confusing SyntaxError (code cannot run at all) with ReferenceError (runs but crashes at a specific line)
- Closing DevTools when an error appears instead of reading the message

---

## Topic 5 (Week 5): Control Flow — Conditionals

**Title:** Control Flow — Conditionals

**Programming Language:** JavaScript

**Coding Exercise:** Yes

**Learning Objective:**
Students will write if, else if, else, and switch statements to make decisions in code, use the ternary operator, and understand truthy and falsy values.

**Description for AI:**
Conditionals execute different code based on conditions. if/else if/else chain tests conditions in order and runs the first true block. switch matches a value against multiple fixed cases — more readable than long if/else chains for discrete values. Ternary operator: condition ? valueIfTrue : valueIfFalse — for simple two-option decisions. Truthy values: anything not falsy. Falsy values: false, 0, "" (empty string), null, undefined, NaN. Strict equality (===) checks value AND type. Nigerian context: a shipping fee calculator for a Jumia order — free shipping if order is above ₦10,000, ₦500 if above ₦5,000, ₦1,500 otherwise.

**Common Mistakes:**
- Using a single = (assignment) inside an if condition instead of === (comparison)
- Forgetting break statements in switch cases — execution falls through to the next case
- Not understanding that 0, empty string, and null are all falsy — causes unexpected conditional behaviour
- Over-nesting if statements instead of using else if or switch

---

## Topic 6 (Week 5): Control Flow — Loops

**Title:** Control Flow — Loops

**Programming Language:** JavaScript

**Coding Exercise:** Yes

**Learning Objective:**
Students will repeat operations using for, while, do-while, for-of, and for-in loops, and control execution with break and continue.

**Description for AI:**
Loops repeat code. for loop: for (let i = 0; i < 10; i++) — initialisation, condition, update. while loop: runs while condition is true — use when iterations are unknown. do-while: runs at least once before checking. for-of: iterates over array values cleanly — for (const item of items). for-in: iterates over object keys — for (const key in object). break exits the loop immediately. continue skips to the next iteration. Infinite loops occur when the condition never becomes false — crash the browser. Nigerian context: looping through a list of Nigerian states to display them in a dropdown, or looping through Paystack transaction records to calculate total revenue.

**Common Mistakes:**
- Off-by-one errors — using < vs <= incorrectly in for loop conditions
- Creating infinite while loops by forgetting to update the condition variable
- Using for-in on arrays — it iterates indices not values, and may include prototype properties
- Forgetting that break exits the entire loop while continue only skips one iteration

---

## Topic 7 (Week 5): Functions

**Title:** Functions

**Programming Language:** JavaScript

**Coding Exercise:** Yes

**Learning Objective:**
Students will define and invoke functions using function declarations and arrow functions, use parameters and return values effectively, and understand function scope.

**Description for AI:**
Functions are reusable blocks of code. Function declaration: function name(params) { return value; }. Arrow function: const name = (params) => value. Parameters are local variables receiving arguments. Return sends a value back to the caller — without return, the function returns undefined. Default parameters: function greet(name = "Guest"). Function scope: variables declared inside a function are not accessible outside (local scope). Arrow functions are preferred in modern JavaScript. DRY principle: Do Not Repeat Yourself — extract repeated code into functions. Nigerian context: a function to calculate Naira to USD conversion, a function to format a price with the ₦ symbol.

**Common Mistakes:**
- Forgetting the return keyword — the function runs but gives undefined back
- Confusing function declaration (hoisted) with function expression (not hoisted) — declaration can be called before definition
- Not understanding that parameters are local copies — modifying a primitive parameter does not change the original
- Using the same variable name inside and outside a function and being surprised they are different values

---

## Topic 8 (Week 5): Arrays

**Title:** Arrays

**Programming Language:** JavaScript

**Coding Exercise:** Yes

**Learning Objective:**
Students will create and manipulate arrays using indexing and the most common built-in methods, and iterate arrays using for, for-of, and forEach.

**Description for AI:**
Arrays are ordered collections. Create with square brackets: const items = [1, 2, 3]. Access by index (zero-based): items[0]. length property. Mutating methods: push (add to end), pop (remove from end), shift (remove from start), unshift (add to start), splice (add/remove anywhere). Non-mutating: slice (copy a portion), concat (merge arrays). indexOf finds the position of an element. includes returns boolean. Iterating: for loop with index, for-of for values, forEach(callback) for side effects. Arrays can hold mixed types. Nigerian context: an array of Nigerian states, an array of Flutterwave transaction objects, filtering transactions above a threshold amount.

**Common Mistakes:**
- Accessing an index that does not exist — returns undefined, not an error
- Confusing splice (mutates original) with slice (returns new array)
- Using for-in on arrays — only use for-in on objects
- Forgetting that array methods like push return the new length, not the array

---

## Topic 9 (Week 5): Objects

**Title:** Objects

**Programming Language:** JavaScript

**Coding Exercise:** Yes

**Learning Objective:**
Students will create objects with properties and methods, access and modify properties using dot and bracket notation, and iterate over objects using for-in and Object.keys.

**Description for AI:**
Objects store key-value pairs. Create with curly braces: const user = { name: "Amara", city: "Lagos" }. Access with dot notation: user.name. Bracket notation for dynamic keys: user["name"] or user[variable]. Add new properties: user.email = "amara@email.com". Delete properties: delete user.email. Methods are functions stored as object properties: user.greet = function() { return "Hello " + this.name; }. this refers to the object the method belongs to. Iterate with for-in (gets keys) or Object.keys(obj) which returns an array of keys. Object.values(obj) for values, Object.entries(obj) for key-value pairs. Nigerian context: a customer object for a Nigerian bank with name, account number, balance, and a method to check if the balance is sufficient for a transfer.

**Common Mistakes:**
- Using dot notation with property names that contain spaces or hyphens — must use bracket notation
- Confusing this in methods — this refers to the object, not the function itself
- Modifying properties on objects passed as function arguments — objects are passed by reference
- Not understanding that Object.keys returns an array of strings even if the keys look like numbers

---

## Topic 10 (Week 5): The Document Object Model (DOM)

**Title:** The Document Object Model (DOM)

**Programming Language:** JavaScript

**Coding Exercise:** Yes

**Learning Objective:**
Students will select DOM elements using getElementById, querySelector, and querySelectorAll, and modify element content, attributes, and CSS classes dynamically.

**Description for AI:**
The DOM is a live tree of objects representing the HTML document. JavaScript can read and change any element. Selecting elements: getElementById (by id), querySelector (first match of any CSS selector), querySelectorAll (all matches — returns NodeList). Modifying content: textContent (text only), innerHTML (HTML string — use carefully). Modifying attributes: setAttribute, getAttribute, removeAttribute. Modifying CSS classes: classList.add, classList.remove, classList.toggle, classList.contains. Modifying inline styles: element.style.property = "value". Creating elements: document.createElement, appendChild, insertBefore. Nigerian context: dynamically updating a product price display when a user selects a different Naira amount from a dropdown on a Nigerian e-commerce site.

**Common Mistakes:**
- Selecting an element before the DOM is loaded — script tag must be at bottom of body or use DOMContentLoaded
- Using innerHTML with untrusted user input — creates XSS security vulnerability, use textContent instead
- Forgetting that querySelectorAll returns a NodeList not an array — must convert with Array.from() to use array methods
- Not understanding that classList.toggle adds if absent and removes if present

---

## Topic 11 (Week 5): DOM Events & Interactivity

**Title:** DOM Events & Interactivity

**Programming Language:** JavaScript

**Coding Exercise:** Yes

**Learning Objective:**
Students will attach event listeners to DOM elements, handle common events using the event object, and prevent default browser behaviour — making web pages interactive.

**Description for AI:**
Events are user actions or browser triggers. addEventListener(type, callback) attaches a handler. Common events: click, submit, input, change, keydown, keyup, mouseover, load, DOMContentLoaded. The event object passed to the callback contains: event.target (the element that triggered the event), event.type, event.key (for keyboard events), event.preventDefault() (stops default behaviour like form submission or link navigation). Event bubbling: events propagate up from child to parent — stopPropagation() stops this. Event delegation: attach one listener to a parent to handle events from many children efficiently. Nigerian context: a contact form for a Lagos business that validates fields on submit and shows a success message without reloading the page.

**Common Mistakes:**
- Forgetting event.preventDefault() on form submit — the page reloads and you lose the input
- Not using event.target when the same handler handles multiple elements
- Adding event listeners inside a loop — creates multiple handlers on each element
- Confusing onclick attribute (HTML) with addEventListener (JavaScript) — use addEventListener for separation of concerns

---

## WEEK 6 — JavaScript Part 2 (Topics 1–7)

---

## Topic 1 (Week 6): Array Methods — Higher-Order Functions

**Title:** Array Methods — Higher-Order Functions

**Programming Language:** JavaScript

**Coding Exercise:** Yes

**Learning Objective:**
Students will transform and filter data declaratively using map, filter, and reduce, chain array methods, and understand callback functions in this context.

**Description for AI:**
Higher-order functions take other functions as arguments. map transforms every element and returns a new array of the same length. filter keeps only elements that pass a test and returns a new array of equal or smaller length. reduce accumulates all elements into a single value (sum, average, object). Method chaining: array.filter(...).map(...) — apply multiple transformations in sequence. forEach is for side effects (logging, rendering) — it returns nothing. find returns the first matching element. some returns true if any element passes the test. every returns true if all elements pass. Nigerian context: filtering a list of Nigerian startups by funding stage, mapping prices from Naira to USD, reducing a transaction array to total revenue.

**Common Mistakes:**
- Forgetting that map and filter return new arrays — the original is not modified
- Using forEach when map is needed — forEach cannot return a value
- Forgetting the return statement inside the callback — map returns an array of undefined values
- Chaining methods in the wrong order — filter then map, not map then filter when you need to filter first

---

## Topic 2 (Week 6): Classes & OOP Basics

**Title:** Classes & OOP Basics

**Programming Language:** JavaScript

**Coding Exercise:** Yes

**Learning Objective:**
Students will create classes with constructors and methods, use the this keyword correctly in class context, and implement inheritance using extends and super.

**Description for AI:**
Classes are blueprints for creating objects. class Keyword, constructor method runs when new ClassName() is called. Instance methods are defined inside the class body. this refers to the specific instance. Instances are created with new. Inheritance: class Child extends Parent — the child class inherits all parent methods. super() in the child constructor calls the parent constructor — must be called before using this. Method overriding: define a method in the child with the same name to replace the parent version. static methods belong to the class, not instances. Nigerian context: a BankAccount class with balance, deposit(), and withdraw() methods, extended by a SavingsAccount class with an interestRate property and addInterest() method.

**Common Mistakes:**
- Forgetting to call super() in the child constructor — throws ReferenceError
- Forgetting the new keyword when creating an instance — returns undefined
- Accessing this before super() in the child constructor
- Confusing class methods (all instances share them) with properties (each instance has its own)

---

## Topic 3 (Week 6): Asynchronous JavaScript — Basics

**Title:** Asynchronous JavaScript — Basics

**Programming Language:** JavaScript

**Coding Exercise:** Yes

**Learning Objective:**
Students will understand synchronous vs asynchronous execution, use setTimeout and setInterval, and understand callbacks and their limitations.

**Description for AI:**
JavaScript is single-threaded — it runs one operation at a time. Asynchronous operations (network requests, timers) do not block execution — the rest of the code keeps running while they complete. The event loop: JavaScript executes synchronous code first, then processes the callback queue. setTimeout(callback, delay) runs once after a delay in milliseconds. setInterval(callback, interval) runs repeatedly until clearInterval is called. Callbacks: a function passed as an argument to be called when an async operation completes. Callback hell: deeply nested callbacks for sequential async operations — becomes unreadable. This is why Promises were introduced. Nigerian context: a countdown timer for a Nigerian flash sale, a polling function that checks Paystack payment status every 5 seconds.

**Common Mistakes:**
- Expecting setTimeout with 0ms delay to run synchronously — it still runs after all synchronous code
- Forgetting to store the return value of setInterval to clear it later with clearInterval
- Assuming callbacks run in order — they run when their async operation completes, not when they are defined
- Creating callback hell by nesting multiple async operations — use Promises instead

---

## Topic 4 (Week 6): Working with JSON & Fetch API

**Title:** Working with JSON & Fetch API

**Programming Language:** JavaScript

**Coding Exercise:** Yes

**Learning Objective:**
Students will fetch data from external APIs using the Fetch API, parse JSON responses, check response status, and handle errors.

**Description for AI:**
JSON (JavaScript Object Notation) is the standard data format for web APIs — looks like a JavaScript object but is a string. JSON.parse converts JSON string to JavaScript object. JSON.stringify converts JavaScript object to JSON string. fetch(url) returns a Promise. response.ok is true for 2xx status codes. response.json() parses the response body as JSON (also returns a Promise). Chaining: fetch(url).then(res => res.json()).then(data => ...).catch(err => ...). Always check response.ok before calling .json() — a 404 response is not an error in fetch, it does not throw. Nigerian context: fetching data from a public Nigerian API — CBN exchange rates, or a weather API for Nigerian cities.

**Common Mistakes:**
- Not calling response.json() — trying to use the Response object directly instead of the parsed data
- Not checking response.ok — treating 404 and 500 responses as successful
- Forgetting that fetch does not throw on HTTP errors (4xx, 5xx) — only network failures throw
- Logging the Promise object instead of the resolved value — forgetting to use .then() or await

---

## Topic 5 (Week 6): Promises, Async/Await & Error Handling

**Title:** Promises, Async/Await & Error Handling

**Programming Language:** JavaScript

**Coding Exercise:** Yes

**Learning Objective:**
Students will understand Promise states, chain .then() and .catch(), write async functions using await, handle errors with try/catch, and run parallel operations with Promise.all.

**Description for AI:**
A Promise represents a future value — pending, fulfilled, or rejected. .then(callback) runs when fulfilled. .catch(callback) runs when rejected. Chaining .then() sequences async operations. async/await is syntactic sugar over Promises — async function enables await, await pauses until the Promise resolves. try/catch with await handles errors — errors in awaited Promises are caught by catch. Promise.all(arrayOfPromises) runs Promises in parallel and waits for all to complete — faster than sequential awaits. Promise.allSettled waits for all regardless of rejection. Nigerian context: fetching Nigerian bank account details and transaction history in parallel with Promise.all, handling a failed Paystack webhook with try/catch.

**Common Mistakes:**
- Using await outside an async function — SyntaxError
- Not wrapping await in try/catch — unhandled Promise rejections crash the application
- Using await sequentially when operations are independent — use Promise.all for parallel execution
- Forgetting that async functions always return a Promise — even if you return a plain value

---

## Topic 6 (Week 6): Local Storage & State Persistence

**Title:** Local Storage & State Persistence

**Programming Language:** JavaScript

**Coding Exercise:** Yes

**Learning Objective:**
Students will store and retrieve data between page sessions using localStorage and sessionStorage, understand when to use each, and serialise objects with JSON.

**Description for AI:**
localStorage persists data between sessions — survives page refreshes and browser restarts until cleared. sessionStorage persists only for the current browser session — cleared when the tab closes. Both have the same API: setItem(key, value), getItem(key), removeItem(key), clear(). Values are always stored as strings — use JSON.stringify to store objects and JSON.parse to retrieve them. Check for null when getting an item — returns null if the key does not exist. Nigerian context: saving a user's preferred language (English/Yoruba/Hausa) in a Nigerian news app with localStorage, saving a shopping cart in sessionStorage for a Jumia-style e-commerce checkout flow.

**Common Mistakes:**
- Storing objects directly without JSON.stringify — stores "[object Object]" as a string
- Not checking for null when reading from localStorage — crashes when the key has never been set
- Storing sensitive data (passwords, tokens) in localStorage — it is accessible to any JavaScript on the page
- Confusing localStorage (persistent) with sessionStorage (tab session only)

---

## Topic 7 (Week 6): Modern JavaScript (ES6+ & Modules)

**Title:** Modern JavaScript (ES6+ & Modules)

**Programming Language:** JavaScript

**Coding Exercise:** Yes

**Learning Objective:**
Students will use destructuring, spread and rest operators, and ES6 modules with import and export to write modern, organised JavaScript code.

**Description for AI:**
Destructuring arrays: const [first, second] = array. Destructuring objects: const { name, city } = user. Default values in destructuring. Spread operator (...) expands arrays and objects: [...array1, ...array2] merges arrays, {...obj1, ...obj2} merges objects. Rest operator (...) collects remaining arguments: function sum(...nums) — nums is an array. ES6 modules: export default for one main export per file, export const for named exports, import { name } from './file.js' for named imports, import defaultName from './file.js' for default imports. Modules require type="module" on the script tag or a bundler. Nigerian context: splitting a Nigerian business app into separate modules — auth.js, payments.js, dashboard.js — each exporting its functions.

**Common Mistakes:**
- Confusing spread (expand) with rest (collect) — same syntax, different context
- Forgetting to add type="module" to the script tag when using ES6 modules in the browser
- Using default export for multiple things — default export is for one thing per file, use named exports for multiple
- Importing the wrong name for a named export — names must match exactly (case-sensitive)

---

## WEEK 6 — Git & GitHub (Topics 8–12)

---

## Topic 8 (Week 6): Introduction to Version Control & Git

**Title:** Introduction to Version Control & Git

**Programming Language:** None

**Coding Exercise:** No

**Learning Objective:**
Students will understand what version control is, the difference between Git and GitHub, and the three areas of Git — Working Directory, Staging Area, and Repository — and see how Git fits into a professional developer's daily workflow.

**Description for AI:**
This topic is entirely conceptual — no commands yet. The problem without version control is illustrated with the "final_v2_ACTUALLY_FINAL" file naming chaos pattern that developers without Git fall into. Version control tracks every change, enables rollback, supports collaboration, and answers three daily questions: what changed, who changed it, how to undo it — like Google Docs revision history but for your entire codebase. Git is the version control tool running locally on your computer. GitHub is the cloud hosting platform — they are not the same thing. The three areas: Working Directory (edit), Staging Area (git add), Repository (git commit). File states: Untracked, Modified, Staged, Committed, Ignored. The core cycle: Edit → Stage → Commit → Repeat. Good commit messages start with a verb. .gitignore excludes .env, node_modules/, and OS files. The daily workflow at Flutterwave, Paystack, and Andela is shown — pull, branch, commit, push, PR. Nigerian context: version control as essential practice at every Nigerian tech company.

**Common Mistakes:**
- Confusing Git (the tool that runs locally) with GitHub (the website) — they are separate things
- Not understanding why a staging area exists — it allows grouping related changes into one logical commit
- Manually editing or deleting the .git folder — this destroys the entire project history
- Forgetting to configure git config --global user.name and user.email — commits show no identity
- Writing uninformative commit messages like "changes", "fix", or "asdfgh"

---

## Topic 9 (Week 6): Core Git Workflow

**Title:** Core Git Workflow

**Programming Language:** None

**Coding Exercise:** No

**Learning Objective:**
Students will initialise a Git repository with git init, check file states with git status, stage changes with git add, save snapshots with git commit using verb-first messages, view history with git log --oneline, review changes with git diff, and protect secrets with .gitignore.

**Description for AI:**
Five commands cover 80% of daily Git usage. git init creates a hidden .git folder — only run once in the project root. git status is the most-used command — shows untracked (red), modified (red), and staged (green) files. git add stages a single file or all changes with git add .; always run git status before and after staging. git commit -m saves a permanent snapshot — commit messages start with a verb and describe what changed (not "changes" or "fix"). git log --oneline shows compact one-line history; each commit has a unique hash. git diff shows line-by-line changes before staging; git diff --staged shows staged changes. .gitignore excludes .env (API keys), node_modules/, .DS_Store, Thumbs.db from tracking. Common mistake fixes: git commit --amend (wrong message — before pushing only), git restore --staged (unstage a file), git restore (discard working changes — destructive), git rm --cached .env (stop tracking a file now in .gitignore). The naija-portfolio project with Chidi Okafor in Lagos is the running example throughout.

**Common Mistakes:**
- Running git init in the wrong folder — always navigate to the project root first
- Writing uninformative commit messages — "changes" and "fix" tell nothing; always use verb + description
- Not creating .gitignore before the first commit — risks committing .env secrets
- Not running git status before staging — accidentally stages unintended files
- Using git restore (without --staged) — this permanently discards working directory changes

---

## Topic 10 (Week 6): Branching & Merging

**Title:** Branching & Merging

**Programming Language:** None

**Coding Exercise:** No

**Learning Objective:**
Students will create branches with naming conventions, switch between branches, make commits on feature branches that stay isolated from main, merge branches, and resolve merge conflicts by editing conflict markers.

**Description for AI:**
Branches solve the problem of working on new features without breaking the live codebase. git branch creates a branch; naming conventions: feature/, fix/, update/, experiment/ prefixes. git switch moves to a branch; git switch -c creates and switches in one command. Changes committed on a feature branch are completely invisible on main — switching back to main shows the old state of files. git merge (always from main first) brings feature commits into main. Delete merged branches with git branch -d. Merge conflicts happen when two branches edit the same line — Git inserts conflict markers: <<<<<<< HEAD (current branch version), ======= (separator), >>>>>>> branchname (incoming version). Resolve by editing the file to keep the correct version, removing all three marker lines, staging, and committing. VS Code shows colour-coded conflict resolution buttons. Professional rule: never commit directly to main — every change goes through a feature branch. Nigerian context: Lagos newspaper analogy for parallel editing, Flutterwave/Paystack branch model referenced.

**Common Mistakes:**
- Forgetting to switch to main before merging — merges in the wrong direction
- Not deleting merged branches — leaves stale branch labels cluttering the repo
- Leaving conflict markers in the file — the code will not work with <<<<<<< and >>>>>>> still present
- Not staging and committing after manually resolving a conflict

---

## Topic 11 (Week 6): GitHub — Remote Repositories

**Title:** GitHub — Remote Repositories

**Programming Language:** None

**Coding Exercise:** No

**Learning Objective:**
Students will create a GitHub repository, link it to a local repository with git remote add origin, push commits with git push, pull teammates' updates with git pull, clone repositories with git clone, write a strong README.md, and set up a Personal Access Token for authentication.

**Description for AI:**
GitHub provides cloud backup, public portfolio, collaboration, and open source access. Local vs remote: local is on your laptop (offline capable, not team-accessible), remote is on GitHub (requires internet, enables team collaboration). Creating a GitHub repo: professional username (chidi-okafor, amara-dev — not gamer tags), empty repository (do NOT tick README/.gitignore/licence if local commits exist). git remote add origin <url> — origin is the universal convention. git push -u origin main on the first push sets upstream tracking; subsequent pushes are simply git push. git pull downloads and applies new commits — run every morning before starting work. git clone copies complete repo + history to a new machine (use once); git pull updates an existing local repo (use regularly). Personal Access Token required since 2021 — generate in Settings → Developer Settings, scope: repo, store with git config --global credential.helper store. README.md rendered on the GitHub repository page — include project name, what it includes, how to run it, technologies, author with Lagos/Nigeria location. GitHub profile is the developer CV — contribution graph, pinned repos, bio with location. Nigerian developers use GitHub to get remote jobs internationally.

**Common Mistakes:**
- Initialising GitHub with README when local repo already has commits — creates diverged histories on first push
- Using GitHub password instead of Personal Access Token — authentication fails since 2021
- Closing the PAT generation page before copying the token — GitHub never shows it again
- Using git clone when the repo already exists locally — should use git pull instead
- Not setting upstream with -u on first push — subsequent git push commands fail without it

---

## Topic 12 (Week 6): Collaboration with GitHub — Pull Requests & Issues

**Title:** Collaboration with GitHub — Pull Requests & Issues

**Programming Language:** None

**Coding Exercise:** No

**Learning Objective:**
Students will fork a repository, open a Pull Request with a clear title and description, review code with inline comments, merge via the default merge commit strategy, and close Issues automatically using Closes #N syntax.

**Description for AI:**
The professional GitHub collaboration workflow: Fork/branch → feature branch → push → PR → review → merge → close issue. Forking creates your own GitHub-hosted copy of someone else's repo — used when you do not have write access. Fork vs clone: fork is on GitHub and connected to the original for PRs, clone is local. The full fork workflow: fork on GitHub → clone your fork → create a branch → commit with Closes #N → push to fork → open PR. A Pull Request contains: source/target branches, commit list, line-by-line diff, title, description, comments. Good PR: verb-first title, structured description (what it does, how to test), link to Issue. Bad PR: title "fixes", empty description. Code review roles: Comment (specific line suggestion), Approve, Request Changes — be specific, constructive, respectful. Three merge strategies: Create a merge commit (default — use this), Squash and merge, Rebase and merge. GitHub Issues: structured bug reports with steps to reproduce, expected vs actual behaviour. Labels (bug, enhancement, good first issue), Assignees, Milestones. Closes #N in a commit message or PR description automatically closes Issue N on merge. Complete flow: Issue → Branch → Commits (Closes #N) → Push → PR → Review → Merge → git pull locally → delete branch. Professional rule: no direct commits to main, one PR per feature, create an Issue before significant work.

**Common Mistakes:**
- Opening a PR against the wrong base branch — check base repository and base branch carefully
- Writing a PR with no description — reviewers cannot understand what changed or how to test it
- Bundling multiple unrelated features into one PR — one PR per feature
- Forgetting to delete the branch after merging
- Not using Closes #N — missing the automatic Issue closure on merge

---

*Module 3 of 6 — Generative AI Fellowship Beginner*
