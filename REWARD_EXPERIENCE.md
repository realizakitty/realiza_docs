# Reward experience activity

The wish wall includes a disclosed platform reward experience. A notice in all five interface languages appears before the wall and again before a help intention is submitted. Individual task cards are not separately labeled. Participation does not require payment or delivery of a gift. An intention does not guarantee a reward; administrators explain the experience and reward conditions before further action.

The initial activity contains 24 open tasks: 18 in Brazilian Portuguese, 4 in Spanish and 2 in English. Images are real item reference photos, not photographs of a participant, an actual delivery or a promised exact product. Photo sources, authors, licenses and export changes are available at [Photo credits](https://www.realizakitty.com/campaign/hidden-rewards-v1/credits.html).

System accounts are disabled, have no password, use non-deliverable `.invalid` addresses and receive no staff grants. They are retained solely to associate activity records with an internal identity. Existing real accounts are preserved.

Administrators see system account/task labels. Help intentions inherit their target task's activity classification, while the participant must still verify their real email normally. Activity intentions are shown separately from organic gift intentions. Staff must contact the participant, explain the activity and reward terms, and must not contact the synthetic publisher address or ask for advance payment or gift delivery.

Organic wish, fulfilled wish and help intention metrics exclude system activity. Separate counters show system tasks, system accounts and verified activity intentions. No fulfilled stories, historical dates or reward payouts are fabricated.

The one-time import uses durable per-item journal entries and the same AC/WS ID allocation as other entities. Repeating the maintenance import does not duplicate records, reset moderation or recreate completed seed entries after deletion. Normal server startup does not seed campaign data.

Validation includes standalone Mongo integration tests for preserved real accounts, disabled system users, inherited classification, ordinary email verification with a return to the selected wish, private field exclusion, resistance to client-supplied activity flags, separate metrics and retry behavior.
