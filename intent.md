# Intent: Results-ready notification

Sources: `inputs/01-approved-prd.md` (PRD N-17, approved 2026-09-18), `inputs/02-current-meeting.md` (discovery notes, 2026-09-20). The early request (`inputs/03-superseded-request.md`, 2026-08-02) is superseded by PRD N-17 and approves nothing in this intent.

## Problem

- **I-1** The clinic receives avoidable calls from patients checking whether their results are ready. *(PRD N-17: "The fictional clinic receives avoidable calls from patients checking whether results are ready.")*

## Proposed outcome

- **I-2** The patient is told when a result is ready for their review in the existing authenticated portal. *(PRD N-17: "Notify the patient when a result is ready for their review in the existing authenticated portal.")*
- **I-3** A result counts as ready only after a doctor has reviewed and released it. A completed laboratory upload alone does not make a result ready. *(PRD N-17: "A doctor must review and release the result before any notification is eligible."; discovery notes, product owner: "'Ready' must not mean that the laboratory upload merely finished.")*

### How success will be observed

- **I-4** Results-status calls, counted separately from clinical questions and compared against a measured baseline. *(PRD N-17: "Measure reduction in results-status calls"; discovery notes, support lead: "count status-only calls separately from clinical questions; compare against a measured baseline.")*
- **I-5** Delivery failures. *(PRD N-17: "Measure reduction in ... delivery failures.")*
- **I-6** No baseline or numeric target is approved. Both are to be agreed during discovery. This intent does not set one, and it does not adopt the call-reduction target from the superseded request. *(PRD N-17: "The baseline and numerical target will be agreed during discovery; none is approved yet.")*

## Affected users and systems

- **I-7** Patients are the only approved recipients. *(PRD N-17: "Notify the patient")*
- **I-8** The existing authenticated portal and its existing result route, where the notification directs the patient. *(PRD N-17: "Use the existing portal result route."; discovery notes, engineering lead: "the existing portal path is adequate.")*
- **I-9** Doctors, whose review and release of a result opens the notification gate (I-3).
- **I-10** Notification delivery and its operational logs, which fall under the content rule in C-2.
- **I-11** Clinic support staff, who count status-only calls for I-4.
- The referring doctor is **not** an approved recipient. See OPEN-2.

## Scope

In scope:

- **S-1** Notifying the patient through the existing portal result route.
- **S-2** Preventing duplicate notifications for the same released result version.
- **S-3** Handling delivery failures without sending duplicate patient messages.

*(PRD N-17: "Use the existing portal result route."; "Avoid duplicate notifications for the same released result version; handle delivery failures without duplicate patient messages.")*

## Out of scope

- **X-1** Portal redesign. *(PRD N-17)*
- **X-2** Appointment scheduling. *(PRD N-17)*
- **X-3** Clinical interpretation. *(PRD N-17)*
- **X-4** Notifying third parties. *(PRD N-17: "notifying third parties [is] outside this approved scope")* The referring doctor is handled separately as OPEN-2.
- **X-5** A new or rebuilt patient dashboard. *(Discovery notes, engineering lead: "A new patient dashboard is a separate project.")*
- **X-6** Everything that only the superseded early request asked for is excluded:
  - sending every result automatically when the lab uploads it (conflicts with I-3)
  - putting a result in an SMS (conflicts with C-2, and the channel is still open as OPEN-1)
  - copying the referring doctor (still open as OPEN-2)
  - rebuilding the patient dashboard (X-5)
  - its call-reduction target (I-6)

## Constraints

- **C-1** No notification until a doctor has reviewed and released the result. *(PRD N-17)*
- **C-2** The message only says that an update is available and directs the patient to the existing authenticated portal. No diagnosis, test result, clinical measurement or treatment detail may appear in the message or in notification and operational logs. *(PRD N-17; discovery notes, privacy owner: "no sensitive content in messages or operational logs.")*
- **C-3** Access to results is authenticated in the existing portal. Results are never attached, and no public result link is introduced. *(PRD N-17)*
- **C-4** At most one patient notification per released result version, including when delivery fails and is retried. *(PRD N-17)*
- **C-5** No recipient other than the patient is notified unless their authorization is decided as described in OPEN-2. *(PRD N-17; discovery notes, clinical owner)*

## Open questions

- **OPEN-1 Channel** (SMS, email or portal push). **Owners: product and privacy.** They decide after the consent and delivery review. No channel is chosen in this intent. *(PRD N-17: "Channel (SMS, email, portal push): product owner and privacy owner must decide after consent/delivery review."; discovery notes, privacy owner: "Consent and the actual channel are still under review.")*
- **OPEN-2 Referring-doctor notification.** **Owners: clinical and privacy.** They decide whether the referring doctor is an authorized recipient. It is not approved by PRD N-17 or by the superseded request. *(PRD N-17: "clinical owner and privacy owner must decide recipient authorization. It is not approved by this PRD."; discovery notes, clinical owner: "do not infer approval for referring-doctor notifications from the old request.")*

```json
{
  "notifyWhen": ["doctor-reviewed", "released"],
  "messageMayInclude": ["update-available"],
  "inScope": ["existing-portal-route", "duplicate-prevention", "delivery-failure-handling"],
  "openDecisions": [
    {"topic": "channel", "owners": ["product", "privacy"]},
    {"topic": "referring-doctor-notification", "owners": ["clinical", "privacy"]}
  ],
  "successMeasures": ["status-only-calls", "delivery-failures"],
  "numericTarget": null
}
```
