
## 免费部署

### 方式一：GitHub Pages（推荐）
```bash
# 创建仓库：YOUR_USERNAME.github.io
git init
git add index.html
git commit -m "Initial blog"
git remote add origin https://github.com/YOUR_USERNAME/YOUR_USERNAME.github.io.git
git push -u origin main
# 然后去 Settings → Pages → 选择 main 分支 → Save
```

### 方式二：Vercel
1. 访问 [vercel.com](https://vercel.com)
2. 注册并导入 GitHub 仓库
3. 自动部署，获得免费域名

### 方式三：Netlify
1. 访问 [netlify.com](https://netlify.com)
2. 拖拽 `index.html` 到页面
3. 立即获得 URL

## 本地预览
```bash
# 任意 Web 服务器
npx serve .
# 或
python -m http.server 8080
```
