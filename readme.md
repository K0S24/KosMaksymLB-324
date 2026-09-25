# LB 324

## Aufgabe 1
Für neue Funktionalitäten gibt es die Issue-Vorlage **Neue Anforderung** (`.github/ISSUE_TEMPLATE/anforderung.yml`).
Jede Issue wird manuell mit einer dieser Etiketten versehen: `Funktionale Anforderung`, `Qualitätsanforderung`, `Randanforderung`.

## Aufgabe 2
Erklären Sie hier, wie man `pre-commit` installiert.

Einmalig im Wurzelverzeichnis des Projekts ausführen:

```
pip install -r requirements.txt
pip install pre-commit
pre-commit install
```

`pre-commit install` richtet beide Haken ein (siehe `default_install_hook_types` in `.pre-commit-config.yaml`). Falls nötig, kann man sie auch einzeln installieren:

```
pre-commit install --hook-type pre-commit
pre-commit install --hook-type pre-push
```

Danach läuft alles automatisch:
- **bei jedem `git commit`** wird der Code mit `black` formatiert. Wurde etwas umformatiert, bricht der Commit ab: Dateien nochmals mit `git add` hinzufügen und erneut committen.
- **bei jedem `git push`** werden die Tests mit `pytest` ausgeführt. Schlägt ein Test fehl, wird nicht gepusht.

Manuell alle Hooks ausführen: `pre-commit run --all-files` bzw. `pre-commit run --hook-stage pre-push`.

## Aufgabe 4
Erklären Sie hier, wie Sie das Passwort aus Ihrer lokalen `.env` auf Azure übertragen.

**URL der Applikation:** https://kosmaksym-lb324-gzbkfwe2a9bmdydm.germanywestcentral-01.azurewebsites.net

Die `.env` ist in der `.gitignore` und kommt deshalb nie auf github. Das Passwort wird darum von Hand in Azure eingetragen:

1. Im [Azure Portal](https://portal.azure.com) den App Service öffnen.
2. Links unter **Settings** auf **Environment variables** (früher **Configuration → Application settings**) klicken.
3. **+ Add** klicken, Name `PASSWORD`, Wert = Wert aus der `.env` (auf Azure: mein github-Benutzername), **Apply** und nochmals **Apply / Save** klicken, Neustart bestätigen.
4. Die App liest den Wert mit `os.getenv("PASSWORD")` aus den Umgebungsvariablen, genau wie lokal aus der `.env`.

Startup Command (Settings → Configuration → Stack settings): `gunicorn --bind=0.0.0.0 --timeout 600 app:app`

**Automatische Auslieferung:** Im Deployment Center ist die github-Ablage mit dem Ast `main` verbunden. Azure hat dafür die Datei `.github/workflows/main_*.yml` erstellt, welche bei jedem push auf `main` (also bei jedem erfolgreichen merge in `main`) die Applikation neu auf Azure ausliefert.
