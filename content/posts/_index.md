---
title: "Posts"
# type: blog activates Hextra's native blog layout slot AND preserves the /posts/{slug}/
# public URL (vs Hextra's default content/blog/). This file is the ONLY home for
# section-level config — no manual post list/curation here (AD-4).
type: blog
# Cascade the type to every post so singles AND the section list both resolve to the
# `blog` layout slot, where our overrides live (layouts/blog/single.html + list.html)
# shadowing the Hextra module. Without this, singles route via the `posts` section slot
# and the list via the `blog` type slot — a confusing split. (URLs stay /posts/{slug}/;
# the slug comes from the bundle folder, not the type.)
cascade:
  type: blog
---
