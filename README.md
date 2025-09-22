# Svelte + D3 (with runes) example

A demonstration of integrating D3.js with Svelte 5 using the new runes syntax. This project showcases different approaches to creating data visualizations with bar charts rendered using HTML, SVG, and D3 axes.

## Live Demo

The project is automatically deployed to GitHub Pages on every push to main.

## Developing

Once you've created a project and installed dependencies with `npm install`, start a development server:

```sh
npm run dev

# or start the server and open the app in a new browser tab
npm run dev -- --open
```

## Building

To create a production version of your app:

```sh
npm run build
```

You can preview the production build with `npm run preview`.

## Data Source

The charts visualize health and income data from the [Gapminder dataset](https://vega.github.io/vega-datasets/data/gapminder-health-income.csv), showing health scores by country with regional color coding.
