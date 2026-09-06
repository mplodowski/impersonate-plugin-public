# Upgrade guide

Versions not listed here need no action. Back up the database before upgrading.

## Upgrading To 2.0.0

Plugin requires October CMS 3.0 or higher, Laravel 9.0 or higher and PHP >=8.0.

## Upgrading To 3.0.1

Plugin requires October CMS 4.0 or higher.

## Upgrading To 3.1.0

**Security release. Upgrade every installation running 3.0.x.** Plugin requires PHP 8.2 or higher. Run
`php artisan october:migrate`.

The `User impersonation` permission is now granted by default only to super users and the Developer role; the
Publisher role loses it. The new settings page at **Settings → Team → Impersonate** needs the separate
`Manage impersonation settings` permission. Grant either to a custom role where needed.

The privilege escalation guard is on by default, so a non-super user can no longer impersonate anyone holding
permissions they lack. Switch it off in the settings if you rely on that.

Leaving impersonation goes through `/backend/renatio/impersonate/leave`; the `onStopImpersonateUser` AJAX handler
was removed. The plugin now fires `renatio.impersonate.*` events (see the README).
