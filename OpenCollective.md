This page will collect details on using our OpenCollective presence.

## Payouts
### Payouts via bank transfer
Other than Liberapay (performing payouts only via PayPal or Strype), OpenCollective supports payouts via bank transfer, which we meanwhile tested successfully.

If you have a payout request, first step is to file it as [issue with our admin repo](https://gitlab.com/fdroid/admin/-/issues), pointing out what the payout is for, and how much it should be. This issue will take a week to be processed, so team members have a chance to chime in, approve, or ask for clarification.

Before starting your payout request at OpenCollective, make sure to choose the correct "categories":

* for work done, choose "invoice" (no uploads needed for individuals)
* if it's about purchases or bills you paid for F-Droid, it's "reimbursement" (and you must upload the bills for that later)
* both can be requested either by individuals (easiest) or "businesses", depending on your requirements.

Easiest approach is doing it as an individual. If you want to do it as a business, you'll first have to create an organization and have the expense paid to that.

1. Go to the [expenses page](https://opencollective.com/f-droid/expenses) (the "Budget" tab)
1. in the upper-right you'll find a button labeled "submit expense", hit that.
1. Choose the category. As your payout will mostly be about "work done", this would be "invoice" here. Check that radio button, and further details appear.
1. "Who is being paid" is usually you (you can request that for other parties too, but I'll skip this here to keep things straight). In "Payout Method" choose "New Bank Account" (this can be saved, so next time you can simply pick yours). Select your local currency, fill in your details.  
**Important:** "Recipient type" must be "Person" if you request payout to a person, "Business" can only be used for organisations! You will need to add your postal address as well then (for tax purposes). Note your personal data will **NOT** be public. For business, both the address on the invoice you must upload and the account holder of the bank details must be the business, not you as individual.
1. After tapping the "Next" button, you have to file your expense details. Give it a clear title, and from the description also reference the admin issue. You **can** upload an invoice, and this is obligatory for a business, but for individuals this is not needed – the details you specify here substitute for it. Set a due-date and specify the amount, cross-check the details, hit the "next" button.
1. next comes a summary screen: make sure all details are correct. If they're not, hit the "edit" button and fix – else hit "Submit".

Your expense is filed now, and needs to be approved by an "admin" – i.e. an F-Droid team member which is "admin" for our organisation at OpenCollective. Who that is can be seen on [our OpenCollective page](https://opencollective.com/f-droid/) if you scroll down to the "Our contributors" section: admins are listed in the first column there. Just leave a note on your issue in the admin tracker here at GitLab that you've completed your request and one of the admins shall approve (they get a mail notification anyway, but having the timeline in the issue cannot hurt).

After this, usually within less than a week, a representative of our Fiscal Host will respond – and either confirm the request will be processed (it then first gets the blue "Expense processing" and later the green "Expense paid" label in its timeline), or "unapprove" it and ask you for corrections (which can only be made in "unapproved" status and then need to be approved again, which for formalities will be done automatically by the Fiscal Host again).

Once the "Expense processing" label was set, if your invoice was for a sum of USD 600 or more, you will receive a tax form. For non-US citizens, this usually is the [W8BEN](https://tipalti.com/what-is-w-8ben/), and you'll receive a link to that from HelloWorks. Their form makes filling the W8BEN a breeze: instead of the 5 pages with cryptical fields, it's a question-and-answer game with about 5 or 6 questions (as individual you e.g. don't need to fill in "business details", so those are simply skipped). The only tricky one was about "tax treaty benefits for chapter 3" (as if everybody in the world knows what that means). The IRS instructions (see below) explain that in detail; usually you cannot claim such benefits, and a "no" saves you from additional field filling.

Confirm you tell the truth, the full truth, and nothing but… well, send the form. Within a day or two your payment status should turn green ("Expense paid"), and in another day the money should arrive on your account. Ugh.


### Useful links
* OpenCollective: [Submitting Expenses](https://docs.opencollective.com/help/expenses-and-getting-paid/submitting-expenses)
* OpenCollective: [Tax Information](https://docs.opencollective.com/help/expenses-and-getting-paid/tax-information)
* IRS: [Instructions for Form W-8BEN](https://www.irs.gov/instructions/iw8ben)
