---
layout: post
title: "styled components and storybooks"
date: 2018-03-02
sharing: true
categories: [javascript, react]
---

I recently discovered two new react development tools that have changed how I approach React component development: [Storybook](https://storybook.js.org/basics/introduction/) and [styled-components](https://www.styled-components.com/docs/basics#getting-started). <!--more-->I can quickly prototype a component with its data requirements, styles, and behaviour in one file, and in isolation from the rest of my application. This rapid prototyping has greatly increased my development cycle time, as I spend more time in code and less time flipping between the browser window, the browser console, css files, jsx files, and my terminal.

Using these libraries has also had the pleasant side-effect of helping me design more pure components: instead of using whatever context is available in my giant application, I think about my component interface with real encapsulation in mind.

### Storybook

Storybook creates a hot-reloading development sandbox outside of your running application, where you can create components free from global app dependencies. Building a component outside of your application context makes you think about what props are really needed to render a component. Is one component actually two components? What is the least amount of information your component needs to be a single atom.

Writing a story for storybook is like writing a spec: you describe the features of the component variant ("with long title"), and provide a callback that renders your component with props that you specify.

```javascript
storiesOf("Playbill", module)
  .add("basic", () => (
    <Playbill
      onClick={action("navigate to playbill")}
      title="foo"
      imageUrl="https://virtualplaybill.s3.amazonaws.com/1517893742975_Magellanica"
    />
  ))
  .add("with long title", () => (
    <Playbill
      onClick={action("navigate to playbill")}
      title="we are proud to present a presentation"
      imageUrl="https://virtualplaybill.s3.amazonaws.com/1517893742975_Magellanica"
    />
  ));
```

You can interact with your components and see the consequences of those interactions in the action pane below.

{% img /images/180303-styled-components/styled-components.png %}

### Styled-components

Styled-components remove the disconnect between styles and components (they are literally connected with a hyphen 😄). Before styled-components, there were clear sides to the Great CSS debate: why do we even need CSS if we inline component styles? Is it just for performance? Why not use the React render cycle to handle dynamic styles?

I am coming pretty late to the party here, but as a solid supporter of CSS over inline styles, styled components have completely won me over. Instead of filling your markup with `style={{}}` attributes, you create a new component that expresses the styles you specify.

```javascript
import styled from "styled-components";

const PlaybillContainer = styled.div`
  height: 415px;
  width: 250px;
  border: 1px solid black;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
`;

const Playbill = () => {
  return <PlaybillContainer />;
};
```

Under the hood, styled-components will generate a stylesheet and inject it into the head of your page:

```html
<style type="text/css" data-styled-components="fAjaVQ cjXYkN ktobWA bNUGoX fSQmFU sPlKa jPyvbr cDsQtG ddUpJT buzCko llbHNh wUSUC eapGFo gcivQF llqivM cHJMlW" data-styled-components-is-local="true" nonce="undefined">
...
/* sc-component-id: sc-fjdhpX */
.sc-fjdhpX {}.cHJMlW{display:block;height:300px;width:250px;}</style>
```

Styled-components have effectively put an end to the styles-in-Javascript debate. They are an expressive way to have the best of both worlds: inline styles and cascading style sheets.

### Quick start with both Storybook and styled-components

```bash
# install create-react-app
# for babel boilerplate
> npm i -g create-react-app
> create-react-app storybook-test-app
> cd storybook-test-app
> npm i

# install storybook and styled-components
> npm i -g @storybook/cli
> getstorybook
> npm i styled-components
> npm run storybook

# open your new storybook at
# http://localhost:9009/
```

Your stories are saved in `src/stories`, and you can import them from an adjacent components folder for both quick development, and quick incorporation into your application.

### Example

I am doing a React rebuild of my side project Virtual-Playbill, and storybook is the perfect place to play around with tweaks to the UI.

#### My component

```javascript
import React from "react";
import styled from "styled-components";

const PlaybillContainer = styled.div`
  height: 415px;
  width: 250px;
  border: 1px solid black;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
`;

const PlaybillHeader = styled.div`
  padding: 10px 0 10px 0;
  font-size: x-large;
  max-height: 2em;
  text-align: center;
  width: 100%;
  background-color: yellow;
  border-bottom: 1.5px solid black;
  text-transform: uppercase;
  font-weight: bold;
  letter-spacing: 2.5px;
`;

const PlaybillTitle = styled.div`
  text-align: center;
  font-weight: bold;
  font-size: large;
  max-width: 95%;
`;

const PlaybillImage = styled.img`
  display: block;
  height: 300px;
  width: 250px;
`;

const Playbill = ({ title, imageUrl, onClick }) => {
  return (
    <PlaybillContainer onClick={onClick}>
      <PlaybillHeader>Virtual Playbill</PlaybillHeader>
      <PlaybillTitle>{title}</PlaybillTitle>
      <PlaybillImage src={imageUrl} />
    </PlaybillContainer>
  );
};

export default Playbill;
```

#### My stories

```javascript
import React from "react";

import { storiesOf } from "@storybook/react";
import { action } from "@storybook/addon-actions";

import Playbill from "../components/playbill";

storiesOf("Playbill", module)
  .add("basic", () => (
    <Playbill
      onClick={action("navigate to playbill")}
      title="foo"
      imageUrl="https://virtualplaybill.s3.amazonaws.com/1517893742975_Magellanica"
    />
  ))
  .add("with long title", () => (
    <Playbill
      onClick={action("navigate to playbill")}
      title="we are proud to present a presentation"
      imageUrl="https://virtualplaybill.s3.amazonaws.com/1517893742975_Magellanica"
    />
  ));
```

Between styled-components and storybook, I don't need to leave my component file until the component is finished. Feedback cycles are tight, which means I can try out different variations quickly before dropping the component into my application.

Awesome! 😎
