# oir-patches — small patches to upstream AOSP

The entire OIR integration modifies **5 upstream AOSP files** totalling **69 inserted lines**. Those edits live here as standalone patches so `oir-framework-addons` can stay purely additive.

## Patched files (as of v0.6.9)

| Path | Diff size | Why |
|---|---|---|
| `frameworks/base/core/res/AndroidManifest.xml` | +16 lines | Declares `oir.permission.USE_TEXT` / `USE_AUDIO` / `USE_VISION` at platform level |
| `frameworks/base/core/res/res/values/strings.xml` | +8 lines | Permission labels + descriptions |
| `frameworks/base/core/res/Android.bp` | +2 lines | Enables the OIR AIDL package in the platform framework build |
| `frameworks/base/services/core/Android.bp` | +24 lines | Wires `OIRService` + `oir_sdk_aosp` companion into `services.core` build |
| `frameworks/base/services/java/com/android/server/SystemServer.java` | +9 lines | Registers `OIRService` during system_server bootstrap |

Total: 5 files, +69 lines, zero line deletions.

## Why a patches repo (vs fork)

Forking `frameworks/base` costs ~20 GB of history to carry 69 lines of diff. A patches repo lets us:

- Track every upstream-AOSP edit in one clear place.
- Bump the upstream tag without re-resolving merges on thousands of unrelated files.
- Ship JibarOS to AOSP-derivative OSes that aren't based on vanilla AOSP.

## Applying

At tree-assembly time (by the `manifest` repo's bake script, or manually):

```bash
cd <aosp-root>
for p in /path/to/oir-patches/patches/*.patch; do
    git -C frameworks/base am "$p"
done
```

Patches are split by file and numbered by apply order. They are plain unified diffs (`git diff` output, not `git format-patch`) so they apply via `git apply` rather than `git am` — no `Signed-off-by` or commit-message header is included. The applying script is responsible for any logging.

## Forward-porting to a new AOSP bump

When upstream AOSP moves forward:

1. Rebase the patches against the new upstream.
2. Resolve conflicts (rare — touched files are stable surfaces).
3. Re-validate on Cuttlefish.
4. Tag a new `oir-patches` release.

## See also

- [`oir-framework-addons`](https://github.com/jibar-os/oir-framework-addons) — the new files these patches wire into place
- [`manifest`](https://github.com/jibar-os/manifest) — applies this repo as part of tree assembly

