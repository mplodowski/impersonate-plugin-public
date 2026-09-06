# Upgrade guide

Versions not listed here need no action. Back up the database before upgrading.

## Upgrading To 2.0.0

Plugin requires October CMS version 3.0 or higher, Laravel 9.0 or higher and PHP >=8.0.

Drop support for October CMS version 2.x.

## Upgrading To 3.0.1

Plugin requires October CMS 4.0 or higher. Support for October CMS 3.x is dropped; stay on 2.x for those sites.

## Upgrading To 3.1.0

Plugin requires PHP 8.2 or higher and October CMS 4.0 or higher. Run `php artisan october:migrate` to record the
version.

**Security release.** Upgrade every installation running 3.0.x; user names were rendered unescaped in the
impersonate column and button tooltips.

The plugin now has a settings page at **Settings → Team → Impersonate**. The defaults apply immediately, without
opening the page:

- **Audit log** on — every started, stopped and refused impersonation is written to the
  system event log.
- **Block privilege escalation** on — a non-super user can no longer impersonate anyone who holds a permission they
  lack. Users who so far impersonated "upwards" now get a refusal with the reason; switch the guard off in the
  settings if you rely on that behaviour.
- **Session lifetime** `0` — no automatic end, as before.

The settings page requires the new `Manage impersonation settings` permission
(`renatio.impersonate.manage_settings`) instead of `User impersonation`. Super users and the built-in Developer role
have it automatically. The built-in Publisher role does not and, being a system role, cannot be granted it from the
role editor. Give the permission to a custom role for anyone else who should change the audit log, privilege
escalation guard or session lifetime.

The `User impersonation` permission (`renatio.impersonate.impersonate_user`) is now granted by default only to
super users and the built-in Developer role. The built-in Publisher role loses it on upgrade and, being a system role,
cannot get it back from the role editor. Give the permission to a custom role for anyone else who should impersonate.

Impersonation is refused for users who are not activated or who lack the `general.backend` permission, since such a
session could only ever show an access denied page.

Leaving impersonation now happens through `/backend/renatio/impersonate/leave`, which works regardless of the
impersonated user's permissions and lands on the dashboard rather than the users list. Opening the address shows a
confirmation page; the actual exit is a `POST` with the CSRF token. The `onStopImpersonateUser` AJAX handler of the
`UserImpersonator` widget has been removed; anything that called it should link to the new address instead.

The plugin fires `renatio.impersonate.started`, `stopped` and `denied` events (see the **Events** section of the
README); nothing changes unless you listen to them. Starting an impersonation now asks for confirmation, and
leaving one shows a flash message naming the user you left.
