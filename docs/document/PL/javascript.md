# js脚本


## 常用脚本
### 控制台颜色
```JavaScript
/**
 * 增强 console.log，支持通过第一个参数指定关键字来应用样式。
 * 当前支持关键字:
 * - 'info': 绿色 (#09c409)
 * - 'warm': 橙色 (#e2ae27)
 *
 * 使用方式:
 * 调用 enhanceConsoleLog() 一次进行初始化。
 * 之后即可使用:
 * console.log('info', '这是一个重要的信息');
 * console.log('warm', '需要关注的警告:', someVariable);
 * console.log('这是一个普通日志，不受影响');
 *
 * @returns {void}
 */
function enhanceConsoleLog() {
    // 检查是否已经在增强状态，避免重复执行
    if (console.__enhanced_by_wolong__) {
        console.log("info", "%c console log 增强已初始化，跳过重复执行。", "color: blue;");
        return;
    }

    // === 预设样式配置 ===
    // 未来可在此处添加更多关键字及其对应的CSS样式
    const styleMap = {
        // 关键字 (小写) : CSS 样式字符串
        'info': "color: #09c409; font-weight: bold;", // 绿色
        'warm': "color: #e2ae27; font-weight: bold;", // 橙色
        'error': "color: red; font-weight: bold;",    // 红色 (示例)
        'debug': "color: gray; font-style: italic;",  // 灰色 (示例)
    };

    // === 保存原始 console.log 方法 ===
    // 避免循环调用或丢失原生功能
    const originalConsoleLog = console.log;

    // === 覆盖 console.log ===
    console.log = function(...args) {
        // 逻辑判断:
        // 1. 至少有一个参数
        // 2. 第一个参数是字符串类型
        // 3. 第一个参数（转为小写后）存在于 styleMap 中
        if (args.length > 0 && typeof args[0] === 'string') {
            const firstArgLower = args[0].toLowerCase(); // 转换为小写进行匹配
            const style = styleMap[firstArgLower];

            if (style) {
                // === 匹配到关键字，应用样式 ===

                // 获取原始的关键字字符串（保留大小写）
                const keyword = args[0];
                // 获取除第一个参数以外的剩余参数
                const restArgs = args.slice(1);

                // 构建新的参数列表 for originalConsoleLog
                // 第一个参数: %c + 关键字字符串 (例如: "%cinfo")
                // 第二个参数: CSS 样式字符串 (例如: "color: #09c409;")
                // 后续参数: 原始调用中的剩余参数
                const newArgs = [`%c${keyword}`, style, ...restArgs];

                // 使用 apply 调用原始方法，以便传递数组形式的参数
                originalConsoleLog.apply(console, newArgs);

                // 处理完毕，直接返回，不再执行后续的原始log调用
                return;
            }
        }

        // === 未匹配到关键字 或 参数不符合预期，回退到原始行为 ===
        // 直接使用原始 console.log 调用，传入原样参数
        originalConsoleLog.apply(console, args);
    };

    // === 添加标记，表示已增强 ===
    // 这有助于未来的检查和避免重复初始化
    console.__enhanced_by_wolong__ = true;

    // === 初始化成功提示 (可选) ===
    // 使用增强后的 console.log 报告初始化状态
    console.log("info", "%c console log 增强初始化成功！", "color: blue;");
}

// === 如何使用 ===
// 在您的应用入口文件或初始化脚本中调用此函数一次：
// enhanceConsoleLog();

// === 示例调用 ===
// enhanceConsoleLog(); // 假设您已经调用了初始化函数
// console.log('info', '应用启动...');
// console.log('warm', '配置项缺失，使用默认值:', { default: true });
// console.log('error', '数据库连接失败!');
// console.log('debug', '用户请求处理中...', { userId: 123 });
// console.log('这是一条正常的日志消息');
// console.log(123, { data: 'object' }); // 非字符串开头，正常打印
```
