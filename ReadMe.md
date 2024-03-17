# Sidewinder22 Blog

## Commands

### Development

```bash
hexo clean
hexo generate

hexo server
```

### Add new draft

```bash
hexo new draft "My New Draft"
```

### Display drafts

```bash
hexo clean
hexo generate --watch

hexo server --draft (in an another console)
```

### Publish draft

When your post is ready to be a "live" post, use belowe command:

```bash
hexo publish Your-draft-name
hexo generate
```

### Add new post

```bash

hexo new "My New Post"
```

### Remove post

1. Delete the post from `source/_post` dir
2. Run `hexo clean` to delete the database (db.json) and assets folder
3. Run `hexo generate` to generate the new blog without your deleted post
4. Run `hexo server` to check you post
5. Run `hexo deploy` to deploy your blog 

### Add new page

```bash
hexo new page "My new page"
cd source/aboutme/
vim index.md
```

### Deploy

```bash
hexo generate --watch


hexo clean
hexo deploy
```

## Docs

* [Documentation](https://hexo.io/docs/)
* [How to use Hexo and deploy to GitHub Pages](https://gist.github.com/btfak/18938572f5df000ebe06fbd1872e4e39)
* [clean-blog-theme](https://github.com/klugjo/hexo-theme-clean-blog)
