---
title: Dispatcher configureren om CSRF-aanvallen te voorkomen
seo-title: Adobe AEM Dispatcher configureren om CSRF-aanvallen te voorkomen
description: Leer hoe te om AEM Dispatcher te vormen om de aanvallen van de Vervalsing van het Verzoek van de dwars-Plaats te verhinderen.
seo-description: Leer hoe u Adobe AEM Dispatcher configureert voor het voorkomen van aanvallen met smeden tussen verschillende sites.
uuid: f290bdeb-54e2-4649-b0fc-6257b422af2d
topic-tags: dispatcher
content-type: reference
discoiquuid: d61d021e-b338-4a1d-91ee-55427557e931
translation-type: tm+mt
source-git-commit: 69edbe7608b46c93d238515e4223606eadad0ac4

---


# Dispatcher configureren om CSRF-aanvallen te voorkomen{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM biedt een kader dat bedoeld is om aanvallen met smeden van verschillende sites te voorkomen. Als u dit framework correct wilt gebruiken, moet u de volgende wijzigingen aanbrengen in de configuratie van de verzender:

>[!NOTE]
>
>Ben zeker om de regelaantallen in de hieronder voorbeelden bij te werken die op uw bestaande configuratie worden gebaseerd. Herinner dat de verzenders de laatste passende regel zullen gebruiken om toe te staan of te ontkennen, zodat plaats de regels dichtbij de bodem van uw bestaande lijst.

1. In de `/clientheaders` sectie van uw auteur-farm.any en publish-farm.any, voeg de volgende ingang aan de bodem van de lijst toe:\
   `CSRF-Token`
1. In de /filters sectie van uw `author-farm.any` en `publish-farm.any` of `publish-filters.any` dossier, voeg de volgende lijn toe om verzoeken `/libs/granite/csrf/token.json` door de verzender toe te staan.\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`
1. Voeg onder de `/cache /rules` sectie van uw `publish-farm.any`bestand een regel toe om te voorkomen dat de verzender het `token.json` bestand in de cache plaatst. Auteurs omzeilen caching gewoonlijk, zodat zou u niet de regel in uw `author-farm.any`moeten toevoegen.\
   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

Om te bevestigen dat de configuratie werkt, bekijk de dispatcher.log in DEBUG wijze om te bevestigen dat het token.json- dossier niet in de cache wordt opgeslagen en niet door filters wordt geblokkeerd. U zou berichten moeten zien gelijkend op:\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

U kunt ook controleren of aanvragen slagen in uw apache `access_log`. Verzoeken om &quot;/libs/granite/csrf/token.json zouden een HTTP 200 statuscode moeten terugkeren.
