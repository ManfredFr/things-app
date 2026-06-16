# Things AppleScript

Reference for reading and scripting Things via AppleScript / `osascript`.

> Placeholder. Fill in from the official AppleScript guide:
> https://culturedcode.com/things/support/articles/2803572/

## Examples

```applescript
-- Count to-dos in the Today list
tell application "Things3" to count of to dos of list "Today"
```

```applescript
-- List names of to-dos in the Inbox
tell application "Things3" to name of to dos of list "Inbox"
```

## Notes

- The scripting target is `Things3` (the app's scripting name).
- Use AppleScript for richer reads/queries; use the URL scheme for writes.
