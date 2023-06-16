# MicroToy AI: Full Browser Automation

MicroToy uses GPT-4 to control your browser and perform repetitive actions on your behalf. Currently it allows you to define ad-hoc instructions. In the future it will also support saved and scheduled workflows.

## How it Works - The Action Cycle

1. MicroToy runs a content script on the webpage to pull the entire DOM. It simplifies the html it receives to only include interactive or semantically important elements, like buttons or text. It assigns an id to each interactive element. It then "templatizes" the DOM to reduce the token count even further.
2. MicroToy sends the simplified DOM, along with the user's instructions, to a selected LLM (currently GPT-3.5 and GPT-4 are supported). MicroToy informs the LLM of two methods to interact with the webpage:
   1. `click(id)` - click on the interactive element associated with that id
   2. `setValue(id, text)` - focus on a text input, clear its existing text, and type the specified text into that input
3. When MicroToy gets a completion from the LLM, it parses the response for an action. The action cycle will end at this stage if any of the following conditions are met:
   1. The LLM believes the task is complete. Instead of an action, the LLM can return an indication that it believes the user's task is complete based on the state of the DOM and the action history up to this point.
   2. The user stopped the task's execution. The user can stop the LLM's execution at any time, without waiting for it to complete.
   3. There was an error. MicroToy's safety-first architecture causes it to automatically halt execution in the event of an unexpected response.
4. MicroToy executes the action using the [chrome.debugger API](https://developer.chrome.com/docs/extensions/reference/debugger/).
5. The action is added to the action history and MicroToy cycles back to step 1 and parses the updated DOM. All prior actions are sent to the LLM as part of the prompt used to determine the next action. MicroToy can currently complete a maximum of 50 actions for a single task, though in practice most tasks require fewer than 10 actions.

## Tech Stack

Technology currently used by this extension:

- [Chrome Extension Manifest V3](https://developer.chrome.com/docs/extensions/mv3/intro/mv3-overview/)
- [React 17](https://reactjs.org)
- [Webpack 5](https://webpack.js.org/)
- [Webpack Dev Server 4](https://webpack.js.org/configuration/dev-server/)
- [React Hot Loader](https://github.com/gaearon/react-hot-loader)
- [eslint-config-react-app](https://www.npmjs.com/package/eslint-config-react-app)
- [Prettier](https://prettier.io/)
- [TypeScript](https://www.typescriptlang.org/)
