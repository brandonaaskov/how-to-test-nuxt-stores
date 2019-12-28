# How to Test Nuxt Stores

I would argue that testing your stores is the most important part of your front end app that needs testing. Unfortunately, it's not well-documented how to do this with a Nuxt app.

## The Problem
Nuxt is building up Vuex modules for you under the hood, but you can't just import the stores like you would import a file. The magic happens during nuxt's build step, and that's what we need access to.

## The Solution
The majority of the magic is happening in `jest.setup.js`. There are three primary steps here:
1. Overrides: we override the default config for building nuxt, disabling everything but the `store` feature. We also override the mode (no need for SSR when testing stores), and ignore unneccessary folders.
2. We kick off a nuxt build, and we wait...
3. When it completes, we set an env var for ourselves for later: the `buildDir`. We'll need that to create the store in our tests.

The rest of the magic comes into play in the setup of `movies.test.js`. This whole part of the setup is the other critical piece in this puzzle:
```js
const localVue = createLocalVue();
localVue.use(Vuex);
let NuxtStore;
let store;

beforeAll(async () => {
  // note the store will mutate across tests
  const storePath = `${process.env.buildDir}/store.js`;
  NuxtStore = await import(storePath);
});

beforeEach(async () => {
  store = await NuxtStore.createStore();
});
```

## Notes

### Mutations and Actions
You an _see_ the mutations and actions on the store as they're stored with the private variable convetion of `_mutations` and `_actions`, respectively. However, the store also has `commit` and `dispatch` on it, so you can use it as you would in your actual application.

_(Note for the future: Vue 3 will likely drop the need to commit an action to make a mutation, so this language may get out of date)_

### `forceExit` in `jest.config.js`
In some CI environments, jest can hang, so this will force it to exit when tests are complete.
