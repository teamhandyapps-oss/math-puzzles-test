# Icon font for /app/ (fonts-mc.css)

The Expo web bundle (p1.js) registers its icon font from an `/assets/node_modules/...`
path that is not published to this site. `fonts-mc.css` fixes that by embedding a
subset of the bundled MaterialCommunityIcons font as a base64 data URI (woff2, ~2 KB),
registered under both family names the app uses: `material-community` and
`MaterialCommunityIcons`. It covers every icon name in the current bundle.

## Regenerate when the app adds NEW icon names (or icons turn to tofu after a rebuild)

1. List icon names used by the published bundle:
   `grep -oE '(icon|source):\\?"[a-z0-9-]+\\?"' app/p1.js | sort -u`
2. Map names to codepoints with the glyphmap in the app repo:
   `node_modules/@expo/vector-icons/build/vendor/react-native-vector-icons/glyphmaps/MaterialCommunityIcons.json`
3. Subset the bundled ttf (source: `@expo/vector-icons@15.1.1` vendor font,
   md5 `6e435534bd35da5fef04168860a9b8fa`):
   `pyftsubset MaterialCommunityIcons.ttf --flavor=woff2 --unicodes=U+XXXX,U+YYYY,...`
4. Base64 the woff2 into `fonts-mc.css` in both `@font-face` rules.
5. Verify live: `document.fonts` shows `material-community:loaded` and icons render
   on a phone viewport. A `material-community:error` entry from the bundle's own
   registration attempt is expected and harmless while the full ttf is unpublished.

If the full ttf is ever published at the bundle's original
`/assets/node_modules/@expo/vector-icons/build/vendor/react-native-vector-icons/Fonts/MaterialCommunityIcons.6e435534bd35da5fef04168860a9b8fa.ttf`
path, this subset file can stay (it just becomes one of two same-origin sources).
