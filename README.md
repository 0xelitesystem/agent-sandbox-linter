# Agent Sandbox Linter

Paste an AI agent's command allowlist and host allowlist and see which permitted programs are documented to run code from their own argv, and which host wildcards anyone can provision a name under.

## Live demo

https://0xelitesystem.github.io/agent-sandbox-linter/

## Features

The input artifact is an allowlist config, the two lists your agent actually runs behind. Paste them as plain lines, or paste a JSON array or a settings object and the parser will pull the strings out of it. Wrapper forms such as `Bash(git log:*)` are unwrapped to the program name.

- **Two columns, from two different places.** Column A is computed here and is runtime independent: does your entry name a program whose own manual documents that it takes a command on its own argv? Column B is declared by you: does your matcher constrain arguments, or does it only match the program name? The ranking is A and not B.
- **Every catalogue row carries a primary source URL and a quoted sentence.** git on `core.pager` and on alias entries prefixed with an exclamation point, composed with the documented `-c` flag. POSIX on `find -exec`, on `awk` `BEGIN` with `system()`, on `xargs` and on `env`. GNU tar on `--checkpoint-action=exec`, cited to the Texinfo manual. OpenBSD on `ssh -o ProxyCommand`. Docker on bind mounts having host write access by default.
- **Wildcard scope explosion on the host half.** An entry such as a wildcard over `githubusercontent.com`, `s3.amazonaws.com`, `blob.core.windows.net`, `pages.dev`, `workers.dev`, `vercel.app` or an ngrok managed base admits subdomains that anyone on the internet can provision. That is a property of the entry, computed from the string with zero requests.
- **The disclaimer is above the fold, not in a footer.** This tool can prove an allowlist insufficient by exhibiting a permitted escape. It can never prove one sufficient.
- **Copyable report**, per invocation copy buttons, a Load sample button, dark by default with a light toggle that persists, keyboard reachable throughout, and no console errors.

## How it works

**Column A is a catalogue, not a bypass corpus.** Every entry is documented behaviour of a program that has shipped for decades, and every entry names the flag, links the manual and quotes the sentence. Nothing in it is an exploit signature against a particular build, so nothing in it goes stale when a vendor ships a release.

**Column B is your assertion and the output says so.** The tool does not decide whether an entry like `git log *` admits a dangerous flag. That answer belongs to one product's matcher in one release, it is a moving target, and a repository full of those answers rots in a release cycle. So the tool asks you, and every row that uses your answer says on its face that you asserted it. There is no default: an undeclared row is held at amber rather than guessed at, because a default would be the tool guessing at your matcher.

**The host half computes scope and nothing else.** There is no egress versus ingestion classification, because whether a host serves content an attacker authored is a judgement about your deployment, and printing your own judgement back at you is not a finding. What is computable from a host string with zero requests is scope, so that is all this half computes: a bare `*`, a wildcard over a base whose subdomains are handed out to whoever asks, a wildcard over a base this catalogue does not know, and a wildcard that is not a leading label and therefore depends on your matcher.

**Interpreters are deliberately absent.** `sh`, `bash`, `python3`, `node`, `perl` and `ruby` are not catalogued, because nobody is surprised that an interpreter runs code from argv. The catalogue collects programs that get allowlisted as version control, archive, search or transport tools and still run argv supplied code.

**Dated material is kept visually separate from the logic.** The catalogue section is marked as a snapshot with the date it was fetched. The ranking depends only on whether your entry names a catalogued program or base, so a quote going stale changes a citation without making the ranking wrong. Sources, all read on 2026-08-11:

| What | Source |
| --- | --- |
| `core.pager`, `alias.*` | https://git-scm.com/docs/git-config |
| `git -c` | https://git-scm.com/docs/git |
| `find -exec` | https://pubs.opengroup.org/onlinepubs/9799919799/utilities/find.html |
| `awk` `BEGIN`, `system()` | https://pubs.opengroup.org/onlinepubs/9799919799/utilities/awk.html |
| `xargs` | https://pubs.opengroup.org/onlinepubs/9799919799/utilities/xargs.html |
| `env` | https://pubs.opengroup.org/onlinepubs/9799919799/utilities/env.html |
| GNU tar `--checkpoint-action=exec` | https://www.gnu.org/software/tar/manual/html_node/checkpoints.html |
| `ssh -o`, `ProxyCommand` | https://man.openbsd.org/ssh and https://man.openbsd.org/ssh_config |
| docker bind mounts | https://docs.docker.com/engine/storage/bind-mounts/ |
| raw.githubusercontent.com | https://docs.github.com/en/rest/repos/contents |
| github.io | https://docs.github.com/en/pages/getting-started-with-github-pages/what-is-github-pages |
| s3.amazonaws.com | https://docs.aws.amazon.com/AmazonS3/latest/userguide/VirtualHosting.html |
| blob.core.windows.net | https://learn.microsoft.com/en-us/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata |
| pages.dev | https://developers.cloudflare.com/pages/configuration/preview-deployments/ |
| workers.dev | https://developers.cloudflare.com/workers/configuration/routing/workers-dev/ |
| vercel.app | https://vercel.com/docs/deployments/generated-urls |
| ngrok managed base domains | https://ngrok.com/docs/universal-gateway/domains/ |

Two notes on that table, because both are the kind of row that goes stale:

- **GNU tar is cited to the Texinfo manual on purpose.** The `tar(1)` man page describes the option as one line, "Run ACTION on each checkpoint", and never enumerates `exec`. A reviewer checking the man page alone would conclude the row was invented.
- **ngrok no longer hands out `ngrok.io`.** The ngrok managed base domain table, read on 2026-08-11, lists `ngrok.app`, `ngrok.dev` and `ngrok.pizza` for paying accounts, `ngrok-free.app`, `ngrok-free.dev` and `ngrok-free.pizza` for free accounts, and marks `ngrok.io` as discontinued and only available to older accounts. An allowlist rule naming `ngrok.io` alone is both over broad for old accounts and blind to every suffix a new tunnel would actually get. All of them are in the catalogue.

## Privacy

Everything runs in your browser. The allowlists you paste are read as strings and never leave the page. The tool makes no request to the hosts you paste, no request to any source URL, and no request to anything else: every quote was fetched by hand while the catalogue was written and then frozen into the file. One HTML file, no external dependencies, no analytics, no build step. Open the page source and read it, or open the network tab and watch it stay empty.

## License

MIT. See [LICENSE](LICENSE).

## More

Two neighbours, told apart by what you feed them. This one eats an allowlist config and looks forward at what it could permit.

- [agent-blast-radius](https://github.com/0xelitesystem/agent-blast-radius) eats a session transcript and looks backward at what already happened.
- [ai-agent-guardrails-reference](https://github.com/0xelitesystem/ai-agent-guardrails-reference) is prose, with no input at all.

More tools at https://0xelitesystem.github.io/ and https://elitesystem.ai
