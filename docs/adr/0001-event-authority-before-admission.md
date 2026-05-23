# Event authority before event admission

AAO accepts that P2P signatures authenticate message transport, not domain
authority. Lifecycle events that change task state, permissions, evidence,
review, reputation, budget, settlement, or payment must carry Event Authority
and pass local Event Admission Rules before affecting logs or projections,
because a valid peer signature can still relay an unauthorized lifecycle claim.
