# 可扩展性

## 定义访问令牌类型

访问令牌类型可以用以下两种方法之一来定义：在访问令牌类型注册表中注册（按[11.1](../Section11/11.1.md)节中的过程）的，或者通过使用一个唯一的绝对URI作为它的名字。

采用URI命名的类型应该限定于特定供应商的实现，它们不是普遍适用的并且特定于使用它们的资源服务器的实现细节。

所有其他类型都必须注册。类型名称必需符合type-name ANBF。如果类型定义包含了一种新的HTTP身份验证方案，该类型名称应该与该HTTP身份验证方案名称一致（如[RFC2617][RFC2617]定义）。令牌类型“example”被保留用于样例中。

    type-name  = 1*name-char
    name-char  = "-" / "." / "_" / DIGIT / ALPHA
    
[RFC2617]: http://tools.ietf.org/html/rfc2617 "HTTP Authentication: Basic and Digest Access Authentication"

## 定义新的端点参数

用于授权端点或令牌端点的新的请求或响应参数按照[11.2](../Section11/11.2.md)节中的过程在OAuth参数注册表中定义和注册。

参数名称必须符合param-name ABNF，并且参数值的语法必须是明确定义的（例如，使用ABNF，或现有参数的语法的引用）。

    param-name  = 1*name-char
    name-char   = "-" / "." / "_" / DIGIT / ALPHA

不是普遍适用的并且特定于使用它们的授权服务器的实现细节的未注册的特定供应商的参数扩展应该采用特定供应商的前缀（例如，以“companyname_”开头），从而不会与其他已注册的值冲突。

## 定义新的授权许可类型

新的授权许可类型可以通过赋予它们一个“grant_type”参数使用的唯一的绝对URI来定义。如果扩展许可类型需要其他令牌端点参数，它们必须如[11.2](../Section11/11.2.md)节所述在OAuth参数注册表中注册。

## 定义新的授权端点响应类型

用于授权端点的新的响应类型按照[11.3](../Section11/11.3.md)节中的过程在授权端点响应类型注册表中定义和注册。响应类型名称必须符合response-type ABNF。

    response-type  = response-name *( SP response-name )
    response-name  = 1*response-char
    response-char  = "_" / DIGIT / ALPHA

如果响应类型包含一个或多个空格字符（%x20），它被看作是一个空格分隔的值列表，其中的值的顺序不重要。只有一种值的顺序可以被注册，它涵盖了相同的值的集合的所有其他排列。

例如，响应类型“token code”未由本规范定义。然而，一个扩展可以定义和注册“token code”响应类型。 一旦注册，相同的组合“code token”不能被注册，但是这两个值都可以用于表示相同的响应类型。

## 定义其他错误代码

在协议扩展（例如，访问令牌类型、扩展参数或扩展许可类型等）需要其他错误代码用于授权码许可错误响应（[4.1.2.1](../Section04/4.1.2.1.md)节）、隐式许可错误响应（[4.2.2.1](../Section04/4.2.2.1.md)节）、令牌错误响应（[5.2](../Section5.2.md)节）或资源访问错误响应（[7.2](../Section07/7.2.md)节）的情况下，这些错误代码可以被定义。

如果用于与它们配合的扩展是已注册的访问令牌类型，已注册的端点参数或者扩展许可类型，扩展错误代码必须被注册。用于未注册扩展的错误代码可以被注册。

错误代码必须符合的error ABNF，且可能的话应该以一致的名称作前缀。例如，一个表示给扩展参数“example”设置了无效值的错误应该被命名为“example_invalid”。

     error      = 1*error-char
     error-char = %x20-21 / %x23-5B / %x5D-7E

## 本机应用程序

本机应用程序是安装和执行在资源所有者使用的设备上的客户端（例如，桌面程序，本机移动应用）。本机应用程序需要关于安全、平台能力和整体最终用户体验的特别注意事项。

授权端点需要在客户端和资源所有者用户代理之间进行交互。本机应用程序可以调用外部的用户代理，或在应用程序中嵌入用户代理。例如：

- 外部用户代理-本机应用程序可以捕获来自授权服务器的响应。它可以使用带有操作系统已注册方案的重定向URI调用客户端作为处理程序，手动复制粘贴凭据，运行本地Web服务器，安装用户代理扩展，或者通过提供重定向URI来指定客户端控制下的服务器托管资源，反过来使响应对本机应用程序可用。
- 嵌入式用户代理-通过监视资源加载过程中发生的状态变化或者访问用户代理的cookies存储，本机应用程序直接与嵌入式用户代理通信，获得响应。
当在外部或嵌入式用户代理中选择时，开发者应该考虑如下：
- 外部用户代理可能会提高完成率，因为资源所有者可能已经有了与授权服务器的活动会话，避免了重新进行身份验证的需要。它提供了熟悉的最终用户体验和功能。资源所有者可能也依赖于用户代理特性或扩展帮助他进行身份验证（例如密码管理器、两步设备读取器）
- 嵌入式用户代理可能会提供更好的可用性，因为它避免了切换上下文和打开新窗口的需要。
- 嵌入式用户代理构成了安全挑战，因为资源所有者在一个未识别的窗口中进行身份验证，无法获得在大多数外部用户代理中的可视的保护。嵌入式用户代理教育用户信任未标识身份验证请求（使钓鱼攻击更易于实施）。
当在隐式许可类型和授权码许可类型中选择时，下列应该被考虑：
- 使用授权码许可类型的本机应用程序应该这么做而不需使用用户凭据，因为本机应用程序无力保持客户端凭据的机密性。
- 当使用隐式许可类型流程时，刷新令牌不会返回，这就要求一旦访问令牌过期就要重复授权过程。