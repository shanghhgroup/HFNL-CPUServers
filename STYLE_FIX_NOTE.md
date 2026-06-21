# Style Fix Note

本版本保持公开说明页面的 Markdown 内容不变，仅补充 GitHub Pages / Jekyll 生成 Minimal Mistakes 主题样式所需的入口文件：

- `assets/css/main.scss`
- 在 `_config.yml` 和 `Gemfile` 中补充 `jekyll-remote-theme`

这样 GitHub Pages 构建时会生成 `assets/css/main.css`，避免页面以无样式的纯 HTML 形式显示。
