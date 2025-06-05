
# v1.0 - What's new?

<Head title="What's new in 1.0" />

After a lot of work, Superforms 1.0 has been released! Here are the most important new features and improvements.

## Automatic form id

Setting a form `id` for multiple forms on the same page is not required anymore when using `use:enhance`.

```diff
const loginForm = await superValidate(loginSchema, {
-  id: 'loginForm'
});

const registerForm = await superValidate(registerSchema, {
-  id: 'registerForm'
});

return { loginForm, registerForm }
```

The one exception is if the forms are using the same schema (identical content), then you'll need an id:

```ts
const form1 = await superValidate(schema, { id: 'form1' });
const form2 = await superValidate(schema, { id: 'form2' });

return { form1, form2 };
```

Multiple forms without `use:enhance` work as well, but an id must be manually specified, either as an option, or in a hidden form field called `__superform_id`.

For extra safety, a warning will be emitted if identical id's are detected.

## defaultValues

This method will provide you with the default values for a schema.

```ts
import { defaultValues } from 'sveltekit-superforms/server';
import { z } from 'zod';

const schema = z.object({
  name: z.string().min(2),
  tags: z.string().min(1).array().default(['a', 'b'])
});

// Returns { name: '', tags: ['a', 'b'] }
const defaults = defaultValues(schema);

// Which is the same as form.data in superValidate
// when called with only the schema:
const form = await superValidate(schema);
```

This was previously an undocumented function called `defaultData`. If you've used it, rename it to `defaultValues`.

## superValidateSync

When using `superValidate` on the client, you previously had to use a `+page.ts` file to call `superValidate`, since it is asynchronous. Now you can import `superValidateSync` and use it in components directly (which assumes that there is no async validation in the schema). It can be very convenient in SPA's, just be aware that the client bundle size will increase a bit compared to using `superValidate` only on the server.

```svelte
```

## String path accessors

For errors and proxies with nested data, the array syntax was a bit clunky. It has now been replaced with a typesafe string path, so you can write it just as you would access an object property in normal JS:

```diff
import { setError } from 'sveltekit-superforms/server'

const i = 1;

- setError(form, ['tags', i, 'name'], 'Incorrect name');
+ setError(form, `tags[${i}].name`, 'Incorrect name');
```

```diff
import { intProxy } from 'sveltekit-superforms/client'

const { form } = superForm(data.form);

- const idProxy = intProxy(form, ['user', 'profile', 'id']);
+ const idProxy = intProxy(form, 'user.profile.id');
```

## New 'posted' store

You can now test if the form has been previously posted by deconstructing the boolean `$posted` store from `superForm`.

## Extra options for reset

You can now use the `data` and `id` options when calling reset, to reset the form to different data and id than the initial one. `data` can be partial.

## Better empty value support for proxies

`intProxy`, `numberProxy`, `dateProxy`, and `stringProxy` now have an `empty` option, so empty values can be automatically set to `null` or `undefined`.

## Validate the whole form on the client

The `validate` function can be used to validate a specific field in the form, but now you can also call validate with no arguments, and get a validation result back for the whole form.

## Errors for arrays and objects

Previously, it wasn't possible to handle errors for arrays in the schema, like a minimum or maximum number of items. Now it's possible:

```ts
const schema = z.object({
  tags: z.string().array().max(3)
});

const { form, errors } = superForm(data.form);
```

```svelte
{#if $errors.tags._errors}
  {$errors.tags._errors}
{/if}
```

The [1.0 release notes](https://github.com/ciscoheat/sveltekit-superforms/releases/tag/v1.0.0) have a full list of changes for 1.0, and as usual, let me know on Github or Discord if something is unclear or not working.
