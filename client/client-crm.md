# Client CRM: Timeline, Documents and Consignment Pipeline

From version 3.5 the client page works as a small CRM. Everything that happened with a client is in one **Timeline**, the client's paperwork is in a **Documents** tab with expiry dates, and consignment enquiries are followed from the first contact to a created consignment in the **Consignment pipeline**.

<video class="release-video" controls preload="metadata" playsinline src="https://circuit-kubernetes.s3.eu-central-1.amazonaws.com/openmontage/tutorials/release-3.5-client-crm/final.mp4">Your browser does not support the video tag. <a href="https://circuit-kubernetes.s3.eu-central-1.amazonaws.com/openmontage/tutorials/release-3.5-client-crm/final.mp4">Download the video</a>.</video>

## The client page tabs

The tabs of the client page are now grouped by purpose:

| Group | Tabs |
| --- | --- |
| Who is this | **Client**, **Addresses**, **Documents** |
| What is going on | **Timeline**, **Tasks** |
| As a buyer | **Bidding info**, **Viewings**, **Items bought**, **Bidder invoices** |
| As a seller | **Consignment pipeline**, **Consignments**, **Items consigned** |
| Money | **Transactions**, **Statement of Account**, **Statistics** |
| Logs | **Emails**, **Activities** |

The **History** tab is now called **Statistics**, and **Emails and Documents** is now called **Emails**. The **Generate Client Data Sheet** button moved from the Emails tab to the Statistics tab.

## Timeline

The **Timeline** tab shows one feed, grouped by day, of everything recorded for the client: **Emails**, **Notes**, **Calls**, **Tasks**, **Viewings**, **Activities** and **Milestones** (last bid, last win, last consignment, last seen on the website).

![The Timeline tab of a client: source filters, period selector and the entries grouped by day](../assets/screenshots/client-crm-timeline.png)

1. Open a client and click **Timeline**.
2. Click a source at the top to hide or show it. Hover a source and click **only** to see just that source; **All** brings everything back.
3. Choose a period: **30 days**, **90 days**, **This year**, **All time**, or set the **From** and **To** dates.
4. On an email entry, click **View email** to read it. Attachments and viewed lots are linked from their entries.
5. Use **Load more** at the bottom to load older entries of a source.

## Documents

The **Documents** tab lists the client's documents with their **Type**, **Expiry date**, the files, the date and who added them. The document types are ID, Tax form, FFL, Consignment agreement, Bank details and Other.

To add a document:

1. Click **Add document**.
2. Choose the **Document type** and, when the document expires, the **Expiry date**.
3. Upload the files, add a note text if you like, and click **Add a note**.

An expiry date that is less than 30 days away is shown in red with the number of days left, and an expired document is marked **expired**.

## Notes and calls

The buttons at the top of the client page are now **+ Notes / Documents** and **+ Calls**. They open a short form to add a note, a document or a call. Existing notes, documents and calls are no longer listed in these windows: you find them in the **Timeline** and **Documents** tabs.

## Consignment pipeline

A consignment enquiry is a possible consignment that is not in the system as a consignment yet: someone called, wrote, or filled in a form on the website. The pipeline keeps these enquiries visible until they become a consignment or are declined.

You find the pipeline in two places:

- **Consignment pipeline** in the main menu: all enquiries of the auction house, with quick filters for **Stage**, **Source** and **Responsible staff(s)**.
- The **Consignment pipeline** tab on the client page: the enquiries of this client.

![The Consignment pipeline page with the Stage, Source and Responsible staff filters and the list of enquiries](../assets/screenshots/client-crm-pipeline.png)

### Create an enquiry

1. Click **New enquiry**.
2. Select the **Client**, or enter a **Name**, **Email** and **Phone** when the person is not a client yet. When you start from the client page these fields are already filled in.
3. Fill in the **Category**, **Estimated value**, **Source**, **Responsible staff(s)**, **Due date** and a **Description**. You can upload up to 10 files.
4. Click **Save**.

### Stages

An enquiry moves through these stages: **Enquiry**, **Valuation**, **Agreement sent**, **Agreement signed**, **Received**, and then **Converted** or **Declined**. Edit the enquiry and change the **Stage** as the conversation advances. Every stage change is recorded on the client and appears in the Timeline.

### Convert an enquiry into a consignment

1. Edit the enquiry and set the **Stage** to **Converted**.
2. Select the sale under **Convert into a consignment of sale**.
3. Click **Save**.

A consignment is created in that sale for the client, with the client's commission and the enquiry's responsible staff. The enquiry then shows a link to the consignment. The enquiry must be linked to a client before it can be converted. The uploaded files stay on the enquiry.

### Prospects

The **Prospects** tab of the Consignment pipeline page lists strong buyers who never consigned: candidates to approach for consignments. It shows their lifetime buying and selling totals, status, tier, interests and responsible staff. Click a client to open the client page.

## Who to call

The **Who to call** tab of the item page (right after **Item History**) lists up to 10 clients who are most likely interested in the lot, based on their bidding history in the lot's category. For each client you see a score, the signals behind it (**watching**, **underbid similar**, **offered before**), phone, email and responsible staff.

Clients who blocked emails are left out unless you tick **Include clients who blocked emails**. Only clients with a bid history are suggested.

## Consignor performance

The consignment page shows a **Consignor performance** card above the tabs: lifetime seller and buyer totals, status and tier, the last consignment, the **Sold ratio** and the **Open balance** of the consignor.

## Status, tier and automatic tasks

Every night the system classifies each client and stores the result as client tags, which you can use wherever tags can be filtered:

- **Status**: prospect, new, active, cooling, lapsed or dormant, depending on how many sales passed since the client last bid, bought or consigned.
- **Tier**: A, B or C, by the client's lifetime turnover as buyer and seller. Tier A is the top 5%, tier B the next 20%.

It also creates follow-up tasks, at most one open task of each kind per client or lot:

| Task | Created when |
| --- | --- |
| CRM: lapsing client | A tier A or B client becomes cooling or lapsed. |
| CRM: lot matches | A tier A or B client has at least 3 lots matching their interests in an upcoming sale. |
| CRM: post-sale offer | A new post-sale offer arrives on a lot. |
| CRM: document expiring | A client document expires within 30 days. |
| CRM: new enquiry | An enquiry arrives from the website. |

On the [Tasks](../tasks.md) page, the Clients tab has two new filters: **Assigned to me** and **CRM tasks only**.

## Recent pages

The history icon at the top of the page, next to the logo, opens **Recent pages**: the pages you visited last. It replaces the recent list that used to be in the main menu.

## FFL validation

Auction houses that sell firearms can switch on **FFL validation**. An FFL is a client document of the type FFL with an expiry date. When the setting is on:

- The clients list and the bidder invoices list get an **FFL** column and filter: **Valid**, **Expired** or **Missing**.
- The bidder invoice page shows **FFL Valid** or **FFL Required**, with a link to the client's documents.

An FFL without an expiry date counts as expired.

## For administrators

- The CRM is switched on with the update. The pipeline needs the permission **manage consignment enquiries**; Who to call, Prospects and the Consignor performance card need **view client stat**.
- The thresholds for status and tier, the interest scores and the task rules can be changed, and each automatic task can be switched off, on the **Client CRM** settings page (permission **administer client crm**).
- **Enable FFL validation** is in the server settings, section Shipping. It is off by default.
