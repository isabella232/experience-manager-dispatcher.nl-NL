---
title: Een website optimaliseren voor cacheprestaties
seo-title: Optimizing a Website for Cache Performance
description: Leer hoe u uw website ontwerpt om de voordelen van caching te maximaliseren.
seo-description: Dispatcher offers a number of built-in mechanisms that you can use to optimize performance. Learn how to design your web site to maximize the benefits of caching.
uuid: 2d4114d1-f464-4e10-b25c-a1b9a9c715d1
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: ba323503-1494-4048-941d-c1d14f2e85b2
redirecttarget: https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/configuring-performance.html
index: y
internal: n
snippet: y
source-git-commit: 762f575a58f53d25565fb9f67537e372c760674f
workflow-type: tm+mt
source-wordcount: '1134'
ht-degree: 0%

---


# Een website optimaliseren voor cacheprestaties {#optimizing-a-website-for-cache-performance}

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-10-25T04:13:34.919-0400

<p>This is a redirect to /experience-manager/6-2/sites/deploying/using/configuring-performance.html</p>

 -->

>[!NOTE]
>
>Dispatcher-versies zijn onafhankelijk van AEM. U bent mogelijk omgeleid naar deze pagina als u een koppeling naar de Dispatcher-documentatie hebt gevolgd die is ingesloten in de documentatie voor een vorige versie van AEM.

Dispatcher biedt een aantal ingebouwde mechanismen die u kunt gebruiken om de prestaties te optimaliseren. In deze sectie wordt uitgelegd hoe u uw website kunt ontwerpen om de voordelen van caching te maximaliseren.

>[!NOTE]
>
>Het kan u helpen herinneren dat de Dispatcher het geheime voorgeheugen op een standaardWebserver opslaat. Dit betekent dat u:
>
>* kan alles opslaan dat u als pagina kunt opslaan en aanvragen via een URL
>* kan geen andere dingen opslaan, zoals HTTP-headers, cookies, sessiegegevens en formuliergegevens.
>
>In het algemeen, impliceren veel caching strategieën het selecteren van goede URLs en het verlaten van deze extra gegevens.

## Consistente paginacodering gebruiken {#using-consistent-page-encoding}

HTTP-aanvraagheaders worden niet in het cachegeheugen opgeslagen en er kunnen zich dus problemen voordoen als u pagina-coderingsgegevens in de header opslaat. Als Dispatcher dan een pagina uit de cache bedient, wordt de standaardcodering van de webserver gebruikt voor de pagina. Dit probleem kan op twee manieren worden voorkomen:

* Als u slechts één codering gebruikt, moet u ervoor zorgen dat de codering die op de webserver wordt gebruikt, gelijk is aan de standaardcodering van de AEM website.
* Een `<META>` -tag in de HTML `head` -sectie om de codering in te stellen, zoals in het volgende voorbeeld:

```xml
        <META http-equiv="Content-Type" content="text/html; charset=EUC-JP">
```

## URL-parameters vermijden {#avoid-url-parameters}

Vermijd indien mogelijk URL-parameters voor pagina&#39;s die u in cache wilt plaatsen. Als u bijvoorbeeld een fotogalerie hebt, wordt de volgende URL nooit in de cache opgeslagen (tenzij Dispatcher wordt verzonden) [dienovereenkomstig geconfigureerd](dispatcher-configuration.md#main-pars_title_24)):

```xml
www.myCompany.com/pictures/gallery.html?event=christmas&amp;page=1
```

U kunt deze parameters echter als volgt in de pagina-URL plaatsen:

```xml
www.myCompany.com/pictures/gallery.christmas.1.html
```

>[!NOTE]
>
>Deze URL roept dezelfde pagina en dezelfde sjabloon aan als gallery.html. In de sjabloondefinitie kunt u opgeven welk script de pagina rendert of u kunt hetzelfde script voor alle pagina&#39;s gebruiken.

## Aanpassen via URL {#customize-by-url}

Als u gebruikers toestaat de fontgrootte (of een andere lay-outaanpassing) te wijzigen, moet u ervoor zorgen dat de verschillende aanpassingen worden weerspiegeld in de URL.

Cookies worden bijvoorbeeld niet in de cache opgeslagen. Als u de fontgrootte opslaat in een cookie (of een vergelijkbaar mechanisme), blijft de fontgrootte dus niet behouden voor de pagina in de cache. Als gevolg hiervan retourneert Dispatcher willekeurige documenten van een willekeurige tekengrootte.

Door de tekengrootte als kiezer in de URL op te nemen voorkomt u dit probleem:

```xml
www.myCompany.com/news/main.large.html
```

>[!NOTE]
>
>Voor de meeste indelingsaspecten is het ook mogelijk stijlpagina&#39;s en/of clientscripts te gebruiken. Deze werken meestal heel goed met caching.
>
>Dit is ook handig voor een afdrukversie, waarin u bijvoorbeeld een URL kunt gebruiken:
>
>`www.myCompany.com/news/main.print.html`
>
>Met behulp van de scriptglobling van de sjabloondefinitie kunt u een afzonderlijk script opgeven dat de afdrukpagina&#39;s rendert.

## Als titels gebruikte afbeeldingsbestanden ongeldig maken {#invalidating-image-files-used-as-titles}

Als u paginatitels of andere tekst als afbeeldingen rendert, is het raadzaam de bestanden op te slaan, zodat deze worden verwijderd bij een update van de inhoud op de pagina:

1. Plaats het afbeeldingsbestand in dezelfde map als de pagina.
1. Gebruik de volgende naamgevingsindeling voor het afbeeldingsbestand:

   `<page file name>.<image file name>`

U kunt bijvoorbeeld de titel van de pagina myPage.html opslaan in het bestand myPage.title.gif. Dit bestand wordt automatisch verwijderd als de pagina wordt bijgewerkt. Wijzigingen in de paginatitel worden dus automatisch doorgevoerd in de cache.

>[!NOTE]
>
>Het afbeeldingsbestand bestaat niet noodzakelijkerwijs fysiek op de AEM. U kunt een script gebruiken waarmee het afbeeldingsbestand dynamisch wordt gemaakt. Dispatcher slaat het bestand vervolgens op de webserver op.

## Beeldbestanden die voor navigatie worden gebruikt ongeldig maken {#invalidating-image-files-used-for-navigation}

Als u foto&#39;s gebruikt voor de navigatie-items, is de methode in feite hetzelfde als bij titels, iets complexer. Sla alle navigatieafbeeldingen op de doelpagina&#39;s op. Als u twee afbeeldingen gebruikt voor normaal en actief, kunt u de volgende scripts gebruiken:

* Een script waarmee de pagina als normaal wordt weergegeven.
* Een script dat &quot;.normal&quot;-verzoeken verwerkt en het normale beeld retourneert.
* Een script dat &quot;.active&quot;-aanvragen verwerkt en het geactiveerde beeld retourneert.

Het is belangrijk dat u deze afbeeldingen maakt met dezelfde naamgevingsgreep als de pagina, zodat deze afbeeldingen en de pagina worden verwijderd door een update van de inhoud.

Voor pagina&#39;s die niet worden gewijzigd, blijven de afbeeldingen in het cachegeheugen staan, hoewel de pagina&#39;s zelf meestal automatisch ongeldig worden gemaakt.

## Personalisatie {#personalization}

Dispatcher kan geen gepersonaliseerde gegevens in het voorgeheugen onderbrengen, zodat wordt geadviseerd dat u verpersoonlijking beperkt tot waar het noodzakelijk is. Ter illustratie:

* Als u een vrij aanpasbare startpagina gebruikt, moet die pagina telkens worden samengesteld wanneer een gebruiker erom vraagt.
* Als u daarentegen 10 verschillende startpagina&#39;s kunt kiezen, kunt u elk van deze pagina&#39;s in cache plaatsen, waardoor de prestaties verbeteren.

>[!NOTE]
>
>Als u elke pagina personaliseert (bijvoorbeeld door de naam van de gebruiker in de titelbalk te plaatsen) kunt u deze niet in cache plaatsen, wat een grote invloed op de prestaties kan hebben.
>
>Als u dit echter moet doen, kunt u:
>
>* Gebruik iFrames om de pagina op te splitsen in één onderdeel dat voor alle gebruikers hetzelfde is en één onderdeel dat voor alle pagina&#39;s van de gebruiker hetzelfde is. Vervolgens kunt u beide onderdelen in cache plaatsen.
>* gebruik client-side JavaScript om gepersonaliseerde informatie weer te geven. U moet er echter voor zorgen dat de pagina nog steeds correct wordt weergegeven als een gebruiker JavaScript uitschakelt.
>


## Vaste verbindingen {#sticky-connections}

[Vaste verbindingen](dispatcher.md#TheBenefitsofLoadBalancing) Zorg ervoor dat de documenten voor één gebruiker allen op de zelfde server samengesteld zijn. Als een gebruiker deze map verlaat en er later weer naar terugkeert, blijft de verbinding behouden. Definieer één map voor alle documenten die voor de website kleverige verbindingen vereisen. Probeer er geen andere documenten in op te nemen. Dit is van invloed op de taakverdeling als u gepersonaliseerde pagina&#39;s en sessiegegevens gebruikt.

## MIME-typen {#mime-types}

Er zijn twee manieren waarop een browser het type bestand kan bepalen:

1. Door de uitbreiding (bijvoorbeeld .html, .gif, .jpg, enz.)
1. Door het MIME-type dat de server met het bestand verzendt.

Voor de meeste bestanden wordt het MIME-type geïmpliceerd in de bestandsextensie. i.e.:

1. Door de uitbreiding (bijvoorbeeld .html, .gif, .jpg, enz.)
1. Door het MIME-type dat de server met het bestand verzendt.

Als de bestandsnaam geen extensie heeft, wordt deze weergegeven als onbewerkte tekst.

Het MIME-type maakt deel uit van de HTTP-header en wordt daarom niet in de cache opgeslagen. Als uw AEM bestanden retourneert die geen herkend bestand hebben dat eindigt, maar in plaats daarvan afhankelijk zijn van het MIME-type, worden deze bestanden mogelijk onjuist weergegeven.

Volg de onderstaande richtlijnen om ervoor te zorgen dat bestanden correct in het cachegeheugen worden opgeslagen:

* Zorg ervoor dat bestanden altijd de juiste extensie hebben.
* Vermijd generische de manuscripten van de dossierserver, die URLs zoals download.jsp?file=2214 hebben. Schrijf het script opnieuw om URL&#39;s te gebruiken die de bestandsspecificatie bevatten; voor het vorige voorbeeld is dit download.2214.pdf.

