# How to Add Consent to an Existing XMTP App

Managing user consent is essential for enhancing privacy and user experience. If you already have an XMTP application, integrating portable consent features becomes crucial. This guide walks you through adding the consent logic to your existing XMTP app.

![](video.gif)

<div
  style={{
    display: "flex",
    justifyContent: "space-evenly",
    textAlign: "center",
    marginTop: "20px",
  }}
>
  <h3 style={{ width: "50%" }}>**Without Consent**</h3>
  <h3 style={{ width: "50%" }}>**With Consent**</h3>
</div>

<div className="widget-container" style={{ padding: "0px", height: "400px" }}>
  <FloatingInbox env="production" isContained={true} isConsent={false} />
  <FloatingInbox env="production" isContained={true} isConsent={true} />
</div>

#### Initialize XMTP Client with Consent

Here's your existing `initXmtp` function, updated to include the consent refresh logic.

```jsx
const initXmtpWithKeys = async function () {
  // ... previous code
  const xmtp = await Client.create(wallet);
  // Add these lines to refresh the consent list
  await xmtp.contacts.refreshConsentList();
};
```

#### Filtering conversations based on consent

Using the `consentState` property of the conversation object, you can filter the conversations based on consent.

```jsx
// Filtering based on consent state
const allowed = conversations.filter(
  (conversation) => conversation.consentState === "allowed",
);
const requests = conversations.filter(
  (conversation) => conversation.consentState === "unknown",
);
```

#### Request inbox

You can now create a separate inbox for requests. This inbox will only show the conversations that have not been allowed yet.

```jsx
{
  activeTab === "requests" ? (
    <button
      style={styles.conversationListItem}
      onClick={() => setActiveTab("allowed")}>
      ← Allowed
    </button>
  ) : (
    <button
      style={styles.conversationListItem}
      onClick={() => setActiveTab("requests")}>
      Requests →
    </button>
  );
}
```

#### Allow and denied actions

Every time you open a conversation on the request tab you can show a popup with the allow and deny actions. You can use the `consentState` property of the conversation object to show the popup only when the consent state is `unknown`.

```jsx
// Inside your MessageContainer component
const [showPopup, setShowPopup] = useState(
  conversation.consentState === "unknown",
);

// Function to handle the acceptance of a contact
const handleAccept = async () => {
  // Allow the contact
  await client.contacts.allow([conversation.peerAddress]);
  // Hide the popup
  setShowPopup(false);
  // Refresh the consent list
  await client.contacts.refreshConsentList();
  // Log the acceptance
  console.log("accepted", conversation.peerAddress);
};

// Function to handle the blocking of a contact
const handleBlock = async () => {
  // Block the contact
  await client.contacts.deny([conversation.peerAddress]);
  // Hide the popup
  setShowPopup(false);
  // Refresh the consent list
  await client.contacts.refreshConsentList();
  // Log the blocking
  console.log("denied", conversation.peerAddress);
};
```

#### Conclusion

By following these steps, you'll successfully integrate portable consent into your existing XMTP application. This enables users to have better control over who can send them messages, enhancing user privacy and experience.
