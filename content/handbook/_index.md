---
title: "Engineering Manager Handbook"   # page H1 / <title> — full identity; nav label stays "Handbook"
cascade:
  type: docs        # REQUIRED — turns the /handbook subtree into Hextra docs mode (sidebar tree). Build-verified (AD-14).
---

<!-- The Start Here landing (welcome copy + guided-path step cards + section grid +
     About nudge) is rendered by layouts/docs/list.html (Story 3.1, AD-19), which guards
     on the root path so only /handbook/ becomes Start Here; the 12 Section indexes keep
     Hextra's default docs section-list. The override lives at the docs list.html slot
     (not layouts/handbook/list.html): cascade type:docs makes Hugo resolve handbook
     section-list pages to the docs TYPE slot, and type beats section in the lookup order.
     This page intentionally has no body content — all landing markup is in the layout. -->
