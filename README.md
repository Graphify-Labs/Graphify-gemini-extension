# Graphify for Gemini CLI

Search indexed code, trace dependencies, assess change impact, and retrieve
repository memory from [Graphify](https://graphify.com) inside
[Gemini CLI](https://github.com/google-gemini/gemini-cli).

This official Graphify extension connects Gemini CLI to the hosted Graphify MCP
server at `https://api.graphify.com/mcp` using Streamable HTTP and OAuth. It also
includes concise guidance for choosing repository scope and grounding answers in
code evidence. There is no local MCP executable or bundled credential.

## Install and connect

You need working Gemini CLI model access and a Graphify account with access to an
indexed repository. These are two separate authentication steps.

**Check Gemini account eligibility first.** Google retired consumer Google sign-in
for Gemini CLI on June 18, 2026, including free individual accounts and Google AI
Pro/Ultra. The error "This client is no longer supported for Gemini Code Assist
for individuals" concerns Google's model access. Reinstalling Graphify or retrying
Graphify OAuth will not resolve it. See Google's
[consumer account notice](https://developers.google.com/gemini-code-assist/docs/deprecations/code-assist-individuals).

Google documents paid Gemini API keys as a continuing Gemini CLI access route;
choose **Use Gemini API Key** in Gemini CLI if you have eligible API access.
Model usage and billing are separate from Graphify. Existing Code Assist
Standard/Enterprise subscriptions follow Google's
[separate retirement schedule](https://docs.cloud.google.com/gemini/docs/codeassist/sunset).
For consumer Google sign-in, Google directs users to
[Antigravity CLI](https://antigravity.google/docs/cli/gcli-migration/).
This package's installation checks cover Gemini CLI; Antigravity migration has
not been validated for this package. See Google's
[transition announcement](https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli/).

Confirm Gemini CLI can respond to a normal prompt before connecting Graphify.
Sign in at [app.graphify.com](https://app.graphify.com), connect a repository you
are authorized to use, and wait for indexing to complete.

Install the extension from your terminal:

```sh
gemini extensions install https://github.com/Graphify-Labs/Graphify-gemini-extension
```

Review the extension details and approve installation when Gemini CLI prompts.
Start a new Gemini CLI session. To connect your Graphify account, enter:

```text
/mcp auth graphify
```

Complete Graphify sign-in in the browser and return to Gemini CLI. OAuth requires
a local browser and a reachable localhost callback. No API key or client secret
needs to be pasted into this extension. Use `/mcp list` to inspect the connection.

Ask Gemini to list your Graphify workspaces and repositories, then identify the
repository you want to investigate. If it needs to change workspace, review the
choice: workspace selection can also update your account's default workspace.

## Try it

Replace the example repository and symbol names with names from your index:

- "Using Graphify, find where authentication is implemented in `my-repository`
  and cite the relevant files."
- "Trace the callers of `validateSession` and explain how requests reach it."
- "What could be affected if I change this function, and which linked tests
  should I inspect?"
- "Find saved repository context about our deployment process."

Tools and memory features depend on your Graphify access, workspace configuration,
and plan. Your Graphify account and billing remain with Graphify.

## Data and behavior

Tool arguments are sent to Graphify's hosted service under your authenticated
workspace permissions. Returned code context becomes available to Gemini CLI.
Avoid secrets and unrelated private information in queries.

Graphify MCP tools do not edit source files, but they are not all read-only:
`remember` persists memory, and `set_workspace` changes workspace scope and can
change the account's default workspace. When trail capture is enabled, some
search and recall tools may record query context as repository memory. Follow
the connected server's live tool descriptions and Gemini CLI's approval prompts.

Results reflect an indexed snapshot, which may differ from the current branch
or local edits. Inspect current source before editing. Static graph analysis
does not prove runtime behavior or exhaustive test coverage. Semantic lookup
fallbacks are suggestions to verify, and an empty caller list does not prove a
function is unused. Saved memories may be historical or unverified.

The extension adds no hooks, installation scripts, or permission overrides.
Graphify's [privacy policy](https://graphify.com/privacy) and
[terms](https://graphify.com/terms) govern the hosted service. The extension's
source license does not grant hosted service access.

## Manage the extension

```sh
gemini extensions list
gemini extensions update graphify
gemini extensions uninstall graphify
```

Restart Gemini CLI after changing your installed extensions.

## Troubleshooting

- **Google says this client is no longer supported:** review the Gemini account
  eligibility notice above. Resolve Gemini model authentication first, then
  authenticate Graphify separately with `/mcp auth graphify`.
- **Browser says localhost refused to connect:** the browser could not reach
  Gemini's local OAuth callback listener. A callback URL is specific to one
  active login attempt. Keep Gemini CLI running, start a fresh
  `/mcp auth graphify`, and complete sign-in in the browser on the same machine.
  Do not reload an old callback tab or reuse its URL. Gemini CLI 0.63.0's
  [callback implementation](https://github.com/google-gemini/gemini-cli/blob/v0.63.0/packages/core/src/utils/oauth-flow.ts)
  closes the listener after five minutes. If a fresh attempt still fails in
  native Windows Command Prompt, capture the terminal error at that moment and
  check whether the current callback port is listening. For WSL, containers, or
  remote sessions, confirm the browser can reach the CLI's callback environment.
  Redact authorization codes, state values, tokens, and full callback URLs from
  shared logs. This browser error alone does not establish an issuer or token
  exchange failure.
- **Unauthorized or expired login:** run `/mcp auth graphify` and complete the
  browser flow. Do not commit or paste access tokens.
- **No repositories or empty results:** check workspace access and completed
  indexing in Graphify. Use `list_repositories` to discover current IDs.
- **Stale results:** compare with the branch you are editing and reindex through
  Graphify if needed.
- **Duplicate or unexpected connection:** keep one Graphify configuration. A
  `graphify` entry in Gemini CLI's `settings.json` takes precedence over the
  extension's server entry; review existing manual configuration if necessary.
- **Managed or remote environment:** your organization may restrict extensions
  or MCP access. Browser OAuth needs a reachable local callback; authenticate in
  a supported local environment.

## Development and releases

The root `gemini-extension.json` defines the connection, and `GEMINI.md` contains
agent guidance. To try local changes, use `gemini extensions link .` in this
directory. Review installation prompts and use a separate test profile if you
already have Graphify installed.

The default branch is the stable install source. Versioned GitHub releases keep
the manifest version and release tag aligned. The repository's
`gemini-cli-extension` topic enables discovery by the
[Gemini CLI gallery](https://geminicli.com/extensions/browse/); gallery indexing
runs daily and is separate from direct installation.

## Support

- [Graphify website](https://graphify.com) · [Documentation](https://docs.graphify.com)
- [Report an extension issue](https://github.com/Graphify-Labs/Graphify-gemini-extension/issues)
- [Contact Graphify](https://graphify.com/contact) · [founders@graphify.com](mailto:founders@graphify.com)

MIT licensed. See [LICENSE](LICENSE).
