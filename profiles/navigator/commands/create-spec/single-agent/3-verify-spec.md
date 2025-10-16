Now that we've prepared this spec's `spec.md` and `tasks.md`, we must verify that this spec is fully aligned with the current requirements to ensure we're ready for implementation.

Follow these instructions:

{{workflows/specification/verify-spec}}

## Display confirmation and pause for review

1. **Display summary**:
   - Verification report location: `agent-os/specs/[this-spec]/verification/spec-verification.md`
   - Highlight any issues found during verification

2. **List files created**:
   - `spec.md` - Comprehensive specification
   - `tasks.md` - Task breakdown with assignments
   - `verification/spec-verification.md` - Verification report

3. **PAUSE FOR REVIEW**:
   - Provide suggested commit message: `docs(spec): create specification and tasks for [spec-name]`
   - Ask user: "Ready to review and commit the specification documents? Reply 'yes' to commit and proceed, or provide feedback for adjustments."
   - **WAIT for user approval**
   - After user approves, **create the commit**
   - Confirm commit creation
   - Inform user they can proceed with `/implement-spec` when ready

## User Standards & Preferences Compliance

IMPORTANT: Ensure that your verifications are ALIGNED and DOES NOT CONFLICT with the user's preferences and standards as detailed in the following files:

{{standards/*}}
