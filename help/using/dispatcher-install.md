---
title: Dispatcher installeren
seo-title: AEM Dispatcher installeren
description: Leer hoe u de module Dispatcher installeert op Microsoft Internet Information Server, Apache Web Server en Sun Java Web Server-iPlanet.
seo-description: Leer hoe u de AEM Dispatcher-module installeert op Microsoft Internet Information Server, Apache Web Server en Sun Java Web Server-iPlanet.
uuid: 2384b907-1042-4707-b02f-fba2125618cf
contentOwner: User
converted: true
topic-tags: dispatcher
content-type: reference
discoiquuid: f00ad751-6b95-4365-8500-e1e0108d9536
translation-type: tm+mt
source-git-commit: 024348672c2a9a4f8a01429572eba27ea8b8a490
workflow-type: tm+mt
source-wordcount: '3684'
ht-degree: 0%

---


# Dispatcher installeren {#installing-dispatcher}

<!-- 

Comment Type: draft

<h2>Introduction</h2>

 -->

Gebruik de pagina Opmerkingen bij de release van [Dispatcher](release-notes.md) om het meest recente Dispatcher-installatiebestand voor uw besturingssysteem en webserver te verkrijgen. De versienummers van de Dispatcher zijn onafhankelijk van de Adobe Experience Manager-releasenummers en zijn compatibel met de Adobe Experience Manager 6.x-, 5.x- en Adobe CQ 5.x-releases.

>[!NOTE]
>
>Voor Adobe Experience Manager 6.5 is Dispatcher versie 4.3.2 of hoger vereist. Dat gezegd hebbende, zijn de versies van de Verzender onafhankelijk van AEM, bijvoorbeeld, is Dispatcher versie 4.3.2 ook compatibel met Adobe Experience Manager 6.4.

De volgende naamgevingsconventie voor bestanden wordt gebruikt:

`dispatcher-<web-server>-<operating-system>-<dispatcher-version-number>.<file-format>`

Het `dispatcher-apache2.4-linux-x86_64-ssl-4.3.1.tar.gz` bestand bevat bijvoorbeeld Dispatcher versie 4.3.1 voor een Apache 2.4-webserver die op Linux i686 wordt uitgevoerd en in een pakket wordt geplaatst met de **teerindeling** .

De volgende tabel bevat de id van de webserver die wordt gebruikt in bestandsnamen voor elke webserver:

| Webserver | Installatiekit |
|--- |--- |
| Apache 2.4 | dispatcher-apache **2.4**-&lt;other parameters> |
| Microsoft Internet Information Server 7.5, 8, 8.5 | dispatcher-**is**-&lt;other parameters> |
| Sun Java Web Server iPlanet | dispatcher-**ns**-&lt;other parameters> |

>[!CAUTION]
>
>Installeer de nieuwste versie van Dispatcher die beschikbaar is voor uw platform. Op jaarbasis, zou u uw instantie moeten bevorderen Dispatcher om de recentste versie te gebruiken om uit productverbeteringen voordeel te halen.

Elk archief bevat de volgende bestanden:

* de Dispatcher-modules
* een voorbeeldconfiguratiebestand
* het README-bestand met installatie-instructies en informatie van het laatste moment
* het dossier van WIJZIGINGEN dat van kwesties een lijst maakt die in huidige en vroegere versies worden opgelost

>[!NOTE]
>
>Controleer het README-bestand op eventuele laatste wijzigingen/platformspecifieke opmerkingen voordat u de installatie start.

<!-- 

Comment Type: draft

<h3>Supported Web Servers</h3>

 -->

<!-- 

Comment Type: draft

<p>The following web servers are supported for use with Dispatcher version 4.1.12:</p>

 -->

<!-- 

Comment Type: draft

<p>The following sections detail the specific web server installation procedures.</p>

 -->

## Microsoft Internet Information Server {#microsoft-internet-information-server}

Zie de volgende bronnen voor informatie over het installeren van deze webserver:

* Microsoft&#39;s eigen documentatie op de Internet Information Server
* [&quot;De officiële Microsoft IIS-site&quot;](https://www.iis.net/)

### Vereiste IIS-componenten {#required-iis-components}

IIS versies 8.5 en 10 vereisen dat de volgende componenten IIS worden geïnstalleerd:

* ISAPI-extensies

Ook, moet u de rol van de Server van het Web (IIS) toevoegen. Gebruik Serverbeheer om de rol en componenten toe te voegen.

## Microsoft IIS - De module Dispatcher installeren {#microsoft-iis-installing-the-dispatcher-module}

Het vereiste archief voor Microsoft Internet Information System is:

* `dispatcher-iis-<operating-system>-<dispatcher-release-number>.zip`

Het ZIP-bestand bevat de volgende bestanden:

| Bestand | Beschrijving |
|--- |--- |
| `disp_iis.dll` | Het bibliotheekbestand voor dynamische koppelingen voor Dispatcher. |
| `disp_iis.ini` | Het dossier van de configuratie voor IIS. Dit voorbeeld kan met uw vereisten worden bijgewerkt. **Opmerking**: Het ini-bestand moet dezelfde naamhoofdmap hebben als de dll. |
| `dispatcher.any` | Een voorbeeldconfiguratiebestand voor de Dispatcher. |
| `author_dispatcher.any` | Een voorbeeldconfiguratiebestand voor Dispatcher dat werkt met de auteurinstantie. |
| README | Leesmij-bestand met installatie-instructies en informatie van het laatste moment. **Opmerking**: Controleer dit bestand voordat u de installatie start. |
| WIJZIGINGEN | Wijzigt het bestand met de problemen die zijn opgelost in de huidige en eerdere versies. |

Gebruik de volgende procedure om de Dispatcher-bestanden naar de juiste locatie te kopiëren.

1. Gebruik Windows Verkenner om de `<IIS_INSTALLDIR>/Scripts` map te maken, bijvoorbeeld `C:\inetpub\Scripts`.

1. Pak de volgende bestanden uit het pakket Dispatcher uit in deze map Scripts:

   * `disp_iis.dll`
   * `disp_iis.ini`
   * Een van de volgende bestanden is afhankelijk van het feit of Dispatcher werkt met een AEM instantie van de auteur of een publicatie-instantie:
      * Instantie van auteur: `author_dispatcher.any`
      * Instantie publiceren: `dispatcher.any`

## Microsoft IIS - Het INI-bestand Dispatcher configureren {#microsoft-iis-configure-the-dispatcher-ini-file}

Bewerk het `disp_iis.ini` bestand om de installatie van Dispatcher te configureren. De basisindeling van het `.ini` bestand is als volgt:

```xml
[main]
configpath=<path to dispatcher.any>
loglevel=1|2|3
servervariables=0|1
replaceauthorization=0|1
```

In de volgende tabel wordt elke eigenschap beschreven.

| Parameter | Beschrijving |
|--- |--- |
| configpath | De locatie van `dispatcher.any` het lokale bestandssysteem (absoluut pad). |
| logbestand | De locatie van het `dispatcher.log` bestand. Als dit niet wordt geplaatst dan gaan de logboekberichten naar het logboek van de venstersgebeurtenis. |
| logniveau | Definieert het logniveau dat wordt gebruikt voor het uitvoeren van berichten naar het gebeurtenislogboek. De volgende waarden kunnen worden opgegeven:Logniveau voor het logbestand: <br/>0 - alleen foutberichten. <br/>1 - fouten en waarschuwingen. <br/>2 - fouten, waarschuwingen en informatieberichten <br/>3 - fouten, waarschuwingen, informatie en foutopsporingsberichten. <br/>**Opmerking**: Het wordt aanbevolen het logniveau tijdens de installatie en het testen in te stellen op 3 en op 0 wanneer u in een productieomgeving werkt. |
| vervangvergunning | Geeft aan hoe machtigingsheaders in de HTTP-aanvraag worden verwerkt. De volgende waarden zijn geldig:<br/>0 - de kopballen van de Vergunning worden niet gewijzigd. <br/>1 - Vervangt een koptekst met de naam &quot;Autorisatie&quot;, anders dan &quot;Basis&quot;, door de `Basic <IIS:LOGON\_USER>` equivalente koptekst.<br/> |
| servervariabelen | Definieert hoe servervariabelen worden verwerkt.<br/>0 - IIS de servervariabelen worden verzonden naar noch de Dispatcher noch AEM. <br/>1 - alle IIS-servervariabelen (zoals `LOGON\_USER, QUERY\_STRING, ...`) worden samen met de aanvraagheaders (en ook naar de AEM-instantie als deze niet in cache is geplaatst) naar de Dispatcher verzonden.  <br/>Servervariabelen zijn onder andere `AUTH\_USER, LOGON\_USER, HTTPS\_KEYSIZE` en vele andere. Zie de documentatie IIS voor de volledige lijst van variabelen, met details. |
| enable_chunked_transfer | Bepaalt of om (1) of onbruikbaar te maken (0) geknotte overdracht voor de cliëntreactie. De standaardwaarde is 0. |

Een voorbeeldconfiguratie:

```xml
[main]
configpath=C:\Inetpub\Scripts\dispatcher.any
loglevel=1
servervariables=1
replaceauthorization=0
```

### Microsoft IIS configureren {#configuring-microsoft-iis}

Vorm IIS om de Dispatcher ISAPI module te integreren. In IIS gebruikt u toewijzing van jokertekens voor toepassingen.

### Het vormen Anonieme Toegang - IIS 8.5 en 10 {#configuring-anonymous-access-iis-and}

De standaard Flush replicatieagent op de instantie van de Auteur wordt gevormd zodat het geen veiligheidsgeloofsbrieven met flush verzoeken verzendt. Daarom moet de website die u een Dispatcher geheime voorgeheugen gebruikt anonieme toegang toestaan.

Als uw website een authentificatiemethode gebruikt, moet de Flush replicatieagent dienovereenkomstig worden gevormd.

1. Open IIS Manager en selecteer de website die u als Disptcher-cache gebruikt.
1. Gebruikend de wijze van de Mening van Eigenschappen, in de sectie IIS dubbelklikt Authentificatie.
1. Als Anonieme verificatie niet is ingeschakeld, selecteert u Anonieme verificatie en klikt u in het gedeelte Handelingen op Inschakelen.

### Integratie van de Dispatcher ISAPI Module - IIS 8.5 en 10 {#integrating-the-dispatcher-isapi-module-iis-and}

Gebruik de volgende procedure om de Dispatcher ISAPI Module aan IIS toe te voegen.

1. Open IIS Manager.
1. Selecteer de website die u als Dispatcher Cache gebruikt.
1. Gebruikend de wijze van de Mening van Eigenschappen, in de sectie IIS dubbelklikt Handler Mappings.
1. Klik in het deelvenster Handelingen van de pagina Handler Mappings op Toevoegen van Jokerscript, voeg de volgende eigenschapswaarden toe en klik op OK:

   * Pad aanvragen: *
   * Uitvoerbaar: Het absolute pad van bijvoorbeeld het bestand disp_is.dll `C:\inetpub\Scripts\disp_iis.dll`.
   * Naam: Een beschrijvende naam voor de handlertoewijzing, bijvoorbeeld `Dispatcher`.

1. Klik op Ja in het dialoogvenster dat wordt weergegeven om de bibliotheek disp_is.dll toe te voegen aan de lijst ISAPI- en CGI-beperkingen.

   Voor IIS 7.0 en 7.5 is de configuratie volledig. Ga met de resterende stappen verder als u IIS 8.0 vormt.

1. (IIS 8.0) Selecteer in de lijst met handlertoewijzingen de afbeelding die u zojuist hebt gemaakt en klik in het gedeelte Handelingen op Bewerken.
1. (IIS 8.0) In het Edit de dialoogvakje van de Kaart van het Manuscript, klik de knoop van de Beperkingen van het Verzoek.
1. (IIS 8.0) Om ervoor te zorgen dat de manager voor dossiers en omslagen wordt gebruikt die nog niet in het voorgeheugen onder worden gebracht, schrap de Beheerde slechts aanhalen als het Verzoek aan wordt toegewezen, en klik dan O.K.
1. (IIS 8.0) Klik in het dialoogvenster Scripttoewijzing bewerken op OK.

### Het vormen Toegang tot het Geheime voorgeheugen - IIS 8.5 en 10 {#configuring-access-to-the-cache-iis-and}

Geef de standaardgebruiker van de App Pool schrijftoegang tot de map die wordt gebruikt als Dispatcher-cache.

1. Klik met de rechtermuisknop op de hoofdmap van de website die u als Dispatcher-cache gebruikt en klik op Eigenschappen, zoals `C:\inetpub\wwwroot`.
1. Klik op het tabblad Beveiliging op Bewerken en klik vervolgens in het dialoogvenster Machtigingen op Toevoegen. Er wordt een dialoogvenster geopend waarin u gebruikersaccounts kunt selecteren. Klik op de knop Locaties, selecteer de naam van de computer en klik op OK.

   Zorg dat dit dialoogvenster geopend blijft terwijl u de volgende stap uitvoert.

1. in Manager IIS, selecteer de plaats IIS die u voor het geheime voorgeheugen van de Verzender gebruikt, en op de rechterkant van het venster klik Geavanceerde Montages.
1. Selecteer de waarde van het bezit van de Pool van de Toepassing en kopieer het aan het klembord.
1. Ga terug naar het geopende dialoogvenster. Typ in het vak Voer de objectnamen in die u wilt selecteren de inhoud van het klembord `IIS AppPool\` en plak deze. De waarde moet er als volgt uitzien:

   `IIS AppPool\DefaultAppPool`

1. Klik op de knop Namen controleren. Klik op OK als Windows het gebruikersaccount oplost.
1. Selecteer in het dialoogvenster Machtigingen voor de verzendermap de account die u zojuist hebt toegevoegd, schakel alle machtigingen voor de account in, **behalve Volledige controle** , en klik op OK. Klik op OK om het dialoogvenster Eigenschappen van map te sluiten.

### Registreren van het JSON Mime-type - IIS 8.5 en 10 {#registering-the-json-mime-type-iis-and}

Gebruik de volgende procedure om het JSON MIME-type te registreren, wanneer u Dispatcher JSON-aanroepen wilt toestaan.

1. In Manager IIS, selecteer uw website en gebruikend de Mening van Eigenschappen, klik de Types van MIME tweemaal.
1. Als de JSON-extensie niet in de lijst voorkomt, klikt u in het deelvenster Handelingen op Toevoegen, voert u de volgende eigenschapswaarden in en klikt u op OK:

   * Bestandsnaamextensie: `.json`
   * MIME-type: `application/json`

### Het verborgen segment van de bin verwijderen - IIS 8.5 en 10 {#removing-the-bin-hidden-segment-iis-and}

Gebruik de volgende procedure om het `bin` verborgen segment te verwijderen. Websites die niet nieuw zijn, kunnen dit verborgen segment bevatten.

1. In Manager IIS, selecteer uw website en gebruikend de Mening van Eigenschappen, klik het Filtreren van het Verzoek tweemaal.
1. Selecteer het `bin` segment, klik verwijderen, en in de bevestigingsdialoogdoos klik ja.

### Het registreren IIS Berichten aan een Dossier - IIS 8.5 en 10 {#logging-iis-messages-to-a-file-iis-and}

Gebruik de volgende procedure om het logboekberichten van de Ontvanger aan een logboekdossier in plaats van aan het logboek van de Gebeurtenis van Vensters te schrijven. U moet Dispatcher vormen om het logboekdossier te gebruiken, en IIS van schrijven-toegang tot het dossier te voorzien.

1. Gebruik Windows Verkenner om een map te maken met de naam `dispatcher` onder de logbestandsmap van de IIS-installatie. Het pad van deze map voor een standaardinstallatie is `C:\inetpub\logs\dispatcher`.

1. Klik met de rechtermuisknop op de map dispatcher en klik op Eigenschappen.
1. Klik op het tabblad Beveiliging op Bewerken en klik vervolgens in het dialoogvenster Machtigingen op Toevoegen. Er wordt een dialoogvenster geopend waarin u gebruikersaccounts kunt selecteren. Klik op de knop Locaties, selecteer de naam van de computer en klik op OK.

   Zorg dat dit dialoogvenster geopend blijft terwijl u de volgende stap uitvoert.

1. in Manager IIS, selecteer de plaats IIS die u voor het geheime voorgeheugen van de Verzender gebruikt, en op de rechterkant van het venster klik Geavanceerde Montages.
1. Selecteer de waarde van het bezit van de Pool van de Toepassing en kopieer het aan het klembord.
1. Ga terug naar het geopende dialoogvenster. Typ in het vak Voer de objectnamen in die u wilt selecteren de inhoud van het klembord `IIS AppPool\` en plak deze. De waarde moet er als volgt uitzien:

   `IIS AppPool\DefaultAppPool`

1. Klik op de knop Namen controleren. Klik op OK als Windows het gebruikersaccount oplost.
1. Selecteer in het dialoogvenster Machtigingen voor de verzendermap de account die u zojuist hebt toegevoegd, schakel alle machtigingen voor de account in, **behalve Volledige controle,** en klik op OK. Klik op OK om het dialoogvenster Eigenschappen van map te sluiten.
1. Open het `disp_iis.ini` bestand met een teksteditor.
1. Voeg een tekstregel toe die lijkt op het volgende voorbeeld om de locatie van het logbestand te configureren en sla het bestand vervolgens op:

   ```xml
   logfile=C:\inetpub\logs\dispatcher\dispatcher.log
   ```

### Volgende stappen {#next-steps}

Voordat u de Dispatcher kunt gaan gebruiken, moet u weten:

* [Dispatcher configureren](dispatcher-configuration.md)
* [AEM](page-invalidate.md) werken met Dispatcher.

## Apache Web Server {#apache-web-server}

>[!CAUTION]
>
>Hier worden instructies voor installatie onder zowel **Windows** als **Unix** besproken. Wees voorzichtig wanneer u de stappen uitvoert.

### Apache Web Server installeren {#installing-apache-web-server}

Lees de installatiehandleiding - [online](https://httpd.apache.org/) of in de distributie - voor informatie over het installeren van een Apache Web Server.

>[!CAUTION]
>
>Als u een binair Apache-bestand maakt door de bronbestanden te compileren, moet u de ondersteuning voor **dynamische modules inschakelen**. Dit kan worden gedaan door om het even welk **-toelaten-gedeelde** opties te gebruiken. Neem minimaal de `mod_so` module op.
>
>Meer informatie vindt u in de installatiehandleiding van Apache Web Server.

Zie ook de Apache HTTP Server [Security Tips](https://httpd.apache.org/docs/2.4/misc/security_tips.html) en [Security Reports](https://httpd.apache.org/security_report.html).

### Apache Web Server - Add the Dispatcher Module {#apache-web-server-add-the-dispatcher-module}

De Dispatcher wordt geleverd als:

* **Windows**: een Dynamic Link Library (DLL)
* **Unix**: een dynamisch gezamenlijk object (DSO)

De archiefbestanden voor de installatie bevatten de volgende bestanden, afhankelijk van of u Windows of Unix hebt geselecteerd:

| Bestand | Beschrijving |
|--- |--- |
| disp_apache&lt;x.y>.dll | Windows: Het bibliotheekbestand voor dynamische koppelingen voor Dispatcher. |
| dispatcher-apache&lt;x.y>-&lt;rel-nr>.so | Unix: Het bibliotheekbestand van het gezamenlijke object Dispatcher. |
| mod_dispatcher.so | Unix: Een voorbeeldkoppeling. |
| http.conf.disp&lt;x> | Een voorbeeld van een configuratiebestand voor de Apache-server. |
| dispatcher.any | Een voorbeeldconfiguratiebestand voor de Dispatcher. |
| README | Leesmij-bestand met installatie-instructies en informatie van het laatste moment. **Opmerking**: Controleer dit bestand voordat u de installatie start. |
| WIJZIGINGEN | Wijzigt het bestand met de problemen die zijn opgelost in de huidige en eerdere versies. |

Ga als volgt te werk om Dispatcher toe te voegen aan uw Apache Web Server:

1. Plaats het Dispatcher-bestand in de juiste Apache-modulemap:

   * **Windows**: Plaatsen `disp_apache<x.y>.dll` `<APACHE_ROOT>/modules`
   * **Unix**: Zoek de `<APACHE_ROOT>/libexec` map of de `<APACHE_ROOT>/modules`map volgens de installatie.\
      Kopieer `dispatcher-apache<options>.so` naar deze map.\
      Om het langetermijnonderhoud te vereenvoudigen, kunt u ook een symbolische koppeling maken met de naam `mod_dispatcher.so` Dispatcher:\
      `ln -s dispatcher-apache<x>-<os>-<rel-nr>.so mod_dispatcher.so`

1. Kopieer het bestand dispatcher.any naar de `<APACHE_ROOT>/conf` map.

   **Opmerking:** U kunt dit dossier in een verschillende plaats plaatsen, zolang het bezit DispatcherLog van de module van de Verzender dienovereenkomstig wordt gevormd. (Zie Verzendspecifieke configuratiegegevens verderop.)

### Apache Web Server - SELinux-eigenschappen configureren {#apache-web-server-configure-selinux-properties}

Als u Dispatcher uitvoert op RedHat Linux Kernel 2.6 terwijl SELinux ingeschakeld is, worden mogelijk foutberichten als deze weergegeven in het logbestand van de verzender.

`Mon Jun 30 00:03:59 2013] [E] [16561(139642697451488)] Unable to connect to backend rend01 (10.122.213.248:4502): Permission denied`

Dit is waarschijnlijk het gevolg van een ingeschakelde SELinux-beveiliging. Vervolgens moet u de volgende taken uitvoeren:

* Vorm de context SELinux van het de moduledossier van de verzender.
* Schakel HTTP-scripts en -modules in om netwerkverbindingen te maken.
* Vorm de context SELinux van de docroot, waar de caching dossiers worden opgeslagen.

Ga de volgende bevelen in een eindvenster in, die met de weg aan de module van de Verzender vervangen die u aan de Server van het Web van Apache installeerde, en `[path to the dispatcher.so file]` *`path to the docroot`* met de weg vervangen waar de docroot (b.v. `/opt/cq/cache`):

```shell
semanage fcontext -a -t httpd_modules_t [path to the dispatcher.so file]
setsebool -P httpd_can_network_connect on
chcon -R --type httpd_sys_content_t [path to the docroot]
semanage fcontext -a -t httpd_sys_content_t "[path to the docroot](/.*)?"
```

### Apache Web Server - Apache Web Server voor Dispatcher configureren {#apache-web-server-configure-apache-web-server-for-dispatcher}

De Apache Web Server moet worden geconfigureerd, met gebruik van `httpd.conf`. In de installatiekit Dispatcher vindt u een voorbeeld van een configuratiebestand met de naam `httpd.conf.disp<x>`.

Deze stappen zijn verplicht:

1. Ga naar `<APACHE_ROOT>/conf`.
1. Openen `httpd.conf`voor bewerken.
1. De volgende configuratieingangen moeten, in de vermelde orde worden toegevoegd:

   * **LoadModule** om de module bij opstarten te laden.
   * Dispatcher-specifieke configuratieingangen, met inbegrip van **DispatcherConfig, DispatcherLog** en **DispatcherLogLevel**.
   * **SetHandler** om Dispatcher te activeren. **LoadModule**.
   * **ModMimeUsePathInfo** om gedrag van **mod_mime** te vormen.

1. (Optioneel) U wordt aangeraden de eigenaar van de map htdocs te wijzigen:

   * De apache-server begint als root, hoewel de onderliggende processen beginnen als daemon (voor beveiligingsdoeleinden). De DocumentRoot (`<APACHE_ROOT>/htdocs`) moet tot de gebruikersdaemon behoren:

      ```xml
      cd <APACHE_ROOT>  
      chown -R daemon:daemon htdocs
      ```

**LoadModule**

De volgende tabel bevat voorbeelden die kunnen worden gebruikt. de exacte gegevens zijn in overeenstemming met uw specifieke Apache Web Server:

|  |  |
|--- |--- |
| Windows | `... LoadModule dispatcher_module modules\disp_apache.dll ...` |
| Unix (symbolische koppeling wordt gebruikt) | `... LoadModule dispatcher_module libexec/mod_dispatcher.so ...` |

>[!NOTE]
>
>De eerste parameter van elke instructie moet precies zo worden geschreven als in de bovenstaande voorbeelden.
>
>Zie de dossiers van de voorbeeldconfiguratie en de documentatie van de Server van het Web Apache voor volledige details over dit bevel worden verstrekt die.

**Specifieke configuratiegegevens van Dispatcher**

De Dispatcher-specifieke configuratieingangen worden geplaatst na de ingang LoadModule. De volgende lijst maakt een lijst van een voorbeeldconfiguratie die voor zowel Unix als Vensters van toepassing is:

**Windows en Unix**

```
...
<fModule disp_apache2.c>
DispatcherConfig conf/dispatcher.any
DispatcherLog logs/dispatcher.log DispatcherLogLevel 3
DispatcherNoServerHeader 0 DispatcherDeclineRoot 0
DispatcherUseProcessedURL 0
DispatcherPassError 0
DispatcherKeepAliveTimeout 60
</IfModule>
...
```

De individuele configuratieparameters:

| Parameter | Beschrijving |
|--- |--- |
| DispatcherConfig | Locatie en naam van het Dispatcher-configuratiebestand. <br/>Wanneer dit bezit in de belangrijkste serverconfiguratie wordt gevestigd, erven alle virtuele gastheren de bezitswaarde. Virtuele hosts kunnen echter een eigenschap DispatcherConfig opnemen om de hoofdserverconfiguratie te overschrijven. |
| DispatcherLog | Locatie en naam van het logbestand. |
| DispatcherLogLevel | Logniveau voor het logbestand: <br/>0 - Fouten <br/>1 - Waarschuwingen <br/>2 - Info <br/>3 - Foutopsporingsnotitie <br/>****: Het wordt aanbevolen het logniveau tijdens de installatie en het testen in te stellen op 3 en op 0 wanneer u in een productieomgeving werkt. |
| DispatcherNoServerHeader | *Deze parameter is vervangen en heeft geen effect meer.*<br/><br/> Hiermee definieert u de serverkoptekst die moet worden gebruikt: <br/><ul><li>undefined of 0 - de de serverkopbal van HTTP bevat de AEM versie. </li><li>1 - de header van de Apache-server wordt gebruikt.</li></ul> |
| DispatcherDeclineRoot | Bepaalt of verzoeken aan de wortel &quot;/&quot; te weigeren: <br/>**0** - aanvragen bij / <br/>**1** - aanvragen bij / worden niet door de verzender afgehandeld; gebruik mod_alias voor de correcte afbeelding. |
| DispatcherUseProcessURL | Hiermee wordt gedefinieerd of vooraf verwerkte URL&#39;s moeten worden gebruikt voor alle verdere verwerking door Dispatcher: <br/>**0** - gebruik de oorspronkelijke URL die aan de webserver is doorgegeven. <br/>**1** - de verzender gebruikt de URL die al is verwerkt door de handlers die aan de verzender voorafgaan (d.w.z. `mod_rewrite`) in plaats van de oorspronkelijke URL die aan de webserver is doorgegeven.  Het origineel of de verwerkte URL wordt bijvoorbeeld gekoppeld aan de Dispatcher-filters. De URL wordt ook gebruikt als basis voor de structuur van het cachebestand.   Raadpleeg de documentatie bij de Apache-website voor informatie over mod_rewrite. bijvoorbeeld Apache 2.4. Als mod_rewrite wordt gebruikt, is het raadzaam de markering &#39;passthrough&#39; te gebruiken | PT&#39; (ga door aan volgende manager) om de herschrijfmotor te dwingen om het uri gebied van de interne request_rec structuur aan de waarde van het filename gebied te plaatsen. |
| DispatcherPassError | Definieert hoe foutcodes voor ErrorDocument-afhandeling worden ondersteund: <br/>**0** - Dispatcher spoolt alle foutreacties op de client. <br/>**1** - De verzender spoolt geen foutenreactie aan de cliënt (waar de statuscode groter of gelijk aan 400 is), maar gaat de statuscode tot Apache over, die bijvoorbeeld een ErrorDocument richtlijn toestaat om zulk een statuscode te verwerken. <br/>**Codebereik** - Geef een bereik foutcodes op waarvoor het antwoord wordt doorgegeven aan Apache. Andere foutcodes worden doorgegeven aan de client. De volgende configuratie geeft bijvoorbeeld reacties voor fout 412 door aan de client en alle andere fouten worden doorgegeven aan Apache: DispatcherPassError 400-411,413-417 |
| DispatcherKeepAliveTimeout | Geeft de time-out bij &#39;houden in leven&#39; in seconden aan. Vanaf Dispatcher versie 4.2.0 is de standaardwaarde voor het in leven houden 60. Met de waarde 0 wordt het in leven houden uitgeschakeld. |
| DispatcherNoCanonURL | Als u deze parameter instelt op Aan, wordt de onbewerkte URL doorgegeven aan de achterkant in plaats van de gecanonicaliseerde URL en worden de instellingen van DispatcherUseProcessURL genegeerd. De standaardwaarde is Uit. <br/>**Opmerking**: De filterregels in de configuratie Dispatcher worden altijd geëvalueerd op basis van de ontsmette URL en niet op basis van de onbewerkte URL. |




>[!NOTE]
>
>Paditems zijn relatief ten opzichte van de hoofdmap van de Apache Web Server.

>[!NOTE]
>
>De standaardinstellingen voor de serverkoptekst zijn: `  
ServerTokens Full` `  
DispatcherNoServerHeader 0`\
Dit is de AEM versie (voor statistische doeleinden). Als u het beschikbaar zijn van dergelijke informatie in de kopbal wilt onbruikbaar maken kunt u plaatsen: `  
ServerTokens Prod`\
Zie de Documentatie van [Apache over de Richtlijn van ServerTokens (bijvoorbeeld, voor Apache 2.4)](https://httpd.apache.org/docs/2.4/mod/core.html) voor meer informatie.

**SetHandler**

Na deze ingangen moet u een **verklaring SetHandler** aan de context van uw configuratie ( `<Directory>`, `<Location>`) voor de Ontvanger toevoegen om de inkomende verzoeken te behandelen. In het volgende voorbeeld wordt de Dispatcher geconfigureerd voor het afhandelen van aanvragen voor de volledige website:

**Windows en Unix**

```
...  
<Directory />  
<IfModule disp\_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
  
Options FollowSymLinks  
AllowOverride None  
</Directory>  
...
```

In het volgende voorbeeld wordt de Dispatcher geconfigureerd om aanvragen voor een virtueel domein af te handelen:

**Windows**

```
...  
<VirtualHost 123.45.67.89>  
ServerName www.mycompany.com  
DocumentRoot _\[cache-path\]_\\docs  
<Directory _\[cache-path\]_\\docs>  
<IfModule disp\_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
AllowOverride None  
</Directory>  
</VirtualHost>  
...
```

**Unix**

```
...  
<VirtualHost 123.45.67.89>  
ServerName www.mycompany.com  
DocumentRoot /usr/apachecache/docs  
<Directory /usr/apachecache/docs>  
<IfModule disp\_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
AllowOverride None  
</Directory>  
</VirtualHost>  
...
```

>[!NOTE]
De parameter van de **verklaring SetHandler** moet *precies zoals in de bovengenoemde voorbeelden* worden geschreven, aangezien dit de naam van de manager is die in de module wordt bepaald.
Zie de dossiers van de voorbeeldconfiguratie en de documentatie van de Server van het Web Apache voor volledige details over dit bevel worden verstrekt die.

**ModMimeUsePathInfo**

Na de **instructie SetHandler** moet u ook de definitie **ModMimeUsePathInfo** toevoegen.

>[!NOTE]
De `ModMimeUsePathInfo` parameter zou slechts moeten worden gebruikt en worden gevormd als u versie 4.0.9 van de Verzender, of hoger gebruikt.
(Let op: Dispatcher versie 4.0.9 is uitgebracht in 2011. Als u een oudere versie gebruikt, kunt u een upgrade uitvoeren naar een recente versie van Dispatcher.)

De **parameter ModMimeUsePathInfo** moet worden ingesteld `On` voor alle Apache-configuraties:

`ModMimeUsePathInfo On`

De module mod_mime (zie bijvoorbeeld Mod_mime [van de Module van](https://httpd.apache.org/docs/2.4/mod/mod_mime.html)Apache) wordt gebruikt om inhoudmeta-gegevens aan de inhoud toe te wijzen die voor een reactie van HTTP wordt geselecteerd. De standaardopstelling betekent dat wanneer mod_mime het inhoudstype bepaalt, slechts het deel van URL dat aan een dossier of een folder in kaart brengt zal worden overwogen.

Wanneer `On`, specificeert de `ModMimeUsePathInfo` parameter dat het inhoudstype `mod_mime` moet bepalen dat op *volledige* URL wordt gebaseerd; dit betekent dat voor virtuele bronnen metagegevens zullen worden toegepast op basis van hun extensie .

In het volgende voorbeeld wordt **ModMimeUsePathInfo** geactiveerd:

**Windows en Unix**

```
...  
<Directory />  
<IfModule disp\_apache2.c>  
SetHandler dispatcher-handler  
ModMimeUsePathInfo On  
</IfModule>  
  
Options FollowSymLinks  
AllowOverride None  
</Directory>  
...
```

### Ondersteuning inschakelen voor HTTPS (Unix en Linux) {#enable-support-for-https-unix-and-linux}

Dispatcher gebruikt OpenSSL om veilige communicatie via HTTP te implementeren. Vanaf Dispatcher versie **4.2.0** worden OpenSSL 1.0.0 en OpenSSL 1.0.1 ondersteund. Dispatcher gebruikt standaard OpenSSL 1.0.0. Als u OpenSSL 1.0.1 wilt gebruiken, gebruikt u de volgende procedure om symbolische koppelingen te maken, zodat Dispatcher de geïnstalleerde OpenSSL-bibliotheken gebruikt.

1. Open een terminal en wijzig de huidige map in de map waarin de OpenSSL-bibliotheken zijn geïnstalleerd, bijvoorbeeld:

   ```shell
   cd /usr/lib64
   ```

1. Voer de volgende opdrachten in om symbolische koppelingen te maken:

   ```shell
   ln -s libssl.so libssl.so.1.0.1
   ln -s libcrypto.so libcrypto.so.1.0.1
   ```

>[!NOTE]
Als u een aangepaste versie van Apache gebruikt, moet u ervoor zorgen dat Apache en Dispatcher zijn gecompileerd met dezelfde versie van [OpenSSL](https://www.openssl.org/source/).

### Volgende stappen {#next-steps-1}

Voordat u de Dispatcher kunt gaan gebruiken, moet u nu:

* [Dispatcher configureren](dispatcher-configuration.md)
* [AEM](page-invalidate.md) werken met Dispatcher.

## Sun Java System Web Server / iPlanet {#sun-java-system-web-server-iplanet}

>[!NOTE]
Hier worden instructies voor zowel Windows- als Unix-omgevingen besproken.
Wees voorzichtig wanneer u selecteert welke u wilt uitvoeren.

### Sun Java System Web Server / iPlanet - Uw webserver installeren {#sun-java-system-web-server-iplanet-installing-your-web-server}

Raadpleeg de documentatie bij de betreffende webservers voor volledige informatie over het installeren van deze webservers:

* Sun Java System Web Server
* iPlanet-webserver

### Sun Java System Web Server / iPlanet - Voeg de Dispatcher Module toe {#sun-java-system-web-server-iplanet-add-the-dispatcher-module}

De Dispatcher wordt geleverd als:

* **Windows**: een Dynamic Link Library (DLL)
* **Unix**: een dynamisch gezamenlijk object (DSO)

De archiefbestanden voor de installatie bevatten de volgende bestanden, afhankelijk van of u Windows of Unix hebt geselecteerd:

| Bestand | Beschrijving |
|---|---|
| `disp_ns.dll` | Windows: Het bibliotheekbestand voor dynamische koppelingen voor Dispatcher. |
| `dispatcher.so` | Unix: Het bibliotheekbestand van het gezamenlijke object Dispatcher. |
| `dispatcher.so` | Unix: Een voorbeeldkoppeling. |
| `obj.conf.disp` | Een voorbeeldconfiguratiebestand voor de iPlanet/Sun Java System-webserver. |
| `dispatcher.any` | Een voorbeeldconfiguratiebestand voor de Dispatcher. |
| README | Leesmij-bestand met installatie-instructies en informatie van het laatste moment. Opmerking: Controleer dit bestand voordat u de installatie start. |
| WIJZIGINGEN | Wijzigt het bestand met de problemen die zijn opgelost in de huidige en eerdere versies. |

Ga als volgt te werk om de Dispatcher aan uw webserver toe te voegen:

1. Plaats het Dispatcher-bestand in de `plugin` map van de webserver:

### Sun Java System Web Server / iPlanet - Configureren voor Dispatcher {#sun-java-system-web-server-iplanet-configure-for-the-dispatcher}

De webserver moet worden geconfigureerd met gebruik van `obj.conf`. In de installatiekit Dispatcher vindt u een voorbeeld van een configuratiebestand met de naam `obj.conf.disp`.

1. Ga naar `<WEBSERVER_ROOT>/config`.
1. Openen `obj.conf`voor bewerken.
1. Kopieer de regel die begint:\
   `Service fn="dispService"`\
   van `obj.conf.disp` tot de initialisatiesectie van `obj.conf`.

1. Sla de wijzigingen op.
1. Openen `magnus.conf` voor bewerken.
1. Kopieer de twee regels die beginnen:\
   `Init funcs="dispService, dispInit"`\
   and\
   `Init fn="dispInit"`\
   van `obj.conf.disp` tot de initialisatiesectie van `magnus.conf`.

1. Sla de wijzigingen op.

>[!NOTE]
De volgende configuraties moeten allemaal op één regel staan en de `$(SERVER_ROOT)` en `$(PRODUCT_SUBDIR)` moeten door de respectieve waarden worden vervangen.

**Init**

De volgende tabel bevat voorbeelden die kunnen worden gebruikt. de exacte gegevens zijn volgens uw specifieke webserver :

**Windows en Unix**

```
...  
Init funcs="dispService,dispInit" fn="load-modules" shlib="$(SERVER\_ROOT)/plugins/dispatcher.so"  
Init fn="dispInit" config="$(PRODUCT\_SUBDIR)/dispatcher.any" loglevel="1" logfile="$(PRODUCT\_SUBDIR)/logs/dispatcher.log"  
keepalivetimeout="60"  
...
```

waarbij:

| Parameter | Beschrijving |
|--- |--- |
| config | Locatie en naam van het configuratiebestand `dispatcher.any.` |
| logbestand | Locatie en naam van het logbestand. |
| logniveau | Logniveau voor het schrijven van berichten naar het logbestand: <br/>**0** Fouten <br/>**1** Waarschuwingen <br/>**2** Info <br/>**3** zuivert <br/>**Nota:** Het wordt aanbevolen het logniveau tijdens de installatie en het testen in te stellen op 3 en op 0 bij het uitvoeren in een productieomgeving. |
| keepalivetimeout | Geeft de time-out bij &#39;houden in leven&#39; in seconden aan. Vanaf Dispatcher versie 4.2.0 is de standaardwaarde voor het in leven houden 60. Met de waarde 0 wordt het in leven houden uitgeschakeld. |

Afhankelijk van uw vereisten kunt u de Dispatcher definiëren als service voor uw objecten. Als u de Dispatcher voor uw gehele website wilt configureren, wijzigt u het standaardobject:


**Windows**

```
...  
NameTrans fn="document-root" root="$(PRODUCT\_SUBDIR)\\dispcache"  
...  
Service fn="dispService" method="(GET|HEAD|POST)" type="\*\\\*"  
...
```

**Unix**

```
...  
NameTrans fn="document-root" root="$(PRODUCT\_SUBDIR)/dispcache"  
...  
Service fn="dispService" method="(GET|HEAD|POST)" type="\*/\*"  
...
```

### Volgende stappen {#next-steps-2}

Voordat u de Dispatcher kunt gaan gebruiken, moet u nu:

* [Dispatcher configureren](dispatcher-configuration.md)
* [AEM](page-invalidate.md) werken met Dispatcher.
