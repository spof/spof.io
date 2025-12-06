# spof.io

Source for [spof.io](https://spof.io) based on [hugo](https://github.com/spf13/hugo)

## Prerequisites

- **Hugo 0.152.2 or later** - This site requires a modern version of Hugo to build correctly. You can download Hugo from the [official releases page](https://github.com/gohugoio/hugo/releases).

To verify your Hugo installation:
```bash
hugo version
```

## Local Development

To run the site locally for development:

```bash
hugo server
```

This will start a local development server, typically at `http://localhost:1313`. The server includes live reload, so changes to content or templates will automatically refresh in your browser.

To run the server with drafts enabled:
```bash
hugo server -D
```

## Building the Site

To build the site for production:

```bash
hugo --gc --minify
```

This command:
- Builds the site and outputs to the `public/` directory
- `--gc` runs garbage collection to clean up unused cache files
- `--minify` minifies the generated HTML, CSS, and JavaScript for optimal performance

The generated static files in `public/` can be deployed to any web server or hosting platform.

## Netlify Deployment

This site is configured for automatic deployment on Netlify. The deployment configuration is defined in `netlify.toml` at the repository root.

### Configuration Details

The `netlify.toml` file specifies:
- **Build command**: `hugo --gc --minify` - Builds an optimized production version
- **Publish directory**: `public` - The directory containing the generated static files
- **Hugo version**: 0.120.4 - Ensures consistent builds across all environments
- **Environment contexts**: Separate configurations for production and deploy previews

### Deployment Process

1. Push changes to your repository
2. Netlify automatically detects the push and triggers a build
3. Netlify uses the Hugo version specified in `netlify.toml`
4. The build command generates the static site
5. Netlify deploys the contents of the `public/` directory

Deploy previews are automatically created for pull requests, allowing you to review changes before merging to production.

## Troubleshooting

### Build fails with "command not found: hugo"

**Solution**: Ensure Hugo is installed and available in your PATH. On Netlify, verify that `HUGO_VERSION` is set in the `netlify.toml` file.

### Build fails with deprecation warnings

**Solution**: This site has been updated to use modern Hugo configuration syntax. If you see deprecation warnings, ensure you're using Hugo 0.120.0 or later and that `config.toml` doesn't contain deprecated parameters like `MetaDataFormat`.

### Theme not found error

**Solution**: Ensure the `themes/hyde-x` directory exists and contains the theme files. The theme should be committed to the repository.

### Changes not appearing on the site

**Solution**: 
- For local development: Check that `hugo server` is running and refresh your browser
- For Netlify: Verify the build completed successfully in the Netlify dashboard. Check the build logs for any errors.

### Static assets (images, CSS) not loading

**Solution**: 
- Verify files exist in the `static/` directory
- Check that the build completed successfully and files were copied to `public/`
- Ensure the `baseurl` in `config.toml` matches your deployment URL

### Build succeeds but site is blank

**Solution**: Check that the `publish` directory in `netlify.toml` is set to `"public"` and that the `public/` directory contains HTML files after building.
