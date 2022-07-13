<div align="center">
  <a href="https://ocjs.org/">
    <img width="180px" height="auto" src="https://raw.githubusercontent.com/donalffons/opencascade.js/master/images/logo.svg" />
  </a>
  <br>
  <h1>Next.js + Opencascade.js Issue</h1>
  <p>Custom Opencascade.js build with Next.js issue</p>
</div>

## Requirements

- Linux
- Node.js + NPM

## Reproduce issue

- `npm install`, install dependencies
- `docker pull donalffons/opencascade.js`, pull the opencascade.js Docker image
- Build custom JavaScript and WebAssembly code (takes a few minutes)
  ```
  docker run \
    --rm \
    -it \
    -v "$(pwd):/src" \
    -u "$(id -u):$(id -g)" \
    donalffons/opencascade.js \
    custom-occ-build.yml
  ```
- `npm run dev`, start development server on http://localhost:3000
- Throws error: `TypeError: Cannot read properties of undefined (reading 'buffer')`

## Workaround

- Backup default full builds
  ```
  mkdir node_modules/opencascade.js/dist/backup && \
  mv node_modules/opencascade.js/dist/opencascade.* node_modules/opencascade.js/dist/backup/
  ```
- Copy custom build files into `node_modules`
  ```
  cp customBuild.examples.d.ts ./node_modules/opencascade.js/dist/opencascade.full.d.ts && \
  cp customBuild.examples.js ./node_modules/opencascade.js/dist/opencascade.full.js && \
  cp customBuild.examples.wasm ./node_modules/opencascade.js/dist/opencascade.full.wasm
  ```
- Change `initOpenCascade({mainJs: opencascade, mainWasm: opencascadeWasm})` to `initOpenCascade()` in [`src/OCJSViewport.tsx`](src/OCJSViewport.tsx)
- `npm run dev`, start development server on http://localhost:3000
- Works just fine!

## Notes

- This is a modified version of [ocjs-create-next-app-12](https://github.com/donalffons/opencascade.js/tree/master/starter-templates/ocjs-create-next-app-12)
- I've changed the `webpack` configuration in [`next.config.js`](next.config.js)
- I've changed the `initOpenCascade` to use the custom build in [`src/OCJSViewport.tsx`](src/OCJSViewport.tsx)
