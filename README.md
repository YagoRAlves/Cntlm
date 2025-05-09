# Cntlm
Step by step on how to configure cntlm.


for ns in $(oc get ns -o jsonpath='{.items[*].metadata.name}'); do
  for secret in $(oc get secrets -n "$ns" -o jsonpath='{.items[*].metadata.name}'); do
    data=$(oc get secret "$secret" -n "$ns" -o jsonpath='{.data}')
    echo "$data" | grep -q '^[[:space:]]*{}' && continue
    for key in $(echo "$data" | sed 's/[{}"]//g' | tr ',' '\n' | cut -d: -f1); do
      val=$(oc get secret "$secret" -n "$ns" -o jsonpath="{.data.$key}" 2>/dev/null | base64 -d 2>/dev/null)
      if echo "$val" | grep -qi "materaRW"; then
        echo "🔐 Namespace: $ns | Secret: $secret | Key: $key"
        echo "$val"
        echo "-------------------------------"
      fi
    done
  done
done
