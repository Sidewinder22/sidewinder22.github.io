# Sidewinder22 Blog

## Commands

### Development
```bash
hexo server
```

### Add new post
```bash
hexo new first-post
```

### Remove post
1. Delete the post from `source/_post` dir
2. Run `hexo clean` to delete the database (db.json) and assets folder
3. Run `hexo generate` to generate the new blog without your deleted post
4. Run `hexo server` to check you post
5. Run `hexo deploy` to deploy your blog 

### Add new page
```bash
hexo new page aboutme
cd source/aboutme/
vim index.md
```

### Deploy
```bash
hexo generate --watch


hexo clean
hexo deploy
```

# Docs
* [Documentation](https://hexo.io/docs/)
* [How to use Hexo and deploy to GitHub Pages](https://gist.github.com/btfak/18938572f5df000ebe06fbd1872e4e39)
