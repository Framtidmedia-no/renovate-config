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
- **Minor/patch**: samles i én PR og automerges på grønn CI.
- **Majors**: én PR per pakke, aldri automerge, label `major`.
- **Økosystem-sperrer**: TypeScript 5.x, ESLint 9.x, brace-expansion 1.x — se
  beskrivelsene i `default.json` for begrunnelse og når de kan fjernes.

## Endringer

Endringer her treffer alle repoene ved neste Renovate-kjøring. Test med:

```bash
npx --yes --package renovate -- renovate-config-validator default.json
```
