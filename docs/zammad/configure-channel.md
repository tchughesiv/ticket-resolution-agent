# Configure the Zammad channel

**Commands and rollout order are defined in** [`it-self-service-agent/Makefile`](https://github.com/rh-ai-quickstart/it-self-service-agent/blob/cecf01b1d34895fdfe70d7c137d64804f37a58ca/Makefile). This page only describes the **contract** this repo cares about.

## Credentials Secret

Default name pattern: **`self-service-agent-zammad-credentials`** (from upstream `MAIN_CHART_NAME` + `-zammad-credentials`; see Makefile).

| Key | Role |
|-----|------|
| **`zammad-url`** | Zammad REST API base for MCP (typically ends with **`/api/v1`** for in-cluster installs—match what the Makefile writes). |
| **`zammad-http-token`** | Token MCP uses for API calls |

Env wiring from Secret to pods: upstream [`helm/templates/_env-helpers.tpl`](https://github.com/rh-ai-quickstart/it-self-service-agent/blob/cecf01b1d34895fdfe70d7c137d64804f37a58ca/helm/templates/_env-helpers.tpl) (when `zammad.enabled`).

## Token creation and rotation

Use Makefile targets (do not re-document their Ruby/curl internals here—they change upstream):

| Target | Use |
|--------|-----|
| **`zammad-bootstrap-token`** | Preferred path after Zammad is up; see target body in [`Makefile`](https://github.com/rh-ai-quickstart/it-self-service-agent/blob/cecf01b1d34895fdfe70d7c137d64804f37a58ca/Makefile). |
| **`zammad-trigger-autowizard`** | If admin/bootstrap is not completed. |
| **`zammad-set-token`** | After creating a token manually in Zammad UI. |

After token changes, upstream targets usually restart **`mcp-zammad-mcp`**; if you patch Secrets by hand, restart that deployment yourself.

## Routes and UI

Routes (**`ssa-zammad`**, **`ssa-zammad-embed`**) and hostnames are created by upstream Helm charts; the ticketing install prints URLs in its checklist. Discover with `oc get route -n "${NAMESPACE}"`.

## Chat widget

Zammad **Admin → Channels → Chat**, embed chart under [`helm/zammad-embed/`](https://github.com/rh-ai-quickstart/it-self-service-agent/tree/cecf01b1d34895fdfe70d7c137d64804f37a58ca/helm/zammad-embed). Align `chatId` in [`helm/zammad-embed/values.yaml`](https://github.com/rh-ai-quickstart/it-self-service-agent/blob/cecf01b1d34895fdfe70d7c137d64804f37a58ca/helm/zammad-embed/values.yaml) if needed.

## Production

Replace demo credentials in [`helm/values-zammad-deploy.yaml`](https://github.com/rh-ai-quickstart/it-self-service-agent/blob/cecf01b1d34895fdfe70d7c137d64804f37a58ca/helm/values-zammad-deploy.yaml) and autoWizard token with your org’s secret management—**never commit real secrets to this wrapper repo.**
