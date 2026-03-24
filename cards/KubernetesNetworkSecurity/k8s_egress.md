Q: What is the least privilege principle as applied to Kubernetes networking?
A: Each workload should only be allowed to communicate with the exact services it needs — nothing more. All other traffic, especially egress to arbitrary endpoints, should be denied by default.

---

Q: What is a Kubernetes NetworkPolicy?
A: A namespaced resource that defines rules controlling which pods can send (egress) or receive (ingress) traffic, selected by pod labels, namespace labels, or IP blocks.

---

Q: What happens to pod traffic when no NetworkPolicy selects a pod?
A: The pod is non-isolated — all ingress and egress traffic is allowed by default. A pod only becomes restricted once at least one NetworkPolicy selects it.

---

Q: How do you deny all egress traffic from a namespace by default?
A: Apply a NetworkPolicy with an empty `egress: []` array and a `podSelector: {}` that matches all pods — this creates a default-deny-all-egress baseline.

---

Q: What is the difference between `podSelector` and `namespaceSelector` in a NetworkPolicy egress rule?
A: `podSelector` restricts traffic to pods matching labels within the same (or a target) namespace. `namespaceSelector` restricts traffic to any pod in namespaces matching labels, regardless of pod labels.

---

Q: How do you allow a pod to reach an external IP range via egress NetworkPolicy?
A: Use an `ipBlock` field with a CIDR in the egress `to` rule. You can add `except` entries to exclude sub-ranges within that CIDR.

---

Q: Why must DNS (port 53 UDP/TCP) be explicitly allowed in an egress policy?
A: Pods resolve service names via the cluster DNS (kube-dns/CoreDNS). Without an egress rule allowing UDP/TCP port 53 to the DNS pod or namespace, all name resolution fails.

---

Q: What is Calico?
A: A CNI (Container Network Interface) plugin and network policy engine that implements Kubernetes NetworkPolicy and extends it with its own richer policy resources (GlobalNetworkPolicy, NetworkSet, etc.).

---

Q: What is a Calico GlobalNetworkPolicy?
A: A cluster-scoped (non-namespaced) Calico resource that applies network policy across all namespaces and can also govern host-level traffic — beyond what standard Kubernetes NetworkPolicy supports.

---

Q: What is a Calico NetworkSet?
A: A Calico resource that names a list of IP CIDRs or domains, allowing egress rules to reference a named set instead of hardcoding IP ranges — making policies easier to maintain.

---

Q: How does Calico policy ordering (order field) work?
A: Calico evaluates policies in ascending order of their `order` value. A lower number means higher priority. The first matching rule (allow or deny) wins for that traffic flow.

---

Q: What is the effect of a Calico `Deny` rule vs a missing allow rule?
A: An explicit `Deny` rule immediately drops traffic and stops policy evaluation. A missing allow rule in a default-deny setup also drops traffic, but relies on the implicit deny at the end of the policy chain.

---

Q: How can you restrict a pod to only egress to services within the same namespace?
A: Use a NetworkPolicy egress rule with a `podSelector: {}` and a `namespaceSelector` matching the pod's own namespace label — combined with a default-deny-all-egress policy for the namespace.

---

Q: What is egress gateway in Calico and why is it useful for least privilege?
A: A dedicated node or pod that all egress traffic from a namespace is routed through. It gives a stable source IP for firewall allowlisting at the perimeter, enabling external systems to trust a single known IP.

---

Q: What tool can you use to verify which egress flows are actually being allowed or denied by NetworkPolicies in a cluster?
A: `calicoctl` with `calico-node` flow logs, or tools like `kubectl exec` with `curl`/`nc` probes, or network observability platforms (e.g., Hubble with Cilium, or Calico's flow log export to SIEM).
