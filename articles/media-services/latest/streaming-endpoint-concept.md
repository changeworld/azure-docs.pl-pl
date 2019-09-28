---
title: Punkty końcowe przesyłania strumieniowego (Źródło) w Azure Media Services | Microsoft Docs
description: W Azure Media Services punkt końcowy przesyłania strumieniowego (Origin) reprezentuje dynamiczne pakowanie i usługę przesyłania strumieniowego, która umożliwia dostarczanie zawartości bezpośrednio do aplikacji odtwarzacza klienta lub Content Delivery Network (CDN) w celu dalszej dystrybucji.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: cd1dc7b55060e8262b300022f5ffd1b4da5f7922
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350348"
---
# <a name="streaming-endpoints"></a>Punkty końcowe przesyłania strumieniowego 

W Microsoft Azure Media Services [punkt końcowy przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints) reprezentuje dynamiczny (just-in-Time) pakiet i pierwotną usługę, która umożliwia dostarczanie zawartości na żywo i na żądanie bezpośrednio do aplikacji odtwarzacza klienckiego przy użyciu jednego z popularnych multimediów przesyłanych strumieniowo protokoły (HLS lub KRESKa). Ponadto **punkt końcowy przesyłania strumieniowego** zapewnia dynamiczne (just-in-Time) szyfrowanie do wiodących w branży protokołów DRM.

Podczas tworzenia konta Media Services zostanie utworzony **domyślny** punkt końcowy przesyłania strumieniowego w stanie zatrzymanym. Nie można usunąć **domyślnego** punktu końcowego przesyłania strumieniowego. Dodatkowe punkty końcowe przesyłania strumieniowego można utworzyć w ramach konta (zobacz Przydziały [i ograniczenia](limits-quotas-constraints.md)). 

> [!NOTE]
> Aby rozpocząć przesyłanie strumieniowe filmów wideo, należy uruchomić **punkt końcowy przesyłania strumieniowego** , z którego chcesz przesłać strumieniowo wideo. 
>  
> Opłaty są naliczane tylko wtedy, gdy punkt końcowy przesyłania strumieniowego jest w stanie uruchomienia.

## <a name="naming-convention"></a>Konwencja nazewnictwa

Format nazwy hosta adresu URL przesyłania strumieniowego to: `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`, gdzie `servicename` = Nazwa punktu końcowego przesyłania strumieniowego lub nazwa zdarzenia na żywo. 

W przypadku korzystania z domyślnego punktu końcowego przesyłania strumieniowego `servicename` jest pomijany, więc adres URL to: `{accountname}-{regionname}.streaming.azure.net`. 

### <a name="limitations"></a>Ograniczenia

* Nazwa punktu końcowego przesyłania strumieniowego ma maksymalną wartość 24 znaków.
* Nazwa powinna być zgodna z tym wzorcem [wyrażenia regularnego](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) : `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

## <a name="types"></a>Typy  

Istnieją dwa typy **punktów końcowych przesyłania strumieniowego**: **Standard** (wersja zapoznawcza) i **Premium**. Typ jest definiowany przez liczbę jednostek skalowania (`scaleUnits`) przydzielonego dla punktu końcowego przesyłania strumieniowego. 

Tabela zawiera opis typów:  

|Type|Jednostki skalowania|Opis|
|--------|--------|--------|  
|**Standardowa**|0|Domyślny punkt końcowy przesyłania strumieniowego jest typem standardowym, można go zmienić na typ Premium przez `scaleUnits`dostosowanie.|
|**Premium**|>0|Wersja **Premium** Punkty końcowe przesyłania strumieniowego są odpowiednie dla zaawansowanych obciążeń i zapewniają dedykowaną i skalowalną przepustowość. Przechodzenie do typu **Premium** przez dostosowanie `scaleUnits` (jednostki przesyłania strumieniowego). `scaleUnits`Zapewnij dedykowaną pojemność wyjściową, którą można zakupić w przyrostach wynoszących 200 MB/s. W przypadku korzystania z typu **Premium** każda włączona jednostka zapewnia dodatkową przepustowość w aplikacji. |

> [!NOTE]
> W przypadku klientów chcących dostarczyć zawartość do dużych odbiorców internetowych zalecamy włączenie usługi CDN w punkcie końcowym przesyłania strumieniowego.

Aby uzyskać informacje o umowie SLA, zobacz [Cennik i Umowa SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Porównywanie typów przesyłania strumieniowego

Cecha|Standardowa (Standard)|Premium
---|---|---
Przepływność |Do 600 MB/s i może zapewnić znacznie wyższą skuteczną przepływność w przypadku użycia sieci CDN.|200 MB/s na jednostkę przesyłania strumieniowego (SU). W przypadku korzystania z sieci CDN można zapewnić znacznie wyższą skuteczną przepływność.
CDN|Azure CDN, Sieć CDN innej firmy lub brak sieci CDN.|Azure CDN, Sieć CDN innej firmy lub brak sieci CDN.
Opłaty są naliczane proporcjonalnie| Codziennie|Codziennie
Szyfrowanie dynamiczne|Tak|Tak
Dynamiczne tworzenie pakietów|Tak|Tak
Skalowanie|Automatycznie Skaluj do dostosowanej przepływności.|Dodatkowe usługi SUs
Filtrowanie/G20 IP/Host niestandardowy <sup>1</sup>|Tak|Tak
Pobieranie progresywne|Tak|Tak
Zalecane użycie |Zalecane w przypadku większości scenariuszy przesyłania strumieniowego.|Profesjonalne użycie.

<sup>1</sup> używany bezpośrednio w punkcie końcowym przesyłania strumieniowego, gdy sieć CDN nie jest włączona w punkcie końcowym.<br/>

## <a name="properties"></a>properties 

Ta sekcja zawiera szczegółowe informacje dotyczące niektórych właściwości punktu końcowego przesyłania strumieniowego. Przykłady tworzenia nowego punktu końcowego przesyłania strumieniowego i opisy wszystkich właściwości można znaleźć w temacie [punkt końcowy przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints/create). 

- `accessControl`— Służy do konfigurowania następujących ustawień zabezpieczeń dla tego punktu końcowego przesyłania strumieniowego: Klucze uwierzytelniania nagłówka sygnatury Akamai i adresy IP, które mogą łączyć się z tym punktem końcowym.<br />Tę właściwość można ustawić tylko wtedy, `cdnEnabled` gdy ustawiono wartość false.
- `cdnEnabled`-Wskazuje, czy Azure CDN integracja z tym punktem końcowym przesyłania strumieniowego jest włączona (domyślnie wyłączona). W przypadku wybrania `cdnEnabled` wartości true następujące konfiguracje zostaną wyłączone: `customHostNames` i `accessControl`.
  
    Nie wszystkie centra danych obsługują integrację Azure CDN. Aby sprawdzić, czy centrum danych ma dostęp do integracji Azure CDN, wykonaj następujące czynności:
 
  - Spróbuj ustawić `cdnEnabled` wartość na true.
  - Sprawdź zwrócony wynik dla `HTTP Error Code 412` (PreconditionFailed) z komunikatem "przesyłanie strumieniowe CdnEnabled właściwości nie można ustawić na wartość true, ponieważ funkcja sieci CDN nie jest dostępna w bieżącym regionie". 

    Jeśli zostanie wyświetlony ten błąd, centrum danych nie będzie go obsługiwać. Należy wypróbować inne centrum danych.
- `cdnProfile`-Gdy `cdnEnabled` ma wartość true, można również przekazać `cdnProfile` wartości. `cdnProfile`jest nazwą profilu CDN, w którym zostanie utworzony punkt końcowy usługi CDN. Możesz podać istniejący cdnProfile lub użyć nowego. Jeśli wartość jest równa `cdnEnabled` null i ma wartość true, zostanie użyta wartość domyślna "AzureMediaStreamingPlatformCdnProfile". Jeśli podane `cdnProfile` już istnieje, jest w nim tworzony punkt końcowy. Jeśli profil nie istnieje, zostanie automatycznie utworzony nowy profil.
- `cdnProvider`— Jeśli Usługa CDN jest włączona, można również przekazać `cdnProvider` wartości. `cdnProvider`kontroluje, który dostawca będzie używany. Obecnie obsługiwane są trzy wartości: "StandardVerizon", "PremiumVerizon" i "StandardAkamai". Jeśli nie podano wartości i `cdnEnabled` ma wartość true, używana jest wartość "StandardVerizon" (to jest domyślna).
- `crossSiteAccessPolicies`— Służy do określania zasad dostępu między lokacjami dla różnych klientów. Aby uzyskać więcej informacji, zobacz [specyfikację plików zasad między domenami](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) i [udostępnianie usługi w granicach domen](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx).<br/>Ustawienia dotyczą tylko Smooth Streaming.
- `customHostNames`— Służy do konfigurowania punktu końcowego przesyłania strumieniowego w celu akceptowania ruchu kierowanego do niestandardowej nazwy hosta.  Ta właściwość jest prawidłowa dla punktów końcowych przesyłania strumieniowego w warstwie Standardowa i `cdnEnabled`Premium i można ją ustawić w przypadku: false.
    
    Własność nazwy domeny musi zostać potwierdzona przez Media Services. Media Services weryfikuje własność nazwy domeny, wymagając `CName` rekordu zawierającego identyfikator konta Media Services jako składnika, który ma zostać dodany do domeny w użyciu. Przykładowo dla elementu "Sports.contoso.com", który ma być używany jako niestandardowa nazwa hosta dla punktu końcowego przesyłania strumieniowego `<accountId>.contoso.com` , rekord dla programu musi być skonfigurowany tak, aby wskazywał jedną z nazw hostów weryfikacyjnych Media Services. Nazwa hosta weryfikacji składa się z verifydns. \<MediaServices — DNS strefy >. 

    Poniżej znajdują się oczekiwane strefy DNS, które mają być używane w zweryfikowanym rekordzie dla różnych regionów świadczenia usługi Azure.
  
  - Ameryka Północna, Europa, Singapur, Hongkong SAR, Japonia:
      
    - `media.azure.net`
    - `verifydns.media.azure.net`
      
  - Chinach
        
    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`
        
    Na przykład `CName` rekord, który mapuje "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" na "verifydns.Media.Azure.NET", udowadnia, że identyfikator Media Services 945a4c4e-28ea-45cd-8ccb-a519f6b700ad ma własność domeny contoso.com, w ten sposób włączenie dowolnej nazwy w obszarze contoso.com do użycia jako niestandardowa nazwa hosta dla punktu końcowego przesyłania strumieniowego w ramach tego konta. Aby znaleźć wartość identyfikatora usługi multimediów, przejdź do [Azure Portal](https://portal.azure.com/) i wybierz swoje konto usługi multimediów. **Identyfikator konta** jest wyświetlany w prawym górnym rogu strony.
        
    Jeśli zostanie podjęta próba ustawienia niestandardowej nazwy hosta bez prawidłowej weryfikacji `CName` rekordu, odpowiedź DNS zakończy się niepowodzeniem, a następnie będzie buforowana przez jakiś czas. Po wprowadzeniu prawidłowego rekordu może upłynąć trochę czasu, dopóki buforowana odpowiedź nie zostanie ponownie sprawdzona. W zależności od dostawcy DNS dla domeny niestandardowej może upłynąć od kilku minut do godziny, aby ponownie sprawdzić poprawność rekordu.
        
    Oprócz `CName` mapowania `<accountId>.<parent domain>` do `CName` `sports.contoso.com`programu należy utworzyć inny, który mapuje niestandardową nazwę hosta (na przykład) na nazwę hosta punktu końcowego Media Services `verifydns.<mediaservices-dns-zone>`streaming (na przykład `amstest-usea.streaming.media.azure.net`).
 
    > [!NOTE]
    > Punkty końcowe przesyłania strumieniowego znajdujące się w tym samym centrum danych nie mogą współużytkować tej samej nazwy hosta niestandardowego.

    Obecnie Media Services nie obsługuje protokołu SSL z domenami niestandardowymi. 
    
- `maxCacheAge`-Zastępuje domyślny maksymalny nagłówek sterowania pamięcią podręczną HTTP ustawiony przez punkt końcowy przesyłania strumieniowego dla fragmentów nośnika i manifestów na żądanie. Wartość jest ustawiona w sekundach.
- `resourceState` -

    - Zatrzymano — początkowy stan punktu końcowego przesyłania strumieniowego po utworzeniu
    - Uruchamianie — przechodzi do stanu uruchomienia
    - Uruchamianie — umożliwia przesyłanie strumieniowe zawartości do klientów
    - Skalowanie — zwiększanie lub zmniejszanie jednostek skalowania
    - Zatrzymywanie — przechodzi do stanu zatrzymania
    - Usuwanie-jest usuwane
    
- `scaleUnits`— Zapewniają dedykowaną pojemność wyjściową, którą można zakupić w przyrostach wynoszących 200 MB/s. Jeśli musisz przejść do typu **Premium** , Dostosuj `scaleUnits`.

## <a name="working-with-cdn"></a>Praca z usługą CDN

W większości przypadków usługa CDN powinna być włączona. Jeśli jednak przewidujesz maksymalną współbieżność poniżej 500 osób wyświetlających, zaleca się wyłączenie usługi CDN, ponieważ jest ona skalowana najlepiej w przypadku współbieżności.

### <a name="considerations"></a>Zagadnienia do rozważenia

* Punkt końcowy przesyłania strumieniowego `hostname` i adres URL przesyłania strumieniowego pozostają takie same niezależnie od tego, czy jest włączona sieć CDN.
* Jeśli potrzebujesz możliwości testowania zawartości z użyciem sieci CDN lub bez niej, możesz utworzyć inny punkt końcowy przesyłania strumieniowego, który nie jest włączony w sieci CDN.

### <a name="detailed-explanation-of-how-caching-works"></a>Szczegółowe wyjaśnienie działania buforowania

Podczas dodawania sieci CDN nie ma określonej wartości przepustowości, ponieważ przepustowość wymagana przez punkt końcowy przesyłania strumieniowego z obsługą sieci CDN jest różna. Partia zależy od typu zawartości, sposobu jej popularnej, szybkości transmisji bitów i protokołów. Usługa CDN umożliwia buforowanie tylko tego, co jest wymagane. Oznacza to, że popularna zawartość będzie obsługiwana bezpośrednio z sieci CDN — tak długo, jak fragment wideo jest buforowany. Zawartość na żywo jest prawdopodobnie w pamięci podręcznej, ponieważ zazwyczaj masz wiele osób oglądających dokładnie te same rzeczy. Zawartość na żądanie może być trickier bitowym, ponieważ można mieć pewną zawartość, która jest popularna, a niektóre z nich nie są. Jeśli masz miliony zasobów wideo, gdy żadna z nich nie jest popularna (tylko 1 lub 2 przeglądający tydzień), ale masz tysiące osób oglądających wszystkie różne wideo, Sieć CDN jest znacznie mniej skuteczna. W przypadku tych chybień w pamięci podręcznej można zwiększyć obciążenie punktu końcowego przesyłania strumieniowego.
 
Należy również wziąć pod uwagę sposób działania adaptacyjnego przesyłania strumieniowego. Każdy pojedynczy fragment wideo jest buforowany jako jego obiekt własny. Na przykład jeśli po raz pierwszy oglądasz film wideo, osoba pominie około zaledwie kilku sekund, a tylko fragmenty wideo skojarzone z tym, co ktoś ogląda w pamięci podręcznej w sieci CDN. Dzięki adaptacyjnemu przesyłaniu strumieniowym zazwyczaj masz od 5 do 7 różnych szybkości transmisji wideo. Jeśli jedna osoba ogląda jedną szybkość transmisji bitów, a inna osoba ogląda inną szybkość transmisji bitów, to każda z nich jest buforowana osobno w sieci CDN. Nawet jeśli dwie osoby oglądają tę samą szybkość transmisji bitów, mogą one być przesyłane strumieniowo przez różne protokoły. Każdy protokół (HLS, MPEG-KRESKa, Smooth Streaming) jest buforowany osobno. Dlatego każda szybkość transmisji bitów i protokół są buforowane oddzielnie, a tylko te fragmenty wideo, które zostały żądane, są buforowane.

### <a name="enable-azure-cdn-integration"></a>Włącz integrację Azure CDN

Po udostępnieniu punktu końcowego przesyłania strumieniowego z włączoną usługą CDN istnieje zdefiniowany czas oczekiwania na Media Services przed aktualizacją usługi DNS w celu zamapowania punktu końcowego przesyłania strumieniowego na punkt końcowy usługi CDN.

Jeśli zechcesz później wyłączyć/włączyć usługę CDN, punkt końcowy przesyłania strumieniowego musi znajdować się w stanie zatrzymanym. Włączenie integracji Azure CDN może potrwać do dwóch godzin, a zmiany zostaną uaktywnione we wszystkich punktach pop usługi CDN. Można jednak uruchomić punkt końcowy przesyłania strumieniowego i strumień bez przerw w punkcie końcowym przesyłania strumieniowego, a po zakończeniu integracji zostanie dostarczony strumień z sieci CDN. W trakcie okresu aprowizacji punkt końcowy przesyłania strumieniowego będzie w stanie **uruchomienia** i może wystąpić spadek wydajności.

Po utworzeniu standardowego punktu końcowego przesyłania strumieniowego jest on konfigurowany domyślnie przy użyciu standardowego Verizon. Korzystając z interfejsów API REST, można skonfigurować Verizon Premium lub standardowego dostawcy Akamai. 

Integracja z usługą CDN jest włączona we wszystkich centrach danych platformy Azure z wyjątkiem Chin i federalnych regionów rządowych.

> [!IMPORTANT]
> Integracja Azure Media Services z Azure CDN jest zaimplementowana w **Azure CDN z Verizon** dla standardowych punktów końcowych przesyłania strumieniowego. Punkty końcowe przesyłania strumieniowego w warstwie Premium można skonfigurować przy użyciu wszystkich **Azure CDN warstw cenowych i dostawców**. Aby uzyskać więcej informacji na temat funkcji Azure CDN, zobacz [Omówienie usługi CDN](../../cdn/cdn-overview.md).

### <a name="determine-if-dns-change-has-been-made"></a>Ustal, czy wprowadzono zmianę w systemie DNS

Można określić, czy zmiana DNS została wprowadzona w punkcie końcowym przesyłania strumieniowego (ruch jest kierowany do Azure CDN) za pomocą programu https://www.digwebinterface.com. Jeśli wyniki zawierają nazwy domen azureedge.net w wynikach, ruch jest teraz wskazywany do sieci CDN.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

W przykładzie [w tym repozytorium](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) przedstawiono sposób uruchamiania domyślnego punktu końcowego przesyłania strumieniowego przy użyciu platformy .NET.

