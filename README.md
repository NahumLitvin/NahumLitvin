### Hi, I'm Nahum

[Wix](https://www.wix.com) runs about 2.5% of the websites out there. When a site owner writes their own backend code, it runs on the Kubernetes platform I keep running, one site per gVisor sandbox. Dublin.

Untrusted code finds every crack. When a pod hangs and the cause isn't ours, I follow it down the stack until I find whose bug it is. Usually it's open source, so I fix it there and [write up how I found it](https://www.catchkill9.dev).

This is the stack, and where it broke on me:

```mermaid
flowchart TD
  ms["metrics-server<br/><i>one slow scrape, node gone</i>"] -.-> kubelet
  kubelet -->|CRI| ctr["containerd<br/><i>snapshotter hung, node still Ready</i>"]
  ctr --> soci["SOCI snapshotter<br/><i>fd leak on pod-start bursts</i>"]
  ctr --> shim["gVisor shim<br/><i>wedged sandbox held the lock</i>"]
  shim --> sandbox
  subgraph sandbox["Pod sandbox, one site"]
    direction LR
    code["Untrusted<br/>user code"] -->|syscalls| sentry["gVisor Sentry<br/><i>stuck stub, no teardown</i>"]
  end
  sentry --> kernel["Host kernel"]
  style sandbox fill:#fff8e1,stroke:#bf8700,color:#4d2d00
  style code fill:#fff1c2,stroke:#bf8700,color:#4d2d00
  style ms fill:#326ce5,stroke:#1f4fb4,color:#ffffff
  style kubelet fill:#326ce5,stroke:#1f4fb4,color:#ffffff
  style ctr fill:#8250df,stroke:#5e34a8,color:#ffffff
  style soci fill:#8250df,stroke:#5e34a8,color:#ffffff
  style shim fill:#8250df,stroke:#5e34a8,color:#ffffff
  style sentry fill:#8250df,stroke:#5e34a8,color:#ffffff
  style kernel fill:#1a7f37,stroke:#0f5323,color:#ffffff
```

Nodes said Ready while no new pod could start. A snapshotter had hung and containerd's snapshot GC waited on it forever. My fix [bounded the GC](https://github.com/containerd/containerd/pull/13799), got merged, then got reverted because maintainers wanted it fixed on the client side. Fair. The [deadline on proxy snapshotter calls](https://github.com/containerd/containerd/pull/14187) went in instead.

Pod-start bursts leaked file descriptors in the SOCI snapshotter until it hit its limit. One reader in file Verify never got closed. [Now it does](https://github.com/awslabs/soci-snapshotter/pull/2043).

gVisor is where most of my time goes. A wedged sandbox could hold the shim lock forever, so runsc Kill, Stats and Status [got 30s deadlines](https://github.com/google/gvisor/pull/14549). A missed systrap interrupt could block teardown, so now [only the stuck subprocess dies](https://github.com/google/gvisor/pull/14201), not the sandbox. [The last indefinite waits](https://github.com/google/gvisor/pull/15194) are still in review.

One slow kubelet scrape makes metrics-server drop the whole node from `kubectl top`. [Open](https://github.com/kubernetes-sigs/metrics-server/pull/1908).

It started smaller: [AppArmorProfile owners in the Security Profiles Operator](https://github.com/kubernetes-sigs/security-profiles-operator/pull/1994), 2023.

### prismantis

[![prismantis](https://raw.githubusercontent.com/NahumLitvin/prismantis/main/docs/brand/banner.png)](https://github.com/NahumLitvin/prismantis)

My Claude Code mod. It draws replies in color right in the terminal: tables, code with copy buttons, mermaid diagrams and charts, 15 themes. Public since October 2, 2026.

[![stars](https://img.shields.io/github/stars/NahumLitvin/prismantis?style=for-the-badge&color=c6a0f6&logo=github)](https://github.com/NahumLitvin/prismantis/stargazers) [![contributors](https://img.shields.io/github/contributors/NahumLitvin/prismantis?style=for-the-badge&color=8bd5ca)](https://github.com/NahumLitvin/prismantis/graphs/contributors) [![forks](https://img.shields.io/github/forks/NahumLitvin/prismantis?style=for-the-badge&color=8aadf4)](https://github.com/NahumLitvin/prismantis/forks) [![last commit](https://img.shields.io/github/last-commit/NahumLitvin/prismantis?style=for-the-badge&color=f5a97f)](https://github.com/NahumLitvin/prismantis/commits/main)

It started because I'd built a whole VS Code wrapper just to see mermaid diagrams in Claude Code replies. A mod turned out to be the better way.

### Latest posts

- [Pod stuck in Terminating: what it is actually waiting for](https://www.catchkill9.dev/posts/pod-stuck-in-terminating.html)
- [Mermaid charts in Claude Code's terminal: what a mod could do that my VS Code harness couldn't](https://www.catchkill9.dev/posts/prismantis.html)
- [A zero timeout in Go is not no timeout](https://www.catchkill9.dev/posts/go-zero-timeout-is-expired.html)

[Blog](https://www.catchkill9.dev) · [LinkedIn](https://www.linkedin.com/in/lnahum/) · [dev.to](https://dev.to/nahumlitvin) · [RSS](https://www.catchkill9.dev/rss.xml)
