# Easings

[![kjspkg-available](https://github-production-user-asset-6210df.s3.amazonaws.com/79367505/250114674-fb848719-d52e-471b-a6cf-2c0ea6729f1c.svg)](https://kjspkglookup.modernmodpacks.site/#easings)

[Various easing functions](https://easings.net) and [tweeneds](https://svelte.dev/docs/svelte/svelte-motion#tweened) implemented using KubeJS. Very much inspired by [Svelte](https://svelte.dev).

## Implemented easing functions

*All functions are prefixed with* `global.easings.`

* `linear`
* `step(number_of_steps)`
* `steps(number_of_steps)` - alias of `step`
* `sineIn`
* `sineOut`
* `sineInOut`
* `polyIn(degree_of_polynomial)`
* `polyOut(degree_of_polynomial)`
* `polyInOut(degree_of_polynomial)`
* `quadIn` - alias of `polyIn(2)`
* `quadOut` - alias of `polyOut(2)`
* `quadInOut` - alias of `polyInOut(2)`
* `cubicIn` - alias of `polyIn(3)`
* `cubicOut` - alias of `polyOut(3)`
* `cubicInOut` - alias of `polyInOut(3)`
* `quartIn` - alias of `polyIn(4)`
* `quartOut` - alias of `polyOut(4)`
* `quartInOut` - alias of `polyInOut(4)`
* `quintIn` - alias of `polyIn(5)`
* `quintOut` - alias of `polyOut(5)`
* `quintInOut` - alias of `polyInOut(5)`
* `expoIn`
* `expoOut`
* `expoInOut`
* `circIn`
* `circOut`
* `circInOut`
* `backIn`
* `backOut`
* `backInOut`
* `elasticIn`
* `elasticOut`
* `elasticInOut`
* `bounceIn`
* `bounceOut`
* `bounceInOut`

## Usage

### Tweened

```js
/*
    The second argument for the tweened constructor is the tweened options.
    It supports three parameters: duration (number, time in ticks), delay (number, time in ticks), and easing (easing function, see section above for the list).

    All of them are optional, and even the second argument itself is optional.
    By default, options look like the following, and you are able to replace parts of them:

    {
        duration: 0,
        delay: 0,
        easing: global.easings.linear
    }
*/
let val = new global.tweened(0, {duration: 100, delay: 20, easing: global.easings.sineOut})
val.set(10, {easing: global.easings.steps(5)}) // Set the next interpolated value, the second argument can take in option overrides if you need to replace them for just one set. Otherwise - reinitiate the tweened.
onEvent("server.tick", event => { // I recommend using either `client.tick`, `server.tick`, or forge's ViewportEvent to update the tweeneds; you can also try using other events ending in `.tick`, but I wouldn't recommend using anything else
    someCoolFunctionThatAcceptsANumber(val.get()) // Use .get() to get the actual number value of the tweened
    val.update() // Tweeneds do not update by default, please call this function every tick
})
```

### Easing functions

**NOTE:** It is **NOT** recommended to use easing functions by themselves for most sane people, instead try using the `tweened` object described above.

```js
let interpolatedValue = global.easings.expoOut(linearlyIncreasingValueFromZeroToOne) // List of easing funcitions available in the section above
let anotherInterpolatedValue = global.easings.polyInOut(10)(anotherLinearlyIncreasingValueFromZeroToOne)
```

## Showcase

The following server script (made for KubeJS legacy, 1.16/1.18) produces the following result:

```js
const size = 25
const gap = 5
const height = 450

let values = {}
let colors = {}
let filteredEasings = {}

Object.keys(global.easings).forEach(e => {if (!e.startsWith("poly") && !e.startsWith("step")) filteredEasings[e] = global.easings[e]})
Object.keys(filteredEasings).forEach(e => {
    values[e] = new global.tweened(0, {duration: 60, easing: global.easings[e]})
    values[e].set(1)
})

onEvent("player.tick", event => {
    let { player } = event

    let boxes = {}
    for (let i = 0; i < Object.keys(values).length; i++) {
        let easing = Object.keys(values)[i]
        let val = values[easing].get()

        boxes[easing] = {type: 'rectangle', x: i * (size + gap), y: val * height, w: size, h: size, color: "#FFFFFF"}
        values[easing].update()
    }
    console.log(boxes)
    player.paint(boxes)
})
```

