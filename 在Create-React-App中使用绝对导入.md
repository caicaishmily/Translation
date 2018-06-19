![](https://p0.ssl.qhimg.com/t01837865899c4318c4.png)

默认情况下，create-react-app中的ES6模块使用相对路径，这适用于您要导入的文件在文件树中相对比较近的情况：

```
import { createGoal } from ‘./actions’
```

```
import { selectAuth } from ‘./selectors’;
```

```
import App from ‘../App’;
```

但是当你开始处理深度嵌套的树结构时，你就会发现使用相对路径是真的让人痛苦不堪，因为你最终会出现点状综合征：

```
import { editUser } from ‘../../../AppContainer/actions’;
```

```
import { selectAuth } from ‘../../../AppContainer/selectors;
```

当你决定移动该文件时会发生什么？ 或者，也许你想在另一个文件中导入相同的模块？

计算第一次遍历需要多少个点的时间非常繁琐，但现在您必须重新计算每一次，因为当您更改文件的位置时，还会相对于其他文件更改其相对路径。

如果有一种方法每次都以相同的方式导入文件，无论文件与另一文件相关的位置如何？ 这个时候绝对引入就派上用场了：

```
import { editUser } from ‘containers/AppContainer/actions’;
```

```
import { selectAuth } from ‘containers/AppContainer/selectors;
```

无论你从哪里导入这些文件，路径都是一样的。 不用计算要用多少个点。

**在Create-React-App中实现绝对导入**

在深入了解一堆github issue之后，我终于完成了在create-react-app应用程序中实现绝对导入所需的步骤，最终完成了两个步骤：

1.  在根级目录创建'.** env **'文件（与package.json的级别相同）
2.  设置一个环境变量， ‘**NODE_PATH**’ to ‘**src**/’

就是这样

据我所知，create-react-app的配置方式是它的webpack配置会自动选取'.env'文件并读取'NODE_PATH'环境变量，然后可用于绝对导入.

自定义环境变量在开发和生产过程中都可以使用，因为变量是在构建时嵌入的，而不是运行时嵌入的，所以你的应用程序可以通过'process.env'访问它的环境：

[https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-custom-environment-variables](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-custom-environment-variables)

The discussions:

[https://github.com/facebookincubator/create-react-app/issues/253](https://github.com/facebookincubator/create-react-app/issues/253)

[https://github.com/facebookincubator/create-react-app/issues/102](https://github.com/facebookincubator/create-react-app/issues/102)

The pull requests:

[https://github.com/storybooks/storybook/pull/528/files](https://github.com/storybooks/storybook/pull/528/files)

[https://github.com/facebookincubator/create-react-app/pull/342/files](https://github.com/facebookincubator/create-react-app/pull/342/files)

以下是从相对导入转换为绝对导入的快速示例。 首先我们创建我们的文件和文件夹。

![](https://p0.ssl.qhimg.com/t01d63ed268bfaa299c.png)

我们的AppContainer用来加载AppRoutes。

注意它是如何使用相对导入的。 我们需要绝对路径引入。

1.  在根级目录创建'.** env **'文件（与package.json的级别相同）
2.  设置一个环境变量， ‘**NODE_PATH**’ to ‘**src**/’

![](https://p0.ssl.qhimg.com/t01ea4f581bd313267d.png)

现在我们可以使用绝对导入。

它工作得很好。

![](https://p0.ssl.qhimg.com/t01ebd1e70323eabfaa.png)

**结论**

绝对导入可以为您节省大量时间，不再那么头痛，因为您无需在导入或更改文件位置时随时考虑您需要的点数。 感谢create-react-app，设置一个允许你做到这一点的环境非常简单.

_我是一位开发人员，他记录了我遇到的新工具和概念，并发现足够有趣以供分享。 请点击该按钮和/或留下评论，以便我可以写出更适合您兴趣的内容._
