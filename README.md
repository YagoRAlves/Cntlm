# Cntlm
Step by step on how to configure cntlm.


namespace="cp-123456"

for secret in $(oc get secrets -n "$namespace" -o jsonpath='{.items[*].metadata.name}'); do
  data=$(oc get secret "$secret" -n "$namespace" -o jsonpath='{.data}')
  echo "$data" | grep -q '^[[:space:]]*{}' && continue
  for key in $(echo "$data" | sed 's/[{}"]//g' | tr ',' '\n' | cut -d: -f1); do
    val=$(oc get secret "$secret" -n "$namespace" -o jsonpath="{.data.$key}" 2>/dev/null | base64 -d 2>/dev/null)
    if echo "$val" | grep -qi "materaRW"; then
      echo "Namespace: $namespace | Secret: $secret | Key: $key"
      echo "$val"
      echo "-------------------------------"
    fi
  done
done

