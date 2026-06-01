ssh ubuntu@44.255.119.162 -oStrictHostKeyChecking=no
watch kubectl get nodes
kubectl top node
kubectl top pod
kubectl describe nodes | grep --after-context=5 "Non-terminated Pods"
kubectl get pods -o wide
kubectl explain pod.spec.securityContext | more

kubectl create -f pod-no-security-context.yaml
kubectl exec security-context-test-1 -- ls /dev
kubectl exec security-context-test-3 -it -- /bin/sh

kubectl exec db -it -- mongo testdb --quiet --eval 'db.messages.insert({"message": "I was here"}); db.messages.findOne().message'

kubectl exec db -it -- ls /config
kubectl exec db -it -- cat /config/DB_NAME && echo
kubectl create configmap --help | more
kubectl create configmap app-config --from-literal=DB_NAME=testdb \
  --from-literal=COLLECTION_NAME=messages
kubectl explain pod.spec.containers.envFrom.configMapRef
kubectl create secret generic app-secret --from-literal=password=123457
kubectl exec pod-secret -- /bin/sh -c 'echo $PASSWORD'