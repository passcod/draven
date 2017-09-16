# Draven

A Github command bot, à la [bors], but for requesting, taking, and managing
issue assignments, specifically in medium-to-large open source projects.

## The background

Issue requests are when someone takes on a task. In many projects, and
especially in open source where involvement is in one's free time, one may end
up not actually doing that task, for various reasons. In this case, often
others may be stuck in a holding pattern of not being sure if anything is going
on, and not wanting to be too insistent, to put more burden on someone who
could already be very busy.

Additionally, while personal interaction does add value in warmth to projects,
in some cases it could be difficult to either admit or tacitly imply that one
is lazy or even simply forgot. Even with a rigorous process or even a bot doing
it, it can feel disconcerting, awkward, or discouraging.

## The idea

The idea is to replace some of these interactions with _passive_ processes.

Instead of having to say "No, I'm sorry, I won't be able to take it on", or to
actually remember to do so, or to admit a long time later that yeah, you
forgot, or to take time in your suddenly very busy life to say so, or any other
scenario around this, Issue Requests simply _expire_ after a configurable time.
You don't need to do anything. It saves not only time, but cognitive burden.
For the others waiting around the issue, it also brings certainty.

It does so while being very clear about what's happening and when, without
needing to read or remember specific policies, and without needing humans to
_action_ the automatic parts of those policies.

## The specific idea

Draven is to be a bot that answers to a specific, terse, unambiguous, and
simple to parse (both for humans and computers) format, then performs simple
communication with an emphasis on minimising cognitive overhead and burden on
both maintainers and collaborators.

As a draft, a typical interaction would look like this:

1. Issue #123 seems available to be worked on. Janine thinks she can do so, so
   she tells Draven to put a request on the issue for her. She thinks she'll
   have time in the next three days, but is not sure when. So she posts a
   comment, with `req=3d` on a line/paragraph of its own.

2. Draven sees the comment, and immediately answers with a friendly message
   telling Janine that she's got the issue for the next three days. The message
   also has a precise timestamp in UTC, that is also a link to some web service
   that provides various common local times for the timestamp, and a selector
   to show any arbitrary timezone. The message closes with a reminder of a few
   common actions that people can do, as well as a link to Draven's
   documentation. Draven also Github-assigns Janine to the issue.

3. Janine opens a PR referencing the issue. Draven sees that and extends the
   Issue Request indefinitely, as well as linking (in its own reckoning) both
   threads (so a Draven action in one will affect both). It posts a short
   message in the **issue**'s thread saying so. When the PR is merged, Draven
   forgets about the entire thing.

4. Or Janine doesn't do anything. After 3 days, Draven posts a notice that the
   issue is now free for requests, and takes Janine off the Github-assign list.
   The notice is phrased in such a way that it doesn't imply anything about
   Janine, and maintains a happy/friendly attitude. Anyone else can (including
   Janine) can come and request the issue again.

## Draven flows

The flow described above is not the only one available, but it would be the
most common. Here are others:

### Asking, not telling

The `req=duration` is someone _telling_ Draven they're requesting the issue.
Another way to interact with Draven is to _ask_ the maintainers to request the
issue.

1. Paul wants to work on Issue #917 but isn't sure they should. For example,
   the conversation in the thread is left ambiguous, and they want to check
   with the maintainers that it's okay. So they post this: `req?7d`.

2. Draven emails the maintainer(s) with a subject like "Paul (@atreidesP) wants
   to request Issue #917" and a body that details various things:

    - The title and first post of the issue
    - The number of posts in the thread
    - The requesting history of the issue, if any
    - The PR history of the issue, if any
    - The commenting, PR, requesting, etc history of _Paul_ in this thread, if any
    - Whether Paul is a contributor, first time visitor, or other info
    - Who else Draven has emailed about this

   The message closes with a description of the actions the maintainer can
   take. They can reply to the email, or go to the thread, or do nothing. If
   they reply, the first line must be either "Yes" or "No". The rest of the
   message (if any) will be added as comment. If they go to the thread, they
   can `req!yes` (shorthand `req!`) or `req!no`. If they do nothing, Draven
   will auto-reject after two weeks.
   
   In short, the email includes everything relevant, in a terse format, that
   makes it possible for the maintainer to _only_ consult this email to make a
   decision, in 90% of the cases. And replying to the email directly to take
   action means interactions have a very small effort-to-action ratio.

3. Whatever happens, Draven will keep everyone involved informed. Paul will of
   course be told if the query is answered, but other people Draven emailed
   will also be informed about the resolution, so they don't try to resolve
   something that's already been resolved.

### Extending or canceling

Once they have an active Issue Request, someone can:

- Voluntarily cancel the Request before the expiry, with `req-`.
- Extend the Request by a day, with `req+`. They can't extend by more than 3 days.

### Making an issue unavailable

An owner (or contributor?) can mark an issue as unavailable through either
`req!disable` or adding a predefined tag to the issue. That can be reverted
with `req!enable` or by removing the tag.

An issue is also unavailable if there is someone already on the Github-assign
list. And of course an issue would not be double-requestable.

## Configuration

While all the durations and details would be defaulted to something sensible,
everything is of course configurable by project, by organisation, by Draven
deploy.

Notably, some projects could make it mandatory for non-contributors to use the
_Ask_ workflow rather than the _Tell_ one, and enforce that. And the emails
generated by Draven can be configured to go to a custom list of people, not
necessarily the maintainers.

### Some tentative defaults

…beyond those mentioned above.

 - Minimum request time is **1 day**.
 - Maximum request time is **2 weeks**, or **3 months** for contributors/owners.
 - Default request time (with `req=`, nothing after) is **4 days**.

