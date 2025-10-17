The final part of our product planning process is to document this product's tech stack in `agent-os/product/tech-stack.md`.  Follow these instructions to do so:

{{workflows/planning/create-product-tech-stack}}

## Display confirmation and pause for review

Once you've created tech-stack.md:

1. **List files created**:
   - `agent-os/product/mission.md`
   - `agent-os/product/roadmap.md`
   - `agent-os/product/tech-stack.md`

2. **PAUSE FOR REVIEW**:
   - Provide suggested commit message: `docs(product): create product planning documents`
   - Ask user: "Ready to review and commit these product planning documents? Reply 'yes' to commit and proceed, or provide feedback for adjustments."
   - **WAIT for user approval**
   - After user approves, **create the commit**
   - Confirm commit creation
   - Inform user they can proceed with `/new-spec` when ready

## User Standards & Preferences Compliance

The user may provide information regarding their tech stack, which should take precidence when documenting the product's tech stack.  To fill in any gaps, find the user's usual tech stack information as documented in any of these files:

{{standards/global/*}}
