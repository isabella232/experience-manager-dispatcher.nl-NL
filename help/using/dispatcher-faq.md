---
title: Belangrijkste kwesties voor verzending
seo-title: Frequente problemen met AEM Dispatcher
description: Frequente problemen met AEM Dispatcher
seo-description: Meestvoorkomende problemen met Adobe AEM Dispatcher
translation-type: tm+mt
source-git-commit: eed7c3f77ec64f2e7c5cfff070ef96108886a059
workflow-type: tm+mt
source-wordcount: '1644'
ht-degree: 0%

---


# Veelgestelde vragen over AEM belangrijkste problemen met Verzender

![Dispatcher configureren](assets/CQDispatcher_workflow_v2.png)

## Inleiding

### Wat is de Dispatcher?

Dispatcher is een Adobe Experience Manager-programma voor het in cache plaatsen en/of taakverdeling waarmee u een snelle en dynamische webontwerpomgeving kunt realiseren. Voor caching, werkt de Dispatcher als deel van een server van HTTP, zoals Apache, met het doel om zoveel mogelijk van de statische website-inhoud op te slaan (of &quot;in cache plaatsen&quot;) en tot de lay-outmotor van de website zo weinig mogelijk toegang te hebben. In een ladings het in evenwicht brengen rol, verspreidt de Dispatcher gebruikersverzoeken (lading) over verschillende AEM instanties (teruggeeft).

Voor caching, gebruikt de module van de Verzender de capaciteit van de server van het Web om statische inhoud te dienen. De verzender plaatst de caching documenten in de documentwortel van de server van het Web.

### Hoe voert de Dispatcher caching uit?

Dispatcher gebruikt de mogelijkheid van de webserver om statische inhoud te leveren. De Dispatcher slaat cachedocumenten op in de hoofdmap van het document van de webserver. De Dispatcher beschikt over twee primaire methoden voor het bijwerken van de cacheinhoud wanneer wijzigingen in de website worden aangebracht.

* **Met Inhoud** bijwerken worden de gewijzigde pagina&#39;s verwijderd, evenals bestanden die er direct aan zijn gekoppeld.
* **De auto-** Invalidatie maakt automatisch die delen van het geheime voorgeheugen onbruikbaar die verouderd na een update kunnen zijn. Zo worden relevante pagina&#39;s bijvoorbeeld als verouderd gemarkeerd zonder dat er iets wordt verwijderd.

### Wat zijn de voordelen van taakverdeling?

Bij Load Balancing worden gebruikersaanvragen (load) over verschillende AEM instanties verdeeld. In de volgende lijst worden de voordelen voor taakverdeling beschreven:

* **Meer verwerkingskracht**: In de praktijk betekent dit dat de Dispatcher verzoeken om documenten deelt tussen verschillende AEM. Omdat elke instantie minder documenten te verwerken heeft, hebt u snellere reactietijden. Dispatcher houdt interne statistieken voor elke documentcategorie bij, zodat kan het lading schatten en de vragen efficiënt verspreiden.
* **Verhoogde failover-veilige dekking**: Als de verzender geen reacties van een instantie ontvangt, worden aanvragen automatisch doorgestuurd naar een van de andere instanties. Als een instantie dus niet beschikbaar is, is het enige effect een vertraging van de site, in verhouding tot de verloren computerkracht.

>[!NOTE]
>
>Voor meer details, zie [De pagina van het Overzicht van de Verzending](dispatcher.md)

## Installeren en configureren

### Waar kan ik de Dispatcher-module downloaden?

U kunt de nieuwste Dispatcher-module downloaden van de pagina [Opmerkingen bij de release van Dispatcher](release-notes.md).

### Hoe installeer ik de module Dispatcher?

Raadpleeg de pagina [Dispatcher installeren](dispatcher-install.md)

### Hoe vorm ik de module van de Verzender?

Zie [Het vormen Verzender](dispatcher-configuration.md) pagina.

### Hoe vorm ik de Dispatcher voor de auteursinstantie?

Zie [Dispatcher gebruiken met een Instantie van de Auteur](dispatcher.md#using-a-dispatcher-with-an-author-server) voor de gedetailleerde stappen.

### Hoe vorm ik de Dispatcher met veelvoudige domeinen?

U kunt CQ Dispatcher met veelvoudige domeinen vormen, op voorwaarde dat de domeinen aan de volgende voorwaarden voldoen:

* De inhoud van het Web voor beide domeinen wordt opgeslagen in één enkele AEM bewaarplaats
* De bestanden in de Dispatcher-cache kunnen voor elk domein afzonderlijk ongeldig worden gemaakt

Lees [Dispatcher gebruiken met meerdere domeinen](dispatcher-domains.md) voor meer informatie.

### Hoe vorm ik de Dispatcher, zodat alle verzoeken van een gebruiker aan de zelfde Publish instantie worden verpletterd?

U kunt de [sticky connection](dispatcher-configuration.md#identifying-a-sticky-connection-folder-stickyconnectionsfor) eigenschap gebruiken, die ervoor zorgt dat alle documenten voor een gebruiker op de zelfde instantie van AEM worden verwerkt. Deze functie is belangrijk als u persoonlijke pagina&#39;s en sessiegegevens gebruikt. De gegevens worden opgeslagen op de instantie. Daarom moeten de verdere verzoeken van de zelfde gebruiker aan die instantie terugkeren of het gegeven wordt verloren.

Omdat de kleverige verbindingen de capaciteit van de Verzender beperken om verzoeken te optimaliseren, zou u deze benadering slechts wanneer noodzakelijk moeten gebruiken. U kunt de map opgeven die de &quot;plakke&quot; documenten bevat, zodat alle documenten in die map op hetzelfde exemplaar voor een gebruiker worden verwerkt.

### Kan ik kleverige verbindingen en caching in combinatie gebruiken?

Voor de meeste pagina&#39;s die kleverige verbindingen gebruiken, zou u caching moeten uitzetten. Anders wordt hetzelfde exemplaar van de pagina aan alle gebruikers weergegeven, ongeacht de inhoud van de sessie.

Voor sommige toepassingen kan het mogelijk zijn om zowel kleverige verbindingen als caching te gebruiken. Als u bijvoorbeeld een formulier weergeeft waarin gegevens naar een sessie worden geschreven, kunt u naast elkaar kleverige verbindingen gebruiken en in cache plaatsen.

### Kunnen een Dispatcher en een AEM Publish instantie op de zelfde fysieke machine verblijven?

Ja, als de machine voldoende krachtig is. Het wordt echter aanbevolen de Dispatcher en de AEM Publish-instantie op verschillende computers in te stellen.

Gewoonlijk, verblijft de Publish instantie binnen de firewall en Dispatcher in DMZ. Als u besluit om zowel de instantie Publiceren als de Verzender op de zelfde fysieke machine te hebben, zorg ervoor dat de firewallmontages directe toegang tot de Publish instantie van externe netwerken verbieden.

### Kan ik alleen bestanden met specifieke extensies in cache plaatsen?

Ja. Als u bijvoorbeeld alleen GIF-bestanden in de cache wilt plaatsen, geeft u *.gif op in de cachesectie van het configuratiebestand dispatcher.any.

### Hoe kan ik bestanden uit de cache verwijderen?

U kunt bestanden uit de cache verwijderen met behulp van een HTTP-aanvraag. Wanneer de HTTP-aanvraag wordt ontvangen, verwijdert Dispatcher de bestanden uit de cache. Dispatcher plaatst de bestanden alleen opnieuw in het cachegeheugen als het een clientverzoek voor de pagina ontvangt. Het verwijderen van cachebestanden op deze manier is geschikt voor websites die waarschijnlijk geen gelijktijdige aanvragen voor dezelfde pagina ontvangen.

De HTTP-aanvraag heeft de volgende syntaxis:

```
POST /dispatcher/invalidate.cache HTTP/1.1
CQ-Action: Activate
CQ-Handle: path-pattern
Content-Length: 0
```

Dispatcher verwijdert de cachebestanden en mappen met namen die overeenkomen met de waarde van de CQ-Handle-header. Een CQ-Handle van `/content/geomtrixx-outdoors/en` komt bijvoorbeeld overeen met de volgende items:

Alle bestanden (van een willekeurige bestandsextensie) die en in de map geometrixx-outdoor zijn genoemd
Elke map met de naam `_jcr_content` onder de map en (die, indien aanwezig, in de cache opgeslagen renderingen van subknooppunten van de pagina bevat)
De map en wordt alleen verwijderd als `CQ-Action` `Delete` of `Deactivate` is.

Zie [De Dispatcher Cache](page-invalidate.md) handmatig ongeldig maken voor meer informatie over dit onderwerp.

### Hoe implementeer ik toestemming-gevoelige caching?

Zie de pagina [Beveiligde inhoud in cache plaatsen](permissions-cache.md).

### Hoe kan ik communicatie tussen de instanties Dispatcher en CQ beveiligen?

Zie [Beveiligingschecklist voor Dispatcher](security-checklist.md) en de [AEM Beveiligingschecklist](https://helpx.adobe.com/experience-manager/6-4/sites/administering/using/security-checklist.html) pagina&#39;s.

### Dispatcher-probleem `jcr:content` gewijzigd in `jcr%3acontent`

**Vraag**: Onlangs hebben we een probleem op het niveau van de verzender gezien, waarbij een van de ajax-oproepen die gegevens van de CQ-gegevensopslagplaats kreeg,  `jcr:content`   `jcr%3acontent` erin zat en die tot een verkeerde resultaatset werden gecodeerd.

**Antwoord**: Gebruik een  `ResourceResolver.map()` methode om een &#39;Friendly&#39;-URL te gebruiken of te verzenden en krijg aanvragen van en om het cacheprobleem met Dispatcher op te lossen. De map()-methode codeert de `:`-dubbele punt om onderstrepingstekens te verkrijgen en de resolve()-methode decodeert deze terug naar de leesbare indeling van SLING JCR.U moet de map()-methode gebruiken om de URL te genereren die in de Ajax-aanroep wordt gebruikt.

Lees verder: [https://sling.apache.org/documentation/the-sling-engine/mappings-for-resource-resolution.html#namespace-mangling](https://sling.apache.org/documentation/the-sling-engine/mappings-for-resource-resolution.html#namespace-mangling)

## De Dispatcher verwijderen

### Hoe vorm ik de agenten van de Dispatcher op een Publish instantie?

Zie de pagina [Replication](https://helpx.adobe.com/content/help/en/experience-manager/6-4/sites/deploying/using/replication.html#ConfiguringyourReplicationAgents).

### Hoe los ik problemen op die Dispatcher opblazen?

[Raadpleeg dit ](https://helpx.adobe.com/content/help/en/experience-manager/kb/troubleshooting-dispatcher-flushing-issues.html) artikel over probleemoplossing waarin de volgende vragen worden beantwoord:

* Hoe zou ik een situatie moeten zuiveren waar geen inhoud wordt bewaard in het geheime voorgeheugen van de Verzender?
* Hoe kan ik fouten opsporen in een situatie waarin cachebestanden niet worden bijgewerkt?
* Hoe kan ik fouten opsporen in een situatie waarin niets met betrekking tot het leegmaken van Dispatcher werkt?

Als de verrichtingen van de Schrapping de Dispatcher veroorzaken om te spoelen, [gebruik de tijdelijke oplossing in deze communautaire blog post door Sensei Martin](https://mkalugin-cq.blogspot.in/2012/04/i-have-been-working-on-following.html).

### Hoe kan ik DAM-elementen uit de Dispatcher-cache verwijderen?

U kunt de functie voor ketenreplicatie gebruiken.  Met deze toegelaten eigenschap, verzendt de verzender flush agent een spoelverzoek wanneer een replicatie van auteur wordt ontvangen.

U schakelt dit als volgt in:

1. [Volg de stappen ](page-invalidate.md#invalidating-dispatcher-cache-from-a-publishing-instance) om bij publicatie spoelmiddelen te maken
1. Ga naar elk van die configuratie van die agent en op **Triggers** controle het **Op Receive** vakje.

## Overig

Hoe bepaalt de Dispatcher of een document up-to-date is?
Om te bepalen of een document bijgewerkt is, voert de Dispatcher de volgende handelingen uit:

Hiermee wordt gecontroleerd of het document automatisch wordt ongeldig gemaakt. Als dat niet het geval is, wordt het document als bijgewerkt beschouwd.
Als het document is geconfigureerd voor automatische validatie, controleert de Dispatcher of het ouder of nieuwer is dan de laatste beschikbare wijziging. Als deze ouder is, vraagt de Dispatcher de huidige versie van de AEM instantie en vervangt de versie in het geheime voorgeheugen.

### Hoe retourneert de Dispatcher documenten?

U kunt bepalen of de Verzender een document in het voorgeheugen onderbrengt door [de configuratie van de Verzender](dispatcher-configuration.md) dossier, `dispatcher.any` te gebruiken. De verzender controleert het verzoek aan de lijst van cacheable documenten. Als het document niet in deze lijst staat, wordt het document door de Dispatcher opgevraagd bij het AEM.

Met de eigenschap `/rules` bepaalt u welke documenten in de cache worden geplaatst op basis van het documentpad. Ongeacht de eigenschap `/rules` plaatst Dispatcher een document nooit in de cache in de volgende omstandigheden:

* Als de aanvraag-URI een vraagteken `(?)` bevat.
* Dit geeft meestal een dynamische pagina aan, zoals een zoekresultaat dat niet in de cache hoeft te worden opgeslagen.
* De bestandsextensie ontbreekt.
* De webserver heeft de extensie nodig om het documenttype (het MIME-type) te bepalen.
* De authentificatiekopbal wordt geplaatst (dit kan worden gevormd)
* Als de AEM instantie met de volgende kopballen antwoordt:
   * geen cache
   * no-store
   * moet opnieuw valideren

De Dispatcher slaat cachebestanden op de webserver op alsof ze deel uitmaken van een statische website. Als een gebruiker een document in de cache opvraagt, controleert de Dispatcher of het document bestaat in het bestandssysteem van de webserver. Als dit het geval is, retourneert de Dispatcher de documenten. Als dat niet het geval is, vraagt de Dispatcher het document op bij de AEM instantie.

>[!NOTE]
>
>De methoden GET of HEAD (voor de HTTP-header) kunnen door de Dispatcher in cache worden geplaatst. Zie de sectie [HTTP-responsheaders in cache plaatsen](dispatcher-configuration.md#caching-http-response-headers) voor aanvullende informatie over het in cache plaatsen van responsheaders.

### Kan ik veelvoudige Dispatchers in een opstelling uitvoeren?

Ja. In dergelijke gevallen moet u ervoor zorgen dat beide verzenders rechtstreeks toegang hebben tot de AEM website. Een Dispatcher kan geen verzoeken van een andere Dispatcher verwerken.

