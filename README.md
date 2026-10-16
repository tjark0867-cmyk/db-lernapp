# DB Lernapp – Update-Ordner

Inoffizielle Lernhilfe. Dieses Repository verteilt neue Versionen an die installierten Apps.

* `version.json` – Nummer der aktuellen Version und Prüfsummen
* `app/*.bin` – die App selbst, **verschlüsselt** (AES-256-GCM). Nur die installierte App kann sie öffnen.
* `projekt.bin` – Sicherung des Projekts, **verschlüsselt** mit dem Projekt-Schlüssel des Besitzers.

Ohne die passenden Schlüssel sind die Dateien nicht lesbar.

## Weiterarbeiten in einer neuen Claude-Sitzung

1. Dieses Repository in der Sitzung hinzufügen und den Projekt-Schlüssel nennen.
2. Wiederherstellen:

```bash
python3 - <<'PY'
import base64, io, tarfile, os
from cryptography.hazmat.primitives.ciphers.aead import AESGCM
key = input("Projekt-Schlüssel: ").strip()
k = base64.urlsafe_b64decode(key + "=" * (-len(key) % 4))
blob = open("projekt.bin", "rb").read()
raw = AESGCM(k).decrypt(blob[:12], blob[12:], b"dbl-projekt")
os.makedirs("../signaltrainer", exist_ok=True)
tarfile.open(fileobj=io.BytesIO(raw)).extractall("../signaltrainer")
open("../signaltrainer/.projekt-schluessel", "w").write(key + "\n")
print("Projekt wiederhergestellt in ../signaltrainer")
PY
```

3. Änderungen in `template.html`, dann `python3 release.py --push` (Repo-Ordner über `DBL_REPO_DIR`).
