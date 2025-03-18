## API Types

Cosmos SDK exposes multiple endpoints to the outside world which can be a source of confusion. here is a recap of those endpoints

| Endpoint | Exposed By | default port | description  |
|----------|------------|--------------|--------------|
| gRPC     |  Cosmos    | 9090         | grpc endpoint allows for typed queries and also broadcast transactions back to tendermint.| 
| gRPC-web |  Cosmos    | 9091         | just a wrapper around grpc, which allows browsers to consume grpc.| 
| REST     |  Cosmos    | 1317         | exposes gRPC via gateway, only allows for typed queries, and submitting transactions (no info how to create transactions)| 
| RPC      | Tendermint | 26657        | routes queries to cosmos app gRPC endpoint. call CheckTx for transactions.| 
| P2p      | Tendermint | 26656        | for gossip protocol, consensus voting and anything p2p related.| 
