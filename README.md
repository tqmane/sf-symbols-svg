# SF Symbols to SVG

<a href="https://github.com/MoOx/sf-symbols-svg?sponsor=1">
  <img width="140" align="right" alt="Sponsoring button" src="https://github.com/moox/.github/raw/main/FUNDING.svg">
</a>

[![GitHub package.json version](https://img.shields.io/github/package-json/v/MoOx/sf-symbols-svg) ![npm downloads](https://img.shields.io/npm/dm/sf-symbols-svg)](https://www.npmjs.com/package/sf-symbols-svg)
[![GitHub Workflow Status](https://img.shields.io/github/actions/workflow/status/MoOx/sf-symbols-svg/build.yml?branch=main)](https://github.com/MoOx/sf-symbols-svg/actions)
[![License](https://img.shields.io/github/license/MoOx/sf-symbols-svg)](https://github.com/MoOx/sf-symbols-svg)  
![My website moox.io](https://img.shields.io/badge/%F0%9F%8C%8D%20-https%3A%2F%2Fmoox.io-gray?style=social)
[![GitHub followers](https://img.shields.io/github/followers/MoOx?style=social&label=GitHub)](https://github.com/MoOx)
[![LinkedIn Follow](https://img.shields.io/badge/LinkedIn-%20?style=social&logo=invision&logoColor=%230077B5)](https://www.linkedin.com/in/maxthirouin/)
[![BlueSky Follow](https://img.shields.io/badge/BlueSky-%20?style=social&logo=bluesky)](https://bsky.app/profile/moox.io)
[![X Follow](https://img.shields.io/twitter/follow/MoOx?style=social&label=)](https://x.com/MoOx)

> SF Symbols to SVGs. Period.

[Apple SF Symbols](https://developer.apple.com/sf-symbols/) is an icon set with more thant 6,000 symbols.
Unfortunately, this are not available on the web.
This tool solve this.

> [!WARNING]
> This tool requires you to have the [_SF Pro Text_ font](https://developer.apple.com/fonts/) installed on your system or in a custom directory.

> [!TIP]
> SF Symbols to SVG can be coupled with [React from SVG](https://github.com/MoOx/react-from-svg) to generate React components from SVGs.

## Usage

SF Symbols to SVG is available as a CLI tool:

```console
npx sf-symbols-svg --help
```

#### Available Options

```console
--size, -s       Font size for symbols (default: 24)
--padding, -p    Padding in pixels (default: 2)
--weight, -w     Font weights to include (default: regular)
                 Can specify multiple: -w regular -w bold
--output, -o     Output directory (default: ./sf-symbols-svgs)
--fonts-dir, -f  Directory containing SF Pro Text fonts (default: /Library/Fonts)
--sf-version     SF Symbols version to use (default: latest)
--sources-dir    Directory containing SF Symbols data files (default: ./sources)
--icons-list     Path to a file containing a list of icons to process, one name per line (optional)
--help, -h       Show this help message
--version        Show version of SF Symbols supported by this tool.
```

> [!NOTE]
> SF Symbols to SVG will always try to use the latest version of SF Symbols supported by this tool.
> You can check in the `sources/` directory to see which versions are supported.

#### Examples

```console
# Generate SVGs for the latest version of SF Symbols, in 24x24 SVGs with 2px padding, in ./svg-symbols-svgs folder
npx sf-symbols-svg --weight all

# Generate SVGs with larger size and padding
sf-symbols-svg --size 32 --padding 4

# Generate SVGs for multiple weights
sf-symbols-svg --weight bold --weight black

# Specify custom output directory
sf-symbols-svg --output ./my-icons

# Specify a different SF Symbols version (if available)
sf-symbols-svg --sf-version 6.0

# Combine options
sf-symbols-svg --size 48 --padding 8 --weight light --weight regular --weight bold --output ./custom-icons --fonts-dir /Users/moox/Library/Fonts

# Process only specific icons
sf-symbols-svg --icons-list /path/to/your/icons-list.txt
```

## Creating a new version

When Apple ships a new SF Symbols version, add it to this tool by creating a new
folder in `sources/`. The tool detects versions from the folder names and uses
the most recent one as the default (see [SF Symbols Versions](#sf-symbols-versions)).

1. Create a new directory in `sources/{version}/` (example: `sources/8.0/`).
   Use a plain numeric name (`8.0`, not `8.0-beta`) so version sorting keeps
   working — the tool compares versions numerically.
2. Extract the character mappings from the SF Symbols app:
   - Get the [SF Symbols app](https://developer.apple.com/sf-symbols/) and open it
   - Switch to the **list view** (the list icon in the toolbar)
   - Select all symbols (`cmd + A` or `Edit` > `Select All`)
   - Press the **right arrow key** to expand every group, so all variants
     (`.fill`, `.circle`, `.slash`, numbered variants, …) are revealed and
     selected — see the note below
   - With everything selected, right click and press `Copy {x} symbols as Text`
   - Paste into a file at `sources/{version}/symbols.txt`
   - Right click again and press `Copy {x} names`
   - Paste into a file at `sources/{version}/names.txt`
   - Both files must have the **same number of entries**, aligned line by line
     (the tool pairs them by index). `symbols.txt` may be one glyph per line or
     a single continuous string — both are supported.

   > [!IMPORTANT]
   > Since SF Symbols 8, the app **groups symbol variants** under a single base
   > symbol in the default grid view. A plain "Select All + Copy" there only
   > copies the base of each group and silently drops thousands of variants. Use
   > the **list view** and **expand all groups with the right arrow key** before
   > copying so the full set is captured. After copying, sanity-check the counts:
   > the number of lines in `symbols.txt` and `names.txt` must match, and should
   > be close to the symbol count the app shows on launch (e.g. 7151 for the
   > SF Symbols 8 beta).

3. Make sure you have the matching _SF Pro_ font version installed (see
   [Font Compatibility](#font-compatibility)).
4. Test the new version locally before releasing:

   ```console
   # Generate every symbol of the new (now default) version, all weights
   npm run dev

   # Or target a subset to iterate quickly
   node --experimental-strip-types ./src/index.ts --sf-version 8.0 --icons-list ./src/test-icons.txt --output ./test-output

   # Run the test suite
   npm run test
   ```

That's it! The tool will automatically detect the new version and use it as the
default (since it's the most recent).

### Releasing a beta version

When a new SF Symbols version is still in beta, Apple may add or rename symbols
before the final release. Ship it as a prerelease so it does not become the
default `npm install` for everyone:

1. Add the version folder as above (e.g. `sources/8.0/`) and fill in
   `symbols.txt` / `names.txt` from the **beta** SF Symbols app.
2. Set a prerelease version in `package.json` (e.g. `8.0.0-beta.123`).
3. Publish under the `beta` dist-tag so it does not become the default
   `latest`:

   ```console
   npx npmpub --tag beta
   ```

Users opt into the beta explicitly:

```console
npm install sf-symbols-svg@beta
# or
npx sf-symbols-svg@beta --weight all
```

> [!IMPORTANT]
> Once the SF Symbols version is final, re-extract the data from the stable app
> (the beta is often missing or renames symbols), set the version to the stable
> `x.y.z`, and release it normally with `npm run release` so the stable version
> becomes the default (`latest`).

## About SF Symbols Versions and Font Compatibility

### SF Symbols Versions

This tool automatically detects supported SF Symbols versions by scanning the `sources/` directory. Each version requires its own data files (`symbols.txt` and `names.txt`) which are already included in the repository for some versions.

The tool will automatically use the most recent version as the default, but you can specify a different version using the `--sf-version` option. If no matching versions are detected in the `sources/` directory, the tool will display an error message.

To add support for a new SF Symbols version, see [Creating a new version](#creating-a-new-version).

### Using a Custom Sources Directory

If you want to use a different directory for your SF Symbols data files, you can specify it with the `--sources-dir` option:

```sh
sf-symbols-svg --sources-dir /path/to/your/sources
```

The custom sources directory must follow the same structure as the default one:

```
sources/
  ├── 6.0/
  │   ├── symbols.txt
  │   └── names.txt
  ├── 6.1/
  │   ├── symbols.txt
  │   └── names.txt
  └── ...
```

The tool will automatically detect available versions from the provided directory and use the most recent one as the default.

## Processing Only Specific Icons

If you want to process only a limited subset of icons, you can create a text file with one icon name per line and use the `--icons-list` option:

```sh
sf-symbols-svg --icons-list /path/to/your/icons-list.txt
```

Example of an icons list file:

```
moon.stars.fill
puzzlepiece
amplifier
figure.hiking
```

This is particularly useful for:

- Testing the tool with a smaller set of icons
- Generating only the specific icons you need for your project
- Reducing processing time when you only need a few symbols

### Font Compatibility

> [!WARNING]
> SF Symbols requires specific _SF Pro Text_ font versions that match the SF Symbols version you're using. If the font versions don't match, the symbols may not render correctly.

> [!CAUTION]
> The Apple SF Symbols app will display a warning at the top of the application if your installed fonts don't match the expected version. Make sure to check this warning and install the appropriate font version from [Apple's website](https://developer.apple.com/fonts/).

### About Font Files

To use this script, you need to have the SF Pro Text fonts installed on your system or in a custom directory.

#### Using system-installed fonts (recommended)

If you have SF Pro Text fonts installed on your system (typically in `/Library/Fonts`), the script will automatically find and use them. This is the default behavior.

```console
# Use fonts from the default location (/Library/Fonts)
sf-symbols-svg
```

#### Using fonts from a custom directory

If your fonts are installed in a different location, you can specify it with the `--fonts-dir` option:

```console
# Use fonts from a custom location
sf-symbols-svg --fonts-dir ~/Library/Fonts
````

#### Installing SF Pro Text fonts

If you don't have the fonts installed:

1. Download SF Pro font from [Apple's website](https://developer.apple.com/fonts/).
2. Install the font using the provided installer.
3. The fonts will be installed in `/Library/Fonts` by default.

---

> [!NOTE]
> For legal reasons, this repository does not include the SF Pro Text font files. You must download and install them from Apple's website. Make sure to use font versions that are compatible with the SF Symbols version you are using (check for warnings in the SF Symbols app).
