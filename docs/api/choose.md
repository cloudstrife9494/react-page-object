### `.choose(propValue[, options]) => Page`

Choose a radio button

**Default Checked Props:** `id` and `name`

#### Arguments
The `propValue` and `options` arguments are passed to
[`.findWrapperForChoose`][find-wrapper-method] to find a
[`ReactWrapper`][react-wrapper]. The [`ReactWrapper`][react-wrapper] will be
used to simulate events.

1. `propValue` (`String`): Value is compared with the values of the checked
   props to assert a match.
2. `options` (`Object`): Optional.
  * `propToCheck` (`String`): Name of prop to check against instead of the default checked props.

#### Returns

`Page` object which invoked the method. This allow the method to be chained
with another `Page` object method.

#### Simulated Events
If a [`ReactWrapper`][react-wrapper] is found by
[`.findWrapperForChoose`][find-wrapper-method], then the following events will
be simulated on the [`ReactWrapper`][react-wrapper]'s React element:

1. `blur` event on the React element which is focused. This will occur if there
   is a focused React element and it is not the same as the
   [`ReactWrapper`][react-wrapper]'s React element.
2. `focus` event on the [`ReactWrapper`][react-wrapper]'s React element unless
   it is already in focus.
3. `change` event on the [`ReactWrapper`][react-wrapper]'s React element.

If no [`ReactWrapper`][react-wrapper] is found, then an error is thrown.

#### Related Methods

- [`.findWrapperForChoose(propValue[, options]) => ReactWrapper`][find-wrapper-method]

[react-wrapper]: https://github.com/airbnb/enzyme/blob/master/docs/api/mount.md#reactwrapper-api
[find-wrapper-method]: findWrapperForChoose.md

#### Example in Jest

```js
import React, { Component } from 'react'
import Page from 'react-page-object'

class App extends Component {
  state = { selectedOption: "1" }

  onChange = event => this.setState({ selectedOption: event.target.value })

  render() {
    return (
      <div>
        {this.state.selectedOption}
        <input
          id="input-id-option-1"
          type="radio"
          value="1"
          checked={this.state.selectedOption === "1"}
          onChange={this.onChange}
        />
        <input
          id="input-id-option-2"
          type="radio"
          value="2"
          checked={this.state.selectedOption === "2"}
          onChange={this.onChange}
        />
        <input
          name="input-name-option-2"
          type="radio"
          value="2"
          checked={this.state.selectedOption === "2"}
          onChange={this.onChange}
        />
        <input
          className="input-class-option-2"
          type="radio"
          value="2"
          checked={this.state.selectedOption === "2"}
          onChange={this.onChange}
        />
      </div>
    )
  }
}

describe('choose', () => {
  let page

  beforeEach(() => {
    page = new Page(<App />)
  })

  afterEach(() => {
    page.destroy()
  })

  it('chooses the radio button - targeting id', () => {
    expect(page.content()).toMatch(/1/)
    page.choose('input-id-option-2')
    expect(page.content()).toMatch(/2/)
  })

  it('chooses the radio button - targeting name', () => {
    expect(page.content()).toMatch(/1/)
    page.choose('input-name-option-2')
    expect(page.content()).toMatch(/2/)
  })

  it('chooses the radio button - targeting non-default prop', () => {
    expect(page.content()).toMatch(/1/)
    page.choose('input-class-option-2', { propToCheck: 'className' })
    expect(page.content()).toMatch(/2/)
  })
})
```
