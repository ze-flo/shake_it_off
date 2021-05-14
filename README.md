![90e8780c608449c0a73a7b678d2776cb](https://user-images.githubusercontent.com/6879688/118304430-a927c180-b4b4-11eb-9d06-7d2612d2a653.jpeg)



# Getting Started with Create React App

This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

## Debug Mantle tree-shaking using YALC

`yalc add @seismic/mantle --no-pure`

Mantle Rollup config currently used (without much success):

```
/* eslint-disable import/no-anonymous-default-export */
import resolve from '@rollup/plugin-node-resolve'
import commonjs from '@rollup/plugin-commonjs'
import { babel } from '@rollup/plugin-babel'
import pkg from './package.json'
import { terser } from 'rollup-plugin-terser'
import typescript from 'rollup-plugin-typescript2'
import { tsToProps } from './lib/ts_to_props'

const extensions = ['.js', '.jsx', '.ts', '.tsx', '.json']
const root = __dirname

export default {
    input: `${root}/src/index.ts`,
    external: [...Object.keys(pkg.peerDependencies), ...Object.keys(pkg.dependencies)],
    output: [
        { file: pkg.main, format: 'cjs' },
        { format: 'esm', dir: `${root}/dist`, preserveModules: true },
    ],
    plugins: [
        resolve({
            // Forces resolving modules to root's node_modules & prevents bundling the
            // same package multiple times if package is imported from dependencies.
            // dedupe: ['react', 'react-dom'],
            extensions,
        }),
        typescript({
            rollupCommonJSResolveHack: true,
            include: ['src/index.ts'],
            clean: true,
            tsconfig: 'tsconfig.rollup.json',
            useTsconfigDeclarationDir: true,
        }),
        tsToProps(),
        commonjs(), // before babel
        babel({
            envName: 'production',
            extensions,
            babelHelpers: 'runtime',
            exclude: 'node_modules/**',
        }),
        terser(),
    ],
}
```

## Available Scripts

In the project directory, you can run:

### `yarn start`

Runs the app in the development mode.\
Open [http://localhost:3000](http://localhost:3000) to view it in the browser.

The page will reload if you make edits.\
You will also see any lint errors in the console.

### Analyzing the Bundle Size

```
    yarn build
    yarn analyze
```

[source](https://facebook.github.io/create-react-app/docs/analyzing-the-bundle-size)

### `yarn test`

Launches the test runner in the interactive watch mode.\
See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.

### `yarn build`

Builds the app for production to the `build` folder.\
It correctly bundles React in production mode and optimizes the build for the best performance.

The build is minified and the filenames include the hashes.\
Your app is ready to be deployed!

See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.

### `yarn eject`

**Note: this is a one-way operation. Once you `eject`, you can’t go back!**

If you aren’t satisfied with the build tool and configuration choices, you can `eject` at any time. This command will remove the single build dependency from your project.

Instead, it will copy all the configuration files and the transitive dependencies (webpack, Babel, ESLint, etc) right into your project so you have full control over them. All of the commands except `eject` will still work, but they will point to the copied scripts so you can tweak them. At this point you’re on your own.

You don’t have to ever use `eject`. The curated feature set is suitable for small and middle deployments, and you shouldn’t feel obligated to use this feature. However we understand that this tool wouldn’t be useful if you couldn’t customize it when you are ready for it.
