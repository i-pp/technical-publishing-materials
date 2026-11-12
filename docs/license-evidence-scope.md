<!--
KISS Open License Version 1.0 (KOL-v1)

SPDX-FileCopyrightText: 2026 I-plusplus Inc.
SPDX-License-Identifier: LicenseRef-KOL-v1

Copyright remains with its respective holders; original copyright and attribution notices must be preserved.
Contributors may add copyright notices for their own original modifications.
Distributed modifications must be identified as required by Section 4(c) of KOL-v1.

Use, modification, distribution, and incorporation into commercial or proprietary software are permitted
without license fees or source disclosure, subject to all terms of the complete KOL-v1 license.

Contributors grant the patent permissions stated in KOL-v1. Outputs are not covered merely because a
KOL-licensed tool produced them; embedded licensed material remains subject to applicable license terms.

THIS SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OR LIABILITY TO THE EXTENT PERMITTED BY APPLICABLE LAW.

This header is a summary, not a separate license or an amendment to KOL-v1.
All terms and conditions of KOL-v1 apply. See LICENSES/LicenseRef-KOL-v1.txt for the complete license text.
-->

# License evidence and minimum CI

Existing editorial-policy-v2.0.md and npu-technical-record.md remain under their express CC BY 4.0 notices. Their bytes, attribution and external-material exclusions are unchanged. The root LICENSE supplies that existing license text; it does not convert those documents to KOL.

## Declared scope

New first-party CI and maintenance files use LicenseRef-KOL-v1, with I-plusplus Inc. attribution. Preserve the complete applicable license and NOTICE with distributions. No new rights are claimed in external tools, GitHub Actions, quoted material, SDKs, models, or dependencies. A root license does not override a file's separate terms.

## Evidence

The original documents already identify CC-BY-4.0 (CC BY 4.0). The license copy is from https://github.com/spdx/license-list-data/blob/main/text/CC-BY-4.0.txt.

Minimum CI checks tracked UTF-8 files, JSON syntax, local Markdown links and declared license evidence using the pinned license-check-ci v0.1.0 artifact. Both jobs must succeed. It runs on pull requests, main pushes, merge groups and manual dispatch. It neither tests product functionality nor proves legal ownership, compatibility, certification or editorial readiness. Existing source bytes are preserved.
