# 为什么选择Playwright

Playwright能够跨越所有现代浏览器实现快速、可靠和功能强大的测试和自动化工作。本指南涵盖了这些关键的区别点，以帮助您为自动化测试选择正确的工具。



## 支持所有浏览器

- 在Chromium、Firefox和WebKit上进行测试：Playwright的API覆盖了所有现代浏览器，包括谷歌Chrome和微软Edge（Chrome内核）、苹果Safari（WebKit内核）和Mozilla Firefox；
- 跨平台WebKit测试：通过Playwright，使用针对Windows、Linux和macOS的WebKit版本，测试应用程序在Apple Safari中的表现。支持在本地和CI环境下进行测试；
- 模拟移动端测试：使用Playwithr提供的设备仿真功能，测试web应用程序在移动端浏览器中的表现；
- 支持无头和有头模式：Playwright支持所有浏览器和所有平台的无头（无浏览器用户界面）和有头（有浏览器用户界面）模式。有头模式非常适合调试，而无头模式速度更快，适合CI/云执行；





## 快速可靠的执行

- 支持自动等待的API：Playwright内置的API会自动等待元素准备就绪，这提高了测试可靠性并简化了测试用例编写；
- 无超时自动化：Playwright接收浏览器信号，如网络请求、页面导航和页面加载事件，消除在用例中使用硬编码的方式来设置最大超时时间；
- 与浏览器上下文快速隔离：对具有浏览器上下文的多个独立执行环境重用单个浏览器实例；
- 弹性的页面元素选择器：Playwright可以依靠面向用户的字符串，如文本内容和label标签来选择元素，这些字符串往往比紧密耦合到DOM结构的选择器更有弹性。





## 强大的自动化能力

- 支持处理多个域名、多个页面或多个frame的场景：Playwright是一个进程外自动化驱动程序，它不受页面内JavaScript执行范围的限制；
- 强大的网络控制能力：Playwright将上下文范围的网络拦截引入存根以及模拟网络请求；
- 支持现代化的Web API：Playwright支持阴影穿透选择器、地理位置、权限、web开发者等web api;
- 覆盖所有场景的能力：支持文件下载和上载、进程外iFrame、本机输入事件，甚至黑暗模式；







# 快速开始

## 安装

> 查看[系统要求](#系统要求)

### Pip

```shell
pip install --upgrade pip
pip install playwright
playwright install
```



### Conda

```shell
conda config --add channels conda-forge
conda config --add channels microsoft
conda install playwright
playwright install
```

注意：`playwright install` 命令会安装Chromium、Firefox和WebKit浏览器的二进制安装包，要修改此行为，请参见安装参数部分的内容。





## 用法

安装Playwright库后，可以在Python脚本中导入，并启动3种浏览器（chromium、firefox和webkit）中的任意一种。

```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch()
    page = browser.new_page()
    page.goto("http://playwright.dev")
    print(page.title())
    browser.close()
```

Playwright支持同步和异步API。如果您的项目使用asyncio，则应使用async API：

```python
import asyncio
from playwright.async_api import async_playwright

async def main():
    async with async_playwright() as p:
        browser = await p.chromium.launch()
        page = await browser.new_page()
        await page.goto("http://playwright.dev")
        print(await page.title())
        await browser.close()

asyncio.run(main())
```





### First script

在第一个脚本中，我们将导航到whatsmyuseragent.org页面，并在WebKit中截图。

```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.webkit.launch()
    page = browser.new_page()
    page.goto("http://whatsmyuseragent.org/")
    page.screenshot(path="example.png")
    browser.close()
```

在默认情况下，playwright以无头模式运行浏览器，若要查看浏览器界面，需要在启动浏览器时传递headless=False参数。您还可以传入`slow_mo`参数来降低执行速度，这一部分的内容将会在调试工具部分进行详细介绍。

```python
firefox.launch(headless=False, slow_mo=50)
```





### 录制脚本

Playwright提供了可用于记录用户交互，并自动生成Python代码的命令行接口。

```shell
playwright codegen wikipedia.org
```





### 结合Pytest

有关Pytest说明和示例，后续有专门的章节进行介绍。





### Pyinstaller

您可以使用Playwright和Pyinstaller来创建独立的可执行文件。

```python
# main.py
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch()
    page = browser.new_page()
    page.goto("http://whatsmyuseragent.org/")
    page.screenshot(path="example.png")
    browser.close()
```

如果要将浏览器与可执行文件捆绑在一起：

```python
# Linux/macOS
PLAYWRIGHT_BROWSERS_PATH=0 playwright install chromium
pyinstaller -F main.py

# Windows with cmd.exe
set PLAYWRIGHT_BROWSERS_PATH=0
playwright install chromium
pyinstaller -F main.py

# Windows with PowerShell
$env:PLAYWRIGHT_BROWSERS_PATH="0"
playwright install chromium
pyinstaller -F main.py
```

> 将浏览器与可执行文件捆绑在一起将生成更大的二进制文件，建议仅捆绑您使用的浏览器。





### 已知问题

#### 使用time.sleep() 会导致过时状态

您应该使用`page.wait_for_timeout(5000)`而不是`time.sleep(5)`，最好不要等待超时，但有时它对调试很有用。在这些情况下，Playwright源码中使用的等待方法并不是`time`模块，这是因为我们内部依赖于异步操作，当使用`time.sleep(5)`时，它们无法得到正确的处理。





### 系统要求

Playwright需要Python 3.7或更高版本，以及可跨系统运行的Chromium、Firefox以及WebKit浏览器的二进制安装包。

**Windows**

适用于Windows和Windows Linux子系统（WSL）。

**macOS**

系统版本10.14 (Mojave) 以上。

**Linux**

根据不同的Linux发行版本，可能需要安装额外的依赖项来运行浏览器。



# 调试工具

Playwright脚本使用现有的调试工具，如Node.js调试器和浏览器开发者工具。除此之外，Playwright还为浏览器自动化引入了新的调试功能。



## Playwright Inspector

Playwright Inspector是一个GUI工具，帮助编写和调试Playwright脚本，这是默认推荐的脚本故障排除工具。

 ![Playwright Inspector](https://tva1.sinaimg.cn/large/008i3skNgy1gu6v6qwe69j60g90iaq5602.jpg)



## Playwright Trace Viewer

[Playwright Trace Viewer]()是一个GUI工具，可帮助以事后方式对测试运行进行故障排除。





## 以有头方式启动浏览器

默认情况下，Playwright以无头模式运行浏览器。要更改此行为，请使用headless:false作为启动选项。您还可以使用slow_mo选项来降低执行速度，并在调试时进行跟踪。

```python
# sync
chromium.launch(headless=False, slow_mo=100) # or firefox, webkit

#async
await chromium.launch(headless=False, slow_mo=100) # or firefox, webkit
```





## 浏览器开发者工具

您可以在Chromium、Firefox和WebKit中使用浏览器开发工具，同时在headed模式下运行剧作家脚本。开发人员工具有助于：

-   检查DOM树并查找元素选择器；

-   在执行期间查看控制台日志（或了解如何[通过API读取日志](https://playwright.dev/python/docs/verification#console-logs)）；

-   检查网络活动和其他开发人员工具功能；

使用`page.pause`方法是在开发人员工具中暂停Playwright脚本执行和检查页面的一种简单方法。它还将打开Playwright Inspector以帮助调试。

对于Chromium：您还可以通过启动选项打开开发人员工具：

```python
# sync
chromium.launch(devtools=True)

# async
await chromium.launch(devtools=True)
```

>   对于WebKit：在执行期间启动WebKit Inspector将阻止剧作家脚本进一步执行。



## 以debug模式运行

可以通过设置`PWDEBUG` 环境变量的方式，让Playwright脚本以debug方式运行。

设置 PWDEBUG=1 将打开Playwright Inspector。

设置 PWDEBUG=console 将在开发者工具控制台中配置用于调试的浏览器，其具有以下特点：

-   浏览器将始终以有头模式启动；

-   将Playwright语句**默认**的超时设置为0（也就是不超时）； 

    >   补充：如果在代码中手动设置了超时时间，如：page.set_default_timeout(30000)，则调试模式下仍然会生效，导致运行超时。

-   在浏览器页面中配置Playwright对象，并高亮显示Playwright选择器，可用于验证文本或复合选择器；

```shell
# Linux/macOS
PWDEBUG=console pytest -s

# Windows with cmd.exe
set PWDEBUG=console
pytest -s

# Windows with PowerShell
$env:PWDEBUG="console"
pytest -s
```



## 【1】开发者工具控制台中的选择器

当设置 PWDEBUG=console 以debug模式运行脚本时，在开发者工具控制台中将会有一个`playwright`对象可用。



## 控制台打印详细的API日志

Playwright支持通过设置环境变量的方式，在运行脚本时打印详细的API日志。

```shell
# Linux/macOS
DEBUG=pw:api pytest -s

# Windows with cmd.exe
set DEBUG=pw:api
pytest -s

# Windows with PowerShell
$env:DEBUG="pw:api"
pytest -s
```



# 命令行工具

## 用法

```shell
% playwright
Usage: npx playwright [options] [command]

Options:
  -V, --version                          output the version number
  -h, --help                             display help for command

Commands:
  open [options] [url]                   open page in browser specified via -b, --browser
  codegen [options] [url]                open page and generate code for user actions
  debug <app> [args...]                  run command in debug mode: disable timeout, open inspector
  install [options] [browser...]         ensure browsers necessary for this version of Playwright are installed
  install-deps [browser...]              install dependencies necessary to run browsers (will ask for sudo permissions)
  cr [options] [url]                     open page in Chromium
  ff [options] [url]                     open page in Firefox
  wk [options] [url]                     open page in WebKit
  screenshot [options] <url> <filename>  capture a page screenshot
  pdf [options] <url> <filename>         save page as pdf
  show-trace [options] [trace]           Show trace viewer
  help [command]                         display help for command

```



## install browsers

运行以下命令，将会安装默认浏览器（包含Chromium、Firefox和WebKit三种），这将会花费一些时间。

```shell
playwright install
```

也可以显式指定要安装的浏览器类型：

```shell
playwright install webkit
```

查看所有支持的浏览器类型：

```shell
playwright install --help
...
Install custom browsers, supports chromium, chrome, chrome-beta, msedge, msedge-beta, msedge-dev, firefox, webkit
```

>   补充： 在某些Linux发行版上，还需要安装浏览器依赖，详情可参考`playwright install-deps --help`

## codegen

```
playwright codegen sspai.com
```

运行codegen并在浏览器中执行操作，Playwright CLI将根据用户交互自动生成指定语言的代码，并且codegen生成的代码中将优先使用弹性的页面元素选择器。

>   补充：什么是弹性的页面元素选择器？
>
>   Playwright可以依靠面向用户的字符串，如文本内容和label标签来选择元素，这些字符串往往比紧密耦合到DOM结构的选择器更有弹性。

![](https://tva1.sinaimg.cn/large/008i3skNgy1gu6ydkhosxj617p0f077r02.jpg)



>   补充：经过实际测试，macOS下，如果在环境变量中设置了`PWDEBUG=console pytest -s`，是无法通过codegen录制生成代码的。



### 保持身份验证状态

运行以下命令，将 cookies 和 localStorage保存在本地的`auth.json` 文件中，这有助于单独记录身份验证步骤并在以后重用。

```shell
playwright codegen --save-storage=auth.json sspai.com
```

使用`--load storag`加载之前的存储数据，所有Cookie和localStorage都将恢复，大多数web应用程序将会处于用户身份已验证状态。

```shell
playwright open --load-storage=auth.json sspai.com
playwright codegen --load-storage=auth.json sspai.com
```



### 自定义设置的codegen

如果想在某些非标准设置中使用codegen，例如`browser_context.route(url，handler))`，可以调用`page.pause()`，它将打开一个包含codegen控件的单独窗：

```python
# sync
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    # Make sure to run headed.
    browser = p.chromium.launch(headless=False)

    # Setup context however you like.
    context = browser.new_context() # Pass any options
    context.route('**/*', lambda route: route.continue_())

    # Pause the page, and start recording manually.
    page = context.new_page()
    page.pause()
    
# async
import asyncio
from playwright.async_api import async_playwright

async def main():
    async with async_playwright() as p:
        # Make sure to run headed.
        browser = await p.chromium.launch(headless=False)

        # Setup context however you like.
        context = await browser.new_context() # Pass any options
        await context.route('**/*', lambda route: route.continue_())

        # Pause the page, and start recording manually.
        page = await context.new_page()
        await page.pause()

asyncio.run(main())
```



## Open pages

通过open命令，您可以使用Playwright绑定的浏览器浏览网页。查看命令帮助：

```shell
Usage: npx playwright open [options] [url]

open page in browser specified via -b, --browser

Options:
  -b, --browser <browserType>  browser to use, one of cr, chromium, ff, firefox, wk, webkit (default: "chromium")
  --channel <channel>          Chromium distribution channel, "chrome", "chrome-beta", "msedge-dev", etc
  --color-scheme <scheme>      emulate preferred color scheme, "light" or "dark"
  --device <deviceName>        emulate device, for example  "iPhone 11"
  --geolocation <coordinates>  specify geolocation coordinates, for example "37.819722,-122.478611"
  --ignore-https-errors        ignore https errors
  --load-storage <filename>    load context storage state from the file, previously saved with --save-storage
  --lang <language>            specify language / locale, for example "en-GB"
  --proxy-server <proxy>       specify proxy server, for example "http://myproxy:3128" or "socks5://myproxy:8080"
  --save-storage <filename>    save context storage state at the end, for later use with --load-storage
  --timezone <time zone>       time zone to emulate, for example "Europe/Rome"
  --timeout <timeout>          timeout for Playwright actions in milliseconds (default: "10000")
  --user-agent <ua string>     specify user agent string
  --viewport-size <size>       specify browser viewport size in pixels, for example "1280, 720"
  -h, --help                   display help for command

Examples:

  $ open
  $ open -b webkit https://example.com
```

示例：

```shell
# Open page in Chromium
playwright open sspai.com

# Open page in WebKit
playwright open -b wk sspai.com
```

### 设备仿生

open命令可以模拟playwright.devices列表中的移动和平板电脑设备打开浏览器。

```shell
playwright open --device="iPhone 11" example.com
```

>   补充： 要查看所有playwright支持的仿生设备，可通过以下方法：
>
>   ```python
>   from playwright.sync_api import sync_playwright
>   
>   
>   def run(playwright):
>       print(playwright.devices)		# 105个
>   
>   
>   with sync_playwright() as playwright:
>       run(playwright)
>   ```



### 模拟颜色方案和窗口大小

```shell
playwright open --viewport-size=800,600 --color-scheme=dark example.com
```



### 模拟地理位置、语言和时区

```shell
playwright open --timezone="Europe/Rome" --geolocation="41.890221,12.492348" --lang="it-IT" maps.google.com
```



## Inspect selectors

在使用 open 或 codegen 命令期间，您可以在任何浏览器的developer tools控制台中使用以下API：

-   playwright.$(selector)

    根据selectot，查询匹配的页面元素；

-   playwright.$$(selector)

    和 playwright.$(selector) 类似，但是会返回所有匹配的元素（数组形式）；

    ![](https://tva1.sinaimg.cn/large/008i3skNgy1gu6zydl7c0j60ql09z40602.jpg)

-   playwright.inspect(selector)

    在 Elements 面板显示此元素（如果相应浏览器的DevTools支持它）；

-   playwright.selector(element)

    为给定元素生成选择器。

    

>   补充：打开WebKit Web Inspector将断开playwright与浏览器的连接。在这种情况下，codegen功能将无法工作。





## screenshot

查看命令帮助：

```shell
pw screenshot -h
Usage: npx playwright screenshot [options] <url> <filename>

capture a page screenshot

Options:
  --wait-for-selector <selector>  wait for selector before taking a screenshot
  --wait-for-timeout <timeout>    wait for timeout in milliseconds before taking a screenshot
  --full-page                     whether to take a full page screenshot (entire scrollable area)
  -b, --browser <browserType>     browser to use, one of cr, chromium, ff, firefox, wk, webkit (default: "chromium")
  --channel <channel>             Chromium distribution channel, "chrome", "chrome-beta", "msedge-dev", etc
  --color-scheme <scheme>         emulate preferred color scheme, "light" or "dark"
  --device <deviceName>           emulate device, for example  "iPhone 11"
  --geolocation <coordinates>     specify geolocation coordinates, for example "37.819722,-122.478611"
  --ignore-https-errors           ignore https errors
  --load-storage <filename>       load context storage state from the file, previously saved with --save-storage
  --lang <language>               specify language / locale, for example "en-GB"
  --proxy-server <proxy>          specify proxy server, for example "http://myproxy:3128" or "socks5://myproxy:8080"
  --save-storage <filename>       save context storage state at the end, for later use with --load-storage
  --timezone <time zone>          time zone to emulate, for example "Europe/Rome"
  --timeout <timeout>             timeout for Playwright actions in milliseconds (default: "10000")
  --user-agent <ua string>        specify user agent string
  --viewport-size <size>          specify browser viewport size in pixels, for example "1280, 720"
  -h, --help                      display help for command

Examples:

  $ screenshot -b webkit https://example.com example.png
```

示例：

```shell
# Wait 3 seconds before capturing a screenshot after page loads ('load' event fires)
playwright screenshot \
    --device="iPhone 11" \
    --color-scheme=dark \
    --wait-for-timeout=3000 \
    sspai.com sspai-iphone11.png
```

 ![](https://tva1.sinaimg.cn/large/008i3skNgy1gu707uwiv0j60ac0h0jsa02.jpg)



```shell
# 自动滑动页面进行截图
playwright screenshot --full-page sspai.com sspai-full.png
```



## Generate PDF

此命令仅在无头模式下运行Chromium浏览器时支持。

```shell
playwright pdf sspai.com sspai.pdf
```



## 安装系统依赖

Ubuntu 18.04和Ubuntu 20.04系统依赖项可以自动安装。这对于CI环境来说非常有用。

```shell
playwright install-deps
playwright install-deps chromium
```



# 支持的语言

Playwright有多种语言实现的API版本。

## JavaScript and TypeScript[#](https://playwright.dev/python/docs/languages#javascript-and-typescript)

[Playwright for Node.js](https://playwright.dev/docs/intro/) is available.

-   [NPM](https://www.npmjs.com/package/playwright)
-   [Documentation](https://playwright.dev/docs/intro/)
-   [API](https://playwright.dev/docs/api/class-playwright)
-   [GitHub repo](https://github.com/microsoft/playwright)



## Python[#](https://playwright.dev/python/docs/languages#python)

[Playwright for Python](https://playwright.dev/python/docs/intro/) is available.

-   [Documentation](https://playwright.dev/python/docs/intro/)
-   [API](https://playwright.dev/python/docs/api/class-playwright)
-   [Playwright on PyPI](https://pypi.org/project/playwright/)
-   [GitHub repo](https://github.com/microsoft/playwright-python)
-   [Pytest integration](https://github.com/microsoft/playwright-pytest)



## Java[#](https://playwright.dev/python/docs/languages#java)

[Playwright for Java](https://playwright.dev/java/docs/intro/) is available.

-   [Documentation](https://playwright.dev/java/docs/intro/)
-   [API](https://playwright.dev/java/docs/api/class-playwright)
-   [GitHub repo](https://github.com/microsoft/playwright-java)





## .NET[#](https://playwright.dev/python/docs/languages#net)

[Playwright for .NET](https://playwright.dev/dotnet/docs/intro/) is available.

-   [Documentation](https://playwright.dev/dotnet/docs/intro/)
-   [API](https://playwright.dev/dotnet/docs/api/class-playwright)
-   [GitHub repo](https://github.com/microsoft/playwright-dotnet)
-   [Playwright on NuGet](https://www.nuget.org/packages/Microsoft.Playwright)



# 版本说明

[Playwright Release](https://playwright.dev/python/docs/release-notes)

