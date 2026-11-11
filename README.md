# Product Filtering

A product grid with live text search, category checkboxes, and a per-item cart toggle — no framework, styled entirely with Tailwind's CDN build instead of hand-written CSS.

## Overview

Twelve hardcoded products (cameras, smartphones, games, TVs) render into a responsive grid on load. Typing in the search box filters by product name in real time, and checking one or more category boxes narrows the grid further — search and categories combine with AND logic, while multiple checked categories combine with OR logic (checking "Games" and "Televisions" shows both). Hovering a product reveals an "Add To Cart" control that toggles to "Remove From Cart" and updates a live count badge in the nav.

## Why I Built It

This was a practice project for filtering a rendered list against two independent, simultaneously-active criteria (a text query and a set of checkboxes), and for building UI entirely out of Tailwind utility classes rather than a separate stylesheet. It also turned into a small accessibility exercise once I noticed the cart toggle was built as a clickable `<span>` — visually fine, but invisible to keyboard users, which is the kind of thing that's easy to miss when a `<div>`/`<span>` with a click handler *looks* exactly like a button.

## Features

- Grid of 12 products, each with an image, name, and price
- Live search-as-you-type filtering by product name (case-insensitive)
- Category checkboxes (cameras, smartphones, games, televisions) that OR together, and AND against the search term
- Per-product "Add To Cart" / "Remove From Cart" toggle button with `aria-pressed` reflecting its state
- Live cart-count badge in the nav that increments/decrements as items are toggled

## JavaScript Concepts

- **`Array.prototype.filter`/`map`** to derive the list of checked category IDs and cross-reference it against each product's `type`
- **DOM diffing via class toggling**: filtering doesn't re-render the grid — it just adds/removes a `hidden` class on each existing product element based on whether it currently matches
- **Event delegation on a container**: a single `change` listener on `#filters-container` handles all four category checkboxes instead of binding one listener per checkbox
- **Template literals** to build each product card's inner markup from a plain data array
- **Elements built and queried together**: each product's cart button is grabbed with `querySelector('.status')` right after its markup is injected, so the click handler is attached exactly once per card

## How It Works

`script.js` holds a `products` array of plain objects (name, image path, category, price) and renders one card per entry into `#products-wrapper` via `createProductElement`, which also wires up a click listener on that card's cart button. `filterProducts` runs on every keystroke in the search box and every checkbox change: it reads the current search term and the list of checked category IDs, then loops over every rendered product element and toggles a `hidden` class based on whether the product's name includes the search term *and* (no categories are checked, or its category is one of the checked ones). `addToCart` just flips a button between two visual/text states and increments or decrements a shared counter that's written into the nav badge.

## How to Run

Open `index.html` in a browser, or serve the folder with a static file server — the script path is now a relative `script.js`, so it works from any subpath, not just a domain root. Tailwind is loaded from its CDN build at runtime, so no build step or npm install is needed.

## Technologies

- HTML5
- Tailwind CSS (via CDN, no local build)
- Vanilla JavaScript

## What I Learned

Filtering against two criteria at once is straightforward logically, but it clarified for me the difference between OR-within-a-group and AND-across-groups — checked categories need to OR together (any match counts), while the category match as a whole needs to AND against the search term (both must pass). It's a small thing but easy to get backwards. I also came away more careful about interactive elements: a `<span>` with a click handler works for a mouse, but it's not a control as far as the keyboard or a screen reader is concerned, and swapping it for a real `<button>` was a one-line fix once I noticed it.

## Future Improvements

- Persist cart contents and active filters across page reloads (currently everything resets on refresh)
- An actual checkout flow — right now the cart is just a count badge with no cart view or purchase step, which is out of scope for what's meant to be a filtering demo
- A "no results" message when the current search/filter combination matches nothing
