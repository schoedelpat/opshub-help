Title: Add replacements when compiling ticket_assigned template

Goal
- Ensure assigned-ticket emails have placeholders replaced (id, title, ticketUrl, assignedTo.name) by passing a replacements object to Handlebars when compiling the template.

Patch (conceptual change)

File to edit: apps/api/src/lib/nodemailer/ticket/assigned.ts

Before:

var template = handlebars.compile(testhtml?.html);
var htmlToSend = template({}); // No replacements provided

After:

var template = handlebars.compile(testhtml?.html);
// Build replacements from the ticket data (adjust names to match available variables)
const replacements = {
  id: ticket?.id || ticketId || '',
  title: ticket?.title || ticket?.Number || '',
  ticketUrl: `${process.env.BASE_URL || 'https://your-host'}/ticket/${ticket?.id || ticketId || ''}`,
  assignedTo: { name: ticket?.assignedTo?.name || '' }
};
var htmlToSend = template(replacements);

Notes & steps to apply on your VPS
1. SSH into your VPS and navigate to the peppermint project folder.
2. Create a branch for the change: git checkout -b feat/email-assigned-replacements
3. Edit the file `apps/api/src/lib/nodemailer/ticket/assigned.ts` and replace the template compilation lines as shown.
4. Run lint / build if your project uses it (recommended): yarn build or npm run build.
5. Test by invoking the assigned email flow (assign a ticket) or by running a small script that calls sendAssignedEmail with a fake ticket object.
6. Commit and push the branch, open a PR, and merge to main if all tests pass.

If you want I can prepare a ready-to-apply patch (git format-patch or a small PR) — tell me whether you want me to create a patch file or a concrete git patch you can apply on the VPS.
