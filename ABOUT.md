# Zano Circle

*A savings circle where nobody can run off with the pot. Built for Zano's EVM layer, settling in fUSD.*

## What it is

Zano Circle is a savings-circle app where nobody can run off with the pot. A small group of people who already trust each other each put in the same amount every week, and each week one of them takes the whole pot, in a fixed order, until everyone has had a turn. Every culture has this: ajo and esusu in Nigeria, kitty and committee in India, tanda in Latin America, hui in China. Hundreds of millions of people save this way today, on paper and in group chats, and the only thing that ever goes wrong is the person holding the money.

Zano Circle replaces the person holding the money with a smart contract. Members pay in fUSD (Freedom Dollar, Zano's stablecoin), the contract holds the pot and pays it out, and the app on their phone is a stamp card that shows who has paid. There is no gas to buy, no seed phrase to write down, no bank account and no identity check. It is built for Zano's EVM layer and runs today on a local EVM with a public demo anyone can tap through.

## The problem, and why it is still open

The failure mode of every savings circle is the same: the organizer holds the cash, so every theft, dispute and robbery lands on one person, and a member who has already collected can simply stop paying. The circles work because of trust, and they break for the same reason.

Every crypto attempt to fix this has died. WeTrust (Ethereum, 2017) is dormant and delisted. HaloFi, formerly GoodGhosting, is out of business and its repos are archived. Bloinx pivoted away and went quiet. The newer attempts on Stellar, Hedera and Sui repeat the same mistakes: they demand collateral from people who have none, match strangers at random, or turn reputation into a tradeable token.

The only thing that works at scale is not crypto. Money Fellows in Egypt has over eight million users and charges up to 16 percent for the promise that the pot is always full, and it gets there by requiring ID, bank statements and credit checks, which excludes exactly the people who need it most.

Nobody has built the version that digitises groups that already exist, asks for no collateral, keeps members private and costs nothing to use during the pilot, with a fee cap of 1 percent written into the contract for good. That is the gap Zano Circle is built for.

## How it works

An organizer creates a circle: the members, the weekly amount, the payout order. Members join from an invite link. Each week everyone pays in, the contract pays the pot to that week's collector automatically when the last payment lands, and the stamp card on every phone updates. Four members at 100 fUSD a week means a 400 fUSD pot every week for four weeks.

The parts that make it hold up:

- **The pot is always full.** The organizer posts a bond before anyone joins. If a member misses, the bond tops up the pot so the collector is paid in full, and the missing member owes the organizer, not the group. The organizer keeps their role, their members and their income, and loses the part where they carry the cash.
- **No gas, no seed phrase.** Each member gets a contract account created from a key that lives on their phone. A relayer submits their transactions and pays the gas in ZANO, so a member only ever sees fUSD in and fUSD out.
- **Lose your phone, keep your money.** Guardian recovery: two members of your circle can approve moving your account to a new phone. Your circle has your back, which is what a circle is for.
- **A badge for finishing.** Completing a circle mints a non-transferable badge. Trust that was only ever in someone's head becomes a record the next organizer can see.
- **An escape hatch.** A majority can dissolve a circle mid-run and everyone gets their contributions back; a refund that cannot be delivered is held for that member to claim later, so one bad address can never block the exit.
- **Your group chat is the talking layer.** The app never tries to be the group chat. It hands the stamp card and the messages that matter (paid, pot day, reminder) to whatever app is already on your phone: WhatsApp, Telegram, SMS, anything with a share button. The chat can say anything; the card is the truth.

## What is built and proven

This is a working system, not a mockup. Everything below has been run end to end, with real transactions, on two physical devices.

| Layer | What exists | How it is proven |
| --- | --- | --- |
| Contracts (Solidity, Foundry) | Factory, RoscaCircle (rounds, bond, debt, dissolution, late-slot sweep), MinimalAccount (gasless contract accounts), guardian recovery, CircleBadge | 128 tests across 15 suites, all green: unit, invariant and fuzz suites (random bonds, misses and repayments, conserved to the wei), a size-20 boundary suite, and a poisoned-recipient drill that aims a false-returning and a reverting token at every transfer site |
| Relayer (Node, ethers) | Onboards accounts, relays signed user operations, pays gas, canonical-address check, exit logging | Served every live transaction in the demo; survives restarts; every exit path writes a log line |
| App (single HTML file, no framework) | Join, pay, stamp card, pot day, protection explainer, share image, invite links, recovery flow, FAQ, two regional presets, light and dark | Driven from a phone and a laptop against a live chain: three full circles run through, a fourth joining, a settled week and the pot screen captured on device |
| Demo | Public, tap-through, no wallet needed | Live at the link below |

The contract design has had an adversarial pass: a threat-model appendix comparing it against WeTrust's published ROSCA contract, and a seeded-bug audit challenge used to test reviewers. The one real finding from that work, that a reverting token could block dissolution, was closed and is now covered by the test suite.

## Why it matters for Zano

It gives fUSD something to do. A stablecoin needs a reason to be held, not just traded, and a weekly savings commitment is the most natural one there is: the same amount, every week, to people you know.

It brings in users who are not crypto people. Nobody has to be taught what a savings circle is; their family already runs one. The pitch is the committee system where nobody can run off with the money, and that sentence lands with hundreds of millions of people who have never heard of a wallet. Onboarding is one organizer at a time, and each one brings their whole group.

It is the kind of thing ZEL exists for. Zano stays the private layer; the execution layer is where ZANO and fUSD get used. Zano Circle is a complete, tested, gasless dApp waiting for that layer, built so that moving to it is a configuration change, not a rewrite.

And it is honest about privacy. Members are addresses on a public EVM; the app does not claim otherwise. What it protects is the thing that actually gets stolen in a savings circle, which is the pot.

## Where it stands

Zano Circle runs on a local EVM (Anvil) today, with the full stack live and a public demo. It was deploy-ready for the ZEL testnet in September and had confirmed the chain was reachable when the Gateway Address incident took Zano back to before Hard Fork 6. ZEL and the bridge are offline while the team completes its audit, and nothing in Zano Circle depends on the feature that was rolled back.

The plan is deliberately pilot before features. The next steps, in order:

1. Keep rehearsing on the local chain: full circles, misses and bond cover, recovery, dissolution, on real phones.
2. Deploy to the ZEL testnet the day it returns, with a test token standing in for fUSD.
3. Run one real circle at tiny stakes with one trusted organizer, with fUSD on ZEL when it is available.
4. Only then add anything: organizer tooling, badges that unlock earlier pots, a names service.

What would help from the ecosystem: ZEL testnet access and test funds when they return, early word on fUSD on ZEL, and introductions to communities where circles already run.

## Try it

- Live demo (tap through a circle, no wallet needed): [honeytones.github.io/circle-demo](https://honeytones.github.io/circle-demo/). Add `?v=in` for the kitty preset.
- App source: this repository (branch `main`).
- Contracts, tests and relayer: in the `zano-circle` repository, currently private; the test suite and contract source are available to reviewers on request, and will be published with the first testnet deployment.
- Contact: @TappyoakTony on X, or on the Zano forum.
