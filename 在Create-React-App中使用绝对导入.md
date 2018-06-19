![](https://p0.ssl.qhimg.com/t01837865899c4318c4.png)

By default ES6 modules in create-react-app use relative paths, which is fine for cases where the files you’re importing are relatively close within the file tree:

```
import { createGoal } from ‘./actions’
```

```
import { selectAuth } from ‘./selectors’;
```

```
import App from ‘../App’;
```

But using relative paths is a real pain when you start dealing with deeply nested tree structures because you end up with dot-dot syndrome:

```
import { editUser } from ‘../../../AppContainer/actions’;
```

```
import { selectAuth } from ‘../../../AppContainer/selectors;
```

And what happens when you decide you want to move that file? Or maybe you want to import the same module in another file?

It was tedious enough counting how many dots you needed to traverse the first time but now you must recount every single time because as you change the location of a file you also change its relative path with respect to other files.

What if there was a way to import a file the same way every time, regardless of where the file sat in relation to another? This is where absolute imports come in handy:

```
import { editUser } from ‘containers/AppContainer/actions’;
```

```
import { selectAuth } from ‘containers/AppContainer/selectors;
```

No matter where you import those files from the path will be the same. No more counting dots.

**Implementing Absolute Imports in Create-React-App**

After digging through a bunch of github issues I was finally able boil down the steps required to implement absolute imports in create-react-app applications down to two steps:

1.  Create a ‘.**env**’ file at the root level (same level as package.json)
2.  Set an environment variable, ‘**NODE_PATH**’ to ‘**src**/’

And that’s it.

From what I understand, create-react-app is configured in such a way that its webpack configuration will automatically pick up ‘.env’ files and read the ‘NODE_PATH’ environment variable, which can then be used for absolute imports.

Custom environment variables also work both during development and in production because the variables are embedded during build time instead of runtime, so your app will have access to its environment by ‘process.env’:

[https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-custom-environment-variables](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-custom-environment-variables)

The discussions:

[https://github.com/facebookincubator/create-react-app/issues/253](https://github.com/facebookincubator/create-react-app/issues/253)

[https://github.com/facebookincubator/create-react-app/issues/102](https://github.com/facebookincubator/create-react-app/issues/102)

The pull requests:

[https://github.com/storybooks/storybook/pull/528/files](https://github.com/storybooks/storybook/pull/528/files)

[https://github.com/facebookincubator/create-react-app/pull/342/files](https://github.com/facebookincubator/create-react-app/pull/342/files)

Here’s a quick example of converting from relative to absolute imports. First we create our files and folders.

![](https://p0.ssl.qhimg.com/t01d63ed268bfaa299c.png)

We have our AppContainer that loads AppRoutes.

Notice how it uses relative imports. We want absolute imports.

1.  Create a ‘.**env**’ file at the root level (same level as package.json)
2.  Set an environment variable, ‘**NODE_PATH**’ to ‘**src**/’

![](https://p0.ssl.qhimg.com/t01ea4f581bd313267d.png)

And now we can use absolute imports.

And it works just fine.

![](https://p0.ssl.qhimg.com/t01ebd1e70323eabfaa.png)

**Conclusion**

Absolute imports can save you a lot of time and headaches because you no longer have to account for how many dots you need anytime you’re importing or changing the location of a file. Thanks to create-react-app it’s quite simple to set up an environment that allows you to do just that.

_I’m a developer who documents new tools and concepts I come across and find interesting enough to share. Please click that heart button and/or leave a comment so I can write content more suited to your interests._
