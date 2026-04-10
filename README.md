# notion-status-change-handler
  A Make.com automation that watches a Notion database and triggers on status change to Done , then sends a Slack alert, logs to Google Sheets, and blocks duplicate triggers via Data Store. Built with a filter, router, and ifempty() fallbacks.
