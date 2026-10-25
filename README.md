# NotaryCentral Embed Setup

An Agent Skills package that helps an AI set up the NotaryCentral RON signer experience on WordPress or a developer-managed website.

It explains which URL belongs in each setting, routes each invitation into the iframe, and verifies setup with a real signer invitation. It also covers common WordPress, origin, camera, and payment-return problems.

## Install

Clone the repository into your AI tool's skills directory. For Codex's default personal skills location:

```sh
git clone https://github.com/NotaryCentral/notarycentral-embed-setup.git ~/.codex/skills/notarycentral-embed-setup
```

If you use a custom `CODEX_HOME`, place it in that directory's `skills/notarycentral-embed-setup` folder instead. For another Agent Skills-compatible tool, use its documented skills directory. The skill entrypoint is [SKILL.md](SKILL.md); no build or runtime dependencies are required.

## Use

Example request:

> Use $notarycentral-embed-setup to help me add NotaryCentral signing to my WordPress site at https://example.com/notary-signing/.

The AI should adapt to your hosting and editor permissions, show the exact settings, and distinguish a published page from a verified signing flow. You still need access to your website and NotaryCentral account. Keep invitation tokens private.

## Official references

- [WordPress guide](https://www.notarycentral.org/embed-notarycentral-wordpress)
- [General embedding guide](https://www.notarycentral.org/embed-customer-experience)
- [React/Vite sample](https://github.com/NotaryCentral/embedded-experience-sample-code)

This package covers RON signer embedding. Booking-only widgets have a separate integration. Website instructions and account controls can change; the skill directs the AI to check the current sources and actual UI before acting.
