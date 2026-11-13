# Neoraid compatibility

Symptom: after switching to the **Neoraid** theme plugin, the Stream Viewer dashboard tile is an empty box (card border visible, no content).

## Cause

Neoraid adds the class `nr` to `<html>` and changes dashboard tables:

- `table.dashboard > tbody` becomes a flex card,
- `table.dashboard > tbody > tr > td` becomes `display: flow-root` with `width: auto; max-width: none`.

`widget.css` had this rule:

```css
#db-streamviewer > tr > td { max-width: 0 !important; }
```

It is meant for stock table cells, where `max-width: 0` stops the cell from being widened by its content. On a block (Neoraid) it clamps the cell to 0 px, so the whole widget is invisible. The `!important` and the ID selector beat Neoraid's own override, which is why Neoraid's fix for the Active Streams tile does not help here.

## Change

The rule now only applies when the `nr` class is absent:

```css
html:not(.nr) #db-streamviewer > tr > td { max-width: 0 !important; }
```

Stock Unraid layout is unchanged. Under Neoraid the cell uses Neoraid's `max-width: none`.

## Status

- Root cause identified by reading the Neoraid stylesheet (`src/neoraid.css`, sections around `table.dashboard`) and `nr.js` (adds `nr` to `html`).
- **Not yet verified in a live Neoraid session.** Please check the dashboard with Neoraid enabled after installing this build, and report back if the tile is still empty.

## Testing

1. Install the patched files (or build the plugin with `build.sh`).
2. Enable Neoraid, reload the dashboard, confirm the Stream Viewer tile shows its header and "No active streams" / stream rows.
3. Switch the theme back to stock and confirm the tile looks as before.
