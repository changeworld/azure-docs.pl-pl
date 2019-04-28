---
title: Przesyłanie strumieniowe punktów końcowych w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera wyjaśnienie, co to są punkty końcowe przesyłania strumieniowego i jak są one używane przez usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: 8b6deadca610916a10f719d715fe6a17e29148bb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125427"
---
# <a name="streaming-endpoints"></a>Punkty końcowe przesyłania strumieniowego

W programie Microsoft Azure Media Services (AMS), [punkty końcowe przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints) jednostki reprezentuje usługę przesyłania strumieniowego, który może dostarczać zawartość bezpośrednio do aplikacji odtwarzacza klienta lub dalsze do Content Delivery Network (CDN) dla Dystrybucja. Strumień wychodzący, z **punkt końcowy przesyłania strumieniowego** usługa może być strumień na żywo lub element zawartości wideo na żądanie w ramach konta usługi Media Services. Po utworzeniu konta usługi Media Services, **domyślne** punkt końcowy przesyłania strumieniowego zostanie utworzony w stanie zatrzymania. Nie można usunąć **domyślne** punkt końcowy przesyłania strumieniowego. Dodatkowe punkty końcowe przesyłania strumieniowego mogą być tworzone w ramach konta. 

> [!NOTE]
> Aby rozpocząć przesyłanie strumieniowe filmów wideo, wówczas musisz uruchomić **punkt końcowy przesyłania strumieniowego** z którego chcesz strumieniowo przesyłać wideo. 
>  
> Opłaty są naliczane tylko wtedy, gdy punkt końcowy przesyłania strumieniowego jest w stanie uruchomienia.

## <a name="naming-convention"></a>Konwencje nazewnictwa

Dla domyślnego punktu końcowego: `{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

Aby uzyskać wszystkie dodatkowe punkty końcowe: `{EndpointName}-{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

## <a name="types"></a>Typy  

Istnieją dwa typy **punktów końcowych przesyłania strumieniowego**: **Standardowy** i **Premium**. Typ jest zdefiniowany przez liczbę jednostek skalowania (`scaleUnits`) możesz przydzielić dla punktu końcowego przesyłania strumieniowego. 

Tabela zawiera opis typów:  

|Type|Jednostki skalowania|Opis|
|--------|--------|--------|  
|**Standardowy punkt końcowy przesyłania strumieniowego** (zalecany)|0|Domyślny punkt końcowy przesyłania strumieniowego jest **standardowa** typu, ale można ją zmienić na typ Premium.<br/> Standardowy typ jest to zalecana opcja, do niemal wszystkich scenariuszy przesyłania strumieniowego i publiczność każdej wielkości. Typ **Standardowy** automatycznie skaluje przepustowość wychodzącą. Przepływność z tego typu punktu końcowego przesyłania strumieniowego jest do 600 MB/s. Wideo fragmenty są przechowywane w usłudze CDN nie należy używać przepustowości punkt końcowy przesyłania strumieniowego.<br/>W przypadku klientów z bardzo dużymi wymaganiami usługa Media Services oferuje punkty końcowe przesyłania strumieniowego **Premium**, które umożliwiają skalowanie pojemności w poziomie dla największych odbiorców w Internecie. Jeśli spodziewasz się dużej liczby odbiorców i osoby przeglądające współbieżnych, skontaktuj się z nami pod amsstreaming\@microsoft.com, aby uzyskać wskazówki dotyczące tego, czy należy przenieść do **Premium** typu. |
|**Punkt końcowy przesyłania strumieniowego Premium**|>0|Punkty końcowe przesyłania strumieniowego **Premium** są odpowiednie w przypadku zaawansowanych obciążeń, ponieważ zapewniają dedykowaną i skalowalną pojemność przepustowości. Przenieś do **Premium** typu, dostosowując `scaleUnits`. `scaleUnits` umożliwiają pojemności dedykowanej ruch wychodzący, który można zakupić według przyrostów 200 MB/s. W przypadku korzystania z typu **Premium** każda włączona jednostka zapewnia dodatkową przepustowość w aplikacji. |
 
## <a name="comparing-streaming-types"></a>Porównywanie typów przesyłania strumieniowego

### <a name="features"></a>Funkcje

Cecha|Standardowa (Standard)|Premium
---|---|---
Bezpłatne pierwsze 15 dni| Yes |Nie
Przepływność |Do 600 MB/s, gdy nie jest używana sieć CDN systemu Azure. Skaluje się przy użyciu usługi CDN.|200 MB/s dla przesyłania strumieniowego (SU) jednostki. Skaluje się przy użyciu usługi CDN.
CDN|Usługa Azure CDN, innej sieci CDN lub nie sieci CDN.|Usługa Azure CDN, innej sieci CDN lub nie sieci CDN.
Opłaty są naliczane proporcjonalnie| Codziennie|Codziennie
Szyfrowanie dynamiczne|Yes|Yes
Dynamiczne tworzenie pakietów|Yes|Yes
Skalowanie|Automatyczne jest skalowany w górę docelowe przepływności.|Dodatkowe jednostki przesyłania strumieniowego
Host filtrowanie/G20/Custom IP <sup>1</sup>|Yes|Yes
Pobierania progresywnego|Yes|Yes
Zalecane użycie |Zalecane w przypadku większość przesyłania strumieniowego scenariuszy.|Użycie Professional.<br/>Jeśli Twoim zdaniem mogą mieć potrzeb poza Standard. Skontaktuj się z nami (amsstreaming@microsoft.com) Jeśli oczekujesz, rozmiar współbieżnych odbiorców większych niż 50 000 osób przeglądających.

<sup>1</sup> warunkiem bezpośrednio na punkt końcowy przesyłania strumieniowego nie włączono usługę CDN w punkcie końcowym.

## <a name="properties"></a>Właściwości 

Ta sekcja zawiera szczegółowe informacje o niektórych właściwości końcowego przesyłania strumieniowego. Aby zapoznać się z przykładami sposobu tworzenia nowego punktu końcowego przesyłania strumieniowego i opisy wszystkich właściwości, zobacz [punkt końcowy przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints/create). 

- `accessControl` — Umożliwiają skonfigurowanie następujących ustawień zabezpieczeń dla tego punktu końcowego przesyłania strumieniowego: Akamai podpis nagłówka uwierzytelniania klucze i adresy IP, które mogą połączyć się z tym punktem końcowym.<br />Tę właściwość można ustawić tylko podczas `cdnEnabled` jest ustawiona na wartość false.
- `cdnEnabled` — Wskazuje, czy integracji usługi Azure CDN dla tego punktu końcowego przesyłania strumieniowego jest włączone (domyślnie wyłączony). Jeśli ustawisz `cdnEnabled` na wartość true, następujące konfiguracje wyłączone: `customHostNames` i `accessControl`.
  
    Nie wszystkie centra danych obsługują integracji usługi Azure CDN. Aby sprawdzić, czy centrum danych ma dostępne integracji usługi Azure CDN, wykonaj następujące czynności:
 
  - Próbuje ustawić `cdnEnabled` na wartość true.
  - Sprawdź wynik zwracany `HTTP Error Code 412` (PreconditionFailed) z komunikatem "Właściwości CdnEnabled punktu końcowego strumienia nie można ustawić wartość true, ponieważ funkcje sieci CDN nie jest dostępna w bieżącym regionie." 

    Jeśli ten błąd w centrum danych nie obsługuje. Należy spróbować innego centrum danych.
- `cdnProfile` — W przypadku `cdnEnabled` jest ustawiona na wartość true, można również przekazać `cdnProfile` wartości. `cdnProfile` jest nazwa profilu CDN, w której zostanie utworzony punkt końcowy usługi CDN. Można podać istniejące cdnProfile lub użyć nowej. Jeśli wartość jest równa NULL i `cdnEnabled` jest true, wartość domyślna "AzureMediaStreamingPlatformCdnProfile" jest używana. Jeśli podane `cdnProfile` już istnieje, punkt końcowy został utworzony w nim. Jeśli profil nie istnieje, nowy profil pobiera tworzone automatycznie.
- `cdnProvider` -Jeśli CDN została włączona, można również przekazać `cdnProvider` wartości. `cdnProvider` Określa, który dostawca, który będzie używany. Obecnie obsługiwane są trzy wartości: "StandardVerizon", "PremiumVerizon" and "StandardAkamai". Jeśli podano żadnej wartości i `cdnEnabled` ma wartość true, "StandardVerizon" jest używana (która jest wartością domyślną).
- `crossSiteAccessPolicies` — Służy do określania zasad dostępu cross-site dla różnych klientów. Aby uzyskać więcej informacji, zobacz [określenie pliku zasad między domenami](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) i [wprowadzania Service Available Across Domain Boundaries](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx).<br/>Ustawienia dotyczą tylko Smooth Streaming.
- `customHostNames` — Umożliwia skonfigurowanie punktu końcowego przesyłania strumieniowego, aby akceptować ruch skierowany do niestandardową nazwą hosta.  Ta właściwość jest prawidłowa dla standardowego i punkty końcowe przesyłania strumieniowego w warstwie Premium i mogą być ustawiane podczas `cdnEnabled`: wartość false.
    
    Własność nazwy domeny musi zostać potwierdzone przy usługi Media Services. Usługi Media Services sprawdza własność nazwy domeny, wymagając `CName` rekord zawierający identyfikator konta usługi Media Services jako składnik, który ma zostać dodany do domeny w użyciu. Na przykład dla "sports.contoso.com", który ma być używany jako niestandardową nazwą hosta punktu końcowego przesyłania strumieniowego, rekord `<accountId>.contoso.com` należy skonfigurować, aby wskazywały na jeden z nazw hostów weryfikacji usługi Media Services. Nazwa hosta Weryfikacja składa się z verifydns. \<mediaservices dns strefy >. 

    Poniżej przedstawiono oczekiwane stref DNS ma być używany w rekordzie Sprawdź, czy dla różnych regionów platformy Azure.
  
  - Ameryka Północna, Europa, Singapur, Hongkong SAR, Japonia, część:
      
    - `media.azure.net`
    - `verifydns.media.azure.net`
      
  - Chińska wersja:
        
    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`
        
    Na przykład `CName` rekord, który mapuje "945a4c4e-28ea-45 cd-8ccb-a519f6b700ad.contoso.com" do "verifydns.media.azure.net" okazuje się 945a4c4e-28ea-45cd-8ccb-a519f6b700ad Media Services identyfikator związku z tym jest właścicielem domeny contoso.com Włączanie dowolną nazwę w domenie contoso.com, ma być używany jako niestandardową nazwą hosta dla punktu końcowego przesyłania strumieniowego w ramach tego konta. Aby znaleźć wartość Identyfikatora usługi multimediów, przejdź do [witryny Azure portal](https://portal.azure.com/) i wybierz swoje konto usługi multimediów. **Accountid** pojawia się w górnym rogu strony.
        
    Jeśli podjęto próbę ustawienia niestandardową nazwą hosta bez prawidłowego weryfikacji `CName` rekordu, to odpowiedź DNS zakończy się niepowodzeniem i wówczas buforowana przez pewien czas. Po właściwego rekordu znajduje się w miejscu, może potrwać chwilę, aż ponownie sprawdzić poprawności nazwy odpowiedzi z pamięci podręcznej. W zależności od dostawcy DNS dla domeny niestandardowej jego może dopiero po pewnym czasie od kilku minut do godziny można przechowywać w rekordzie.
        
    Oprócz `CName` mapujący `<accountId>.<parent domain>` do `verifydns.<mediaservices-dns-zone>`, należy utworzyć inny `CName` mapujący niestandardową nazwą hosta (na przykład `sports.contoso.com`) do nazwy hosta usługi Media Services punkt końcowy przesyłania strumieniowego firmy (na przykład `amstest-usea.streaming.media.azure.net`).
 
    > [!NOTE]
    > W tym samym centrum danych, punkty końcowe przesyłania strumieniowego nie mają taką samą nazwę niestandardowego hosta.

    Obecnie usługa Media Services nie obsługuje protokołu SSL z zastosowaniem domen niestandardowych. 
    
- `maxCacheAge` -Zastąpienia domyślny pamięci podręcznej HTTP max-age kontrolować ustawione przez punkt końcowy przesyłania strumieniowego multimediów fragmentów i manifesty na żądanie. Wartość jest ustawiona w ciągu kilku sekund.
- `resourceState` -

    - Zatrzymano — początkowy stan punktu końcowego przesyłania strumieniowego po utworzeniu
    - Uruchamianie — są przenoszone do stanu uruchomienia
    - Uruchamianie — jest w stanie przesyłanie strumieniowe zawartości do klientów
    - Skalowanie — skalowania są jednostki zwiększanie lub zmniejszanie
    - Zatrzymywanie — są przenoszone do stanu zatrzymania
    - Usuwanie — jest ona usuwana
    
- `scaleUnits` -Umożliwiają pojemności dedykowanej ruch wychodzący, który można zakupić według przyrostów 200 MB/s. Jeśli musisz przenieść **Premium** wpisz, dostosować `scaleUnits`.

## <a name="working-with-cdn"></a>Praca z usługą CDN

W większości przypadków usługa CDN powinna być włączona. Jeśli jednak przewidujesz maksymalną współbieżność poniżej 500 osób wyświetlających, zaleca się wyłączenie usługi CDN, ponieważ jest ona skalowana najlepiej w przypadku współbieżności.

### <a name="considerations"></a>Zagadnienia do rozważenia

* Punkt końcowy przesyłania strumieniowego `hostname` i adres URL przesyłania strumieniowego pozostaje taki sam, czy włączyć sieć CDN.
* Jeśli potrzebna jest możliwość testowania treści z lub bez usługi CDN, możesz utworzyć inny punkt końcowy przesyłania strumieniowego nie włączono usługę CDN.

### <a name="detailed-explanation-of-how-caching-works"></a>Szczegółowe wyjaśnienie, jak działa buforowanie

Podczas dodawania sieci CDN, ponieważ ilość przepustowości, który jest wymagany w przypadku sieci CDN włączony punkt końcowy przesyłania strumieniowego zmienia nie istnieje wartość określonej przepustowości. Wiele zależy od typu zawartości, jak popularna jest, szybkości transmisji i protokołów. Sieć CDN jest tylko pamięci podręcznej, co to jest wymagana. Oznacza to, że popularnej zawartości będą udostępnianie bezpośrednio z sieci CDN — tak długo, jak wideo fragment jest buforowany. Zawartości na żywo jest prawdopodobnie można buforować, ponieważ zwykle mają wiele osób oglądanie dokładnie tak samo. Zawartość na żądanie może być nieco trudniejszy, ponieważ może mieć zawartości, niektóre popularne i niektóre nie jest. Jeśli masz milionów zasobów wideo, gdy żaden z nich są popularne (tylko 1 lub 2 osób przeglądających w tygodniu), ale masz tysiące osób obserwujących różnych plików wideo, usługi CDN staje się znacznie mniej skuteczne. Z tą pamięcią podręczną Chybienia, zwiększasz obciążenie na punkt końcowy przesyłania strumieniowego.
 
Należy również wziąć pod uwagę sposób adaptacyjnego przesyłania strumieniowego działa. Poszczególne fragmenty w poszczególnych wideo są buforowane, ponieważ jest własne jednostki. Na przykład jeśli po raz pierwszy niektórych wideo jest monitorowana, osoba pomija wokół oglądania tylko kilka sekund tu i tam tylko fragmentów wideo skojarzonych z obserwowane osoby Pobierz buforowane w usłudze CDN. Za pomocą adaptacyjnego przesyłania strumieniowego, zazwyczaj mają różne szybkości transmisji 5 do 7 wideo. Jeśli jedna osoba obserwuje o jednej szybkości transmisji bitów i innej osobie obserwuje różne szybkości transmisji bitów, następnie one są każdego buforowane osobno w usłudze CDN. Nawet wtedy, gdy dwie osoby obserwujesz tego samego szybkości transmisji bitów można przesyłanie strumieniowe za pośrednictwem różnych protokołów. Dla każdego protokołu (HLS, MPEG-DASH, Smooth Streaming) są buforowane osobno. Dlatego każdego szybkość transmisji bitów i protokół są buforowane osobno, a tylko tych fragmentów wideo, które zażądano są buforowane.

### <a name="enable-azure-cdn-integration"></a>Włączanie integracji z usługą Azure CDN

Był zaopatrzony punktu końcowego przesyłania strumieniowego włączono usługę CDN po czas oczekiwania zdefiniowanych w usłudze Media Services przed mapowania punktu końcowego przesyłania strumieniowego na punkt końcowy usługi CDN odbywa się aktualizacji DNS.

Jeśli zechcesz później wyłączanie/włączanie usługi CDN, musi należeć do punktu końcowego przesyłania strumieniowego **zatrzymana** stanu. Może potrwać do dwóch godzin dla integracji usługi Azure CDN włączany i zmian jako aktywny we wszystkich punktach obecności usługi CDN. Jednakże możesz uruchomić punkt końcowy przesyłania strumieniowego i strumienia bez przerw w pracy z punktu końcowego przesyłania strumieniowego, a po ukończeniu integracji strumień jest dostarczany z sieci CDN. W okresie udostępniania punktu końcowego przesyłania strumieniowego będą w **uruchamianie** stanu i użytkownik może obserwować pogorszenie wydajności.

Gdy zostanie utworzony standardowy punkt końcowy przesyłania strumieniowego, jest on skonfigurowany domyślnie Verizon — standardowa. Można skonfigurować Verizon — Premium lub standardowa firmy Akamai dostawców przy użyciu interfejsów API REST. 

Integracja usługi CDN jest włączona w centrach danych platformy Azure z wyjątkiem Chin i rządu federalnego regionów.

> [!IMPORTANT]
> Integracja usługi Azure Media Services z usługą Azure CDN jest wdrażana w **Azure CDN from Verizon** dla standardowych punktów końcowych przesyłania strumieniowego. Punkty końcowe przesyłania strumieniowego Premium można skonfigurować za pomocą wszystkich **usługi Azure CDN ceny warstwy i dostawców**. Aby uzyskać więcej informacji na temat funkcji usługi Azure CDN, zobacz [Omówienie usługi CDN](../../cdn/cdn-overview.md).

### <a name="determine-if-dns-change-has-been-made"></a>Określić, jeśli wprowadzono zmiany w systemie DNS

Jeśli wprowadzono zmiany w systemie DNS w punkcie końcowym przesyłania strumieniowego (ruch jest być kierowany do usługi Azure CDN) można określić za pomocą https://www.digwebinterface.com. Jeśli wyniki zawiera nazwy domeny azureedge.net, w wynikach, ruch jest teraz wskazywany sieci CDN.

## <a name="provide-feedback"></a>Przekazywanie opinii

Zapoznaj się z [społeczności usługi Azure Media Services](media-services-community.md) artykuł, aby wyświetlić różne sposoby zadawaj pytania, Prześlij opinię i pobrać aktualizacje o usłudze Media Services.

## <a name="next-steps"></a>Kolejne kroki

Przykład [w tym repozytorium](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) pokazuje, jak uruchomić domyślny punkt końcowy przy użyciu platformy .NET przesyłania strumieniowego.

