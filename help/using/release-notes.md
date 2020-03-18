---
title: Opmerkingen bij de release AEM Dispatcher
seo-title: Opmerkingen bij de release AEM Dispatcher
description: Opmerkingen bij de release specifiek voor Adobe Experience Manager Dispatcher
seo-description: Opmerkingen bij de release specifiek voor Adobe Experience Manager Dispatcher
uuid: ae3ccf62-0514-4c03-a3b9-71799a482cbd
topic-tags: release-notes
content-type: reference
products: SG_EXPERIENCEMANAGER/6.4
discoiquuid: ff3d38e0-71c9-4b41-85f9-fa896393aac5
translation-type: tm+mt
source-git-commit: 328bc82673783b4a2df2d68481fa7eec88b74b01

---


# Opmerkingen bij de release AEM Dispatcher{#aem-dispatcher-release-notes}

## Geen informatie {#release-information}

|  |  |
|--- |--- |
| Producten | Dispatcher Adobe Experience Manager (AEM) |
| Versie | 4.3.3 |
| Type | Kleine release |
| Date | 18 oktober 2019 |
| URL downloaden | <ul><li>[Apache 2.4](release-notes.md#apache)</li><li>[Microsoft Internet Information Services (IIS)](release-notes.md#iis)</li></ul> |
| Compatibiliteit | AEM 6.1 of hoger |

## Systeemvereisten en -vereisten {#system-requirements-and-prerequisites}

Raadpleeg de pagina [Ondersteunde platforms](https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/technical-requirements.html) voor meer informatie over vereisten en voorwaarden.

Adobe raadt u ten zeerste aan de nieuwste versie van AEM Dispatcher te gebruiken om de meest recente functionaliteit, de meest recente opgeloste problemen en de best mogelijke prestaties te benutten.

## Installatie-instructies {#installation-instructions}

Zie [Dispatcher](dispatcher-install.md)installeren voor gedetailleerde instructies.

## Releasegeschiedenis {#release-history}

### Release 4.3.3 (2019-okt-18) {#october}

**Opgeloste problemen**:

* DISP-739 - LogLevel dispatcher: **niveau** werkt niet
* DISP-749 - Alpine Linux dispatcher crasht met spoor logboekniveau

**Verbeteringen**:

* DISP-813 - Steun in Dispatcher voor openssl 1.1.x
* DISP-814 - Apache 40x-fouten tijdens cachefoppen
* DISP-818 - mod_validate voegt cache-Control kopballen voor uncacheable inhoud toe
* DISP-821 - Sla logcontext niet op in socket
* DISP-822 - de Verzender zou opiniepeiling in plaats van uitgezochte moeten gebruiken
* DISP-824 - Secure DispatcherUseForwardedHost
* DISP-825 - Logboek speciaal bericht wanneer er geen ruimte meer op schijf is
* DISP-826 - Steun terugzetURIs met een vraagkoord

**Nieuwe functies**:

* DISP-703 - Farm Specific Cache Hit Ratio
* DISP-827 - Lokale server voor het testen
* DISP-828 - Een testdockerafbeelding maken voor een dispatcher

### Release 4.3.2 (2019-jan-31) {#jan}

**Opgeloste problemen**:

* DISP-734 - de Verzender veroorzaakt neerstorting in insert_output_filter als niet geplaatst als manager
* DISP-735 - REs werkt niet op Alpine Linux
* DISP-740 - Laden van dispatcher in macOS Mojave is standaard uitgeschakeld
* DISP-742 - Geblokkeerde verzoeken kunnen informatie aan auth checker beschermde middelen lekken

**Verbeteringen**:

* DISP-746 - Tekenreeksen zonder label in dispatcher.any moet een waarschuwing genereren

**Nieuwe functie**:

* DISP-747 - Geef aanvraaginformatie op in een Apache-omgeving

### Release 4.3.1 (2018-okt-16) {#oct}

**Opgeloste problemen**:

* DISP-656 - De Dispatcher dient onjuiste kopbal ETag
* DISP-694 - onderdruk waarschuwingen wanneer het houden van levende verbindingen stale gaat
* DISP-714 - Het gebaseerde zittingsbeheer van Cookie werkt niet in IIS
* DISP-715 - Veilige vlag voor teruggegeven koekje
* DISP-720 - Tijdelijke bestanden niet gesloten, kan tot uitputting (teveel geopende bestanden) leiden
* DISP-721 - Dispatcher onderbreekt opiniepeiling() wanneer Apache het onderliggende item probleemloos opnieuw start
* DISP-722 - De dossiers van het geheime voorgeheugen worden gecreeerd met octale wijze 0600
* DISP-723 - impliciete timeout van 10 minuten (en probeer opnieuw) wanneer Render timeouts aan 0 worden geplaatst
* DISP-725 - Navolgende karakters na koorden worden stil omgezet in naamloze waarde
* DISP-726 - Logboek een waarschuwing wanneer geen landbouwbedrijf eigenlijk de inkomende gastheer aanpast
* DISP-727 - Verzender controleert de lengte van de aanvraag voor lege cachebestanden
* DISP-730 - 404 wanneer het proberen om tot kopbaldossier over dispatcher toegang te hebben
* DISP-731 - Dispatcher is kwetsbaar voor Log Injection
* DISP-732 - Dispatcher moet opeenvolgende ‘/’ verwijderen in de URL
* DISP-733 - Dispatcher moet een paginakoptekst instellen (berekenen)

**Verbeteringen**:

* DISP-656 - De Dispatcher dient onjuiste kopbal ETag
* DISP-694 - onderdruk waarschuwingen wanneer het houden van levende verbindingen stale gaat
* DISP-715 - Veilige vlag voor teruggegeven koekje
* DISP-722 - De dossiers van het geheime voorgeheugen worden gecreeerd met octale wijze 0600
* DISP-726 - Logboek een waarschuwing wanneer geen landbouwbedrijf eigenlijk de inkomende gastheer aanpast

### Release 4.3.0 (2018-jun-13) {#jun}

**Opgeloste problemen**:

* DISP-682 - Numeriek logniveau onjuist toegepast
* DISP-685 - 32 beetje de binaire getallen van de SPARC van Solaris hebben een ongedefinieerde verwijzing naar __divdi3
* DISP-688 - Dispatcher retourneert geen &quot;X-Cache-Info&quot; header bij 404-reactie
* DISP-690 - Last-Modified header is not cacheable
* DISP-691 - de Overtredingen van de toegang in w3wp.exe
* DISP-693 - Behoefte om de architecturale details voor solaris servers op de de downloadpagina van de verzender bij te werken
* DISP-695 - Probleem met het niveau DispatcherLog in de module Dispatcher 4.2.3
* DISP-698 - De Verzender TTL moet s-maxage en privé richtlijnen steunen
* DISP-700 - Module werkt niet correct op Alpine Linux
* DISP-704 - Browser verzoeken die %2b bevatten worden verzonden naar de uitgever niet-gecodeerd
* DISP-705 - Dispatcher crashte vanwege dubbele vrije waarde of corruptie (fasttop)
* DISP-706 - tijdens ongeldigverklaring, de verzender achterverwijzingssymlinks volgt die een oneindige lijn kunnen veroorzaken
* DISP-709 - blokkeer sommige vanity URL uitbreidingen
* DISP-710 - Builds for Linux not utable on Cent OS 6

**Verbeteringen**:

* DISP-652 - De Verzender dient onjuiste kopbal van de Datum

## Nuttige bronnen {#helpful-resources}

* [Overzicht van AEM-verzending](dispatcher.md)

## Downloads {#downloads}

### Apache 2.4 {#apache}

| Platform | Architectuur | OpenSSL-ondersteuning | Downloaden |
|---|---|---|---|
| Linux | i686 (32-bits) | Geen | [dispatcher-apache2.4-linux-i686-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.3.tar.gz) |
| Linux | i686 (32-bits) | 1.0 | [dispatcher-apache2.4-linux-i686-ssl1.0-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.0-4.3.3.tar.gz) |
| Linux | i686 (32-bits) | 1.1 | [dispatcher-apache2.4-linux-i686-ssl1.1-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.1-4.3.3.tar.gz) |
| Linux | x86_64 (64-bits) | Geen | [dispatcher-apache2.4-linux-x86_64-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.3.tar.gz) |
| Linux | x86_64 (64-bits) | 1.0 | [dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.3.tar.gz) |
| Linux | x86_64 (64-bits) | 1.1 | [dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.3.tar.gz) |
| macOS | x86_64 (64-bits) | Geen | [dispatcher-apache2.4-darwin-x86_64-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.3.tar.gz) |

### IIS {#iis}

| Platform | Architectuur | OpenSSL-ondersteuning | Downloaden |
|---|---|---|---|
| Windows | x86 (32 bits) | Geen | [dispatcher-is-windows-x86-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.3.zip) |
| Windows | x86 (32 bits) | 1.0 | [dispatcher-is-windows-x86-ssl1.0-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.0-4.3.3.zip) |
| Windows | x86 (32 bits) | 1.1 | [dispatcher-is-windows-x86-ssl1.1-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.1-4.3.3.zip) |
| Windows | x64 (64-bits) | Geen | [dispatcher-is-windows-x64-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.3.zip) |
| Windows | x64 (64-bits) | 1.0 | [dispatcher-is-windows-x64-ssl1.0-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.0-4.3.3.zip) |
| Windows | x64 (64-bits) | 1.1 | [dispatcher-is-windows-x64-ssl1.1-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.1-4.3.3.zip) |
