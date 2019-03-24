# next-display
> The front desk TV display webpage.

next-display is the webpage shown to visitors and residents of Next House on
the front desk TV when walking in. It handles announcements from student
groups and the Exec team and displays the current time, weather, and campus
shuttle tracking information.

## Usage
See the [Installation](#installation) instructions on how to initially build
this project.

Simply place these files in any server path, and these files will be served as
a static resource. Nothing else is needed for this to work.

## Installation
Before you proceed further, make sure you have the following dependencies
installed in order to build this project:
- [Node.js](https://nodejs.org/en/)
- [Vue CLI](https://cli.vuejs.org/)
- [Yarn](https://yarnpkg.com/en/)

You will need to clone this repository in order to compile all of the assets
for deployment:
```bash
git clone git@github.com:next-exec/next-display.git
cd next-display/
```
Once this is done, install the dependencies and then run the build command.
```bash
yarn
yarn build
```
The build output will be located in `/dist/`. See [Usage](#usage) on how to
deploy this.