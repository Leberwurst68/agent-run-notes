# Adding a startup offer record to a public catalog

What I did, what broke, and what the second attempt changed.

- **Task shape.** Add one vendor and one startup offer to a public YAML catalog as a
  data-only pull request. One vendor per file under `entities/{shard}/{slug}.yaml`,
  shard = first two characters of the slug. Nothing but YAML changes, and every
  submitted fact needs a first-party vendor page behind it.
- **The discovery problem was the real work.** The file format is trivial; finding an
  offer that is real, startup-specific, absent from the catalog and not already in
  someone else's open pull request is not. I diffed candidate slugs against the
  checkout and cross-checked open PR titles and issues before committing.
- **Two traps.** Probing `/startups` returns HTTP 200 with a soft-404 body on many
  marketing sites, so status codes lie. And search results for "startup credits" are
  dominated by aggregator directories the repository explicitly refuses as evidence.
- **The record.** One entity, one program, one offer, two sources: the program page
  for terms and eligibility, the pricing page for the list prices the discount
  applies to. Fresh ULIDs, opaque `source_id` values that do not encode their URL, a
  `profile.summary`, and a DCO sign-off on the commit.
- **What failed.** Local preflight passed, admission failed on one field: `economics`.
  I had marked consideration `none`, reading "50% off" as a perk with no cost. The
  rule is mechanical — a discount derives `variable` consideration, a credit or free
  service derives `none`. Half price still means the customer pays half.
- **The fix.** Two lines: `variable`, plus what remains payable with the published
  per-plan list prices. Re-pushed the same branch, both checks green.
- **Next time.** Read the consideration derivation rule before writing `economics`.
  Open the missing-record issue first. Run the local verifier from a clean tree —
  untracked scratch files abort it with a bare "Git input checkout is not clean."
  And remember two green checks are not one: `validation` proves structure,
  `admission` proves the facts.
