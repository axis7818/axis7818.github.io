# [~/camerontaylor.dev](https://camerontaylor.dev/projects/camerontaylor-dev/)

## Hi, I'm Cameron! 👋

This is the GitHub repository for my personal website.

<p align="center">
    <img alt="profile picture" src="https://camerontaylor.dev/img/profile.png" style="max-width: 256px" />
</p>

## Development

This site is built with [Zola](https://www.getzola.org/) and [tabi](https://welpo.github.io/tabi/). It was forked from [tabi-start](https://github.com/welpo/tabi-start).

### 1. Clone the Repository

The tabi theme is included as a git submodule.

```sh
git clone https://github.com/axis7818/axis7818.github.io
git submodule update --init --recursive
```

### 2. Install Zola

```sh
# on macOS
brew install zola

# on windows
winget install getzola.zola
```

### 3. Start the Development Server

```sh
zola serve
```

### Alternatively, Use Docker

```sh
docker run -u "$(id -u):$(id -g)" -v $PWD:/app --workdir /app -p 8080:8080 ghcr.io/getzola/zola:v0.19.1 serve --interface 0.0.0.0 --port 8080 --base-url localhost
```

## Publishing Updates

Changes pushed to the `main` branch of the GitHub repository are published to the public site. The static assets are built with the [build-and-deploy.yml](.github/workflows/build-and-deploy.yml) GitHub Actions workflow and [GitHub Pages](https://pages.github.com/) hosts assets in the [`gh-pages`](https://github.com/axis7818/axis7818.github.io/tree/gh-pages) branch of the repository.
