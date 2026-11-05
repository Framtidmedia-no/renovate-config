# renovate-config

Felles Renovate-preset for alle repoer i **Framtidmedia-no**, **Gange-Rolv-AS** og **frlund3**.

Repoet er offentlig fordi Renovate-appen må kunne lese preseten på tvers av org-er.
Det inneholder kun dependency-policy — aldri secrets, interne URL-er eller kundedata.

## Bruk

Hvert repos `renovate.json` skal kun inneholde:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>Framtidmedia-no/renovate-config"]
}
```

Repo-spesifikke regler (f.eks. sharp-koblingen i publify/Somify, Node-major-sperren i
GangeRolv-Drift, kjernegruppene i Framtid-AD/widget-plattform) legges i repoets egen
`renovate.json` **etter** `extends` — lokale verdier vinner over preseten.

## Policy (kort)

- **Månedlig kadens**: vanlige oppdaterings-PRs åpnes natt til den 1. i måneden.
  Sikkerhetsfikser (`vulnerabilityAlerts`) går alltid utenom schedule.
- **minimumReleaseAge 3 dager**: nyutgivelser må modne før de foreslås — beskytter mot
  ødelagte utgivelser (jf. Sentry 10.72.0, aug 2026).
- **Minor/patch**: samles i én PR og automerges på grønn CI. Renovate merger selv
  (`platformAutomerge: false`) og venter på *alle* sjekker på PR-en — ikke GitHubs
  auto-merge, som bare venter på påkrevde sjekker i branch protection. De fleste
  repoene har ingen påkrevde sjekker, og da ville GitHub merget umiddelbart.
- **Socket (FRA-1763):** der Socket-appen er installert (Framtidmedia-no og Gange-Rolv-AS, alle
  repoer) legger den to check runs på hver PR: «Socket Security: Pull Request Alerts» og «Socket Security: Project
  Report» (verifisert på Somify, Vegsundno og GangeRolv-Drift 05.11.2026). Renovate leser alle check runs
  (`getBranchStatus` i GitHub-plattformen) og automerger bare når ingen er failure og alle
  er success, neutral eller skipped. `ignoreTests: false` står eksplisitt i preseten så ingen
  kan tro at den er av. **Preseten kan ikke kreve et bestemt sjekknavn.** Det som garanterer
  at ingen merge skjer uten grønn Socket er at sjekken er *påkrevd* i branch protection eller
  ruleset: da avviser GitHub også Renovates merge via API. Legg kontekstene over til i
  `required_status_checks` når Socket er installert i repoet (ikke før: en påkrevd sjekk som
  aldri kommer blokkerer alle merger). Endringen i preseten er en eksplisitt `ignoreTests: false`,
  som er Renovates standard, så ingen repoer (heller ikke Gange-Rolv) får endret oppførsel.
- **Ingen Dependency Dashboard** (`dependencyDashboard: false`): issuet ble speilet til
  Linear som en sak som aldri kan lukkes (FRA-188). Majors kommer fortsatt som egne PR-er.
- **Majors**: én PR per pakke, aldri automerge, label `major`.
- **Økosystem-sperrer**: TypeScript 5.x, ESLint 9.x, brace-expansion 1.x — se
  beskrivelsene i `default.json` for begrunnelse og når de kan fjernes.

## Endringer

Endringer her treffer alle repoene ved neste Renovate-kjøring. Test med:

```bash
npx --yes --package renovate -- renovate-config-validator default.json
```
