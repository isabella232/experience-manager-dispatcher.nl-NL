---
title: De beveiligingscontrolelijst voor verzending
seo-title: The Dispatcher Security Checklist
description: Een lijst met beveiligingscontroles die moet worden ingevuld voordat de productie wordt voortgezet.
seo-description: A security checklist that should be completed before going on production.
uuid: 7bfa3202-03f6-48e9-8d2e-2a40e137ecbe
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: fbfafa55-c029-4ed7-ab3e-1bebfde18248
jcr-lastmodifiedby: remove-legacypath-6-1
index: y
internal: n
snippet: y
exl-id: 49009810-b5bf-41fd-b544-19dd0c06b013
source-git-commit: a21b527700b3a5c3a11eadaefc729e754ba2b37b
workflow-type: tm+mt
source-wordcount: '638'
ht-degree: 0%

---

# De beveiligingscontrolelijst voor verzending{#the-dispatcher-security-checklist}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-05T05:14:35.365-0400

<p>Food for thought listed on <a href="https://jira.corp.adobe.com/browse/DOC-5649">DOC-5649</a>. To be considered while proof-reading.</p> 
<p> </p>

 -->

Adobe raadt u ten zeerste aan de volgende checklist in te vullen voordat u verdergaat met de productie.

>[!CAUTION]
>
>U moet ook de lijst Beveiligingscontrole van uw versie van AEM voltooien voordat u live kunt gaan. Raadpleeg de betreffende [Adobe Experience Manager-documentatie](https://helpx.adobe.com/experience-manager/6-5/sites/administering/using/security-checklist.html).

## De nieuwste versie van Dispatcher gebruiken {#use-the-latest-version-of-dispatcher}

Installeer de nieuwste beschikbare versie die beschikbaar is voor uw platform. U zou uw instantie van Dispatcher moeten bevorderen om de recentste versie te gebruiken om uit product en veiligheidsverhogingen voordeel te halen. Zie [Dispatcher installeren](dispatcher-install.md).

>[!NOTE]
>
>U kunt de huidige versie van uw verzenderinstallatie controleren door het logboekbestand van de verzender te bekijken.
>
>`[Thu Apr 30 17:30:49 2015] [I] [23171(140735307338496)] Dispatcher initialized (build 4.1.9)`
>
>Om het logboekdossier te vinden, inspecteer de dispatcherconfiguratie in uw `httpd.conf`.

## Clients beperken die uw cache kunnen leegmaken {#restrict-clients-that-can-flush-your-cache}

Adobe raadt u aan [beperkt de clients die uw cache kunnen leegmaken.](dispatcher-configuration.md#limiting-the-clients-that-can-flush-the-cache)

## HTTPS inschakelen voor beveiliging van transportlagen {#enable-https-for-transport-layer-security}

Adobe raadt u aan de HTTPS-transportlaag in te schakelen voor auteur- en publicatieinstanties.

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:41:28.841-0400

<p>Recommended to have SSL termination, front end SSL.</p> 
<p>Question is do we want to have SSL communication between dispatcher and AEM instances (publish and/or author).</p> 
<p>We might want to have two items:</p> 
<ul> 
 <li>MUST HTTPS clients -&gt; dispatcher / load balancer</li> 
 <li>NICE load balancer -&gt; dispatcher<br /> </li> 
 <li>NICE dispatcher -&gt; instances if sensitive information such as credit cards / or infrastructure requirements such as DMZ</li> 
</ul>

 -->

## Toegang beperken {#restrict-access}

Wanneer het vormen van de Dispatcher zou u externe toegang zoveel mogelijk moeten beperken. Zie [Voorbeeld-/filtersectie](dispatcher-configuration.md#main-pars_184_1_title) in de Dispatcher documentatie.

## Zorg ervoor dat toegang tot administratieve URL&#39;s wordt geweigerd {#make-sure-access-to-administrative-urls-is-denied}

Zorg ervoor dat u filters gebruikt om externe toegang tot eventuele beheerURL&#39;s, zoals de webconsole, te blokkeren.

Zie [Beveiliging van Dispatcher testen](dispatcher-configuration.md#testing-dispatcher-security) voor een lijst met URL&#39;s die moeten worden geblokkeerd.

## Lijsten van gewenste personen gebruiken in plaats van Lijsten van gewezen personen {#use-allowlists-instead-of-blocklists}

Lijsten van gewenste personen zijn een betere manier om toegangscontrole te verlenen aangezien zij inherent, veronderstellen zij dat alle toegangsverzoeken zouden moeten worden ontkend tenzij zij uitdrukkelijk deel van de lijst van gewenste personen uitmaken. Dit model verstrekt meer restrictieve controle over nieuwe verzoeken die niet zouden kunnen nog zijn herzien of in overweging genomen tijdens een bepaalde configuratiestadium.

## Dispatcher uitvoeren met een specifieke systeemgebruiker {#run-dispatcher-with-a-dedicated-system-user}

Wanneer het vormen van de Dispatcher zou u moeten ervoor zorgen dat de Webserver door een specifieke gebruiker met minste voorrechten wordt in werking gesteld. Het wordt aanbevolen alleen schrijftoegang te verlenen tot de cachemap van de verzender.

Bovendien, moeten de gebruikers IIS hun website als volgt vormen:

1. Selecteer in de fysieke padinstelling voor uw website de optie **Verbinding maken als specifieke gebruiker**.
1. Stel de gebruiker in.

## Ontkenning van service-aanvallen (DoS) voorkomen {#prevent-denial-of-service-dos-attacks}

Een ontkenning van de dienst (Dos) aanval is een poging om een computermiddel niet beschikbaar te maken aan zijn voorgenomen gebruikers.

Op het niveau van de dispatcher, zijn er twee methodes om aanvallen van Dos te vormen te verhinderen: [](https://docs.adobe.com/content/docs/en/dispatcher.html#/filter (Filters))

* Gebruik de module mod_rewrite (bijvoorbeeld [Apache 2.4](https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html)) om URL-validaties uit te voeren (als de URL-patroonregels niet te complex zijn).

* Voorkom dat de verzender URL&#39;s in cache plaatst met onduidelijke extensies door [filters](dispatcher-configuration.md#configuring-access-to-conten-tfilter).\
   Wijzig bijvoorbeeld de caching-regels om caching te beperken tot de verwachte mime-typen, zoals:

   * `.html`
   * `.jpg`
   * `.gif`
   * `.swf`
   * `.js`
   * `.doc`
   * `.pdf`
   * `.ppt`

   Een voorbeeldconfiguratiebestand is zichtbaar voor [beperking van externe toegang](#restrict-access)Dit omvat beperkingen voor mime-typen.

Om volledige functionaliteit op de publiceer instanties veilig toe te laten, vorm filters om toegang tot de volgende knopen te verhinderen:

* `/etc/`
* `/libs/`

Configureer vervolgens filters om toegang tot de volgende knooppuntpaden toe te staan:

* `/etc/designs/*`
* `/etc/clientlibs/*`
* `/etc/segmentation.segment.js`
* `/libs/cq/personalization/components/clickstreamcloud/content/config.json`
* `/libs/wcm/stats/tracker.js`
* `/libs/cq/personalization/*` (JS, CSS en JSON)
* `/libs/cq/security/userinfo.json` (Gebruikersgegevens CQ)
* `/libs/granite/security/currentuser.json` (**gegevens mogen niet in cache worden geplaatst**)

* `/libs/cq/i18n/*` (Internationalisatie)

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:38:17.016-0400

<p>We need to highlight whether a path applies to all versions or specific ones.<br /> </p>

 -->

## Dispatcher configureren om CSRF-aanvallen te voorkomen {#configure-dispatcher-to-prevent-csrf-attacks}

AEM biedt een [kader](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#verification-steps) gericht op het voorkomen van aanvallen van smeedstukken tussen verschillende sites. Als u dit framework op de juiste manier wilt gebruiken, moet u de ondersteuning voor CSRF-token in de verzender lijsten van gewenste personen. U kunt dit doen door:

1. Een filter maken om de `/libs/granite/csrf/token.json` pad;
1. Voeg de `CSRF-Token` aan de `clientheaders` sectie van de configuratie van de Verzender.

## Klikaanvallen voorkomen {#prevent-clickjacking}

Om klikaanvallen te verhinderen adviseren wij dat u uw webserver vormt om `X-FRAME-OPTIONS` HTTP-header ingesteld op `SAMEORIGIN`.

Voor meer [informatie over klikjacking gelieve te zien de plaats OWASP](https://www.owasp.org/index.php/Clickjacking).

## Een beveiligingstest uitvoeren {#perform-a-penetration-test}

Adobe raadt u ten zeerste aan een penetratietest van uw AEM uit te voeren voordat u verdergaat met de productie.
