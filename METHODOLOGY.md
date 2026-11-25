# Token Radar: methodology

This document explains how the board is built, what it means, and what it
does not mean. It is written for a reader who wants to check the ranking
rather than take it on trust.

## The question

A protocol with no token is a company with no share price. The radar asks:
if it launched a token tomorrow, what would that token be worth? The answer
is an estimate built from what the market already pays, not a forecast of
what the market will pay. A forecast would need to guess the future; this
board only reads the present.

## The data

The only input is a sealed snapshot of every protocol DefiLlama tracks. For
each protocol the snapshot gives total value locked (TVL), its category, the
chains it runs on, its age, and its seven-day TVL change. The snapshot is
sealed, which means the board is a picture of one moment: it does not update
itself, and it does not fetch anything from the network when the program
runs. Re-running the program on the same snapshot gives the same board,
byte for byte.

The snapshot also tells the program which protocols already have a token.
Those protocols are not on the board. They are the price reference: the
market has already valued them, and their valuations are what prices the
rest.

## The model: comparable multiples

The model is called radar-2. It prices a pre-token protocol the way the
market prices the same kind of protocol that does have a token, per dollar
of TVL.

For each category, the model takes the median market-cap-to-TVL ratio among
that category's live-token protocols. That median is the category's multiple,
called comps. A median is used instead of a mean because a single
outlier (one protocol with an unusually high or low ratio) should not move
the price of a whole category.

The median is then shrunk toward the global median. The shrinkage is the
part of the model that handles thin categories: a category with only one or
two live tokens gives a noisy median, so the prior (the global median, with
weight 3) pulls it back toward the market as a whole. A category with many
live tokens barely moves; a category with few moves a lot. The shrunk
multiple is clamped to the range [0.05, 1.00]. The upper clamp says no
token is worth more than the value its protocol secures. The lower clamp
says no protocol is worth nothing.

The implied value of a protocol is its TVL times its category's multiple,
adjusted three ways:

1. **Growth.** A protocol whose TVL rose over the last seven days earns a
   premium; one whose TVL fell takes a discount. The seven-day change is
   clamped to [-50, +150] percent and enters at 60 percent weight, so a
   single hot week cannot dominate the valuation.
2. **Age.** A protocol that has survived a long time without a token is
   more likely to launch one, and to launch a valuable one. Age enters as
   a factor from 0.6 (brand new) to 1.0 (three years or older). Youth is a
   discount, never a disqualification. When the snapshot gives no age, the
   model assumes one year.
3. **Breadth.** A protocol live on several chains is worth more than the
   same TVL on one chain, because each chain is another distribution
   channel. Each extra chain, up to four, adds 5 percent.

The formula is:

    implied = TVL x comps x growthFactor x ageFactor x chainFactor

rounded to the nearest dollar. The board is sorted by implied value,
highest first. Ranks run 1 through N with no arbitrary tie-breaking: two
protocols with the same implied value keep the snapshot's order.

## The board

The board has 226 rows. It covers the whole 40-protocol watchlist (the
floor, never a ceiling) and adds every earlier pre-token protocol below the
$50M floor, by TVL. The 24 live-token protocols are set aside in a
separate view: their market-cap-to-TVL ratios are what prices the board,
so they are shown as the reference, not as candidates.

The top of the board is steep. The first row, Steakhouse Financial, is
valued at $2.35B; the second, Gauntlet, at $1.41B; the board then falls
through Nonce Capital ($283.1M), RockawayX ($214.1M), and Veda ($197.4M)
down to the bottom row, Valdora Finance, at $354,915. That shape is the
point of the radar: a small number of pre-token protocols hold most of the
implied value, and the board exists to find them.

## The farm plan

Each row carries a seven-step farm plan: an action, a repeat action, a
weekly action, a hold, a claim, and a log. The plan is derived from the
protocol's own figures (its category, chain count, TVL, and age), not
written by hand. A larger, older, multi-chain protocol gets a bigger,
slower plan; a small, young, single-chain one gets a leaner plan. Every
step carries a cost and a time, and every step's text is between 20 and
600 characters.

The plan is a starting point for a farmer, not financial advice. It says
what a reasonable person would do to position for a launch; it does not
say the launch will happen, or that it will be profitable.

## The dashboard

The dashboard is a static page. It reads the board from a data file
written beside it and renders it with no network and no build step. It
offers search, category and chain filters, a minimum TVL, a gainers-only
switch, pre-token/live tabs, and sortable columns (including the comps
multiple, so a reader can re-rank by the model's inputs). A detail drawer
per protocol shows the full equation and the farm plan. A model panel
states the formula and lists the top ten comparables. A sandbox with three
sliders re-prices the top five live protocols, so a reader can feel how
sensitive the board is to the model's assumptions.

The page recomputes every row from the data file and checks that its
numbers match the board file. If they ever disagreed, the page would be
wrong, not the board.

## Limitations

Four limits matter.

- The board is only as good as the snapshot. If the snapshot's TVL is
  stale, gamed, or mis-categorized, the ranking inherits the error. The
  model does not audit the snapshot.
- A multiple is a blunt instrument. Two protocols in the same category
  with the same TVL get the same multiple, even if one has a better team,
  a better product, or a worse token design. The adjustments (growth, age,
  breadth) soften this, but they do not remove it.
- The board says nothing about whether a protocol will launch a token, or
  when. A high implied value is a reason to watch a protocol, not a
  promise that it will pay.
- The farm plan is generic by construction. It is derived from four
  numbers, so it cannot know a protocol's specific quests, points, or
  eligibility rules. A farmer should read the protocol's own docs before
  acting.

## Reproducing the board

Run the entry program (Node 22, standard library only) and it writes the
board, the dashboard data, and the dossier. Run it twice into two
different directories and the three files come out byte-identical: there
is no clock, no random number, and no network in the scoring path. The
only input is the snapshot, and the only output is the board, the data
file, and the dossier.
