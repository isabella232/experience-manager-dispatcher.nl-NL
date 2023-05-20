---
title: Dispatcher configureren om CSRF-aanvallen te voorkomen
seo-title: Configuring Adobe AEM Dispatcher to Prevent CSRF Attacks
description: Leer hoe te om AEM Dispatcher te vormen om de aanvallen van de Vervalsing van het Verzoek van de Verkeer van de Departement van de Deite te verhinderen.
seo-description: Learn how to configure Adobe AEM Dispatcher to prevent Cross-Site Request Forgery attacks.
uuid: f290bdeb-54e2-4649-b0fc-6257b422af2d
topic-tags: dispatcher
content-type: reference
discoiquuid: d61d021e-b338-4a1d-91ee-55427557e931
exl-id: bcd38878-f977-46a6-b01a-03e4d90aef01
source-git-commit: 3a0e237278079a3885e527d7f86989f8ac91e09d
workflow-type: tm+mt
source-wordcount: '225'
ht-degree: 0%

---

# Dispatcher configureren om CSRF-aanvallen te voorkomen{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM biedt een raamwerk dat bedoeld is om aanvallen met vervalsingen van verzoeken tussen sites te voorkomen. Als u dit framework correct wilt gebruiken, moet u de volgende wijzigingen aanbrengen in de configuratie van de verzender:

>[!NOTE]
>
>Ben zeker om de regelaantallen in de hieronder voorbeelden bij te werken die op uw bestaande configuratie worden gebaseerd. Herinner dat de verzenders de laatste passende regel zullen gebruiken om toe te staan of te ontkennen, zodat plaats de regels dichtbij de bodem van uw bestaande lijst.

1. In de `/clientheaders` de sectie van uw auteur-farm.any en publish-farm.any, voegt de volgende ingang aan de bodem van de lijst toe:\
   `CSRF-Token`
1. In de /filters sectie van uw `author-farm.any` en `publish-farm.any` of `publish-filters.any` bestand, voeg de volgende regel toe om aanvragen voor `/libs/granite/csrf/token.json` via de verzender.\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`
1. Onder de `/cache /rules` deel van uw `publish-farm.any`voegt u een regel toe om te voorkomen dat de verzender de `token.json` bestand. Auteurs omzeilen caching doorgaans, dus u hoeft de regel niet toe te voegen aan uw `author-farm.any`.\
   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

Om te bevestigen dat de configuratie werkt, bekijk de dispatcher.log in DEBUG wijze om te bevestigen dat het token.json- dossier niet in de cache wordt opgeslagen en niet door filters wordt geblokkeerd. U zou berichten moeten zien gelijkend op:\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

U kunt ook controleren of aanvragen slagen in uw apache `access_log`. Verzoeken om &quot;/libs/granite/csrf/token.json zouden een HTTP 200 statuscode moeten terugkeren.
