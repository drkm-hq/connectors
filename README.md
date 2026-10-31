<p align="center">
  <a href="https://github.com/drkm-hq"><img src="darkmatter-mark.svg" width="48" alt="Darkmatter"></a>
</p>

<h1 align="center">Connectors</h1>

<p align="center">How a Darkmatter outpost reaches the systems around it, what connects today, and how to add your own.</p>

<br>

An outpost does its own work through its capabilities. Everything else it reaches through a **connection**: an outside service the outpost joins as a client so its agent can use the service's tools. Connections come in two kinds: built-in ones with their own forms, and any server that speaks the Model Context Protocol.

## Built-in connections

In the console, an administrator opens **Settings → Connections** and chooses **Add connection**; everyone can see each connection's status and tools. The built-in kinds today are a TAK server, Palantir Foundry, a video platform and a media server, each with its own form; weather comes configured by the deployment. When a service answers on its usual port nearby, its row reads *Found locally*; nothing is joined or imported until you choose it.

## MCP servers

Any server that speaks MCP over Streamable HTTP can join, as can a legacy SSE endpoint on a path ending in `/sse`. Under **Settings → Connections → Third-party MCPs**, choose **Add MCP**, paste the server's link and press **Connect**. The outpost works out whether the server wants OAuth, opens the provider's sign-in page and returns you to Connections when it is done; a server that needs no sign-in connects directly. **Test connection** checks the sign-in and counts the tools, and **Review tools** lists them.

What the current release expects of a server:

- OAuth through dynamic client registration or client ID metadata documents. A server that requires a hand-registered OAuth app is not supported.
- Tools. Local stdio servers, custom headers, MCP resources and prompts are not supported.
- At most 32 MCP connections per outpost.

A connected server's row reads *Connected*, *Sign-in required* or *Disabled*. Its switch disables the server without dropping its sign-in. **Disconnect** removes the saved connection and its tokens; the grant on the provider's side stays until you revoke it in the provider's account settings.

## How tools reach the agent

Every connection's tools land in one catalog on the outpost, with the names, descriptions and input schemas you can read on the dialog's Tools step. The agent is not handed them wholesale: it lists, searches and reads a schema, then calls one. MCP tools are named after the server and the tool, `mcp_<server>_<tool>`, and chat attributes each call to the server it came from. A connection that is down costs its tools, not the turn. While a connection is joined, the agent's prompt carries a paragraph about it, so it knows the service is there without being asked.

Inside the agent's sandbox, configured connections also appear as typed Python bindings under `drkm.tools`, one module per connection, with the connector prefix dropped from each function name:

```python
from drkm import tools

help(tools)        # every configured connection, as a module of typed functions
```

Calls run on the outpost with the current turn's authority; the program never sees a credential or an address. The bindings are staged only inside a sandbox, so they are not available over the external SDK connection.

## Scope and keys

A connection belongs to the outpost, not to a person. Every agent turn on that outpost can use it, including turns a peer in the mesh delegates there; it is not replicated to other outposts, and per-operator sign-in is not offered. Give a service account the least access the agent needs.

Saved secrets, connector keys and MCP sign-ins among them, are listed by holder and last four characters under **Settings → Keys**. Deleting a key a connection requires removes that connection, an optional key is cleared on its own, and an MCP sign-in is signed out while its link stays. Keys set by the deployment are read-only there.

## Skills

Connectors supply tools; skills tell the agent how to use them. A skill is a saved procedure the agent follows: a name, a one-paragraph description of when it applies, and a Markdown body of instructions, kept as a `SKILL.md` file. Platform skills ship with each release; personal skills are yours, saved on the outpost. Invoking a skill never runs code or grants credentials, network or permissions: the body is guidance, not authority.

## Request a connector

The software you want the agent to reach is the best signal of what to build next, so ask for it. Open a [connector request](https://github.com/drkm-hq/connectors/issues/new?template=connector-request.yml) with the service, how it is reached (an MCP endpoint, an API, a protocol and port) and what you want the agent to be able to do with it. We review every request, answer on the issue, and build the ones that fit into a coming release, so a connector one team asks for ships for everyone.

Three ways to take part:

- **Ask for one.** A connector request, as above.
- **Add one that works.** If an MCP server connects from its link, open a pull request that adds it to the table below, with a note on what sign-in it needed.
- **Report one that will not.** Open an issue with the link (never a secret), the row's status and what Test connection reported.

## Known to connect

| Service | Kind | Notes |
| --- | --- | --- |
| TAK servers | Built-in | Its own form under Add connection. |
| Palantir Foundry | Built-in | Its own form under Add connection. |
| Video platform | Built-in | Its own form under Add connection. Live feeds and snapshots. |
| Media server (MediaMTX) | Built-in | Its own form under Add connection. |
| Weather | Built-in | Configured by the deployment. |
| AWS | MCP | Connects from its link. |
| Atlassian | MCP | Connects from its link once an Atlassian admin allowlists the console's origin. |

## Models

Darkmatter's models and model cards are published on Hugging Face at [huggingface.co/DarkmatterLabs](https://huggingface.co/DarkmatterLabs).

<p align="center"><sub>© 2026 Darkmatter Labs, Inc. · <a href="https://drkm.ai">drkm.ai</a> · <a href="https://github.com/drkm-hq">GitHub</a> · <a href="https://huggingface.co/DarkmatterLabs">Hugging Face</a></sub></p>
