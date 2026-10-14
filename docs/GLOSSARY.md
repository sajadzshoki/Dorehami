# Glossary — canonical terminology

All documents and (future) code must use these terms. Do not introduce synonyms (e.g. do not use "meetup", "gathering", "club", "ticket" for the concepts below).

| Term (EN) | فارسی | Definition |
|---|---|---|
| Dorehami | دورهمی | The product. Technical identifier: `dorehami` (lowercase, ASCII). |
| User | کاربر | A single account. One account holds all capabilities; there is no separate "organizer account". |
| User profile | نمایه | Public/private display data of a user (display name, bio, avatar, city preference). Distinct from the account/auth record. |
| Interest | علاقه‌مندی | A user-selected topic used for onboarding and discovery ranking. Interests are user-facing labels mapped to categories/tags. |
| Category | دسته‌بندی | Admin-managed taxonomy node for events/communities (e.g. Technology, Arts). |
| City | شهر | Admin-managed location entity. Events may be in-person (city + venue), online, or hybrid. |
| Organizer capability | قابلیت برگزارکنندگی | A capability granted to a user account, enabling event/community creation and an organizer page. Not a separate account or global role alone — see ADR-0006. |
| Organizer page | صفحه برگزارکننده | Public page presenting an organizer's public events/communities. |
| Organizer verification | احراز برگزارکننده | Trust process/level for organizers; affects publication review. |
| Community | انجمن / اجتماع | A public group around shared interests that can host events and posts. (Persian UI copy to be validated — Open Q-07.) |
| Membership | عضویت | A user's membership record in a community, with a membership role. |
| Community manager | مدیر انجمن | A member with resource-scoped management rights over one community. |
| Post / Comment / Reaction | پست / دیدگاه / واکنش | Basic community content and engagement. |
| Event | رویداد | A discoverable activity. May be standalone or linked to one community. Has one or more occurrences. |
| Event series | سری رویداد | The recurring template/rule defining a repeating event. |
| Occurrence | نوبت برگزاری | One concrete instance of an event at a specific start/end time. Registrations attach to occurrences, never to the series. |
| Registration | ثبت‌نام | A user's confirmed reservation for one occurrence, subject to capacity. Registration ≠ attendance. |
| Capacity | ظرفیت | Maximum confirmed registrations for an occurrence. |
| Registration cancellation | لغو ثبت‌نام | Attendee-initiated cancellation, freeing capacity, per the cancellation policy. Distinct from event cancellation. |
| Event cancellation | لغو رویداد | Organizer-initiated cancellation of an occurrence or series (scope must be explicit). |
| Direct conversation / message | گفت‌وگو / پیام مستقیم | Strictly one-to-one private messaging between two authorized participants. |
| Notification | اعلان | In-app (and optionally push) notice to a user. |
| Content report | گزارش محتوا | A user-submitted moderation report about content or a user. |
| Audit log | گزارش حسابرسی | Append-only record of sensitive administrative actions. |
| OTP challenge | چالش کد یک‌بارمصرف | A single phone-authentication attempt record (hashed code, expiry, attempts). |
| Session | نشست | A server-side authenticated session bound to a user and device context. |
| Media asset | فایل رسانه‌ای | An uploaded file stored in MinIO, referenced by metadata in PostgreSQL. |
| Publication review | بازبینی انتشار | Moderation/trust check applied before content becomes public, stricter for new/low-trust organizers. |
| Trust level | سطح اعتماد | Derived organizer standing used to decide review requirements. Provisional concept — see Open Q-05. |
| Slug | نامک | Stable, URL-safe, language-aware identifier for public pages. |
| PWA | وب‌اپ پیش‌رونده | Installable progressive web app behavior of the Nuxt frontend. |

**Naming rules:** DB tables snake_case plural; TypeScript types PascalCase singular matching the English term; API resources use the plural English term (`/events`, `/communities`, `/occurrences`, `/registrations`).
