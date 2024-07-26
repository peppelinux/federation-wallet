# OpenID Federation 1.0 Wallet Architectures

This repository contains the OpenID Federation for Wallet Architectures specification.

It outlines the general architecture of a federated trust infrastructure for wallet ecosystems, identifying participant roles and defining metadata used for those roles.
In particular, it defines the metadata for the roles of Wallet Provider and Wallet Relying Party.

Additionally, it provides practical examples of how to apply policies for typical use cases within wallet ecosystems and offers guidance on defining trust marks for use within wallet ecosystems.

### Current WG-Draft

The current WG-Draft version is built automatically from the main branch and can be accessed at: 
[https://peppelinux.github.io/federation-wallet/main.html](https://peppelinux.github.io/federation-wallet/main.html)

other branches will be build and deployed as well, using the branch name within the html filename, as shown below:

- https://peppelinux.github.io/federation-wallet/$BRANCH-NAME.html


### Build the HTML ###

```docker run -v `pwd`:/data danielfett/markdown2rfc openid-federation-wallet-1_0.md```

## Contact

For further information and to get involved, please visit the [OpenID Connect Working Group website](https://openid.net/wg/connect/).
