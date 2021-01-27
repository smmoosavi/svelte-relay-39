# svelte-relay issue 39

[issue 39][issue]

## history

- `degit sveltejs/template 'svelte-relay-39'`
- `yarn`
- `node scripts/setupTypeScript.js && yarn`
- `yarn add svelte-relay relay-runtime`
- add `environment.ts` file
- add these lines to `App.svelte`:

```diff
 <script lang="ts">
+       import { setRelayEnvironment } from 'svelte-relay';
+       import environment from './environment';
+
+       setRelayEnvironment(environment);
+
        export let name: string;
 </script>
```

now we got this error:

```
Uncaught ReferenceError: process is not defined
    at invariant.js:11
    at main.ts:8
```

so I have to add replace config to rollup config:

- `yarn add --dev @rollup/plugin-replace`
- add these lines to `rollup.config.js`:

```diff
@@ -1,4 +1,5 @@
 import svelte from 'rollup-plugin-svelte';
+import replace from '@rollup/plugin-replace';
 import commonjs from '@rollup/plugin-commonjs';
 import resolve from '@rollup/plugin-node-resolve';
 import livereload from 'rollup-plugin-livereload';
@@ -46,6 +47,9 @@ export default {
                                dev: !production
                        }
                }),
+               replace({
+                       'process.env.NODE_ENV': JSON.stringify(production ? 'production' : 'development'),
+               }),
                // we'll extract any component CSS out into
                // a separate file - better for performance
                css({ output: 'bundle.css' }),

```

now we got this error:

```
Uncaught Error: Function called outside component initialization
    at get_current_component (index.js:652)
    at Object.setContext (index.js:682)
    at Object.setRelayEnvironment (context.js:7)
    at instance (App.svelte:11)
    at init (index.mjs:1472)
    at new App (App.svelte:11)
    at main.ts:3
    at main.ts:8
```

[issue]: https://github.com/kesne/svelte-relay/issues/39
