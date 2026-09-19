# 60th Anniversary — Digital RSVP Plan

A no-cost RSVP system using Google Forms + Google Drive, fronted by an unlinked
page on thegreatfuldane.com.

> Placeholders to fill in: `[DATE]`, `[TIME]`, `[VENUE ADDRESS]`,
> `[RSVP DEADLINE]`, `[GUEST COUNT]`.

---

## Architecture

```
Paper invite / email ──► thegreatfuldane.com/dane60   (unlinked Squarespace page)
                                    │
                                    │  "RSVP" button (opens in new tab)
                                    ▼
                            Google Form  ──► Google Sheet (responses)
                                                     │
                                                     ▼
                                          Shared Drive folder
                                          (siblings = Editors)
```

Three pieces, one job each:

| Piece | Job |
|---|---|
| Squarespace page | The invitation. Photos, date, place, directions. |
| Google Form | Data capture. |
| Google Sheet + Drive folder | Source of truth. Headcount, dietary, chase list. |

Cost: $0. The Squarespace page is included in the existing site plan.

---

## Decision: link to the form, don't embed it

**The site is on the Squarespace Business plan, so embedding is available** —
iframes and JavaScript in Code Blocks are supported on Core, Plus, Advanced,
Business, and both Commerce plans. This is now a free choice rather than a
constraint.

Recommendation is still to link out, but on narrower grounds than before:

1. ~~Plan gating~~ — no longer applies. The Business plan supports it.
2. **Mobile.** Embedded Google Forms need a fixed iframe height, which produces a
   nested scrollbar on phones. A large share of this guest list will RSVP on a
   phone, and this is the most common complaint about embedded forms.
3. **Free confirmation screen.** Linking out lets the form's own confirmation
   message handle the thank-you, with no extra page to build.
4. **Independence.** A linked form can be edited without anyone touching the
   business's live site.

So: the Squarespace page is the invitation, and a prominent Button block sends
people to the form.

That said, the case is no longer one-sided — embedding keeps guests on
thegreatfuldane.com the whole way, which is nicer if the page is doing real
work as an invitation. If that matters more than the mobile friction, embed it;
just test on an actual phone before sending. This goes in a Code Block (swap in
the real embed URL from Send → `< >`):

```html
<iframe src="https://docs.google.com/forms/d/e/FORM_ID/viewform?embedded=true"
        width="100%" height="1400" frameborder="0"
        marginheight="0" marginwidth="0"
        title="RSVP form">Loading…</iframe>
```

---

## What "private / unlinked" actually means

Pages in Squarespace's **Not linked** section are live at their direct URL — they
are simply absent from the site navigation. Anyone with the link can open them,
and search engines can index them if the URL is ever linked from anywhere.

Two ways to harden it:

| Option | Protection | Friction |
|---|---|---|
| **Page password** | Real. One shared password, case-sensitive, ≤30 chars. | Guests will call you about it. |
| **Unlinked + noindex** ✅ | Obscurity only. | None. |

**Recommended: unlinked + noindex.** Set Page Settings → SEO → hide from search
engines. Nobody guesses the slug, and no guest gets locked out at 9pm. The
tradeoff is acceptable as long as the page shows the *venue* address and not the
parents' home address.

Use a password only if the guest list itself is sensitive.

---

## Build steps

### 1. Drive folder (do this first)

Create the folder before the form, so the response sheet lands in the right place.

```
60th Anniversary — Tom and Yvonne/
├─ RSVPs (Responses)         ← auto-created by the form; don't rename the tab
├─ Guest list (master)       ← who was invited; drives the chase list
├─ Invitation assets/        ← photos, PDF of the paper invite, QR code
└─ Day-of/                   ← seating chart, check-in list, toasts, memory book
```

**Owner:** Matt's personal Google account (`mattevans1977@gmail.com`). A personal
Gmail account is fine here — nothing in this plan needs Workspace.

The thing to fix early: right now one account holds the form, the responses, and
the folder. If that account is ever locked out mid-planning, the RSVP list goes
with it. Two minutes of setup avoids that:

1. Share the **folder** with siblings as **Editors**.
2. Add at least one sibling as a **collaborator on the form itself** (Form →
   ⋮ → Add collaborators). Folder access alone does not grant form editing.

Ownership can stay with Matt; this is just so nobody is a single point of
failure eight weeks out.

### 2. Google Form

Header image: a photo of the venue or of the parents, matching the Squarespace
page, so the handoff feels continuous.

**Questions**

| # | Question | Type | Required | Notes |
|---|---|---|---|---|
| 1 | Your name | Short answer | Yes | Person filling out the form |
| 2 | Email | Short answer | Yes | Updates and reminders |
| 3 | Mobile number | Short answer | No | Day-of texts; more reliable than email here |
| 4 | Will you be joining us? | Multiple choice | Yes | *Joyfully accept* / *Regretfully decline* |
| 5 | How many people in your party, including yourself? | **Dropdown 1–6** | Yes | Dropdown, not free text — keeps the column numeric so it sums. Raise the ceiling if households run large |
| 6 | Names of everyone in your party | Paragraph | Yes | Needed for place cards and name tags |
| 7 | Dietary restrictions or allergies | Paragraph | No | |
| 8 | Song request | Short answer | No | Head start on the playlist |
| 9 | Share a memory or message for Tom and Yvonne | Paragraph | No | See below |

Question 9 is the highest-value field on the form. It costs one question and
yields a printed memory book, or three lines for the toast. Include it.

**The meal is a buffet, so there is no meal-choice question.** That keeps the
form to nine questions, four of them optional — short enough that people finish
it on the first sitting.

Keep the dietary question anyway: a buffet still needs allergy and vegetarian
counts, and it is the caterer's main input from this form.

Use **section branching** on Q4 so people who decline skip straight to Q9 —
they should not have to scroll past dietary questions to say they can't make it.

**Settings**

- Email collection: **Responder input**. Do *not* use "Verified" and do *not*
  require sign-in — a meaningful share of this guest list has no Google account
  they can log into.
- **Allow response editing: ON.** People fix their own party count instead of
  emailing about it.
- Limit to 1 response: **OFF** (it requires sign-in).
- Confirmation message: *"Thank you — we can't wait to celebrate with you.
  Details and directions will follow closer to the date."*
- Responses tab → ⋮ → **Get email notifications for new responses: ON.**

### 3. Squarespace page

1. Pages panel → add a new page → drag it into the **Not linked** section.
2. Slug: short and typeable from a paper card — `/dane60` is better than
   `/60th-anniversary-celebration-2027`. Avoid hyphen-heavy slugs and
   ambiguous characters.
3. Page Settings → SEO → hide from search engines.
4. Content: photo of Tom and Yvonne, the invitation line, [DATE] / [TIME], venue name
   and address, parking or directions notes, dress code, RSVP deadline.
5. A **Button block**: "RSVP" → the Google Form URL → open in new window.
6. Test in a private browser window while logged out. This is the step people
   skip and it is the step that catches a broken embed or a stray nav link.

### 4. Paper invite

Print both the short URL **and** a QR code pointing at the same page. Roughly
half the guests will use the QR code and the other half will type the URL, and
which half a given guest falls into is not predictable by age.

---

## Running it

**The chase list is the part that matters.** The response sheet shows who
*replied*; it cannot show who *didn't*. In the master guest list, add a column
matching each invited household against the responses tab:

```
=IF(COUNTIF('RSVPs (Responses)'!C:C, A2) > 0, "Replied", "—")
```

Filter for `—` about a week after the deadline; that is your call list. Without
this you are reconstructing the outstanding names from memory.

**Before sending the final headcount to the caterer**, File → Download a copy of
the sheet. The live sheet keeps changing; the caterer's number should be a
snapshot you can point back to.

**Suggested timeline**

| When | What |
|---|---|
| ~8 weeks out | Invitations go out with URL + QR code |
| ~3 weeks out | RSVP deadline |
| Deadline + 1 week | Work the chase list by phone |
| ~1 week out | Final headcount to venue; download sheet snapshot |
| Day before | Print check-in list, place cards, memory book |

---

## Open questions

**Logistics — resolved**

- ~~Site plan?~~ **Business plan** — embedding is supported, so it's a free choice.
- ~~Plated or buffet?~~ **Buffet** — meal-choice question dropped.
- ~~Who owns the Google account?~~ **`mattevans1977@gmail.com`** — add siblings as
  collaborators per the Drive section above.

**Still needed**

- **How many households?** Sets the party-size dropdown ceiling and decides
  whether the chase list needs more structure than a single formula column.
- **Date, time, venue address, RSVP deadline** — the remaining placeholders.

**Copy and voice** — needed before the page and form text can be written:

- How do Tom and Yvonne feel about being fussed over? Determines whether this
  reads as a tribute or as an invitation to dinner.
- Whose voice is the invitation in — the children's, Tom and Yvonne's, or the
  family's? Sets the grammatical person of every line.
- One specific detail about each of them. Not adjectives — the grill nobody else
  touches, the Thursday card game, the phrase one of them always says.
- How do they talk to each other? Any long-running, well-worn bit?
- How did they meet, and how does the family tell that story?
- Dancing? Do they have a song? Decides whether the song-request field earns its
  place.

## Alternative considered

Squarespace's native **Form Block** could capture RSVPs directly on the page and
email them or store them. It was set aside because it requires site-admin access
for every change, offers less question logic than Google Forms, and couples the
family's RSVP list to the business's website settings. Google Forms keeps the
two cleanly separate.

## Sources

- [Code blocks — Squarespace Help Center](https://support.squarespace.com/hc/en-us/articles/206543167-Code-blocks)
- [Premium features — Squarespace Help Center](https://support.squarespace.com/hc/en-us/articles/115015517328-Premium-features)
- [The Not linked section — Squarespace Help Center](https://support.squarespace.com/hc/en-us/articles/360025899552-The-Not-linked-section)
- [Page passwords — Squarespace Help Center](https://support.squarespace.com/hc/en-us/articles/205814618-Page-passwords)
- [Controlling who can access your site's pages — Squarespace Help Center](https://support.squarespace.com/hc/en-us/articles/360022365512-Controlling-who-can-see-your-site-s-pages)
