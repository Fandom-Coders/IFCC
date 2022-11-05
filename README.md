# Interim Fandom Coders Congress

This repository serves as an informal, interim site of discussion for
**the Fandom Coders community** on topics of interoperability,
decentralization, and federation.

**The proposals in this repository are of an experimental nature and
make no guarantees of fitness for any purpose. Implementers must make
their own determinations regarding when a proposal is “mature” enough
for implementation.**

## About Fandom Coders

See <https://fancoders.com>.

## About this Repository

This repository constitutes a site of discussion which is both
**interim** and **provisional**, with the goal of allowing
interoperability work on fannish projects to make progress in lieu of
more formal mechanisms.

This repository is under stewardship of [kibigo!][kibigo!], and it is
expected that all contributions conform to the Fandom Coders
community guidelines and code of conduct (at present these are the same
as the [Bobaboard <cite>Expectations &amp; Code of
Conduct</cite>][Boba-CoC]).

### Process

> This process is very informal and decentralized; anybody can submit a
> proposal, and any proposal with interoperable implementations (from
> within the Fandom Coders community) will be upgraded to `implemented/`
> status.
>
> In a future, non‐provisional, non‐interim Fandom Coders Congress,
> there may be specific requirements on proposals in order for them to
> be accepted, or there may be additional stages for distinguishing
> when proposals are “ready for implementation” versus not. For the
> time being, **this is left to the discretion of implementors and
> proposal authors**.

1. Someone (“You”) comes up with an idea for some technology or
   protocol with interoperability requirements between implementers.
   You discuss this idea with people in the Fandom Coders community
   through some channel, for example the Fandom Coders Discord or the
   Discussions on this repo. You decide that the idea has enough merit
   to justify further exploration.

2. You write up your idea as a Markdown document and submit a Pull
   Request to this repository to add it to the `proposals/` directory.

   > If you need help doing this, reach out!
   >
   > Proposals do not need to be “complete” to be accepted, but they
   > should at least clearly communicate the problem which is being
   > solved and a general approach for arising at a solution.

3. If your proposal is deemed “in scope”, it will be assigned a number
   of the form `IFCC-####` and merged into this repository.

   > Proposals are “in scope” if they have relevance to fannish
   > projects and have some kind of technical interoperability
   > requirement. These terms are interpreted loosely.
   >
   > Examples of proposals which would not be considered “in scope” are
   > proposals which do not pertain to technology or proposals which
   > only have relevance to a single fannish project.
   >
   > If there is an obvious forum which is “more appropriate” for your
   > proposal, we may suggest that you make your proposal there
   > instead.

4. Further discussion on the proposal can take place through the
   Issues, Pull Requests, and Discussions of this repository. These
   discussions **should** note the number of the proposal to make them
   easy to find.

5. One of the following happens :—

   1. Your proposal is implemented in such a way that two or more
      services (websites, technologies) are interoperating using it. At
      this point, the proposal is moved to the `implemented/` directory
      to signal that further work needs to be done in co·ordination
      with existing implementations.

   2. It is decided that your proposal is a bad idea and **should not**
      be implemented, for example because of security or ethical
      concerns. At this point, the proposal is moved to the
      `withdrawn/` directory to signal that implementers **should not**
      attempt to implement it.

6. Further work on the proposal may still occur after the proposal has
   left the `proposals/` directory, bearing in mind the considerations
   above.

[Boba-CoC]: <https://docs.bobaboard.com/docs/volunteering/code-of-conduct/>
[kibigo!]: <https://go.KIBI.family/About/#me>
