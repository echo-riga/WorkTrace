# WorkTrace

A mobile workforce app for field-service requests, assignments, location monitoring, client feedback, and completion review. Administrators organize teams, supervisors oversee requests and active work, and engineers carry out jobs.

## Features

- Role-specific workspaces for administrators, supervisors, and engineers.
- Team management, request creation, approval, assignment, and status tracking.
- Engineer location updates and supervisor maps.
- Client feedback and drawn, photographed, or uploaded signatures.
- Supervisor verification and searchable work records.
- Locally saved completion drafts and queued submissions for reconnection.

Built with Expo SDK 54, React Native, TypeScript, Expo Router, React Native Paper, React Native Maps, Socket.io, AsyncStorage, and Cloudinary uploads. This repository contains the mobile client; the remote backend is separate.

## Run the published client

Use Node.js 22 LTS, npm, and an Android device or emulator.

```sh
npm ci
npm start
```

Open the project in a compatible Expo client. For a native development build with the Android SDK configured:

```sh
npm run android
```

The committed client uses the backend URL in [lib/config.ts](lib/config.ts). It requires a reachable compatible API, valid backend accounts, and the expected Socket.io events. Configure your backend address before connecting to another installation. Map and location features require native platform configuration and device permissions. [lib/uploadImage.ts](lib/uploadImage.ts) contains the Cloudinary cloud and upload preset configuration; use your own upload configuration for another installation.

## Source layout

| Path | Purpose |
| --- | --- |
| app/(auth) | Sign-in flow |
| app/(admin) | Overview, teams, and records |
| app/(supervisor) | Requests, active jobs, and review |
| app/(engineer) | Requests, assigned jobs, and completion |
| [lib](lib) | API client, authentication, uploads, and local persistence |
| [assets](assets) | App icons and images |

## Checks and builds

Run npm run lint for static checks. There is no automated test script. Verify each role with a test backend and disposable records, including reconnection and completion submission.

With EAS CLI authenticated to your Expo project:

```sh
eas build --platform android --profile preview
```

The preview profile in [eas.json](eas.json) generates an APK. Review the Expo project association and application identifiers in [app.json](app.json) before building under another account.

## Local development preview

The following notes describe additional local workspace changes. The mock API, styling changes, and npm run dev script are not included in this README-only commit. A fresh clone should use the published-client setup above until those changes are committed separately.

### Run locally on an Android emulator

Install dependencies, start an Android emulator, then run:

```bash
npm install
npm run dev
```

Development builds use an in-app mock API, so no backend or internet connection
is required for app data after dependencies and Expo Go are installed. Map tiles
still use Google Maps, while location coordinates are simulated locally. Sign in
with any password and one of these accounts:

- `admin@local.test`
- `supervisor@local.test`
- `engineer@local.test`

Mock data resets whenever the JavaScript bundle reloads. To deliberately use the
remote API during development, set `EXPO_PUBLIC_USE_REMOTE_API=true`. You can
override its address with `EXPO_PUBLIC_API_URL`.

### Product interface

WorkTrace uses a shared blue and slate palette in `lib/theme.ts`. Screen styles
use `createProductStyles` from `lib/productStyles.ts` for consistent cards,
readable metadata, comfortable form controls, status chips, and map sizing.
Screen-specific layout and state colors remain defined alongside each screen.
Spacing is defined by the screen, rather than injected into shared styles:
16-point page gutters, 12-point card and heading gaps, and 24-point gaps between
dashboard sections. Form footers reserve their own space in normal layout;
screens with floating actions use bottom padding for button clearance.
Use one page title, with section headings only where they separate different
tasks. Avoid repeating tab names, counts, or instructions already clear from the
controls. User-facing language uses Requests, Jobs, and Confirm completion;
API status values remain unchanged. Summary cards show the assignee once;
detail pages show a separate requester only when that person is different.
The dashboard lists each request once with its current status, instead of
repeating those items in a second recent-jobs list.
Bottom navigation accounts for the device safe area so labels stay clear of
Android gesture controls. Employee locations use native density-aware image
markers to avoid Android custom-view clipping.
The status bar uses dark icons throughout the app. Top-level navigation headers
use a 56-point toolbar plus the actual device top inset, preserving the camera
cutout space without adding an extra toolbar-sized gap.
