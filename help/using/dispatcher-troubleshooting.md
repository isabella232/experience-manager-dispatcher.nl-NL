---
title: Problemen met verzending van problemen oplossen
seo-title: Problemen met AEM Dispatcher oplossen
description: Leer problemen met Dispatcher oplossen.
seo-description: Leer problemen met AEM Dispatcher op te lossen.
uuid: 9c109a48-d921-4b6e-9626-1158cebc41e7
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: a612e745-f1e6-43de-b25a-9adcaadab5cf
translation-type: tm+mt
source-git-commit: 76cffbfb616cd5601aed36b7076f67a2faf3ed3b

---


# Problemen met verzending van problemen oplossen {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>Dispatcher-versies zijn onafhankelijk van AEM, maar de Dispatcher-documentatie is ingesloten in de AEM-documentatie. Gebruik altijd de Dispatcher-documentatie die is ingesloten in de documentatie voor de nieuwste versie van AEM.
>
>U bent mogelijk omgeleid naar deze pagina als u een koppeling naar de Dispatcher-documentatie hebt gevolgd die is ingesloten in de documentatie voor een vorige versie van AEM.

>[!NOTE]
>
>Raadpleeg ook de [Dispatcher Knowledge Base](https://helpx.adobe.com/cq/kb/index/dispatcher.html), de [Problemen](https://helpx.adobe.com/adobe-cq/kb/troubleshooting-dispatcher-flushing-issues.html) met het leegmaken van de Dispatcher Dispatcher en de veelgestelde vragen over de [belangrijkste problemen](dispatcher-faq.md) bij Verzender voor meer informatie.

## Controleer de basisconfiguratie {#check-the-basic-configuration}

Zoals altijd zijn de eerste stappen het controleren van de grondbeginselen:

* [Basisbewerking bevestigen](#ConfirmBasicOperation)
* Controleer alle logbestanden op uw webserver en verzender. Verhoog zo nodig de `loglevel` gebruikte waarde voor de [logboekregistratie](#Logging)van de verzender.

* [Controleer uw configuratie](#ConfiguringtheDispatcher):

   * Hebt u meerdere verzenders?

      * Hebt u bepaald welke Dispatcher de website/pagina verwerkt die u onderzoekt?
   * Hebt u filters geïmplementeerd?

      * Heeft dit gevolgen voor de zaak die u onderzoekt?


## IIS Diagnostic Tools {#iis-diagnostic-tools}

IIS verstrekt diverse spoorhulpmiddelen, afhankelijk van de daadwerkelijke versie:

* IIS 6 - diagnostische hulpmiddelen IIS kunnen worden gedownload en worden gevormd
* IIS 7 - het vinden is volledig geïntegreerd

Deze kunnen u helpen activiteit controleren.

## IIS en 404 niet gevonden {#iis-and-not-found}

Wanneer het gebruiken van IIS zou u kunnen ervaren `404 Not Found` die in diverse scenario&#39;s zijn teruggekeerd. Zo ja, zie de volgende artikelen in de Knowledge Base.

* [IIS 6/7: POST-methode retourneert 404](https://helpx.adobe.com/dispatcher/kb/IIS6IsapiFilters.html)
* [IIS 6: Aanvragen aan URL&#39;s die het basispad `/bin` retourneren `404 Not Found`](https://helpx.adobe.com/dispatcher/kb/RequestsToBinDirectoryFailInIIS6.html)

U moet ook controleren of de cachroot van de verzender en de hoofdmap van het IIS-document op dezelfde map zijn ingesteld.

## Problemen bij het verwijderen van workflowmodellen {#problems-deleting-workflow-models}

**Symptomen**

Problemen bij het verwijderen van workflowmodellen wanneer een instantie van een AEM-auteur wordt benaderd via de Dispatcher.

**Stappen om te reproduceren:**

1. Meld u aan bij de instantie van de auteur (bevestig dat aanvragen worden gerouteerd via de dispatcher).
1. Een nieuwe workflow maken; bijvoorbeeld als Titel is ingesteld op workflowToDelete.
1. Controleer of de workflow is gemaakt.
1. Selecteer en klik met de rechtermuisknop op de workflow en klik op **Verwijderen**.

1. Klik op **Ja** om te bevestigen.
1. Er wordt een foutbericht weergegeven:\
   &quot; `ERROR 'Could not delete workflow model!!`&quot;.

**Resolutie**

Voeg de volgende kopteksten aan de `/clientheaders` sectie van uw `dispatcher.any` dossier toe:

* `x-http-method-override`
* `x-requested-with`

`{  
{  
/clientheaders  
{  
...  
"x-http-method-override"  
"x-requested-with"  
}`

## Interferentie met mod_dir (Apache) {#interference-with-mod-dir-apache}

Hierin wordt beschreven hoe de dispatcher communiceert met `mod_dir` binnen de Apache-webserver, aangezien dit tot verschillende, mogelijk onverwachte effecten kan leiden:

### Apache 1.3 {#apache}

In Apache 1.3 `mod_dir` wordt elke aanvraag afgehandeld wanneer de URL wordt toegewezen aan een map in het bestandssysteem.

Het zal ofwel:

* de aanvraag omleiden naar een bestaand `index.html` bestand
* een mappenlijst genereren

Wanneer de verzender wordt toegelaten, verwerkt het dergelijke verzoeken door zich als manager voor het inhoudstype te registreren `httpd/unix-directory`.

### Apache 2.x {#apache-x}

In Apache 2.x zijn de dingen anders. Een module kan verschillende stadia van het verzoek, zoals correctie URL behandelen. `mod_dir` handelt dit werkgebied af door een aanvraag (wanneer de URL naar een map wordt toegewezen) om te leiden naar de URL met een `/` toevoeging.

Dispatcher onderschept de `mod_dir` correctie niet, maar verwerkt de aanvraag volledig naar de omgeleide URL (d.w.z. met `/` toevoeging). Dit kan een probleem opleveren als de externe server (bijvoorbeeld AEM) verzoeken aan `/a_path` verschillende verzoeken aan `/a_path/` (wanneer `/a_path` kaarten aan een bestaande map) behandelt.

Als dit gebeurt, moet u:

* uitschakelen `mod_dir` voor de `Directory` of `Location` substructuur die door de verzender wordt afgehandeld

* gebruiken `DirectorySlash Off` om `mod_dir` niet toe te voegen `/`