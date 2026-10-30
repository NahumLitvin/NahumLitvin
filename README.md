### Hi, I'm Nahum

[Wix](https://www.wix.com) runs about 2.5% of the websites out there. When a site owner writes backend code, it runs on the Kubernetes platform I keep alive, one site per gVisor sandbox. Untrusted code finds every crack. Dublin.

When a pod hangs and it isn't our bug, I go find whose it is.

Once, containerd nodes said Ready while no new pod could start. A snapshotter had hung, and snapshot GC waited on it forever. I [bounded the GC](https://github.com/containerd/containerd/pull/13799). It got merged. Then it got reverted, because the maintainers wanted it fixed on the client side. Fair. So I [put the deadline there](https://github.com/containerd/containerd/pull/14187), and that one stuck.

Most hunts end like that: a fix upstream and a [write-up of how I found it](https://www.catchkill9.dev).

**Other cracks, fixed upstream**

- **gVisor**: a wedged sandbox kept the shim lock forever → [30s deadlines on Kill, Stats, Status](https://github.com/google/gvisor/pull/14549)
- **gVisor**: a missed systrap interrupt blocked teardown → [only the stuck subprocess dies](https://github.com/google/gvisor/pull/14201)
- **gVisor**: a random memory gap put the spinning queue on a thread stack → [the queue moves with the gap](https://github.com/google/gvisor/pull/15573)
- **gVisor**: the last indefinite waits → [in review](https://github.com/google/gvisor/pull/15194)
- **SOCI**: fd leak on pod-start bursts → [close the Verify reader](https://github.com/awslabs/soci-snapshotter/pull/2043)
- **metrics-server**: one slow scrape drops the whole node from `kubectl top` → [open](https://github.com/kubernetes-sigs/metrics-server/pull/1908)
- **Security Profiles Operator**: [AppArmorProfile owners in node status](https://github.com/kubernetes-sigs/security-profiles-operator/pull/1994). The first one, 2023.
### prismantis

[![prismantis](https://raw.githubusercontent.com/NahumLitvin/prismantis/main/docs/brand/banner.png)](https://github.com/NahumLitvin/prismantis)

My Claude Code mod. It draws replies in color right in the terminal: tables, code with copy buttons, mermaid diagrams and charts, 15 themes.

[![stars](https://img.shields.io/github/stars/NahumLitvin/prismantis?style=for-the-badge&color=c6a0f6&logo=github)](https://github.com/NahumLitvin/prismantis/stargazers) [![contributors](https://img.shields.io/github/contributors/NahumLitvin/prismantis?style=for-the-badge&color=8bd5ca)](https://github.com/NahumLitvin/prismantis/graphs/contributors) [![forks](https://img.shields.io/github/forks/NahumLitvin/prismantis?style=for-the-badge&color=8aadf4)](https://github.com/NahumLitvin/prismantis/forks) [![last commit](https://img.shields.io/github/last-commit/NahumLitvin/prismantis?style=for-the-badge&color=f5a97f)](https://github.com/NahumLitvin/prismantis/commits/main)

It started because I'd built a whole VS Code wrapper just to see mermaid diagrams in Claude Code replies. A mod turned out to be the better way.

### Latest posts

- [7 articles in 4 years, then 10 in a month: the blog I kept not starting](https://www.catchkill9.dev/posts/catchkill9-wix-headless.html)
- [Same day, two fixes: 5 days in one project, 175 in containerd](https://www.catchkill9.dev/posts/containerd-part-4-the-fix-lands.html)
- [My fix for this hang was already running. The sandbox hung anyway.](https://www.catchkill9.dev/posts/gvisor-spinning-queue-gap.html)

[Blog](https://www.catchkill9.dev) · [LinkedIn](https://www.linkedin.com/in/lnahum/) · [dev.to](https://dev.to/nahumlitvin) · [RSS](https://www.catchkill9.dev/rss.xml)
