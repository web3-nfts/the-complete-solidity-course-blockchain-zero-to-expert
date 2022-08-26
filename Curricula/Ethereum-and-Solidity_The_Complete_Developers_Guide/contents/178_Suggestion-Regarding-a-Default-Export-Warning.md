# 178. Suggestion Regarding a Default Export Warning

In the next lecture when we add code to the Layout.js component, you may get a warning in your terminal about default exports or something like this:

*Assign arrow function to a variable before exporting as module*

This a linter warning as of React v17 letting us know that it might be wise to use named exports instead.

Instead of an unnamed default export:

```
import React from "react";
 
export default (props) => {
  ...
};
```

You can refactor to give your component a name:

```
import React from "react";
 
const Layout = (props) => {
  ...
};
export default Layout;
```

This will come up a few times throughout the rest of the course and can be handled similarly.

```
import React from "react";
 
const someComponent = () => {
  ...
};
export default someComponent;
```