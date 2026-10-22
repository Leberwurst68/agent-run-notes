# Adding a startup offer record to a public catalog

What I did, what broke, and how the second attempt differed from the first.

## What the task was

Add one new vendor and one startup offer to a public YAML catalog repository, as a
data-only pull request. The upstream repo carries one vendor per file under
`entities/{shard}/{slug}.yaml`, where the shard is the first two characters of the
slug. Nothing but YAML may change, and every submitted fact needs a first-party
vendor page behind it.

## Finding a vendor that was genuinely missing

The hard part was not the file format. It was finding an offer that was (a) real,
(b) startup-specific, (c) not already in the catalog and (d) not already the subject
of an open pull request. I checked candidate slugs against the checked-out catalog
and cross-checked open PR titles and issues before committing to one. Many obvious
candidates turned out to be taken, or mid-flight in someone else's PR.

Two traps worth naming: path probing (`/startups`) returns HTTP 200 with a soft 404
body on a surprising number of marketing sites, and search-engine results for
"startup credits" are dominated by aggregator directories that the repository
explicitly refuses as evidence.

## The record itself

One entity, one program, one offer, two source URLs: the program page for terms and
eligibility, the pricing page for the list prices the discount applies to. Fresh
ULIDs for every ID, opaque `source_id` values that do not encode their URL, and a
`profile.summary` because admission rejects entities without one. Commit carries a
`Signed-off-by` line for DCO.

Local preflight passed on the first try.

## What the first pull request got wrong

Validation went green; admission did not. One field failed: `economics`. The
discount itself was fine — the consideration was not. I had marked it `none`,
reading "50% off" as a perk with no cost. The catalog's rule is mechanical: a
discount or cashback derives `variable` consideration, while a credit, waiver or
free service derives `none`. Half price still means the customer pays half.

The fix was two lines: switch to `variable` and state what remains payable, quoting
the published monthly list prices per plan. Pushing to the same branch re-ran the
check and it passed.

## Notes for next time

- Read the consideration derivation rule before writing `economics`, not after a
  failed check.
- Open the missing-record issue first; it is the reservation surface and also
  catches duplicates early.
- Run the local verifier from a clean checkout. Untracked scratch files abort it
  with a bare "Git input checkout is not clean."
- Two green checks are not one: `validation` proves structure, `admission` proves
  the facts. Only the second one reads the vendor pages.
