# mongo-marbles

1. git clone https://github.com/tongchain/fabric.git

2. git checkout release-1.2.mgo

3. build images
```
make peer-docker
make orderer-docker
make tools-docker
```

4. start network

```
cd basic-network
./start.sh
```

5. docker exec -it cli bash

```
// install cc
peer chaincode install -n marbles03 -v 1.0 -p github.com/marbles03/go/

// instantiate cc
peer chaincode instantiate -o orderer.example.com:7050 -C mychannel -n marbles03 -v 1.0 -c '{"Args":["init"]}'

// three marbles
peer chaincode invoke -o orderer.example.com:7050   -C mychannel  -n marbles03 -c '{"Args":["initMarble","marble1","blue","35","tom"]}'
peer chaincode invoke -o orderer.example.com:7050   -C mychannel  -n marbles03  -c '{"Args":["initMarble","marble2","red","50","tom"]}'
peer chaincode invoke -o orderer.example.com:7050   -C mychannel  -n marbles03 -c '{"Args":["initMarble","marble3","blue","70","tom"]}'

// GetState()
peer chaincode query -o orderer.example.com:7050   -C mychannel  -n marbles03 -c '{"Args":["readMarble","marble1"]}'

// CompositeKey
peer chaincode invoke -o orderer.example.com:7050   -C mychannel  -n marbles03 -c '{"Args":["transferMarblesBasedOnColor","blue","jerry"]}'

// rich query (json string in cc)
peer chaincode query -o orderer.example.com:7050   -C mychannel  -n marbles03  -c '{"Args":["queryMarbles","{\"query\":{\"owner\":\"jerry\"}}"]}'

// rich query
peer chaincode query -o orderer.example.com:7050   -C mychannel  -n marbles03 -c '{"Args":["queryMarblesByOwner","tom"]}'

// range query
peer chaincode query -o orderer.example.com:7050   -C mychannel  -n marbles03 -c '{"Args":["getMarblesByRange","marble1","marble4"]}'

// GetHistoryForKey
peer chaincode query -o orderer.example.com:7050    -C mychannel  -n marbles03 -c '{"Args":["getHistoryForMarble","marble1"]}'
```

6. paging query (e.g.)

```
peer chaincode invoke -o orderer.example.com:7050   -C mychannel  -n marbles03  -c '{"Args":["initMarble","marble4","red","50","jerry"]}'
...
peer chaincode invoke -o orderer.example.com:7050   -C mychannel  -n marbles03  -c '{"Args":["initMarble","marble17","red","50","jerry"]}'


peer chaincode query -o orderer.example.com:7050    -C mychannel  -n marbles03 -c '{"Args":["queryMarbles","{\"query\":{\"owner\":\"jerry\"},\"pagingInfo\":{\"currentPageNum\":1,\"pageSize\":5}}"]}'
peer chaincode query -o orderer.example.com:7050    -C mychannel  -n marbles03 -c '{"Args":["queryMarbles","{\"query\":{\"owner\":\"jerry\"},\"pagingInfo\":{\"currentPageNum\":2,\"totalCount\":16,\"totalPage\":4,\"lastPageRecordCount\":1,\"pageSize\":5,\"lastQueryObjectId\":\"5bd00a6526da21c315192935\",\"lastRecordObjectId\":\"5bd00aab26da21c315192a10\",\"lastQueryPageNum\":1}}"]}'
peer chaincode query -o orderer.example.com:7050    -C mychannel  -n marbles03 -c '{"Args":["queryMarbles","{\"query\":{\"owner\":\"jerry\"},\"pagingInfo\":{\"currentPageNum\":3,\"totalCount\":16,\"totalPage\":4,\"lastPageRecordCount\":1,\"pageSize\":5,\"lastQueryObjectId\":\"5bd00a6526da21c315192935\",\"lastRecordObjectId\":\"5bd00aab26da21c315192a10\",\"lastQueryPageNum\":1}}"]}'
peer chaincode query -o orderer.example.com:7050    -C mychannel  -n marbles03 -c '{"Args":["queryMarbles","{\"query\":{\"owner\":\"jerry\"},\"pagingInfo\":{\"currentPageNum\":4,\"totalCount\":16,\"totalPage\":4,\"lastPageRecordCount\":1,\"pageSize\":5,\"lastQueryObjectId\":\"5bd00a6526da21c315192935\",\"lastRecordObjectId\":\"5bd00aab26da21c315192a10\",\"lastQueryPageNum\":1}}"]}'
```

7. check index with robo3T

```
db.getCollection('test').find({"value.owner":"jerry"}).explain()

"stage" : "FETCH"
```

*Tips:* https://robomongo.org/download
