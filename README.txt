
this is shell-operated Service

  - there are 3 backends in statefulSet
  - ingress should send traffic to only one
  - in case of failure, we want to switch traffic to any healthy backend and stick to it

# deploy backend

  helm template ./backend/ | k apply -f
  k apply -f ingress.yaml
  k apply -f operated-backend.svc.yaml

# deploy operator

  k apply -f deployment.yaml

# query ingress

  while :; do sleep 1; curl -s https://shell-op.k8s1.XXX.org/index.html; done

# watch operator logs

  kl `kgp | grep shell-op | grep Running |c1 | sort | head -1` | jq .msg


# un-ready active backend

  ke backend-0 -- rm -f /app/ready

# kill active backend

  ke backend-1 -- pkill python3

# delete active backend

  k delete pod backend-2

# delete all backends

  k delete pod backend-2 backend-1 backend-0 --force
