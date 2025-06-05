
# Loading timers

<Head title="Timers for loading spinners" />

It's important that users understand that things are happening when they submit a form. Loading timers can be used to provide feedback when there is a server response delay, for example by displaying a loading spinner icon.

## Usage

```ts
const { form, enhance, submitting, delayed, timeout } = superForm(data.form, {
  delayMs?: 500
  timeoutMs?: 8000
})
```

`delayMs` should be positive and always smaller than or equal to `timeoutMs`, otherwise the timer behavior will be undefined. And of course, the Superforms [use:enhance](/concepts/enhance) must be added to the form element, since this is client-side behavior.

## Submit state

After a certain time when the form is submitted, determined by `delayMs` and `timeoutMs`, the timers changes state. The states are:

<Timers />

These states affect the readable stores `submitting`, `delayed` and `timeout`, returned from `superForm`. They are not mutually exclusive, so `submitting` won't change to `false` when `delayed` becomes `true`.

## Loading indicators

A perfect use for these timers is to show a loading indicator while the form is submitting:

**src/routes/+page.svelte**

```svelte

<form method="POST" use:enhance>
  <button>Submit</button>
  {#if $delayed}<img src={spinner} />{/if}
</form>
```

The reason for using `delayed` instead of `submitting` is based on the article [Response Times: The 3 Important Limits](https://www.nngroup.com/articles/response-times-3-important-limits/), which states that for short waiting periods, no feedback is required except to display the result. Therefore, `delayed` is used to show a loading indicator after a little while, not instantly.

## Visualizing the timers

Submit the following form and play around with the different settings. Different loading spinners are set to display when `delayed` and `timeout` are true respectively. 

The default [multipleSubmits](/concepts/submit-behavior#multiplesubmits) setting prevents the form from being submitted multiple times, until the `timeout` state is reached. Click multiple times to see the effect of that.

<Form data={data} />

By experimenting with the timers and the delay between them, it's certainly possible to prevent the feeling of unresponsiveness.

<Next section={concepts} />
