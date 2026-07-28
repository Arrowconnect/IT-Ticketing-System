# Arrow Pipes & Fittings — IT Ticketing System

Internal IT support ticketing tool. Employees raise tickets through a web form;
IT staff triage them from an admin page. Backend is an n8n workflow hosted at
`n8n.arrowpipes.site`.

## Contents

| File | What it is |
|------|------------|
| `it-ticket-form.html` | Employee-facing form to raise a support ticket |
| `it-ticket-admin.html` | Admin page to view all tickets and set priority/status (gated by an admin key) |
| `workflow/arrow-it-ticketing.n8n.json` | Backup export of the n8n workflow (no secrets) |

## How it works

```
Employee form  ──POST──►  /webhook/it-ticket-submit  ──►  save to DataTable  ──►  email IT (Outlook)
Admin page     ──GET───►  /webhook/it-tickets-list   ──►  return all tickets   (header-auth)
Admin page     ──POST──►  /webhook/it-tickets-update ──►  update priority/status (header-auth)
```

### Live endpoints (n8n workflow: "Arrow Pipes - IT Ticketing System")

- `POST https://n8n.arrowpipes.site/webhook/it-ticket-submit`
- `GET  https://n8n.arrowpipes.site/webhook/it-tickets-list`  *(requires `X-Admin-Key` header)*
- `POST https://n8n.arrowpipes.site/webhook/it-tickets-update` *(requires `X-Admin-Key` header)*

Tickets are stored in the n8n **DataTable** `IT_Tickets` (id `JzQhEIveeNkhauOD`).
Notifications are sent via the **Microsoft Outlook** credential configured in n8n.

## Deployment

Both HTML pages are static — host them anywhere employees can reach (internal web
server, intranet share, GitHub Pages, etc.). The webhook URLs are already baked
into each page's `<script>` block; update them there if the n8n host changes.

- `it-ticket-form.html` → give the URL to all staff.
- `it-ticket-admin.html` → give the URL only to IT staff, plus the admin key.

## Admin key

The admin page prompts for a key that is sent as the `X-Admin-Key` header and
validated by n8n's header-auth credential (**IT Ticketing Admin Key**). The key
itself lives only in n8n — it is **not** stored in this repo.

## Restoring the workflow

Import `workflow/arrow-it-ticketing.n8n.json` into n8n, then reattach the two
credentials (Microsoft Outlook account, IT Ticketing Admin Key) and the
`IT_Tickets` DataTable, since credential secrets are never exported.
