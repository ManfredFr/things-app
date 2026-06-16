# Things URL Scheme

Reference for the `things:///` URL scheme — the documented interface for adding
and updating items in Things.

> Placeholder. Fill in from the official docs:
> https://culturedcode.com/things/support/articles/2803573/

## Commands (overview)

- `things:///add` — add a single to-do
- `things:///add-project` — add a project (optionally with to-dos)
- `things:///update` — update an existing to-do (requires auth token)
- `things:///show` — navigate to a list or item
- `things:///search` — open search

## Notes

- Write commands (`add`, `add-project`) do not require an auth token.
- Read/update commands require the auth token from
  Things → Settings → General → Enable Things URLs → Manage.
