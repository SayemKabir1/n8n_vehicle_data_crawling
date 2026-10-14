# Security checklist before publishing

- [ ] Review every file staged for commit.
- [ ] Do not add `.env`, `.n8n/`, n8n database files, or `N8N_ENCRYPTION_KEY`.
- [ ] Do not export or upload n8n credential backups.
- [ ] Do not add Google OAuth Client Secret, app password, access token, or refresh token.
- [ ] Use your own n8n credential manager after importing the workflow.
- [ ] Confirm `Send Email` uses a placeholder recipient in this template.
- [ ] Keep actual input/output workbooks private.
- [ ] Review workflow JSON for email addresses, local paths, IDs, and API keys before every upload.
- [ ] Use only permitted URLs/data and respect website terms.

If a real secret was ever committed to a public repository, revoke/rotate it immediately. Deleting a file in a later commit does not remove the secret from Git history.
