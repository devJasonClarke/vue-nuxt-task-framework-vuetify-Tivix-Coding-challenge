# Tivix Coding Task | Weather App 

## How to get it up and running:

```bash
# install dependencies
$ npm install

# '.env' file created for Open Weather Map Api key.
$ Create a '.env' file in the root of the project

# set your Open Weather Map Api key.
$ Create variable WEATHER_API in '.env' file and set it to your Open Weather Map Api key

# serve with hot reload at localhost:3000
$ npm run dev

# build for production and launch server
$ npm run build
$ npm run start

# generate static project
$ npm run generate

$✨ It's up ✨
```
You can also check it out on Netlify: [https://Jason-Tivix-Coding-Task.netlify.app/](https://Jason-Tivix-Coding-Task.netlify.app/)

## Requirements

1. Implement data tracker class that keeps track of min, max, mean, and mode values.
●  insert(value) - Records a new value into the tracker.
●  showMin() - Show the minimum value from the recorded tracker values.
●  showMax() - Show the maximum value from the recorded tracker values.
●  showMean() - Show the mean value from the recorded tracker values.
●  showMode() - Show the mode value from the recorded tracker values.
 
2. Call 5-day forecast on the target city and create different data tracker objects from above data tracker class:
● Show day temperature.
