## Basic Network Config

Note that this basic configuration uses pre-generated certificates and
key material, and also has predefined transactions to initialize a 
channel named "mychannel".

To regenerate this material, simply run ``generate.sh``.

To start the network, run ``start.sh``.
To stop it, run ``stop.sh``
To completely remove all incriminating evidence of the network
on your system, run ``teardown.sh``.

<a rel="license" href="http://creativecommons.org/licenses/by/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by/4.0/">Creative Commons Attribution 4.0 International License</a>


### modify for mongodb test
// install cc
peer chaincode install -n marbles03 -v 1.0 -p github.com/marbles03/go

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