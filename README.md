<!--
SPDX-License-Identifier: Apache-2.0
SPDX-FileCopyrightText: 2025 The Linux Foundation
-->

# Test Repository for Semantic Versioning Tags

This repository contains test tags used to verify the signature detection
features of the `tag-validate-semantic-action` GitHub Action.

## Purpose

This repository serves as a test fixture containing tags with different
signature types:

- **Unsigned tags** - Tags without any cryptographic signature
- **SSH-signed tags** - Tags signed with SSH keys (Git 2.34+)
- **GPG-signed tags** - Tags signed with GPG keys

## Test Tags

The following tags should exist in this repository for testing:

| Tag Name          | Type     | Purpose                                    |
|-------------------|----------|--------------------------------------------|
| `v1.0.0-unsigned` | Unsigned | Test detection of unsigned tags            |
| `v1.0.0-ssh`      | SSH      | Test detection of SSH-signed tags          |
| `v1.0.0-gpg`      | GPG      | Test detection of GPG-signed tags          |

All tags follow Semantic Versioning (SemVer) format to ensure they pass the
semantic versioning validation.

## Verifying Tags

You can verify the tags manually:

```bash
# Check tag signatures
git verify-tag v1.0.0-unsigned  # Should fail (not signed)
git verify-tag v1.0.0-ssh       # Should show SSH signature
git verify-tag v1.0.0-gpg       # Should show GPG signature

# View tag object to see signature
git cat-file tag v1.0.0-ssh     # Should contain "-----BEGIN SSH SIGNATURE-----"
git cat-file tag v1.0.0-gpg     # Should contain "-----BEGIN PGP SIGNATURE-----"
```

## Usage in Tests

This repository appears in the
`tag-validate-semantic-action/.github/workflows/testing.yaml` workflow to test
signature validation functionality.

The workflow:

1. Checks out this test repository
2. Runs the validation action against each test tag
3. Verifies the `signing_type` output matches expectations
4. Tests the `require_signed` input validation logic

## Contributing

> **Note:** Maintaining the tag fixtures in this repository needs a
> dedicated test signing key, so maintainers configure their working
> clones with the test identity (`test@tag-validate-action.local`). Git
> does not track local config, so a fresh clone does not inherit this
> setting. When a clone uses the test identity, it authors and commits
> under the test key. The
> [Developer Certificate of Origin](https://developercertificate.org/)
> (DCO) check requires a `Signed-off-by` trailer that matches the commit
> author, and `git commit -s` derives that trailer from the committer
> identity, so a commit made under the test key but signed off as yourself
> will fail DCO.

When committing changes, override both the committer identity
(`-c user.name` / `-c user.email`) and the author so they match the
`Signed-off-by` trailer, which `git commit -s` derives from the committer
identity. For example:

```bash
git -c user.name="Your Name" -c user.email="you@example.org" \
  commit -s --author="Your Name <you@example.org>"
```

To correct an existing commit that has already failed DCO, amend it with
the matching committer identity and author, then force-push the branch:

```bash
git -c user.name="Your Name" -c user.email="you@example.org" \
  commit --amend --no-edit -s --author="Your Name <you@example.org>"
git push --force-with-lease
```

## Related Projects

- [tag-validate-semantic-action](
  https://github.com/modeseven-lfreleng-actions/tag-validate-semantic-action) -
  The action under test
- [test-tags-calver](
  https://github.com/modeseven-lfreleng-actions/test-tags-calver) -
  Similar test repository for calendar versioning
