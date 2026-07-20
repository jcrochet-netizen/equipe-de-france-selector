# Équipe de France 2026 — Outil « Encore ou Stop ? »

Outil interactif (1 seul fichier `index.html`, sans dépendance) : les supporters composent
les Bleus de demain en glissant chaque joueur dans **« Encore »** (à garder en sélection) ou
**« Stop »**, puis partagent leur choix.

- Effectif récupéré via l'**API Sportmonks** (photos, noms), figé dans le fichier.
- **48 joueurs** : les 26 de la Coupe du monde 2026 + 22 joueurs additionnels.
- **Sans valeur marchande ni salaire** (logique retirée) — juste photo + nom.
- Drag & drop **souris + tactile**, boutons rapides **Encore / Stop**.
- Écran de résultat (deux colonnes), partage **X, Facebook, WhatsApp, Web Share**, copie,
  **téléchargement d'une image PNG**, détection auto de l'URL de page hôte.
- Section **« Avis de la communauté »** prête (désactivée tant que `CONFIG.votesUrl` est vide).

Widgets jumeaux : [OM](https://github.com/jcrochet-netizen/mercato-om-selector) ·
[Real Madrid](https://github.com/jcrochet-netizen/mercato-real-madrid-selector)

## Embed WordPress (bloc HTML personnalisé)

```html
<iframe
  id="mercato-fr"
  src="https://jcrochet-netizen.github.io/equipe-de-france-selector/"
  scrolling="no"
  loading="lazy"
  referrerpolicy="strict-origin-when-cross-origin"
  style="width:100%;border:0;display:block;height:1600px;overflow:hidden;"
  title="Équipe de France 2026 — Encore ou Stop ?"></iframe>

<script>
(function () {
  var WIDGET_ORIGIN = 'https://jcrochet-netizen.github.io';
  var f = document.getElementById('mercato-fr');
  function sendUrl() {
    try { f.contentWindow.postMessage({ type: 'mercato-fr-parent-url', url: location.href }, WIDGET_ORIGIN); } catch (e) {}
  }
  f.addEventListener('load', sendUrl);
  if (f.contentWindow) { sendUrl(); }
  window.addEventListener('message', function (e) {
    if (e.origin !== WIDGET_ORIGIN) return;
    if (!e.data) return;
    if (e.data.type === 'mercato-fr-ready') { sendUrl(); }
    if (e.data.type === 'mercato-fr-height') {
      var h = parseInt(e.data.height, 10);
      if (h > 0) { f.style.height = h + 'px'; }
    }
  });
})();
</script>
```

## Personnaliser (en haut du `<script>`)

- **`PLAYERS`** : effectif (id Sportmonks, nom, groupe, photo).
- **`SHORT`** : libellé court d'affichage par id (désambiguïse T./L. Hernández, M./K. Thuram, Kolo Muani).
- **`CONFIG.votesUrl`** : endpoint Google Apps Script pour activer « Avis de la communauté »
  (Sheet dédié). Vide = désactivé.
- **`CONFIG.shareUrl`** : fallback si l'URL de la page hôte n'est pas détectée.

## Mettre à jour l'effectif

Équipe de France (national) = ID Sportmonks `18647`. Recherche joueur :
`GET https://api.sportmonks.com/v3/football/players/search/{nom}?api_token=TON_TOKEN`

---
*Données : Sportmonks. Effectif équipe de France (26 CdM 2026 + joueurs additionnels).*
