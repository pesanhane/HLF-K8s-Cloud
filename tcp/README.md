Create a K8s cluster

Connect your cluster with the cloud shell

Clone this repository
  git clone https://github.com/pesanhane/HLF-K8s-Cloud.git
Setup the storage class

    cd HLF-K8s-Cloud/gcp kubectl apply -f . This will setup the storage class
Launch the Acme Orderer

       cd .. kubectl apply -f ./k8s-acme-orderer.yaml Check the logs for 'acme-orderer-0' to ensure there is no error
Launch the Acme Peer

        kubectl apply -f ./k8s-acme-peer.yaml Check the logs for 'acme-peer-0' to ensure there is no error
        Setup the Channel & Join acme peer to it it

        kubectl exec -it acme-peer-0 /bin/bash ./submit-channel-create.sh
        ./join-channel.sh
Ensure that peer has joined the channel

    peer channel list
    exit
    Launch the budget Peer and join it to the channel

    kubectl apply -f ./k8s-budget-peer.yaml Wait for the container to launch & check the logs for errors
    kubectl exec -it budget-peer-0 /bin/bash ./fetch-channel-block.sh ./join-channel.sh
Ensure that peer has joined the channel

        peer channel list
exit ** At this point your K8s Fabric Network is up **

Validating the network

  Install & Instantiate the test chaincode

        kubectl exec -it acme-peer-0 /bin/bash
        ./cc-test.sh install ./cc-test.sh instantiate
  Invoke | Query the chaincode to see the changes in values of a/b

        ./cc-test.sh query ./cc-test.sh invoke
  Check the values inside the Budget peer

      kubectl exec -it acme-peer-0 /bin/bash
      ./cc-test.sh install
    ./cc-test.sh query The query should return the same values as you see in acme-peer Execute invoke/query in both peers to validate
