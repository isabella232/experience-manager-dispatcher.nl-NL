---
title: Dispatcher configureren
seo-title: Dispatcher configureren
description: Leer hoe u Dispatcher configureert.
seo-description: Leer hoe u Dispatcher configureert.
uuid: 253ef0f7-2491-4cec-ab22-97439df29fd6
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
topic-tags: dispatcher
content-type: reference
discoiquuid: aeffee8e-bb34-42a7-9a5e-b7d0e848391a
translation-type: tm+mt
source-git-commit: 183131dec51b67e152a8660c325ed980ae9ef458

---


# Dispatcher configureren{#configuring-dispatcher}

>[!NOTE]
>
>Dispatcher-versies zijn onafhankelijk van AEM. U bent mogelijk omgeleid naar deze pagina als u een koppeling naar de Dispatcher-documentatie hebt gevolgd die is ingesloten in de documentatie voor een vorige versie van AEM.

De volgende secties beschrijven hoe te om diverse aspecten van de Verzender te vormen.

## Ondersteuning voor IPv4 en IPv6 {#support-for-ipv-and-ipv}

Alle elementen van AEM en Dispatcher kunnen in zowel IPv4 als IPv6 netwerken worden geïnstalleerd. Zie [IPV4 en IPV6](https://helpx.adobe.com/experience-manager/6-3/sites/deploying/using/technical-requirements.html#AdditionalPlatformNotes).

## Configuratiebestanden van Dispatcher {#dispatcher-configuration-files}

Standaard wordt de Dispatcher-configuratie opgeslagen in het `dispatcher.any` tekstbestand, maar u kunt de naam en locatie van dit bestand tijdens de installatie wijzigen.

Het configuratiedossier bevat een reeks enig-getaxeerde of multi-getaxeerde eigenschappen die het gedrag van Dispatcher controleren:

* Namen van eigenschappen worden voorafgegaan door een slash `/`.
* Met multi-getaxeerde eigenschappen worden onderliggende items ingesloten met behulp van accolades `{ }`.

Een voorbeeldconfiguratie is als volgt gestructureerd:

```xml
# name of the dispatcher
/name "internet-server"

# each farm configures a set off (loadbalanced) renders
/farms
 {
  # first farm entry (label is not important, just for your convenience)
   /website 
     {  
     /clientheaders
       {
       # List of headers that are passed on
       }
     /virtualhosts
       {
       # List of URLs for this Web site
       }
     /sessionmanagement 
       {
       # settings for user authentification
       }
     /renders
       {
       # List of AEM instances that render the documents
       }
     /filter
       {
       # List of filters
       }
     /vanity_urls
       {
       # List of vanity URLs
       }
     /cache
       {
       # Cache configuration
       /rules
         {
         # List of cachable documents
         }
       /invalidate
         {
         # List of auto-invalidated documents
         }
       }
     /statistics
       {
       /categories
         {
         # The document categories that are used for load balancing estimates
         }
       }
     /stickyConnectionsFor "/myFolder"
     /health_check
       {
       # Page gets contacted when an instance returns a 500
       }
     /retryDelay "1"
     /numberOfRetries "5"
     /unavailablePenalty "1"
     /failover "1"
     }
 }
```

U kunt andere dossiers omvatten die tot de configuratie bijdragen:

* Als het configuratiebestand groot is, kunt u het opsplitsen in verschillende kleinere bestanden (die eenvoudiger te beheren zijn) en deze opnemen.
* Bestanden opnemen die automatisch worden gegenereerd.

Bijvoorbeeld, om het dossier myFarm.any in de /farm configuratie te omvatten gebruik de volgende code:

```xml
/farms
  {
  $include "myFarm.any"
  }
```

Gebruik de asterisk (&quot;*&quot;) als een vervanging om een waaier van dossiers te specificeren om te omvatten.

Bijvoorbeeld, als de dossiers `farm_1.any` door de configuratie van landbouwbedrijven één tot vijf `farm_5.any` bevatten, kunt u hen omvatten als volgt:

```xml
/farms
  {
  $include "farm_*.any"
  }
```

## Omgevingsvariabelen gebruiken {#using-environment-variables}

U kunt omgevingsvariabelen gebruiken in tekenreeksgetaxeerde eigenschappen in het bestand dispatcher.any in plaats van de waarden hard te coderen. Als u de waarde van een omgevingsvariabele wilt opnemen, gebruikt u de indeling `${variable_name}`.

Als het bestand dispatcher.any zich bijvoorbeeld in dezelfde map bevindt als de cachemap, kan de volgende waarde voor de eigenschap [docroot](dispatcher-configuration.md#main-pars-title-30) worden gebruikt:

```xml
/docroot "${PWD}/cache"
```

Als een ander voorbeeld, als u een milieuvariabele creeert genoemd `PUBLISH_IP` die hostname van AEM opslaat publiceer instantie, kan de volgende configuratie van het bezit [/renders](dispatcher-configuration.md#main-pars-127-25-0008) worden gebruikt:

```xml
/renders {
  /0001 {
    /hostname "${PUBLISH_IP}"
    /port "8443"
  }
}
```

## De instantie Dispatcher een naam geven {#naming-the-dispatcher-instance-name}

Gebruik het `/name` bezit om een unieke naam te specificeren om uw instantie van de Verzender te identificeren. Het `/name` bezit is een top-level bezit in de configuratiestructuur.

## Bedrijven definiëren {#defining-farms-farms}

De `/farms` eigenschap definieert een of meer sets Dispatcher-gedrag, waarbij elke set aan verschillende websites of URL&#39;s is gekoppeld. Het `/farms` bezit kan één enkel landbouwbedrijf of veelvoudige landbouwbedrijven omvatten:

* Gebruik één landbouwbedrijf wanneer u Dispatcher al uw Web-pagina&#39;s of Websites op de zelfde manier wilt behandelen.
* Maak meerdere boerderijen wanneer verschillende gebieden van uw website of verschillende websites een ander Dispatcher-gedrag vereisen.

Het `/farms` bezit is een top-level bezit in de configuratiestructuur. Om een landbouwbedrijf te bepalen, voeg een kindbezit aan het `/farms` bezit toe. Gebruik een bezitsnaam die uniek het landbouwbedrijf binnen de instantie van de Verzender identificeert.

De `/farmname` eigenschap is multi-getaxeerd en bevat andere eigenschappen die het gedrag Verzender definiëren:

* De URL&#39;s van de pagina&#39;s waarop het landbouwbedrijf van toepassing is.
* Een of meer service-URL&#39;s (doorgaans van AEM-publicatie-instanties) die moeten worden gebruikt voor het weergeven van documenten.
* De statistische gegevens die moeten worden gebruikt voor meerdere renderers van documenten die een taakverdeling hebben.
* Verschillende andere gedragingen, zoals welke bestanden in cache moeten worden opgeslagen en waar.

De waarde kan elk alfanumeriek teken (a-z, 0-9) bevatten. In het volgende voorbeeld wordt de skeletdefinitie getoond voor twee boerderijen met de naam `/daycom` en `/docsdaycom`:

```xml
#name of dispatcher
/name "day sites"

#farms section defines a list of farms or sites
/farms
{
   /daycom
   {
       ...
   }
   /docdaycom
   {
      ...
   }
}
```

>[!NOTE]
>
>Als u meer dan één gebruikt geef landbouwbedrijf terug, wordt de lijst geëvalueerd bottom-up. Dit is met name van belang bij het definiëren van [virtuele hosts](dispatcher-configuration.md#main-pars-117-15-0006) voor uw websites.

Elk landbouwbedrijfbezit kan de volgende kindeigenschappen bevatten:

| Eigenschapnaam | Beschrijving |
|--- |--- |
| [/homepage](#specify-a-default-page-iis-only-homepage) | Standaardstartpagina (optioneel) (alleen IIS) |
| [/clientheaders](#specifying-the-http-headers-to-pass-through-clientheaders) | De headers van de HTTP-client-aanvraag die moeten worden doorgegeven. |
| [/virtuele hosts](#identifying-virtual-hosts-virtualhosts) | De virtuele gastheren voor dit landbouwbedrijf. |
| [/sessionmanagement](#enabling-secure-sessions-sessionmanagement) | Ondersteuning voor sessiebeheer en verificatie. |
| [/renders](#defining-page-renderers-renders) | De servers die gerenderde pagina&#39;s leveren (doorgaans publiceert AEM-exemplaren). |
| [/filter](#configuring-access-to-content-filter) | Bepaalt URLs waaraan de Verzender toegang toelaat. |
| [/vanity_urls](#enabling-access-to-vanity-urls-vanity-urls) | Vormt toegang tot vanity URLs. |
| [/propagateSyndPost](#forwarding-syndication-requests-propagatesyndpost) | Steun voor de doorzending van verzoeken om syndicatie. |
| [/cache](#configuring-the-dispatcher-cache-cache) | Vormt caching gedrag. |
| [/statistiek](#configuring-load-balancing-statistics) | Statistische categorieën definiëren voor berekeningen van de taakverdeling. |
| [/stickyConnectionsFor](#identifying-a-sticky-connection-folder-stickyconnectionsfor) | De map die kleverige documenten bevat. |
| [/health_check](#specifying-a-health-check-page) | De URL die moet worden gebruikt om de beschikbaarheid van de server te bepalen. |
| [/retryDelay](#specifying-the-page-retry-delay) | De vertraging voordat een mislukte verbinding opnieuw wordt geprobeerd. |
| [/unavailablePenalty](#reflecting-server-unavailability-in-dispatcher-statistics) | Sancties die van invloed zijn op statistieken voor berekeningen voor taakverdeling. |
| [/failover](#using-the-failover-mechanism) | Verzend verzoeken opnieuw naar verschillende renders wanneer het oorspronkelijke verzoek ontbreekt. |
| [/auth_checker](permissions-cache.md) | Zie Beveiligde inhoud [in](permissions-cache.md)cache plaatsen voor door machtigingen gevoelige caching. |

## Een standaardpagina opgeven (alleen IIS) - /homepage {#specify-a-default-page-iis-only-homepage}

>[!CAUTION]
>
>De `/homepage`parameter (alleen IIS) werkt niet meer. In plaats daarvan, zou u [IIS moeten gebruiken herschrijft Module](https://docs.microsoft.com/en-us/iis/extensions/url-rewrite-module/using-the-url-rewrite-module).
>
>Als u Apache gebruikt, moet u de `mod_rewrite` module gebruiken. Raadpleeg de documentatie bij de Apache-website voor informatie over `mod_rewrite` (bijvoorbeeld [Apache 2.4](https://httpd.apache.org/docs/current/mod/mod_rewrite.html)). Bij gebruik `mod_rewrite`is het raadzaam de markering **[&#39;passthrough|PT&#39; (doorgeven naar volgende handler)](https://helpx.adobe.com/dispatcher/kb/DispatcherModReWrite.html)**te gebruiken om de engine voor herschrijven te forceren het`uri`veld van de interne`request_rec`structuur in te stellen op de waarde van het`filename`veld.

<!-- 

Comment Type: draft

<p>The optional /homepage parameter specifies the page that Dispatcher returns when a client requests an undeterminable page or file.</p> 
<p>Typically this situation occurs when a user specifies an URL for which neither IIS or AEM provides an automatic redirection target. For example, if the AEM render instance is shut down after the content is cached, the content redirect URL is unavailable.</p> 
<p>The following example configuration displays the <span class="code">index.html</span> page in such circumstances:</p>

 -->

<!-- 

Comment Type: draft

<codeblock gutter="true" class="syntax xml">
  /homepage&nbsp;"/index.html" 
</codeblock>

 -->

<!-- 

Comment Type: draft

<p>The <span class="code">/homepage</span> section is located inside the <span class="code">/farms</span> section, for example:<br /> </p>

 -->

<!-- 

Comment Type: draft

<codeblock gutter="true" class="syntax xml">
  #name&nbsp;of&nbsp;dispatcher!!discoiqbr!!/name&nbsp;"day&nbsp;sites"!!discoiqbr!!!!discoiqbr!!#farms&nbsp;section&nbsp;defines&nbsp;a&nbsp;list&nbsp;of&nbsp;farms&nbsp;or&nbsp;sites!!discoiqbr!!/farms!!discoiqbr!!{!!discoiqbr!!&nbsp;&nbsp;&nbsp;/daycom!!discoiqbr!!&nbsp;&nbsp;&nbsp;{!!discoiqbr!!&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;/homepage&nbsp;"/index.html"!!discoiqbr!!&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;...!!discoiqbr!!&nbsp;&nbsp;&nbsp;}!!discoiqbr!!&nbsp;&nbsp;&nbsp;/docdaycom!!discoiqbr!!&nbsp;&nbsp;&nbsp;{!!discoiqbr!!&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;...!!discoiqbr!!&nbsp;&nbsp;&nbsp;}!!discoiqbr!!} 
</codeblock>

 -->

## De HTTP-headers opgeven die moeten worden doorgegeven {#specifying-the-http-headers-to-pass-through-clientheaders}

De `/clientheaders` eigenschap definieert een lijst met HTTP-headers die door Dispatcher worden doorgegeven van de HTTP-client-aanvraag naar de renderer (AEM-instantie).

Standaard verzendt Dispatcher de standaard HTTP-headers naar de AEM-instantie. In sommige gevallen wilt u mogelijk extra kopteksten doorsturen of specifieke kopteksten verwijderen:

* Voeg kopballen, zoals douanekopballen, toe die uw instantie AEM in de HTTP- aanvraag verwacht.
* Verwijder kopballen, zoals authentificatiekopballen, die slechts voor de Webserver relevant zijn.

Als u de reeks kopballen aanpast om over te gaan, moet u een uitvoerige lijst van kopballen specificeren, met inbegrip van die die normaal inbegrepen door gebrek zijn.

Voor een Dispatcher-instantie die pagina-activeringsverzoeken voor publicatie-instanties afhandelt, is bijvoorbeeld de `PATH` koptekst in de `/clientheaders` sectie vereist. De `PATH` kopbal laat communicatie tussen de replicatieagent en de verzender toe.

De volgende code is een voorbeeldconfiguratie voor `/clientheaders`:

```shell
/clientheaders
  {
  "CSRF-Token"
  "X-Forwarded-Proto"
  "referer"
  "user-agent"
  "authorization"
  "from"
  "content-type"
  "content-length"
  "accept-charset"
  "accept-encoding"
  "accept-language"
  "accept"
  "host"
  "if-match"
  "if-none-match"
  "if-range"
  "if-unmodified-since"
  "max-forwards"
  "proxy-authorization"
  "proxy-connection"
  "range"
  "cookie"
  "cq-action"
  "cq-handle"
  "handle"
  "action"
  "cqstats"
  "depth"
  "translate"
  "expires"
  "date"
  "dav"
  "ms-author-via"
  "if"
  "lock-token"
  "x-expected-entity-length"
  "destination"
  "PATH"
  }
```

## Virtuele hosts identificeren {#identifying-virtual-hosts-virtualhosts}

De `/virtualhosts` eigenschap definieert een lijst met alle combinaties hostname/URI die Dispatcher accepteert voor dit farm. U kunt het sterretje (&quot;*&quot;) gebruiken als jokerteken. Waarden voor de eigenschap / `virtualhosts` gebruiken de volgende indeling:

```xml
[scheme]host[uri][*]
```

* `scheme`: (Optioneel) Ofwel `https://` of `https://.`
* `host`: De naam of het IP-adres van de hostcomputer en, indien nodig, het poortnummer. (Zie [https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23))
* `uri`: (Optioneel) Het pad naar de bronnen.

Het volgende voorbeeld van de configuratiehandvatten verzoeken om de .com en .ch domeinen van myCompany, en alle domeinen van mySubDivision:

```xml
   /virtualhosts
    {
    "www.myCompany.com"
    "www.myCompany.ch"
    "www.mySubDivison.*"
    }
```

De volgende configuratie behandelt *alle* verzoeken:

```xml
   /virtualhosts
    {
    "*"
    }
```

### De virtuele host oplossen {#resolving-the-virtual-host}

Wanneer de Ontvanger een HTTP of HTTPS verzoek ontvangt, vindt het de virtuele gastheerwaarde die best-past de `host,` , `uri`, en `scheme` kopballen van het verzoek aan. Dispatcher evalueert de waarden in de `virtualhosts` eigenschappen in de volgende volgorde:

* De afzender begint bij het laagste landbouwbedrijf en vordert omhoog in het dispatcher.any dossier.
* Voor elk landbouwbedrijf, begint de Verzender met de hoogste waarde in het `virtualhosts` bezit en vordert onderaan de lijst van waarden.

Dispatcher vindt de best-passende virtuele gastheerwaarde op de volgende manier:

* De eerst-ontmoet virtuele gastheer die alle drie van `host`, de `scheme`, en `uri` van het verzoek aanpast wordt gebruikt.
* Als er geen `virtualhosts` waarden zijn `scheme` en `uri` delen die overeenkomen met de aanvraag `scheme` en `uri` met de aanvraag, wordt de eerst aangetroffen virtuele host gebruikt die overeenkomt met `host` de aanvraag.
* Als geen `virtualhosts` waarden een gastheerdeel hebben dat de gastheer van het verzoek aanpast, wordt de hoogste virtuele gastheer van het hoogste landbouwbedrijf gebruikt.

Daarom zou u uw standaard virtuele gastheer bij de bovenkant van het `virtualhosts` bezit in het hoogste landbouwbedrijf van uw dispatcher.any- dossier moeten plaatsen.

### Voorbeeld virtuele hostresolutie {#example-virtual-host-resolution}

Het volgende voorbeeld vertegenwoordigt een fragment van een dispatcher.any- dossier dat twee landbouwbedrijven van de Verzender bepaalt, en elk landbouwbedrijf bepaalt een `virtualhosts` bezit.

```xml
/farms
  {
  /myProducts 
    { 
    /virtualhosts
      {
      "www.mycompany.com"
      }
    /renders
      {
      /hostname "server1.myCompany.com"
      /port "80"
      }
    }
  /myCompany 
    { 
    /virtualhosts
      {
      "www.mycompany.com/products/*"
      }
    /renders
      {
      /hostname "server2.myCompany.com"
      /port "80"
      }
    }
  }
```

Gebruikend dit voorbeeld, toont de volgende lijst de virtuele gastheren die voor de bepaalde HTTP- verzoeken worden opgelost:

| Aanvraag-URL | Opgeloste virtuele host |
|---|---|
| `https://www.mycompany.com/products/gloves.html` | `www.mycompany.com/products/` |
| `https://www.mycompany.com/about.html` | `www.mycompany.com` |

## Beveiligde sessies inschakelen - /sessionmanagement {#enabling-secure-sessions-sessionmanagement}

>[!CAUTION]
>
>`/allowAuthorized` U **moet** deze functie alleen inschakelen `"0"` in de `/cache` sectie.

Creeer een veilige zitting voor toegang tot teruggeven landbouwbedrijf zodat de gebruikers login moeten om het even welke pagina in het landbouwbedrijf toegang hebben. Na het programma openen, kunnen de gebruikers tot pagina&#39;s in het landbouwbedrijf toegang hebben. Zie Een gesloten gebruikersgroep [](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/cug.html#CreatingTheUserGroupToBeUsed) maken voor informatie over het gebruik van deze functie met CUG&#39;s. Zie ook de [lijst](/help/using/security-checklist.md) Beveiliging van de afzender voordat u live gaat.

De `/sessionmanagement` eigenschap is een subeigenschap van `/farms`.

>[!CAUTION]
>
>Als gedeelten van uw website verschillende toegangsvereisten gebruiken, moet u meerdere boerderijen definiëren.

**/sessionmanagement** heeft verschillende subparameters:

**/directory** (verplicht)

De map waarin de sessiegegevens worden opgeslagen. Als de map niet bestaat, wordt deze gemaakt.

>[!CAUTION]
>
> Wanneer het vormen van de folder sub-parameter richt **niet** aan de wortelomslag (`/directory "/"`) aangezien het ernstige problemen kan veroorzaken. Geef altijd het pad op naar de map waarin de sessiegegevens worden opgeslagen. Bijvoorbeeld:

```xml
/sessionmanagement 
  { 
  /directory "/usr/local/apache/.sessions"
  }
```

**/encode** (optioneel)

Hoe de sessiegegevens worden gecodeerd. Gebruik &quot;md5&quot; voor codering met het md5-algoritme of &quot;hex&quot; voor hexadecimale codering. Als u de sessiegegevens versleutelt, kan een gebruiker met toegang tot het bestandssysteem de sessie-inhoud niet lezen. De standaardwaarde is &quot;md5&quot;.

**/header** (optioneel)

De naam van de HTTP-header of het cookie waarin de autorisatiegegevens zijn opgeslagen. Als u de informatie in de kopbal van http opslaat, gebruik `HTTP:<*header-name*>`. Als u de gegevens in een cookie wilt opslaan, gebruikt u `Cookie:<header-name>`. Als u geen waarde opgeeft, `HTTP:authorization` wordt een waarde gebruikt.

**/timeout** (optioneel)

Het aantal seconden tot de sessietijden uit nadat deze voor het laatst zijn gebruikt. Als niet gespecificeerd &quot;800&quot;wordt gebruikt, zodat de zittingstijden een beetje meer dan 13 minuten na het laatste verzoek van de gebruiker.

Een voorbeeldconfiguratie ziet er als volgt uit:

```xml
/sessionmanagement 
  { 
  /directory "/usr/local/apache/.sessions" 
  /encode "md5" 
  /header "HTTP:authorization" 
  /timeout "800" 
  }
```

## Paginarenderers definiëren {#defining-page-renderers-renders}

De eigenschap /renders definieert de URL waarnaar de afzender een verzoek verzendt om een document te renderen. In de volgende voorbeeldsectie `/renders` wordt één AEM-instantie voor rendering geïdentificeerd:

```xml
/renders
  {
    /myRenderer
      {
      # hostname or IP of the renderer
      /hostname "aem.myCompany.com"
      # port of the renderer
      /port "4503"
      # connection timeout in milliseconds, "0" (default) waits indefinitely
      /timeout "0"
      }
  }
```

De volgende voorbeeldsectie /renders identificeert een instantie AEM die op de zelfde computer zoals verzender loopt:

```xml
/renders
  {
    /myRenderer
     {
     /hostname "127.0.0.1"
     /port "4503"
     }
  }
```

In het volgende voorbeeld /renders-gedeelte worden renderverzoeken gelijkelijk verdeeld over twee AEM-instanties:

```xml
/renders
  {
    /myFirstRenderer
      {
      /hostname "aem.myCompany.com"
      /port "4503"
      }
    /mySecondRenderer
      {
      /hostname "127.0.0.1"
      /port "4503"
      }
  }
```

### Renderopties {#renders-options}

**/timeout**

Geeft de time-out van de verbinding op die de AEM-instantie benadert, in milliseconden. De standaardwaarde is &quot;0&quot;, waardoor de Dispatcher oneindig wacht.

**/receiveTimeout**

Geeft de tijd op in milliseconden die een reactie mag afleggen. De standaardwaarde is &quot;600000&quot;, waardoor de Dispatcher 10 minuten wacht. Met de instelling &quot;0&quot; wordt de time-out volledig verwijderd.\
Wanneer de time-out wordt bereikt tijdens het parseren van responsheaders, wordt een HTTP-status van 504 (Bad Gateway) geretourneerd. Als de time-out wordt bereikt terwijl de hoofdtekst van de reactie wordt gelezen, retourneert de Dispatcher de onvolledige reactie op de client, maar verwijdert de Dispatcher alle cachebestanden die mogelijk zijn geschreven.

**/ipv4**

Geeft aan of Dispatcher de `getaddrinfo` functie (voor IPv6) of de `gethostbyname` functie (voor IPv4) gebruikt om het IP-adres van de rendering te verkrijgen. Bij de waarde 0 wordt `getaddrinfo` het gereedschap gebruikt. Bij de waarde 1 wordt `gethostbyname` het product gebruikt. De standaardwaarde is 0.

De functie getaddrinfo retourneert een lijst met IP-adressen. Dispatcher herhaalt de lijst van adressen tot het een verbinding TCP/IP vestigt. Daarom is ipv4 bezit belangrijk wanneer teruggeven hostname met veelvoudige IP adressen wordt geassocieerd en de gastheer, in antwoord op de functie getaddrinfo, een lijst van IP adressen terugkeert die altijd in de zelfde orde zijn. In deze situatie, zou u de functie moeten gebruiken gethostbyname zodat het IP adres dat de Ontvanger met verbindt willekeurig wordt verdeeld.

De ELB (Amazon Elastic Load Balancing) is een service die reageert op getaddrinfo met een lijst met IP-adressen die mogelijk dezelfde volgorde heeft.

**/secure**

Als de `/secure` eigenschap de waarde 1 heeft, gebruikt Dispatcher HTTPS om te communiceren met de AEM-instantie. Voor extra details, zie ook het [Vormen Verzender om SSL](dispatcher-ssl.md#configuring-dispatcher-to-use-ssl)te gebruiken.

**/always-resolve**

Met Dispatcher versie **4.1.6**, kunt u het `/always-resolve` bezit als volgt vormen:

* Wanneer reeks aan &quot;1&quot;het gastheer-naam op elk verzoek zal oplossen (de Verzender zal nooit om het even welk IP adres in het voorgeheugen onderbrengen). Er kan een lichte prestatiesinvloed toe te schrijven zijn aan de extra vraag die wordt vereist om de gastheerinformatie voor elk verzoek te krijgen.
* Als het bezit niet wordt geplaatst, zal het IP adres door gebrek in de cache worden geplaatst.

Ook, kan dit bezit worden gebruikt voor het geval u in dynamische IP resolutiekwesties loopt, zoals aangetoond in de volgende steekproef:

```xml
/renders {
  /0001 {
     /hostname "host-name-here"
     /port "4502"
     /ipv4 "1"
     /always-resolve "1"
     }
  }
```

## Toegang tot inhoud configureren {#configuring-access-to-content-filter}

Gebruik de `/filter` sectie om de HTTP-aanvragen op te geven die Dispatcher accepteert. Alle andere aanvragen worden teruggestuurd naar de webserver met een foutcode van 404 (pagina niet gevonden). Als er geen `/filter` sectie bestaat, worden alle aanvragen geaccepteerd.

**Opmerking:** Aanvragen voor de [status](dispatcher-configuration.md#main-pars-title-28) worden altijd afgewezen.

>[!CAUTION]
>
>Zie de Controlelijst [van de Veiligheid van de](security-checklist.md) Verzender voor verdere overwegingen wanneer het beperken van toegang gebruikend Verzender. Lees ook de [AEM Security Cheklist](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html) voor meer beveiligingsdetails over uw AEM-installatie.

De /filter sectie bestaat uit een reeks regels die of toegang tot inhoud volgens patronen in het verzoek-lijn deel van het HTTP- verzoek ontkennen of toestaan. U zou een whiltelist strategie voor uw /filter sectie moeten gebruiken:

* Eerst, ontken toegang tot alles.
* Toegang tot inhoud toestaan als dat nodig is.

### Filter definiëren {#defining-a-filter}

Elk item in de `/filter` sectie bevat een type en een patroon die overeenkomen met een specifiek element van de aanvraagregel of de gehele aanvraagregel. Elk filter kan de volgende items bevatten:

* **Type**: Het `/type` geeft aan of toegang voor de aanvragen die overeenkomen met het patroon moet worden toegestaan of geweigerd. De waarde kan ofwel `allow` of `deny`.

* **Element van de aanvraagregel:** Neem `/method`, `/url`, `/query`of `/protocol` en een patroon op voor het filteren van aanvragen volgens deze specifieke onderdelen van het request-line gedeelte van de HTTP-aanvraag. Filteren op elementen van de aanvraaglijn (eerder dan op de volledige verzoeklijn) is de aangewezen filtermethode.

* **Geavanceerde elementen van de aanvraagregel:** Vanaf Dispatcher 4.2.0 zijn er vier nieuwe filterelementen beschikbaar voor gebruik. Deze nieuwe elementen zijn `/path`, `/selectors``/extension` respectievelijk `/suffix` . Neem een of meer van deze items op om de URL-patronen verder te beheren.

>[!NOTE]
>
>Voor meer informatie over welk deel van de verzoeklijn elk van deze elementenverwijzingen, zie de Verschuivende pagina van de Decomposition [](https://sling.apache.org/documentation/the-sling-engine/url-decomposition.html) URL.

* **glob-eigenschap**: De `/glob` eigenschap wordt gebruikt om overeen te komen met de gehele request-line van de HTTP-aanvraag.

>[!CAUTION]
>
>Filteren met globs is afgekeurd in Dispatcher. Als zodanig moet u voorkomen dat globaal wordt gebruikt in de `/filter` secties, omdat dit tot beveiligingsproblemen kan leiden. Dus in plaats van:

`/glob "* *.css *"`

moet u

`/url "*.css"`

#### The request-line Part of HTTP Requests {#the-request-line-part-of-http-requests}

HTTP/1.1 definieert de [request-line](https://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html) als volgt:

*Methode Request-URI HTTP-Version*&lt;CRLF>

De &lt;CRLF>-tekens vertegenwoordigen een regelterugloop gevolgd door een regelinvoer. Het volgende voorbeeld is de verzoek-lijn die wordt ontvangen wanneer een cliënt om de en pagina van Geometrixx-Outoors plaats verzoekt:

/content/geometrixx-outdoors/en.html HTTP.1.1&lt;CRLF>

In uw patronen moet rekening worden gehouden met de spatietekens in de request-line en de &lt;CRLF>-tekens.

#### Dubbele aanhalingstekens versus enkele aanhalingstekens {#double-quotes-vs-single-quotes}

Gebruik bij het maken van filterregels dubbele aanhalingstekens `"pattern"` voor eenvoudige patronen. Als u Dispatcher 4.2.0 of hoger gebruikt en uw patroon een reguliere expressie bevat, moet u het regex-patroon `'(pattern1|pattern2)'` tussen enkele aanhalingstekens plaatsen.

#### Reguliere expressies {#regular-expressions}

Na Dispatcher 4.2.0, kunt u POSIX Uitgebreide Reguliere Uitdrukkingen in uw filterpatronen omvatten.

#### Problemen met filters oplossen {#troubleshooting-filters}

Als uw filters niet in de manier teweegbrengen u zou verwachten, laat het [Spoor Logging](#trace-logging) op verzender toe zodat kunt u zien welke filter de aanvraag onderschept.

#### Voorbeeldfilter: Alles weigeren {#example-filter-deny-all}

In de volgende voorbeeldfiltersectie worden aanvragen voor alle bestanden door Dispatcher afgewezen. U zou toegang tot alle dossiers moeten ontkennen en dan toegang tot specifieke gebieden toestaan.

```xml
  /0001  { /glob "*" /type "deny" }
```

Verzoeken naar een expliciet geweigerd gebied hebben tot gevolg dat een foutcode van 404 (pagina niet gevonden) wordt geretourneerd.

#### Voorbeeldfilter: Toegang tot specifieke gebieden weigeren {#example-filter-deny-acess-to-specific-areas}

Met filters kunt u ook toegang tot verschillende elementen weigeren, zoals ASP-pagina&#39;s en gevoelige gebieden in een publicatie-instantie. Met het volgende filter krijgt u geen toegang tot ASP-pagina&#39;s:

```xml
/0002  { /type "deny" /url "*.asp"  }
```

#### Voorbeeldfilter: POST-verzoeken inschakelen {#example-filter-enable-post-requests}

Met het volgende voorbeeldfilter kunt u formuliergegevens verzenden via de POST-methode:

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002 { /type "allow" /method "POST" /url "/content/[.]*.form.html" }
}
```

#### Voorbeeldfilter: Toegang tot de workflowconsole toestaan {#example-filter-allow-access-to-the-workflow-console}

In het volgende voorbeeld wordt een filter getoond dat wordt gebruikt om externe toegang tot de workflowconsole te weigeren:

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002  {  /type "allow"  /url "/libs/cq/workflow/content/console*"  }
}
```

Als uw publicatie-instantie gebruikmaakt van een webtoepassingscontext (bijvoorbeeld publiceren), kan dit ook aan uw filterdefinitie worden toegevoegd.

```xml
/0003   { /type "deny"  /url "/publish/libs/cq/workflow/content/console/archive*"  }
```

Als u binnen het beperkte gebied nog steeds toegang moet hebben tot enkele pagina&#39;s, kunt u deze toegankelijk maken. Voeg bijvoorbeeld de volgende sectie toe om toegang tot het tabblad Archiveren in de workflowconsole toe te staan:

```xml
/0004  { /type "allow"  /url "/libs/cq/workflow/content/console/archive*"   }
```

>[!NOTE]
>
>Wanneer meerdere filterpatronen van toepassing zijn op een aanvraag, is het laatste filterpatroon dat van toepassing is, effectief.

#### Voorbeeld, filter: Reguliere expressies gebruiken {#example-filter-using-regular-expressions}

Met dit filter schakelt u extensies in mappen met niet-openbare inhoud in met behulp van een reguliere expressie, die hier tussen enkele aanhalingstekens wordt gedefinieerd:

```xml
/005  {  /type "allow" /extension '(css|gif|ico|js|png|swf|jpe?g)' }
```

#### Voorbeeld, filter: Aanvullende elementen van een aanvraag-URL filteren {#example-filter-filter-additional-elements-of-a-request-url}

Hieronder ziet u een voorbeeld van een regel die het ophalen van inhoud van het `/content` pad en de substructuur ervan blokkeert met behulp van filters voor paden, kiezers en extensies:

```xml
/006 {
        /type "deny"
        /path "/content/*"
        /selectors '(feed|rss|pages|languages|blueprint|infinity|tidy)'
        /extension '(json|xml|html)'
        }
```

### Voorbeeld-/filtersectie {#example-filter-section}

Wanneer het vormen van Dispatcher zou u externe toegang zoveel mogelijk moeten beperken. In het volgende voorbeeld wordt minimale toegang geboden aan externe bezoekers:

* `/content`
* diverse inhoud, zoals ontwerpen en clientbibliotheken; bijvoorbeeld:

   * `/etc/designs/default*`
   * `/etc/designs/mydesign*`

Nadat u filters hebt gemaakt, [test u de toegang tot](dispatcher-configuration.md#main-pars-title-19) de pagina om te controleren of uw AEM-instantie veilig is.

De volgende /filter sectie van het dispatcher.any- dossier kan als basis in uw de configuratiedossier [van de](dispatcher-configuration.md) Verzender worden gebruikt.

Dit voorbeeld is gebaseerd op het standaardconfiguratiedossier dat van Dispatcher wordt voorzien en als voorbeeld voor gebruik in een productiemilieu bedoeld is. De punten met # worden vooraf bepaald worden gedeactiveerd (gecommenteerd uit), zou de voorzichtigheid moeten worden genomen als u om het even welk van deze (door # op die lijn te verwijderen) besluit te activeren aangezien dit een veiligheidseffect kan hebben.

U zou toegang tot alles moeten ontkennen, dan toegang tot specifieke (beperkte) elementen toestaan:

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:32:37.986-0400

<p>We should mention the config files that are shipped with the dispatcher distribution and only give a few examples here. This aims to avoid confusion and reduce content maintenance.<br /> </p>

 -->

```xml
  /filter
      {
      # Deny everything first and then allow specific entries
      /0001 { /type "deny" /glob "*" }
      
      # Open consoles
#     /0011 { /type "allow" /url "/admin/*"  }  # allow servlet engine admin
#     /0012 { /type "allow" /url "/crx/*"    }  # allow content repository
#     /0013 { /type "allow" /url "/system/*" }  # allow OSGi console
        
      # Allow non-public content directories
#     /0021 { /type "allow" /url "/apps/*"   }  # allow apps access
#     /0022 { /type "allow" /url "/bin/*"    }
      /0023 { /type "allow" /url "/content*" }  # disable this rule to allow mapped content only
      
#     /0024 { /type "allow" /url "/libs/*"   }
#     /0025 { /type "deny"  /url "/libs/shindig/proxy*" } # if you enable /libs close access to proxy

#     /0026 { /type "allow" /url "/home/*"   }
#     /0027 { /type "allow" /url "/tmp/*"    }
#     /0028 { /type "allow" /url "/var/*"    }

      # Enable extensions in non-public content directories, using a regular expression
      /0041
        {
        /type "allow"
        /extension '(css|gif|ico|js|png|swf|jpe?g)'
        }

      # Enable features 
      /0062 { /type "allow" /url "/libs/cq/personalization/*"  }  # enable personalization

      # Deny content grabbing, on all accessible pages, using regular expressions
      /0081
        {
        /type "deny"
        /selectors '((sys|doc)view|query|[0-9-]+)'
        /extension '(json|xml)'
        }
      # Deny content grabbing for /content and its subtree
      /0082
        {
        /type "deny"
        /path "/content/*"
        /selectors '(feed|rss|pages|languages|blueprint|infinity|tidy)'
        /extension '(json|xml|html)'
        }

#     /0087 { /type "allow" /method "GET" /extension 'json' "*.1.json" }  # allow one-level json requests
}
```

>[!NOTE]
>
>Wanneer u Apache gebruikt, ontwerpt u uw filter-URL-patronen volgens de eigenschap DispatcherUseProcinedURL van de module Dispatcher. (Zie [Apache Web Server - Uw Apache Web Server configureren voor Dispatcher](dispatcher-install.md#main-pars-55-35-1022).)

>[!NOTE]
>
>Filters 0030 en 0031 betreffende dynamische media zijn van toepassing op AEM 6.0 en hoger.

Overweeg de volgende aanbevelingen als u verkiest om toegang uit te breiden:

* Externe toegang tot `/admin` moet altijd *volledig* zijn uitgeschakeld als u CQ-versie 5.4 of een eerdere versie gebruikt.

* Voorzichtigheid is geboden wanneer toegang wordt verleend tot bestanden in `/libs`. Toegang moet op individuele basis worden toegestaan.
* Ontken toegang tot de replicatieconfiguratie zodat kan het niet worden gezien:

   * `/etc/replication.xml*`
   * `/etc/replication.infinity.json*`

* Toegang tot de Google Gadgets reverse-proxy weigeren:

   * `/libs/opensocial/proxy*`

Afhankelijk van uw installatie, zouden er extra middelen onder `/libs`, `/apps` of elders kunnen zijn, die beschikbaar moeten worden gemaakt. U kunt het `access.log` bestand gebruiken als een methode om te bepalen welke bronnen extern worden benaderd.

>[!CAUTION]
>
>De toegang tot consoles en folders kan een veiligheidsrisico voor productiemilieu&#39;s vormen. Tenzij u expliciete redenen hebt, moeten ze gedeactiveerd blijven (gemarkeerd als commentaar).

>[!CAUTION]
>
>Als u rapporten in een publicatiemilieu [](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/reporting.html#UsingReportsinaPublishEnvironment) gebruikt zou u Dispatcher moeten vormen om toegang tot `/etc/reports` voor externe bezoekers te ontkennen.

### Query-tekenreeksen beperken {#restricting-query-strings}

Sinds Dispatcher versie 4.1.5 gebruikt u de `/filter` sectie om querytekenreeksen te beperken. Het wordt hoogst geadviseerd om vraagkoorden uitdrukkelijk toe te staan en generische toelage door `allow` filterelementen uit te sluiten.

Één enkele ingang kan of *glob* of één of andere combinatie *methode*,*url*,*vraag* en *versie* maar niet allebei hebben. In het volgende voorbeeld wordt de `a=*` querytekenreeks toegestaan en worden alle andere querytekenreeksen voor URL&#39;s geweigerd die worden omgezet in het `/etc` knooppunt:

```xml
/filter {
 /0001 { /type "deny" /method "POST" /url "/etc/*" }
 /0002 { /type "allow" /method "GET" /url "/etc/*" /query "a=*" }
}
```

>[!NOTE]
>
>Als een regel een regel bevat `/query`, zal het slechts verzoeken aanpassen die een vraagkoord bevatten en het verstrekte vraagpatroon aanpassen.
>
>In bovenstaand voorbeeld, als de verzoeken aan `/etc` die geen vraagkoord zouden moeten worden toegestaan ook, zouden de volgende regels worden vereist:


```xml
/filter {  
>/0001 { /type "deny" /method “*" /url "/path/*" }  
>/0002 { /type "allow" /method "GET" /url "/path/*" }  
>/0003 { /type “deny" /method "GET" /url "/path/*" /query "*" }  
>/0004 { /type "allow" /method "GET" /url "/path/*" /query "a=*" }  
}  
```

### Beveiliging van Dispatcher testen {#testing-dispatcher-security}

Dispatcher-filters blokkeren de toegang tot de volgende pagina&#39;s en scripts in AEM-publicatieinstanties. Gebruik een webbrowser om te proberen de volgende pagina&#39;s te openen zoals een bezoeker van de site zou doen en om te controleren of code 404 wordt geretourneerd. Pas de filters aan als er andere resultaten worden verkregen.

Merk op dat u normale paginerendering voor /content/add_valid_page.html zou moeten zien?debug=layout.


* /admin
* /system/console
* /dav/crx.default
* /crx
* /bin/crxde/logs
* /jcr:system/jcr:versionStorage.json
* /_jcr_system/_jcr_versionStorage.json
* /libs/wcm/core/content/siteadmin.html
* /libs/collab/core/content/admin.html
* /libs/cq/ui/content/dumplibs.html
* /var/linkchecker.html
* /etc/linkchecker.html
* /home/users/a/admin/profile.json
* /home/users/a/admin/profile.xml
* /libs/cq/core/content/login.json
* /content/../libs/foundation/components/text/text.jsp
* /content/.{./libs/foundation/components/text/text.jsp
* /apps/sling/config/org.apache.felix.webconsole.internal.servlet.OsgiManager.config/jcr%3acontent/jcr%3adata
* /libs/foundation/components/primary/cq/workflow/components/participants/json.GET.servlet
* /content.pages.json
* /content.languages.json
* /content.blueprint.json
* /content.-1.json
* /content.10.json
* /content.infinity.json
* /content.tidy.json
* /content.tidy.-1.blubber.json
* /content/dam.tidy.-100.json
* /content/content/geometrixx.sitemap.txt
* /content/add_valid_page.query.json?statement=/*
* /content/add_valid_page.qu%65ry.js%6Fn?statement=/*
* /content/add_valid_page.query.json?statement=//*[@transportPassword]/(@transportPassword%20|%20@transportUri%20|%20@transportUser)
* /content/add_valid_path_to_a_page/_jcr_content.json
* /content/add_valid_path_to_a_page/jcr:content.json
* /content/add_valid_path_to_a_page/_jcr_content.feed
* /content/add_valid_path_to_a_page/jcr:content.feed
* /content/add_valid_path_to_a_page/pagename._jcr_content.feed
* /content/add_valid_path_to_a_page/pagename.jcr:content.feed
* /content/add_valid_path_to_a_page/pagename.docview.xml
* /content/add_valid_path_to_a_page/pagename.docview.json
* /content/add_valid_path_to_a_page/pagename.sysview.xml
* /etc.xml
* /content.feed.xml
* /content.rss.xml
* /content.feed.html
* /content/add_valid_page.html?debug=layout
* /projecten
* /tagging
* /etc/replication.html
* /etc/cloudservices.html
* /welcome

Geef het volgende bevel in een terminal of bevelherinnering uit om te bepalen of de anonieme schrijftoegang wordt toegelaten. U zou geen gegevens aan de knoop moeten kunnen schrijven.

`curl -X POST "https://anonymous:anonymous@hostname:port/content/usergenerated/mytestnode"`

Geef het volgende bevel in een terminal of bevelherinnering uit om te proberen om het geheime voorgeheugen van de Verzender ongeldig te maken, en ervoor te zorgen dat u code 404 reactie ontvangt:

`curl -H "CQ-Handle: /content" -H "CQ-Path: /content" https://yourhostname/dispatcher/invalidate.cache`

## Toegang tot URL&#39;s met Vanity inschakelen {#enabling-access-to-vanity-urls-vanity-urls}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2015-03-25T14:23:05.185-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For https://jira.corp.adobe.com/browse/DOC-4812</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The "com.adobe.granite.dispatcher.vanityurl.content" package needs to be made public before publishing this contnet.</p>
 -->

Configureer Dispatcher om toegang in te schakelen tot URL&#39;s met ijkpunten die zijn geconfigureerd voor uw CQ- of AEM-pagina&#39;s.

Wanneer toegang tot vanity URLs wordt toegelaten, roept de Verzender periodiek de dienst die op de teruggeeft instantie loopt om een lijst van vanity URLs te verkrijgen. Dispatcher slaat deze lijst op in een lokaal bestand. Wanneer een aanvraag voor een pagina wordt afgewezen vanwege een filter in de `/filter` sectie, raadpleegt Dispatcher de lijst met vanity URL&#39;s. Als de ontkende URL in de lijst staat, geeft Dispatcher toegang tot de vanity URL.

Als u toegang tot URL&#39;s met ijkpunten wilt inschakelen, voegt u een `/vanity_urls` sectie aan de `/farms` sectie toe, vergelijkbaar met het volgende voorbeeld:

```xml
 /vanity_urls {
      /url "/libs/granite/dispatcher/content/vanityUrls.html"
      /file "/tmp/vanity_urls"
      /delay 300
 }
```

De `/vanity_urls` sectie bevat de volgende eigenschappen:

* `/url`: Het pad naar de service vanity URL die wordt uitgevoerd op de renderinstantie. De waarde van deze eigenschap moet `"/libs/granite/dispatcher/content/vanityUrls.html"`zijn.

* `/file`: Het pad naar het lokale bestand waar Dispatcher de lijst met vanity-URL&#39;s opslaat. Zorg ervoor dat Dispatcher schrijftoegang heeft tot dit bestand.
* `/delay`: (Seconden) De tijd tussen vraag aan de dienst van vanity URL.

>[!NOTE]
>
>Als uw teruggeven een geval van AEM is, moet u het pakket [VanityURLS-Components](https://www.adobeaemcloud.com/content/marketplace/marketplaceProxy.html?packagePath=/content/companies/public/adobe/packages/cq600/component/vanityurls-components) installeren om de dienst van vanity URL te installeren. (Zie [Aanmelden bij Delen](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/package-manager.html#SigningIntoPackageShare)pakket.)

Gebruik de volgende procedure om toegang tot vanity URLs toe te laten.

1. Als uw renderservice een AEM-instantie is, installeert u het pakket com.adobe.granite.dispatcher.vanityurl.content op de publicatie-instantie (zie de opmerking hierboven).
1. Voor elke vanity-URL die u voor een AEM- of CQ-pagina hebt geconfigureerd, controleert u of de URL door de ` [/filter](dispatcher-configuration.md#main-pars_134_32_0009)` configuratie wordt geweigerd. Voeg zo nodig een filter toe dat de URL weigert.
1. Voeg de `/vanity_urls` sectie hieronder toe `/farms`.
1. Start Apache-webserver opnieuw.

## Door:sturen de Verzoeken van de Syndicatie - /propagateSyndPost {#forwarding-syndication-requests-propagatesyndpost}

Syndicatieverzoeken zijn gewoonlijk alleen bedoeld voor Dispatcher, zodat ze standaard niet naar de renderer worden verzonden (bijvoorbeeld een AEM-instantie).

Indien nodig, plaats het /propagateSyndPost bezit aan &quot;1&quot;om syndicatieverzoeken aan Dispatcher door:sturen. Indien ingesteld, moet u ervoor zorgen dat POST-aanvragen niet worden afgewezen in de filtersectie.

## De Dispatcher Cache - /cache configureren {#configuring-the-dispatcher-cache-cache}

De `/cache` sectie bepaalt hoe de Verzender documenten in cache plaatst. Vorm verscheidene sub-eigenschappen om uw caching strategieën uit te voeren:


* /docroot
* /statfile
* /serveStaleOnError
* /allowAuthorized
* /rules
* /statfileslevel
* /invalidate
* /invalidateHandler
* /allowedClients
* /ignoreUrlParams
* /headers
* /mode
* /nl/respijtperiode
* /enableTTL


Een voorbeeldgeheim voorgeheugensectie zou als volgt kunnen kijken:

```xml
/cache
  {
  /docroot "/opt/dispatcher/cache"
  /statfile  "/tmp/dispatcher-website.stat"          
  /allowAuthorized "0"
      
  /rules
    {
    # List of files that are cached
    }

  /invalidate
    {
    # List of files that are auto-invalidated
    }
  }
  
```

>[!NOTE]
>
>Voor toestemming-gevoelige caching, lees [Caching Beveiligde Inhoud](permissions-cache.md).

### De cachemap opgeven {#specifying-the-cache-directory}

De `/docroot` eigenschap identificeert de map waarin cachebestanden worden opgeslagen.

>[!NOTE]
>
>De waarde moet precies hetzelfde pad hebben als de hoofdmap van het document van de webserver, zodat de zender en de webserver dezelfde bestanden verwerken.\
>De webserver is verantwoordelijk voor het leveren van de juiste statuscode wanneer het cachebestand van de verzender wordt gebruikt. Daarom is het belangrijk dat de server deze code ook kan vinden.

Als u veelvoudige landbouwbedrijven gebruikt, moet elk landbouwbedrijf een verschillende documentwortel gebruiken.

### De naam van het statusbestand wijzigen {#naming-the-statfile}

De `/statfile` eigenschap identificeert het bestand dat als statfile moet worden gebruikt. Dispatcher gebruikt dit bestand om de tijd van de meest recente inhoudsupdate te registreren. Het statusbestand kan elk bestand op de webserver zijn.

De status heeft geen inhoud. Wanneer de inhoud wordt bijgewerkt, werkt Dispatcher de tijdstempel bij. Het standaardstatusbestand heeft de naam .stat en wordt opgeslagen in de docroot. Dispatcher blokkeert de toegang tot het statfile.

>[!NOTE]
>
>Als `/statfileslevel` wordt gevormd, negeert de Ontvanger het `/statfile` bezit en gebruikt .stat als naam.

### Stale documenten verzenden bij fouten {#serving-stale-documents-when-errors-occur}

De `/serveStaleOnError` eigenschap bepaalt of Dispatcher ongeldig document retourneert wanneer de renderserver een fout retourneert. Wanneer een statusbestand wordt aangeraakt en cacheinhoud ongeldig wordt gemaakt, verwijdert Dispatcher de inhoud in de cache de volgende keer dat deze wordt opgevraagd.

Als `/serveStaleOnError` is ingesteld op &quot;1&quot;, verwijdert Dispatcher geen ongeldig gemaakte inhoud uit de cache, tenzij de renderserver een succesvol antwoord retourneert. Een 5xx-reactie van AEM of een verbindingstijd zorgt ervoor dat Dispatcher de verouderde inhoud levert en reageert met en HTTP-status van 111 (Revalidation Failed).

### In cache plaatsen wanneer verificatie wordt gebruikt {#caching-when-authentication-is-used}

Het `/allowAuthorized` bezit controleert of de verzoeken die om het even welke volgende authentificatieinformatie bevatten in het voorgeheugen worden opgeslagen:

* De `authorization` koptekst.
* Een cookie met de naam `authorization`.
* Een cookie met de naam `login-token`.

Door gebrek, worden de verzoeken die deze authentificatieinformatie omvatten niet in het voorgeheugen ondergebracht omdat de authentificatie niet wordt uitgevoerd wanneer een caching document aan de cliënt is teruggekeerd. Met deze configuratie voorkomt u dat Dispatcher cachedocumenten kan verzenden aan gebruikers die niet de vereiste rechten hebben.

Nochtans, als uw vereisten het caching van voor authentiek verklaarde documenten toestaan, plaats /allowAuthorized aan één:

`/allowAuthorized "1"`

>[!NOTE]
>
>Om zittingsbeheer (gebruikend het `/sessionmanagement` bezit) toe te laten, moet het `/allowAuthorized` bezit worden geplaatst aan `"0"`.

### Documenten opgeven om in cache te plaatsen {#specifying-the-documents-to-cache}

De `/rules` eigenschap bepaalt welke documenten in het cachegeheugen worden opgeslagen volgens het documentpad. Ongeacht het /rules bezit, neemt de Dispatcher nooit een document in de volgende omstandigheden in het voorgeheugen op:

* Als de aanvraag-URI een vraagteken (&quot;?&quot;) bevat.\
   Dit geeft meestal een dynamische pagina aan, zoals een zoekresultaat dat niet in de cache hoeft te worden opgeslagen.
* De bestandsextensie ontbreekt.\
   De webserver heeft de extensie nodig om het documenttype (het MIME-type) te bepalen.
* De authentificatiekopbal wordt geplaatst (dit kan worden gevormd)
* Als de instantie AEM met de volgende kopballen antwoordt:

   * `no-cache`
   * `no-store`
   * `must-revalidate`

>[!NOTE]
>
>De methoden GET of HEAD (voor de HTTP-header) kunnen door de Dispatcher in cache worden geplaatst. Zie de sectie Koppen van HTTP-reacties in [cache plaatsen voor meer informatie over het in cache plaatsen van responsheaders](dispatcher-configuration.md#caching-http-response-headers) .

Elk punt in het /rules bezit omvat een [globpatroon](#designing-patterns-for-glob-properties) en een type:

* Het globale patroon wordt gebruikt om de weg van het document aan te passen.
* Het type geeft aan of de documenten die overeenkomen met het glob-patroon in de cache moeten worden geplaatst. De waarde kan toestaan (het document in cache plaatsen) of weigeren (het document altijd weergeven) zijn.

Als u geen dynamische pagina&#39;s hebt (buiten die reeds uitgesloten door de bovengenoemde regels), kunt u Dispatcher vormen om alles in het voorgeheugen onder te brengen. De sectie Regels hiervoor ziet er als volgt uit:

```xml
/rules
  { 
    /0000  {  /glob "*"   /type "allow" }
  }
```

Zie Patronen [ontwerpen voor globale eigenschappen](#designing-patterns-for-glob-properties)voor meer informatie over eigenschappen van glob.

Als er gedeelten van de pagina dynamisch zijn (bijvoorbeeld een nieuwstoepassing) of zich in een gesloten gebruikersgroep bevinden, kunt u uitzonderingen definiëren:

>[!NOTE]
>
>Gesloten gebruikersgroepen mogen niet in de cache worden geplaatst omdat gebruikersrechten niet worden gecontroleerd op pagina&#39;s in de cache.

```xml
/rules
  {
   /0000  { /glob "*" /type "allow" }
   /0001  { /glob "/en/news/*" /type "deny" }
   /0002  { /glob "*/private/*" /type "deny"  }   
  }
```

**Compressie**

Op Apache-webservers kunt u de documenten in de cache comprimeren. Met compressie kan Apache het document op verzoek van de client in een gecomprimeerd formulier retourneren. Compressie wordt automatisch uitgevoerd door de Apache-module in te schakelen, `mod_deflate`bijvoorbeeld:

```xml
AddOutputFilterByType DEFLATE text/plain
```

De module wordt standaard geïnstalleerd met Apache 2.x.

<!-- 

Comment Type: draft

<note type="note"> 
 <p>Depending on the Apache web server version you can enable <span class="code">gzip</span> compression as follows:</p> 
 <ul> 
  <li>For Apache 1.3 you can enable the <span class="code">mod_gzip </span>module. The module must be downloaded and installed.</li> 
  <li>For Apache 2.x you can enable the <span class="code">mod_deflate</span> module. The module is installed by default with Apache 2.x.<br /> </li> 
 </ul> 
 <p> </p> 
</note>

 -->

<!-- 

Comment Type: draft

<p>The following rule caches all documents in compressed form; Apache can return either the uncompressed or the compressed form to the client:</p>

 -->

<!-- 

Comment Type: draft

<codeblock gutter="true" class="syntax xml">
  /rules!!discoiqbr!!&nbsp;&nbsp;{!!discoiqbr!!&nbsp;&nbsp;&nbsp;/rulelabel&nbsp;&nbsp;{&nbsp;&nbsp;/glob&nbsp;"*"&nbsp;/type&nbsp;"allow"&nbsp;&nbsp;/compress&nbsp;"gzip"&nbsp;}!!discoiqbr!!&nbsp;&nbsp;} 
</codeblock>

 -->

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-11-13T09:23:24.326-0500

<p>Hidden the <span class="code">mod_gzip</span> content as requested in CQDOC-11124.</p>

 -->

### Bestanden op mapniveau ongeldig maken {#invalidating-files-by-folder-level}

Gebruik de `/statfileslevel` eigenschap om cachebestanden ongeldig te maken op basis van het pad:

* Dispatcher maakt in elke map `.stat`bestanden van de hoofdmap tot het niveau dat u opgeeft. De hoofdmap van het document is niveau 0.
* Bestanden worden ongeldig gemaakt door het `.stat` bestand aan te raken. De laatste wijzigingsdatum van het `.stat` bestand wordt vergeleken met de laatste wijzigingsdatum van een document in de cache. Het document wordt opnieuw opgehaald als het `.stat` bestand nieuwer is.

* Wanneer een bestand op een bepaald niveau ongeldig wordt gemaakt, worden **alle** `.stat` bestanden van de hoofdmap **naar** het niveau van het ongeldig gemaakte bestand of de geconfigureerde bestanden `statsfilevel` (welke kleiner is) gewijzigd.

   * Bijvoorbeeld: Als u de `statfileslevel` eigenschap instelt op 6 en een bestand op niveau 5 ongeldig wordt gemaakt, wordt elk `.stat` bestand van docroot naar 5 aangeraakt. Als u doorgaat met dit voorbeeld en een bestand op niveau 7 dan elke ongeldig wordt gemaakt. `stat` bestand van docroot naar 6 wordt gewijzigd (sindsdien `/statfileslevel = "6"`).

Dit heeft alleen invloed op bronnen **langs het pad** naar het ongeldig gemaakte bestand. Bekijk het volgende voorbeeld: Een website gebruikt de structuur `/content/myWebsite/xx/.` Als u instelt `statfileslevel` als 3, wordt als volgt een `.stat`bestand gemaakt:

* `docroot`
* `/content`
* `/content/myWebsite`
* `/content/myWebsite/*xx*`

Wanneer een bestand in ongeldig `/content/myWebsite/xx` wordt gemaakt, `.stat` wordt elk `/content/myWebsite/xx`bestand van de hoofdmap naar de hoofdmap aangeraakt. Dit zou alleen het geval zijn voor `/content/myWebsite/xx` en niet bijvoorbeeld `/content/myWebsite/yy` of `/content/anotherWebSite`.

>[!NOTE]
>
>U kunt validatie voorkomen door een extra koptekst te verzenden `CQ-Action-Scope:ResourceOnly`. Dit kan worden gebruikt om bepaalde middelen te spoelen zonder andere delen van het geheime voorgeheugen ongeldig te maken. Zie [deze pagina](https://adobe-consulting-services.github.io/acs-aem-commons/features/dispatcher-flush-rules/index.html) en maak de Dispatcher Cache [handmatig ongeldig](https://helpx.adobe.com/experience-manager/dispatcher/using/page-invalidate.html) voor meer informatie.

>[!NOTE]
>
>Wanneer u een waarde voor de `/statfileslevel` eigenschap opgeeft, wordt de `/statfile` eigenschap genegeerd.

### Automatisch cachebestanden valideren {#automatically-invalidating-cached-files}

De `/invalidate` eigenschap definieert de documenten die automatisch ongeldig worden gemaakt wanneer de inhoud wordt bijgewerkt.

Met automatische ongeldigmaking verwijdert Dispatcher geen in het cachegeheugen opgeslagen bestanden nadat de inhoud is bijgewerkt, maar controleert de geldigheid van deze bestanden op het moment dat ze de volgende keer worden opgevraagd. Documenten in de cache die niet automatisch worden ongeldig gemaakt, blijven in de cache totdat een inhoudsupdate deze expliciet verwijdert.

Automatische validatie wordt meestal gebruikt voor HTML-pagina&#39;s. HTML-pagina&#39;s bevatten vaak koppelingen naar andere pagina&#39;s, waardoor het moeilijk is te bepalen of een inhoudsupdate een pagina beïnvloedt. Als u ervoor wilt zorgen dat alle relevante pagina&#39;s ongeldig worden gemaakt wanneer de inhoud wordt bijgewerkt, maakt u automatisch alle HTML-pagina&#39;s ongeldig. De volgende configuratie maakt alle HTML-pagina&#39;s ongeldig:

```xml
  /invalidate
  {
   /0000  { /glob "*" /type "deny" }
   /0001  { /glob "*.html" /type "allow" }
  }
```

Zie Patronen [ontwerpen voor globale eigenschappen](#designing-patterns-for-glob-properties)voor meer informatie over eigenschappen van glob.

Deze configuratie veroorzaakt de volgende activiteit wanneer /content/geometrixx/en wordt geactiveerd:

* Alle bestanden met patroon en.* worden verwijderd uit de map /content/geometrixx/.
* De map /content/geometrixx/nl/_jcr_content wordt verwijderd.
* Alle andere dossiers die /invalidate configuratie aanpassen worden niet onmiddellijk geschrapt. Deze bestanden worden verwijderd wanneer de volgende aanvraag wordt uitgevoerd. In ons voorbeeld wordt /content/geometrixx.html niet verwijderd, maar verwijderd wanneer /content/geometrixx.html wordt aangevraagd.

Als u automatisch gegenereerde PDF- en ZIP-bestanden aanbiedt om te downloaden, moet u deze mogelijk ook automatisch ongeldig maken. Een configuratievoorbeeld ziet dit als volgt uit:

```xml
/invalidate
  {
   /0000 { /glob "*" /type "deny" }
   /0001 { /glob "*.html" /type "allow" }
   /0002 { /glob "*.zip" /type "allow" }
   /0003 { /glob "*.pdf" /type "allow" }
  }
```

De integratie van AEM met de Analytics van Adobe levert configuratiegegevens in een analytics.sitecatalyst.js- dossier in uw website. Het voorbeeld dispatcher.any-bestand dat bij Dispatcher wordt geleverd, bevat de volgende regel voor het ongeldig maken van dit bestand:

```xml
{
   /glob "*/analytics.sitecatalyst.js"  /type "allow"
}
```

### Aangepaste validatiescripts gebruiken {#using-custom-invalidation-scripts}

Het /invalidateHandler bezit staat u toe om een manuscript te bepalen dat voor elk ongeldigingsverzoek wordt geroepen dat door Dispatcher wordt ontvangen.

De methode wordt aangeroepen met de volgende argumenten:

* Handgreep\
   Het ongeldig gemaakte inhoudspad
* Actie\
   De replicatieactie (bijvoorbeeld activeren, deactiveren)
* Toepassingsgebied van actie\
   Het bereik van de replicatieactie (leeg, tenzij een koptekst van `CQ-Action-Scope: ResourceOnly` wordt verzonden, zie [Het ongeldig maken van Cached Pagina&#39;s van AEM](page-invalidate.md) voor details)

Dit kan worden gebruikt om een aantal verschillende gebruiksgevallen te behandelen, zoals het ongeldig maken van andere toepassings specifieke geheime voorgeheugens, of om gevallen te behandelen waar extern URL van een pagina en zijn plaats in de documentwortel niet de inhoudspad aanpast.

In het onderstaande voorbeeld wordt elk verzoek om validatie aan een bestand genoteerd.

```xml
/invalidateHandler "/opt/dispatcher/scripts/invalidate.sh"
```

#### voorbeeldscript voor validatiehandlers {#sample-invalidation-handler-script}

```shell
#!/bin/bash

printf "%-15s: %s %s" $1 $2 $3>> /opt/dispatcher/logs/invalidate.log
```

### De clients beperken die de cache kunnen leegmaken {#limiting-the-clients-that-can-flush-the-cache}

Het /allowedClients bezit bepaalt specifieke cliënten die het geheime voorgeheugen mogen leegmaken. De globbende patronen worden aangepast aan IP.

In het volgende voorbeeld:

1. ontzegt toegang tot om het even welke cliënt
1. verleent uitdrukkelijk toegang tot localhost

```xml
/allowedClients
  {
   /0001 { /glob "*.*.*.*"  /type "deny" }
   /0002 { /glob "127.0.0.1" /type "allow" }
  }
```

Zie Patronen [ontwerpen voor globale eigenschappen](#designing-patterns-for-glob-properties)voor meer informatie over eigenschappen van glob.

>[!CAUTION]
>
>Het wordt geadviseerd dat u /allowedClients bepaalt.
>
>Als dit niet wordt gedaan, kan om het even welke cliënt een vraag uitgeven om het geheime voorgeheugen te ontruimen; als dit herhaaldelijk wordt gedaan kan het de plaatsprestaties ernstig beïnvloeden.

### URL-parameters worden genegeerd {#ignoring-url-parameters}

In de `ignoreUrlParams` sectie wordt gedefinieerd welke URL-parameters worden genegeerd wanneer wordt bepaald of een pagina in cache wordt geplaatst of via cache wordt geleverd:

* Wanneer een aanvraag-URL parameters bevat die allemaal worden genegeerd, wordt de pagina in de cache geplaatst.
* Wanneer een aanvraag-URL een of meer parameters bevat die niet worden genegeerd, wordt de pagina niet in de cache opgeslagen.

Wanneer een parameter voor een pagina wordt genegeerd, wordt de pagina in de cache geplaatst de eerste keer dat de pagina wordt aangevraagd. Volgende aanvragen voor de pagina worden op de pagina in de cache geplaatst, ongeacht de waarde van de parameter in het verzoek.

Om te specificeren welke parameters worden genegeerd, voeg glob regels aan het `ignoreUrlParams` bezit toe:

* Als u een parameter wilt negeren, maakt u een eigenschap glob die de parameter toestaat.
* Als u wilt voorkomen dat de pagina in de cache wordt geplaatst, maakt u een glob-eigenschap die de parameter weigert.

In het volgende voorbeeld wordt door Dispatcher de parameter &quot;q&quot; genegeerd, zodat aanvraag-URL&#39;s die de parameter q bevatten, in de cache worden opgeslagen:

```xml
/ignoreUrlParams
{
    /0001 { /glob "*" /type "deny" }
    /0002 { /glob "q" /type "allow" }
}
```

Met behulp van de `ignoreUrlParams` voorbeeldwaarde zorgt de volgende HTTP-aanvraag ervoor dat de pagina in de cache wordt geplaatst omdat de `q` parameter wordt genegeerd:

```xml
GET /mypage.html?q=5
```

Met behulp van de voorbeeldwaarde leidt de volgende HTTP-aanvraag ertoe dat de pagina `ignoreUrlParams` niet **in de cache wordt geplaatst omdat de** `p` parameter niet wordt genegeerd:

```xml
GET /mypage.html?q=5&p=4
```

Zie Patronen [ontwerpen voor globale eigenschappen](#designing-patterns-for-glob-properties)voor meer informatie over eigenschappen van glob.

### HTTP-responsheaders in cache plaatsen {#caching-http-response-headers}

>[!NOTE]
>
>Deze functie is beschikbaar met versie **4.1.11** van de Dispatcher.

Met de `/headers` eigenschap kunt u de HTTP-headertypen definiëren die door de Dispatcher in de cache worden opgeslagen. Op het eerste verzoek aan een middel uncached, worden alle kopballen die één van de gevormde waarden (zie de configuratiemonster hieronder) aanpassen opgeslagen in een afzonderlijk dossier, naast het geheim voorgeheugendossier. Bij verdere verzoeken aan het caching middel, worden de opgeslagen kopballen toegevoegd aan de reactie.

Hieronder ziet u een voorbeeld van de standaardconfiguratie:

```xml
/cache {
  ...
  /headers {
    "Cache-Control"
    "Content-Disposition"
    "Content-Type"
    "Expires"
    "Last-Modified"
    "X-Content-Type-Options"
    "Last-Modified"
  }
}
```

>[!NOTE]
>
>Houd er ook rekening mee dat globbingtekens niet zijn toegestaan. Zie Patronen [ontwerpen voor globale eigenschappen](#designing-patterns-for-glob-properties)voor meer informatie.

>[!NOTE]
>
>Als u Dispatcher nodig hebt om de eBay-antwoordheaders van AEM op te slaan en te leveren, gaat u als volgt te werk:
>
>* Voeg de koptekstnaam toe aan de `/cache/headers`sectie.
>* Voeg de volgende [Apache-instructie](https://httpd.apache.org/docs/2.4/mod/core.html#fileetag) toe aan het gedeelte Dispatcher dat betrekking heeft op:
>



```xml
FileETag none
```

### Machtigingen voor cachebestanden van Dispatcher {#dispatcher-cache-file-permissions}

De `mode` eigenschap geeft aan welke bestandsmachtigingen worden toegepast op nieuwe mappen en bestanden in de cache. Deze instelling wordt beperkt door het aanroepingsproces `umask` . Dit is een octaal getal dat wordt samengesteld uit de som van een of meer van de volgende waarden:

* 0400 Lezen door eigenaar toestaan.
* 0200 Schrijven door eigenaar toestaan.
* 0100 Laat de eigenaar in folders zoeken.
* 0040 Lezen door groepsleden toestaan.
* 0020 Schrijven door groepsleden toestaan.
* 0010 Groepsleden toestaan te zoeken in de directory.
* 0004 Lezen door anderen toestaan.
* 0002 Schrijven door anderen toestaan.
* 0001 Anderen mogen in de map zoeken.

De standaardwaarde is 0755. Hiermee kan de eigenaar lezen, schrijven of zoeken en de groep en anderen lezen of zoeken.

### Startbestand met Throttling .stat aanraken {#throttling-stat-file-touching}

Met de standaardeigenschap maakt elke activering alle `/invalidate` bestanden ongeldig (wanneer het pad ervan overeenkomt met de `.html` `/invalidate` sectie). Op een website met aanzienlijk verkeer zullen meerdere, daaropvolgende activeringen de CPU-belasting op de achtergrond verhogen. In een dergelijk scenario is het wenselijk om het aanraken van bestanden te &quot;vertragen&quot; `.stat` om de website ontvankelijk te houden. U kunt dit doen door het `/gracePeriod` bezit te gebruiken.

De `/gracePeriod` eigenschap definieert het aantal seconden dat een standaard, automatisch ongeldig gemaakte resource mogelijk nog steeds uit de cache wordt aangeboden na de laatste activering. De eigenschap kan worden gebruikt in een installatie waarbij een batch activeringen anders de gehele cache herhaaldelijk ongeldig zouden maken. De aanbevolen waarde is 2 seconden.

Lees ook de bovenstaande `/invalidate` en `/statfileslevel`secties voor meer informatie.

### Het vormen tijd-Gebaseerde Invalidatie van het Geheime voorgeheugen - /enableTTL {#configuring-time-based-cache-invalidation-enablettl}

Als deze is ingesteld, evalueert de `/enableTTL` eigenschap de antwoordheaders vanaf de backend. Als deze een `Cache-Control` max.-age of `Expires` -datum bevatten, wordt een leeg hulpbestand gemaakt naast het cachebestand, met wijzigingstijd die gelijk is aan de vervaldatum. Wanneer het cachebestand na de wijzigingstijd wordt opgevraagd, wordt het automatisch opnieuw opgevraagd vanaf de achtergrond.

>[!NOTE]
>
>Deze functie is beschikbaar in versie **4.1.11** of hoger van de Dispatcher.

## Load Balancing configureren - /statistics {#configuring-load-balancing-statistics}

In de `/statistics` sectie worden categorieën bestanden gedefinieerd waarvoor Dispatcher de responsiviteit van elke rendermethode scoort. Dispatcher gebruikt de scores om te bepalen welke renderen om een aanvraag te verzenden.

Elke categorie die u maakt, definieert een globaal patroon. Dispatcher vergelijkt de URI van de aangevraagde inhoud met deze patronen om de categorie van de gevraagde inhoud te bepalen:

* De volgorde van de categorieën bepaalt de volgorde waarin ze worden vergeleken met de URI.
* Het eerste categoriepatroon dat overeenkomt met de URI is de categorie van het bestand. Er worden niet meer categoriepatronen geëvalueerd.

Dispatcher ondersteunt maximaal 8 statistische categorieën. Als u meer dan 8 categorieën definieert, worden alleen de eerste 8 gebruikt.

**Selectie renderen**

Telkens wanneer Dispatcher een teruggegeven pagina vereist, gebruikt het het volgende algoritme om terug te selecteren:

1. Als de aanvraag de rendernaam in een `renderid` cookie bevat, gebruikt Dispatcher die rendering.
1. Als het verzoek geen `renderid` koekje omvat, vergelijkt de Dispatcher de teruggeven statistieken:

   1. Dispatcher bepaalt de categorie van de aanvraag-URI.
   1. Dispatcher bepaalt welke rendermethode de laagste responsscore voor die categorie heeft en selecteert die rendermethode.

1. Als er nog geen renderbewerking is geselecteerd, gebruikt u de eerste renderbewerking in de lijst.

De score voor de rendercategorie is gebaseerd op vorige responstijden en eerdere mislukte en succesvolle verbindingen die Dispatcher probeert uit te voeren. Voor elke poging, wordt de score voor de categorie van gevraagde URI bijgewerkt.

>[!NOTE]
>
>Als u geen taakverdeling gebruikt, kunt u deze sectie weglaten.

### Categorieën statistieken definiëren {#defining-statistics-categories}

Definieer een categorie voor elk type document waarvoor u statistieken wilt bijhouden voor de renderselectie. De /statistics sectie bevat een /category sectie. Om een categorie te bepalen, voeg een lijn onder de /Categoriesectie toe die het volgende formaat heeft:

`/name { /glob "pattern"}`

De categorie `name` moet uniek zijn voor het bedrijf. Het `pattern` wordt beschreven in de sectie [Ontwerppatronen voor globale eigenschappen](#designing-patterns-for-glob-properties) .

Om de categorie van URI te bepalen, vergelijkt de Dispatcher URI met elk categoriepatroon tot een gelijke wordt gevonden. Dispatcher begint met de eerste categorie in de lijst en behoudt de volgorde van de punten. Plaats daarom eerst categorieën met specifiekere patronen.

Met de standaarddispatcher.any-bestanden definieert u bijvoorbeeld een HTML-categorie en een andere categorie. De categorie HTML is specifieker en wordt daarom als eerste weergegeven:

```xml
/statistics
  {
  /categories
    {
      /html { /glob "*.html" }
      /others  { /glob "*" }
    }
  }
```

In het volgende voorbeeld wordt ook een categorie voor zoekpagina&#39;s opgenomen:

```xml
/statistics
  {
  /categories
    {
      /search { /glob "*search.html" }
      /html { /glob "*.html" }
      /others  { /glob "*" }
    }
  }
```

### Weergeven van serveronbeschikbaarheid in Dispatcher-statistieken {#reflecting-server-unavailability-in-dispatcher-statistics}

Het `/unavailablePenalty` bezit plaatst de tijd (in tiende van een seconde) die op teruggeeft statistieken wordt toegepast wanneer een verbinding aan teruggeeft ontbreekt. De verzender voegt de tijd aan de statistiekcategorie toe die gevraagde URI aanpast.

Bijvoorbeeld, wordt de sanctie toegepast wanneer de verbinding TCP/IP aan aangewezen hostname/haven niet kan worden gevestigd, of omdat AEM niet loopt (en niet luistert) of wegens een netwerk-gerelateerd probleem.

De `/unavailablePenalty` eigenschap is een rechtstreeks onderliggend element van de `/farm` sectie (een neveneffect van de `/statistics` sectie).

Wanneer geen `/unavailablePenalty` eigenschap bestaat, wordt de waarde &quot;1&quot; gebruikt.

```xml
/unavailablePenalty "1"
```

## Identificeer een Vaste Omslag van de Verbinding - /stickyConnectionsFor {#identifying-a-sticky-connection-folder-stickyconnectionsfor}

De `/stickyConnectionsFor` eigenschap definieert een map die plakke documenten bevat. Dit wordt geopend via de URL. Dispatcher verzendt alle aanvragen, van één gebruiker, die zich in deze map bevinden naar dezelfde renderinstantie. De stevige verbindingen zorgen ervoor dat de zittingsgegevens voor alle documenten aanwezig en verenigbaar zijn. Dit mechanisme gebruikt het `renderid` cookie.

In het volgende voorbeeld wordt een kleverige verbinding met de map /products gedefinieerd:

```xml
/stickyConnectionsFor "/products"
```

Wanneer een pagina uit inhoud van verscheidene inhoudsknopen bestaat, omvat het `/paths` bezit dat van de wegen aan de inhoud een lijst maakt. Een pagina bevat bijvoorbeeld inhoud van `/content/image`, `/content/video`en `/var/files/pdfs`. De volgende configuratie laat kleverige verbindingen voor alle inhoud op de pagina toe:

```xml
/stickyConnections {
  /paths {
    "/content/image"
    "/content/video"
    "/var/files/pdfs"
  }
}
```

### httpOnly {#httponly}

Als kleverige verbindingen zijn ingeschakeld, stelt de verzendingsmodule de `renderid` cookie in. Dit cookie heeft niet de `httponly` vlag, die moet worden toegevoegd om de beveiliging te verbeteren. U kunt dit doen door het `httpOnly` bezit in de `/stickyConnections` knoop van een `dispatcher.any` configuratiedossier te plaatsen. De waarde van de eigenschap (0 of 1) definieert of het `renderid` cookie het `HttpOnly` kenmerk heeft dat wordt toegevoegd. De standaardwaarde is 0, wat betekent dat het kenmerk niet wordt toegevoegd.

Lees voor meer informatie over de `httponly` markering [deze pagina](https://www.owasp.org/index.php/HttpOnly).

### beveiligen {#secure}

Als kleverige verbindingen zijn ingeschakeld, stelt de verzendingsmodule de `renderid` cookie in. Dit cookie heeft niet de **beveiligde** vlag, die moet worden toegevoegd om de beveiliging te verbeteren. U kunt dit doen door het `secure` bezit in de `/stickyConnections` knoop van een `dispatcher.any` configuratiedossier te plaatsen. De waarde van de eigenschap (0 of 1) definieert of het `renderid` cookie het `secure` kenmerk heeft dat wordt toegevoegd. De standaardwaarde is 0, wat betekent dat het attribuut wordt toegevoegd **als** het inkomende verzoek veilig is. Als de waarde aan 1 wordt geplaatst dan zal de veilige vlag worden toegevoegd ongeacht of het inkomende verzoek veilig of niet is.

## Renderfouten afhandelen {#handling-render-connection-errors}

Vorm het gedrag van de Verzender wanneer teruggeeft de server een fout 500 terugkeert, of niet beschikbaar is.

### Een pagina voor een health check opgeven {#specifying-a-health-check-page}

Gebruik de `/health_check` eigenschap om een URL op te geven die wordt gecontroleerd wanneer een 500-statuscode plaatsvindt. Als deze pagina ook een 500 statuscode terugkeert wordt de instantie beschouwd als niet beschikbaar en een configureerbare tijd ( `/unavailablePenalty`) wordt toegepast op teruggeven alvorens opnieuw te proberen.

```xml
/health_check
  {
  # Page gets contacted when an instance returns a 500
  /url "/health_check.html"
  }
```

### De vertraging voor opnieuw proberen van pagina opgeven {#specifying-the-page-retry-delay}

De eigenschap / `retryDelay` stelt de tijd (in seconden) in die de verzender wacht tussen de ronden van verbindingspogingen met de farm. Voor elke ronde, is het maximumaantal tijden de Verzender probeert een verbinding aan terug te geven het aantal teruggeeft in het landbouwbedrijf.

Dispatcher gebruikt een waarde van `"1"` als `/retryDelay` niet expliciet is gedefinieerd. De standaardwaarde is in de meeste gevallen geschikt.

```xml
/retryDelay "1"
```

### Het aantal pogingen configureren {#configuring-the-number-of-retries}

De `/numberOfRetries` eigenschap stelt het maximum aantal ronde verbindingspogingen in dat Dispatcher met de renderers uitvoert. Als Dispatcher na dit aantal keren geen verbinding kan maken met een renderbewerking, retourneert Dispatcher een mislukte reactie.

Voor elke ronde, is het maximumaantal tijden de Verzender probeert een verbinding aan terug te geven het aantal teruggeeft in het landbouwbedrijf. Daarom is het maximumaantal tijden dat de Verzender een verbinding probeert ( `/numberOfRetries`) x (het aantal teruggeeft).

Wanneer de waarde niet expliciet wordt gedefinieerd, is de standaardwaarde **5**.

```xml
/numberOfRetries "5"
```

### Het gebruiken van het mechanisme Failover {#using-the-failover-mechanism}

Laat het failovermechanisme op uw landbouwbedrijf van de Verzender toe om verzoeken aan verschillende terug te zenden wanneer het originele verzoek ontbreekt. Wanneer failover wordt toegelaten, heeft de Dispatcher het volgende gedrag:

* Wanneer een verzoek aan teruggeeft HTTP status 503 (UNAVAILABLE) terugkeert, verzendt de Dispatcher het verzoek naar verschillend teruggeven.
* Wanneer een verzoek aan teruggeeft de status van HTTP 50x (buiten 503) terugkeert, verzendt de Ontvanger een verzoek voor de pagina die voor het `health_check` bezit wordt gevormd.

   * Als de gezondheidscontrole 500 (INTERNAL_SERVER_ERROR) terugkeert, verzendt de Dispatcher het originele verzoek naar verschillende teruggeven.
   * Als de verbindingscontrole HTTP status 200 terugkeert, keert de Dispatcher de aanvankelijke fout van HTTP 500 aan de cliënt terug.

Om failover toe te laten, voeg de volgende lijn aan het landbouwbedrijf (of de website) toe:

```xml
/failover "1" 
```

>[!NOTE]
>
>Om HTTP- verzoeken opnieuw te proberen die een lichaam bevatten, verzendt de Dispatcher een `Expect: 100-continue` verzoekkopbal naar terug alvorens de daadwerkelijke inhoud te spoolen. CQ 5.5 met CQSE beantwoordt dan onmiddellijk met of 100 (CONTINUE) of een foutencode. Andere servletcontainers zouden dit ook moeten steunen.

## Onderbrekingsfouten negeren - /ignoreEINTR {#ignoring-interruption-errors-ignoreeintr}

>[!CAUTION]
>
>Deze optie is gewoonlijk niet nodig. U hoeft dit alleen te gebruiken wanneer u de volgende logberichten ziet:
>
>`Error while reading response: Interrupted system call`

Om het even welk systeem georiënteerde systeemvraag kan worden onderbroken `EINTR` als het voorwerp van de systeemvraag op een ver systeem wordt gevestigd dat via NFS wordt betreden. Of deze systeemvraag uit kan tijd of worden onderbroken is gebaseerd op hoe het onderliggende dossiersysteem op de lokale machine werd opgezet.

Gebruik de /ignoreEINTR parameter als uw instantie zulk een configuratie heeft en het logboek het volgende bericht bevat:

`Error while reading response: Interrupted system call`

Intern leest Dispatcher de reactie van de externe server (d.w.z. AEM) met behulp van een lus die kan worden weergegeven als:

`while (response not finished) {  
read more data  
}`

Dergelijke berichten kunnen worden geproduceerd wanneer het in &quot; `EINTR` `read more data`&quot;sectie voorkomt en door de ontvangst van een signaal veroorzaakt alvorens om het even welke gegevens werd ontvangen.

Als u dergelijke onderbrekingen wilt negeren, kunt u de volgende parameter toevoegen aan `dispatcher.any` (voor `/farms`):

`/ignoreEINTR "1"`

Als deze instelling wordt ingesteld, blijft Dispatcher proberen gegevens te lezen totdat de volledige reactie wordt gelezen. `/ignoreEINTR` `"1"` De standaardwaarde is 0 en activeert de optie.

## Patronen ontwerpen voor globale eigenschappen {#designing-patterns-for-glob-properties}

Verschillende secties in het Dispatcher-configuratiebestand gebruiken `glob` eigenschappen als selectiecriteria voor clientaanvragen. De waarden van glob eigenschappen zijn patronen die de Verzender met een aspect van het verzoek, zoals de weg van het gevraagde middel, of het IP adres van de cliënt vergelijkt. Bijvoorbeeld, gebruiken de punten in de `/filter` sectie globpatronen om de wegen van de pagina&#39;s te identificeren die de Verzender op of verwerpt.

De glob-waarden kunnen jokertekens en alfanumerieke tekens bevatten om het patroon te definiëren.

| Jokerteken | Beschrijving | Voorbeelden |
|--- |--- |--- |
| `*` | Komt overeen met nul of meer aaneengesloten instanties van een willekeurig teken in de tekenreeks. Het uiteindelijke teken van de overeenkomst wordt bepaald door een van de volgende situaties: Een <br/>teken in de tekenreeks komt overeen met het volgende teken in het patroon en het patroonteken heeft de volgende kenmerken:<br/><ul><li>Geen *</li><li>Niet een?</li><li>Een letterlijk teken (inclusief een spatie) of een tekenklasse.</li><li>Het einde van het patroon is bereikt.</li></ul>Binnen een tekenklasse wordt het teken letterlijk geïnterpreteerd. | `*/geo*` Komt overeen met elke pagina onder het `/content/geometrixx` knooppunt en het `/content/geometrixx-outdoors` knooppunt. De volgende HTTP-aanvragen komen overeen met het globale patroon: <br/><ul><li>`"GET /content/geometrixx/en.html"`</li><li>`"GET /content/geometrixx-outdoors/en.html"` </li></ul><br/> `*outdoors/*` Komt <br/>overeen met elke pagina onder het `/content/geometrixx-outdoors` knooppunt. De volgende HTTP-aanvraag komt bijvoorbeeld overeen met het glob-patroon: <br/><ul><li>`"GET /content/geometrixx-outdoors/en.html"`</li></ul> |
| `?` | Komt overeen met elk willekeurig enkel teken. Gebruik externe tekenklassen. Binnen een tekenklasse wordt dit teken letterlijk geïnterpreteerd. | `*outdoors/??/*`<br/> Komt overeen met de pagina&#39;s voor elke taal in de geometrixx-outdoorsite. De volgende HTTP-aanvraag komt bijvoorbeeld overeen met het glob-patroon: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>Het volgende verzoek komt niet overeen met het glob-patroon: <br/><ul><li>&quot;GET /content/geometrixx-outdoors/en.html&quot;</li></ul> |
| `[ and ]` | Hiermee wordt het begin en einde van een tekenklasse gedemonstreerd. Tekenklassen kunnen een of meer tekenbereiken en enkele tekens bevatten.<br/>Een overeenkomst treedt op als het doelteken overeenkomt met een van de tekens in de tekenklasse of binnen een gedefinieerd bereik.<br/>Als de accolade sluiten niet is opgenomen, resulteert het patroon niet in overeenkomende waarden. | `*[o]men.html*`<br/> Komt overeen met de volgende HTTP-aanvraag:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>Komt niet overeen met de volgende HTTP-aanvraag:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/> `*[o/]men.html*` Komt <br/>overeen met de volgende HTTP-aanvragen: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `-` | Geeft een tekenbereik aan. Voor gebruik in tekenklassen.  Buiten een tekenklasse wordt dit teken letterlijk geïnterpreteerd. | `*[m-p]men.html*` Komt overeen met de volgende HTTP-aanvraag: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul>Komt niet overeen met de volgende HTTP-aanvraag:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `!` | Hiermee wordt het volgende teken of de volgende tekenklasse genegeerd. Alleen gebruiken voor negerende tekens en tekenbereiken binnen tekenklassen. Gelijk aan de `^ wildcard`. <br/>Buiten een tekenklasse wordt dit teken letterlijk geïnterpreteerd. | `*[!o]men.html*`<br/> Komt overeen met de volgende HTTP-aanvraag: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>Komt niet overeen met de volgende HTTP-aanvraag: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>`*[!o!/]men.html*`<br/> Komt niet overeen met de volgende HTTP-aanvraag:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"` or `"GET /content/geometrixx-outdoors/en/men. html"`</li></ul> |
| `^` | Hiermee wordt het volgende teken- of tekenbereik genegeerd. Wordt gebruikt voor het negeren van alleen tekens en tekenbereiken binnen tekenklassen. Gelijk aan het jokerteken. `!` <br/>Buiten een tekenklasse wordt dit teken letterlijk geïnterpreteerd. | De voorbeelden van het `!` jokerteken zijn van toepassing, waarbij de `!` tekens in de voorbeeldpatronen worden vervangen door `^` tekens. |


<!--- need to troubleshoot table

The following table describes the wildcard characters.

<table border="1" cellpadding="1" cellspacing="0" width="100%"> 
 <tbody> 
  <tr> 
   <th>Wildcard character</th> 
   <th>Description</th> 
   <th>Examples</th> 
  </tr> 
  <tr> 
   <td>*</td> 
   <td><p>Matches zero or more contiguous instances of any character in the string. The final character of the match is determined by either of the following situations:</p> 
    <ul> 
     <li>A character in the string matches the next character in the pattern, and the pattern character has the following characteristics: 
      <ul> 
       <li>Not a *</li> 
       <li>Not a ?</li> 
       <li>A literal character (including a space) or a character class.</li> 
      </ul> </li> 
     <li>The end of the pattern is reached.</li> 
    </ul> <p>Inside a character class, the character is interpreted literally.</p> </td> 
   <td><p>*/geo*</p> <p>Matches any page below the /content/geometrixx node and the /content/geometrixx-outdoors node. The following HTTP requests match the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx/en.html"</li> 
     <li>"GET /content/geometrixx-outdoors/en.html" </li> 
    </ul> <p>*outdoors/*</p> <p>Matches any page below the /content/geometrixx-outdoors node. For example, the following HTTP request matches the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en.html"</li> 
    </ul> </td> 
  </tr> 
  <tr> 
   <td>?</td> 
   <td><p>Matches any single character. Use outside character classes.</p> <p>Inside a character class, this character is interpreted literally.</p> </td> 
   <td><p>*outdoors/??/*</p> <p>Matches the pages for any language in the geometrixx-outdoors site. For example, the following HTTP request matches the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html"</li> 
    </ul> <p>The following request does not match the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en.html" </li> 
    </ul> </td> 
  </tr> 
  <tr> 
   <td>[ and ]</td> 
   <td><p>Demarks the beginning and end of a character class.</p> <p>Character classes can include one or more character ranges and single characters.</p> <p>A match occurs if the target character matches any of the characters in the character class, or within a defined range.</p> <p>If the closing bracket is not included, the pattern produces no matches.</p> <p></p> <p></p> <p></p> </td> 
   <td><p>*[o]men.html*</p> <p>Matches the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html"</li> 
    </ul> <p>Does not match the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html" </li> 
    </ul> <p>*[o/]men.html*</p> <p>Matches the following HTTP requests: </p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html" </li> 
     <li> "GET /content/geometrixx-outdoors/en/men.html" </li> 
    </ul> <p></p> </td> 
  </tr> 
  <tr> 
   <td>-</td> 
   <td><p>Denotes a range of characters. For use in character classes.<br /> </p> <p>Outside of a character class, this character is interpreted literally.</p> <p></p> </td> 
   <td><p>*[m-p]men.html*</p> <p>Matches the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html"</li> 
    </ul> <p>Does not match the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html"</li> 
    </ul> <p></p> </td> 
  </tr> 
  <tr> 
   <td>!</td> 
   <td><p>Negates the character or character class that follows. Use only for negating characters and character ranges inside character classes. Equivalent to the ^ wildcard.</p> <p>Outside of a character class, this character is interpreted literally.</p> <p></p> </td> 
   <td><p>*[!o]men.html*</p> <p>Matches the following HTTP request: </p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html"</li> 
    </ul> <p>Does not match the following HTTP request</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html" </li> 
    </ul> <p>*[!o!/]men.html*</p> <p>Does not match the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html" or "GET /content/geometrixx-outdoors/en/men. html" </li> 
    </ul> </td> 
  </tr> 
  <tr> 
   <td>^</td> 
   <td><p>Negates the character or character range that follows. Use for negating only characters and character ranges inside character classes. Equivalent to the ! wildcard character.</p> <p>Outside of a character class, this charcter is interpreted literally.</p> </td> 
   <td>The examples for the ! wildcard character apply, substituting the ! characters in the example patterns with ^ characters.</td> 
  </tr> 
 </tbody> 
</table>
-->

## Logboekregistratie {#logging}

In de webserverconfiguratie kunt u instellen:

* De locatie van het logboekbestand van de Dispatcher.
* Het logniveau.

Raadpleeg de documentatie bij de webserver en het Lees mij-bestand van de Dispatcher-instantie voor meer informatie.

**Apache geroteerd / Logbestanden met pijplijnen**

Als u een **Apache** -webserver gebruikt, kunt u de standaardfunctionaliteit gebruiken voor geroteerde en/of gepipetteerde logbestanden. Bijvoorbeeld met behulp van stammen met buizen:

`DispatcherLog "| /usr/apache/bin/rotatelogs logs/dispatcher.log%Y%m%d 604800"`

Dit wordt automatisch geroteerd:

* het logbestand van de verzender; met een tijdstempel in de extensie (logs/dispatcher.log%Y%m%d).
* wekelijks (60 x 60 x 24 x 7 = 604800 seconden).

Raadpleeg de documentatie bij de Apache-webserver over logrotatie en pijpleidingen. bijvoorbeeld [Apache 2.4](https://httpd.apache.org/docs/2.4/logs.html).

>[!NOTE]
>
>Op installatie is het standaardlogboekniveau hoog (d.w.z. niveau 3 = zuivert), zodat de Dispatcher alle fouten en waarschuwingen registreert. Dit is zeer nuttig in de eerste stadia.
>
>Nochtans, vereist dit extra middelen, zodat wanneer de Dispatcher regelmatig *volgens uw vereisten* werkt, kunt (zou moeten) u het logboekniveau verminderen.

### Trackregistratie {#trace-logging}

Naast andere verbeteringen voor de Dispatcher, introduceert versie 4.2.0 ook Tracks Logging.

Dit is een hoger niveau dan Debug registreren, die extra informatie in de logboeken tonen. Er wordt logbestanden toegevoegd voor:

* De waarden van de doorgestuurde kopteksten;
* De regel die wordt toegepast voor een bepaalde handeling.

U kunt de Registratie van het Spoor toelaten door het logboekniveau aan `4` in uw Webserver te plaatsen.

Hieronder ziet u een voorbeeld van logboeken waarin overtrekken is ingeschakeld:

```xml
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Host] = "localhost:8443"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[User-Agent] = "curl/7.43.0"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Accept] = "*/*"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL-Client-Cert] = "(null)"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Via] = "1.1 localhost:8443 (dispatcher)"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-For] = "::1"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL] = "on"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL-Cipher] = "DHE-RSA-AES256-SHA"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL-Session-ID] = "ba931f5e4925c2dde572d766fdd436375e15a0fd24577b91f4a4d51232a934ae"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-Port] = "8443"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Server-Agent] = "Communique-Dispatcher"
```

En een gebeurtenis die wordt geregistreerd wanneer een dossier dat een het blokkeren regel aanpast wordt gevraagd:

```xml
[Thu Mar 03 14:42:45 2016] [T] [11831] 'GET /content.infinity.json HTTP/1.1' was blocked because of /0082
```

## Basisbewerking bevestigen {#confirming-basic-operation}

U kunt de volgende stappen uitvoeren om de basisbewerking en interactie van de webserver, de Dispatcher- en de AEM-instantie te bevestigen:

1. Stel de `loglevel` optie in op `3`.

1. Start de webserver; hiermee begint ook de Dispatcher.
1. Start de AEM-instantie.
1. Controleer het logboek en de foutendossiers voor uw Webserver en de Verzender.\
   Afhankelijk van uw webserver kunt u berichten weergeven zoals:\
   `[Thu May 30 05:16:36 2002] [notice] Apache/2.0.50 (Unix) configured`\
   and:\
   `[Fri Jan 19 17:22:16 2001] [I] [19096] Dispatcher initialized (build XXXX)`

1. Surf op de website via de webserver. Bevestig dat de inhoud naar wens wordt weergegeven.\
   Bijvoorbeeld op een lokale installatie waar AEM op haven `4502` en de Webserver op `80` toegang tot de console van Websites loopt gebruikend allebei:\
   ` https://localhost:4502/libs/wcm/core/content/siteadmin.html  
https://localhost:80/libs/wcm/core/content/siteadmin.html  
`De resultaten moeten identiek zijn. Bevestig toegang tot andere pagina&#39;s met het zelfde mechanisme.

1. Controleer of de cachemap wordt gevuld.
1. Activeer een pagina om te controleren of de cache correct wordt leeggemaakt.
1. Als alles correct werkt, kunt u het `loglevel` tot `0`.

## Meerdere verzenders gebruiken {#using-multiple-dispatchers}

In complexe instellingen kunt u meerdere verzenders gebruiken. U kunt bijvoorbeeld het volgende gebruiken:

* één verzender om een website op het Intranet te publiceren
* een tweede verzender, onder een ander adres en met verschillende beveiligingsinstellingen, om dezelfde inhoud op internet te publiceren.

In een dergelijk geval, zorg ervoor dat elk verzoek door slechts één Dispatcher gaat. Een Dispatcher behandelt geen verzoeken die afkomstig zijn van een andere Dispatcher. Zorg er daarom voor dat beide verzenders de AEM-website rechtstreeks openen.

## Foutopsporing {#debugging}

Wanneer het toevoegen van de kopbal `X-Dispatcher-Info` aan een verzoek, beantwoordt de Dispatcher of het doel in het voorgeheugen onder was gebracht, van cacheable of niet in het cachegeheugen was teruggekeerd. De responsheader `X-Cache-Info` bevat deze informatie in leesbare vorm. U kunt deze antwoordkopballen gebruiken om kwesties te zuiveren die reacties impliceren door Dispatcher in het voorgeheugen worden opgenomen.

Deze functionaliteit wordt niet toegelaten door gebrek, zodat moet het landbouwbedrijf de volgende ingang bevatten om de reactiekop `X-Cache-Info` te omvatten:

```xml
/info "1"
```

Bijvoorbeeld:

```xml
/farm
{
    /mywebsite
    {
        # Include X-Cache-Info response header if X-Dispatcher-Info is in request header
        /info "1"
    }
}
```

De `X-Dispatcher-Info` koptekst heeft ook geen waarde nodig, maar als u `curl` voor testen gebruikt, moet u een waarde opgeven om de koptekst te verzenden, zoals:

```xml
curl -v -H "X-Dispatcher-Info: true" https://localhost/content/we-retail/us/en.html
```

Hieronder ziet u een lijst met de antwoordheaders die `X-Dispatcher-Info` worden geretourneerd:

* **caching**\
   Het doelbestand bevindt zich in de cache en de verzender heeft vastgesteld dat het geldig is om het te leveren.
* **caching**\
   Het doelbestand bevindt zich niet in de cache en de verzender heeft vastgesteld dat het geldig is om de uitvoer in cache te plaatsen en te leveren.
* **caching: Het statusbestand is recenter**. Het doelbestand bevindt zich in de cache, maar het wordt ongeldig gemaakt door een recentere statusbestand. De verzender verwijdert het doelbestand, maakt het opnieuw uit de uitvoer en levert het.
* **niet in cache geplaatst: geen documentwortel** De configuratie van het landbouwbedrijf bevat geen documentwortel (configuratieelement `cache.docroot`).
* **niet in cache geplaatst: pad naar cachebestand te lang**\
   Het doelbestand - de samenvoeging van het hoofdbestand van het document en het URL-bestand - overschrijdt de langst mogelijke bestandsnaam op het systeem.
* **niet in cache geplaatst: tijdelijk bestandspad te lang**\
   De sjabloon voor tijdelijke bestandsnamen overschrijdt de langst mogelijke bestandsnaam op het systeem. De verzender maakt eerst een tijdelijk bestand voordat het cachebestand daadwerkelijk wordt gemaakt of overschreven. De tijdelijke bestandsnaam is de naam van het doelbestand waaraan de tekens zijn `_YYYYXXXXXX` toegevoegd, waarbij de naam `Y` `X` en de naam worden vervangen om een unieke naam te maken.
* **niet in cache geplaatst: request-URL heeft geen extensie**\
   De aanvraag-URL heeft geen extensie of er is een pad dat volgt op de bestandsextensie, bijvoorbeeld: `/test.html/a/path`.
* **niet in cache geplaatst: request was not a GET or HEAD** The HTTP method is not a GET or a HEAD. De verzender gaat ervan uit dat de uitvoer dynamische gegevens bevat die niet in de cache moeten worden opgeslagen.
* **niet in cache geplaatst: request bevatte een queryreeks**\
   Het verzoek bevatte een queryreeks. De dispatcher veronderstelt dat de output van het gegeven vraagkoord afhangt en daarom niet geheim voorgeheugen plaatst.
* **niet in cache geplaatst: session manager niet geverifieerd**\
   Het geheime voorgeheugen van het landbouwbedrijf wordt geregeerd door een zittingsmanager (de configuratie bevat een `sessionmanagement` knoop) en het verzoek bevatte niet de aangewezen authentificatieinformatie.
* **niet in cache geplaatst: verzoek bevat vergunning**\
   Het landbouwbedrijf wordt niet toegestaan om output ( `allowAuthorized 0`) in het voorgeheugen onder te brengen en het verzoek bevat authentificatieinformatie.
* **niet in cache geplaatst: target is een directory**\
   Het doelbestand is een map. Dit zou aan één of andere conceptuele fout kunnen wijzen, waar een URL en wat sub-URL allebei cacheable output bevatten, bijvoorbeeld als een verzoek om eerst te `/test.html/a/file.ext` komen en cacheable output bevat, zal de verzender niet de output van een volgend verzoek aan `/test.html`. kunnen in het voorgeheugen onderbrengen.
* **niet in cache geplaatst: request-URL heeft een slash**\
   De aanvraag-URL heeft een slash.
* **niet in cache geplaatst: request-URL niet in cache-regels**\
   De het geheime voorgeheugenregels van het landbouwbedrijf ontkennen uitdrukkelijk caching de output van één of ander verzoek URL.
* **niet in cache geplaatst: controle van autorisatie toegang geweigerd**\
   De de vergunningscontrole van het landbouwbedrijf ontkende toegang tot het caching dossier.
* **niet in cache geplaatst: sessie is ongeldig**. Het cachegeheugen van de farm wordt beheerd door een sessiemanager (configuratie bevat een `sessionmanagement` knooppunt) en de sessie van de gebruiker is niet of niet langer geldig.
* **niet in cache geplaatst: reactie bevat`no_cache `**De externe server heeft een`Dispatcher: no_cache`header geretourneerd, waardoor de verzender de uitvoer niet in de cache kan plaatsen.
* **niet in cache geplaatst: lengte van responsinhoud is nul**. De lengte van de inhoud van de reactie is nul; de verzender maakt geen bestand met een lengte van nul.
