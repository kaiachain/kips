# Kaia Improvement Proposals (KIPs)

The goal of the KIP project is to standardize and provide high-quality documentation for Kaia itself and conventions built upon it. This repository tracks past and ongoing improvements to Kaia in the form of Kaia Improvement Proposals (KIPs). [KIP-1](https://kips.kaia.io/KIPs/kip-1) governs how KIPs are published.

The [status page](https://kips.kaia.io/) tracks and lists KIPs, which can be divided into the following categories:

- [Core KIPs](https://kips.kaia.io/core) are improvements to the Kaia consensus protocol.
- [Networking KIPs](https://kips.kaia.io/networking) specify the peer-to-peer networking layer of Kaia.
- [Interface KIPs](https://kips.kaia.io/interface) standardize interfaces to Kaia, which determine how users and applications interact with the blockchain.
- [KCTs](https://kips.kaia.io/token) specify the various token standards in Kaia.
- [SDKs](https://kips.kaia.io/sdk) specify software developer kits and its architecture to support Kaia.
- [Application](https://kips.kaia.io/application) specify application layer standards, which determine how applications running on Kaia can interact with each other.
- [Meta KIPs](https://kips.kaia.io/meta) are miscellaneous improvements that nonetheless require some sort of consensus.
- [Informational KIPs](https://kips.kaia.io/informational) are non-standard improvements that do not require any form of consensus.

**Before you write an KIP, ideas MUST be thoroughly discussed on [Kaia Research](https://devforum.kaia.io/c/kips). Once consensus is reached, thoroughly read and review [KIP-1](https://kips.kaia.io/KIPS/kip-1), which describes the KIP process.**

Please note that this repository is for documenting standards and not for help implementing them. These types of inquiries should be directed to the [Kaia DevForum](https://devforum.kaia.io/c/kips). For specific questions and concerns regarding KIPs, it's best to comment on the relevant discussion thread of the KIP in https://github.com/kaiachain/kips/issues.

## Preferred Citation Format

The canonical URL for an KIP that has achieved draft status at any point is at <https://kips.kaia.io/>. For example, the canonical URL for KIP-1 is <https://kips.kaia.io/KIPS/kip-1>.

Consider any document not published at <https://kips.kaia.io/> as a working paper. Additionally, consider published KIPs with a status of "draft" or "last call" to be incomplete drafts, and note that their specification is likely to be subject to change.

## Build the status page locally

### Install prerequisites

1. Open Terminal.

2. Check whether you have Ruby 3.1.4 installed. Later [versions are not supported](https://stackoverflow.com/questions/14351272/undefined-method-exists-for-fileclass-nomethoderror).

   ```sh
   ruby --version
   ```

3. If you don't have Ruby installed, install Ruby 3.1.4.

4. Install Bundler:

   ```sh
   gem install bundler
   ```

5. Install dependencies:

   ```sh
   bundle install
   ```

### Build your local Jekyll site

1. Bundle assets and start the server:

   ```sh
   bundle exec jekyll serve
   ```

2. Preview your local Jekyll site in your web browser at `http://localhost:4000`.

More information on Jekyll and GitHub Pages [here](https://docs.github.com/en/enterprise/2.14/user/articles/setting-up-your-github-pages-site-locally-with-jekyll).