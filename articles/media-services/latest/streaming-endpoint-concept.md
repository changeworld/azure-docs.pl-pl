---
title: Punkty końcowe przesyłania strumieniowego (Origin)
titleSuffix: Azure Media Services
description: Dowiedz się więcej o punktach końcowych przesyłania strumieniowego (Origin), dynamicznej usłudze pakowania i przesyłania strumieniowego, która dostarcza zawartość bezpośrednio do aplikacji odtwarzacza klienckiego lub do sieci dostarczania zawartości (CDN).
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: 72cfdf172e4524e302ef2e22826d4f78ce32daf0
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582725"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Punkty końcowe przesyłania strumieniowego (Origin) w usłudze Azure Media Services

W usłudze Microsoft Azure Media Services [punkt końcowy przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints) reprezentuje dynamiczną (just-in-time) usługę pakowania i pochodzenia, która może dostarczać zawartość na żywo i na żądanie bezpośrednio do aplikacji odtwarzacza klienckiego przy użyciu jednego ze wspólnych protokołów multimediów strumieniowych (HLS lub DASH). Ponadto punkt **końcowy przesyłania strumieniowego** zapewnia dynamiczne szyfrowanie (just-in-time) do wiodących w branży pamięci DRM. 

Podczas tworzenia konta usługi Media Services **domyślny** punkt końcowy przesyłania strumieniowego jest tworzony w stanie zatrzymania. Nie można usunąć **domyślnego** punktu końcowego przesyłania strumieniowego. Więcej punktów końcowych przesyłania strumieniowego można utworzyć w ramach konta (zobacz [Przydziały i limity).](limits-quotas-constraints.md)

> [!NOTE]
> Aby rozpocząć przesyłanie strumieniowe wideo, należy uruchomić **punkt końcowy przesyłania strumieniowego,** z którego chcesz przesyłać strumieniowo wideo.
>
> Naliczane są tylko wtedy, gdy punkt końcowy przesyłania strumieniowego jest w stanie uruchomionym.

Zapoznaj się również z tematem [Dynamiczne pakowanie.](dynamic-packaging-overview.md) 

## <a name="naming-convention"></a>Konwencja nazewnictwa

Format nazwy hosta adresu URL `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`przesyłania `servicename` strumieniowego to: , where = nazwa punktu końcowego przesyłania strumieniowego lub nazwa zdarzenia na żywo.

W przypadku korzystania z `servicename` domyślnego punktu końcowego `{accountname}-{regionname}.streaming.azure.net`przesyłania strumieniowego, jest pomijany, więc adres URL jest: .

### <a name="limitations"></a>Ograniczenia

* Nazwa punktu końcowego przesyłania strumieniowego ma maksymalną wartość 24 znaków.
* Nazwa powinna być zgodna z `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`tym wzorem [wyrażenia regularnego:](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) .

## <a name="types"></a>Typy

Istnieją dwa typy **punktów końcowych przesyłania strumieniowego:** **standardowy** (wersja zapoznawcza) i **Premium**. Typ jest definiowany przez liczbę`scaleUnits`jednostek skalowania ( ) przydzielanych dla punktu końcowego przesyłania strumieniowego.

Tabela zawiera opis typów:

|Typ|Jednostki skalowania|Opis|
|--------|--------|--------|  
|**Standardowa**|0|Domyślnym punktem końcowym przesyłania strumieniowego jest typ **standardowy** — `scaleUnits`można go zmienić na typ Premium, dostosowując .|
|**Premium**|>0|**Premium** Punkty końcowe przesyłania strumieniowego są odpowiednie dla zaawansowanych obciążeń i zapewniają dedykowaną i skalowalną przepustowość. Przenieś do typu **Premium,** `scaleUnits` dostosowując (jednostki przesyłania strumieniowego). `scaleUnits`zapewnia dedykowaną pojemność wyjścia, którą można kupić w odstępach co 200 Mb/s. Podczas korzystania z typu **Premium,** każda włączona jednostka zapewnia dodatkową przepustowość aplikacji. |

> [!NOTE]
> W przypadku klientów, którzy chcą dostarczać zawartość do dużych odbiorców internetu, zaleca się włączenie sieci CDN w punkcie końcowym przesyłania strumieniowego.

Aby uzyskać informacje o umowy SLA, zobacz [Cennik i umowy SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Porównywanie typów przesyłania strumieniowego

Funkcja|Standardowa|Premium
---|---|---
Przepływność |Do 600 Mb/s i może zapewnić znacznie wyższą efektywną przepustowość, gdy używana jest sieć CDN.|200 Mb/s na jednostkę przesyłania strumieniowego (SU). Może zapewnić znacznie wyższą efektywną przepustowość, gdy używana jest sieć CDN.
CDN|Usługa Azure CDN, usługa CDN innej firmy lub sieć CDN.|Usługa Azure CDN, usługa CDN innej firmy lub sieć CDN.
Rozliczenia są naliczane proporcjonalnie| Dzienna|Dzienna
Szyfrowanie dynamiczne|Tak|Tak
Dynamiczne tworzenie pakietów|Tak|Tak
Skalowanie|Automatycznie skaluje się do docelowej przepływności.|Dodatkowe SU
Filtrowanie IP/G20/Host niestandardowy <sup>1</sup>|Tak|Tak
Pobieranie progresywne|Tak|Tak
Zalecane użycie |Zalecane dla większości scenariuszy przesyłania strumieniowego.|Profesjonalne użytkowanie.

<sup>1</sup> Używane tylko bezpośrednio w punkcie końcowym przesyłania strumieniowego, gdy sieć CDN nie jest włączona w punkcie końcowym.<br/>

## <a name="streaming-endpoint-properties"></a>Przesyłanie strumieniowe właściwości punktu końcowego

W tej sekcji podano szczegółowe informacje na temat niektórych właściwości punktu końcowego przesyłania strumieniowego. Aby zapoznać się z przykładami tworzenia nowego punktu końcowego przesyłania strumieniowego i opisów wszystkich właściwości, zobacz [Punkt końcowy przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingendpoints/create).

- `accessControl`: Służy do konfigurowania następujących ustawień zabezpieczeń dla tego punktu końcowego przesyłania strumieniowego: klucze uwierzytelniania nagłówka podpisu Akamai i adresy IP, które mogą łączyć się z tym punktem końcowym. Tę właściwość można `cdnEnabled` ustawić tylko wtedy, gdy jest ustawiona na false.

- `cdnEnabled`: Wskazuje, czy integracja usługi Azure CDN dla tego punktu końcowego przesyłania strumieniowego jest włączona (domyślnie wyłączona). Jeśli ustawiono `cdnEnabled` wartość true, następujące konfiguracje zostają wyłączone: `customHostNames` i `accessControl`.

    Nie wszystkie centra danych obsługują integrację usługi Azure CDN. Aby sprawdzić, czy centrum danych ma dostępną integrację usługi Azure CDN, wykonaj następujące czynności:

  - Spróbuj ustawić `cdnEnabled` true.
  - Sprawdź zwrócony `HTTP Error Code 412` wynik dla (PreconditionFailed) z komunikatem "Streaming endpoint CdnEnabled właściwość nie można ustawić true jako true jako możliwości cdn nie jest dostępna w bieżącym regionie."

    Jeśli ten błąd zostanie wyświetlony, centrum danych nie obsługuje go. Spróbuj użyć innego centrum danych.

- `cdnProfile`: `cdnEnabled` Gdy jest ustawiona wartość `cdnProfile` true, można również przekazać wartości. `cdnProfile`to nazwa profilu sieci CDN, w którym zostanie utworzony punkt końcowy sieci CDN. Można podać istniejący cdnProfile lub użyć nowego. Jeśli wartość jest `cdnEnabled` NULL i jest true, używana jest wartość domyślna "AzureMediaStreamingPlatformCdnProfile". Jeśli podane `cdnProfile` już istnieje, punkt końcowy jest tworzony pod nim. Jeśli profil nie istnieje, automatycznie zostanie utworzony nowy profil.
- `cdnProvider`: Gdy sieć CDN jest włączona, można również przekazać `cdnProvider` wartości. `cdnProvider`kontroli, który dostawca będzie używany. Obecnie obsługiwane są trzy wartości: "StandardVerizon", "PremiumVerizon" i "StandardAkamai". Jeśli żadna wartość `cdnEnabled` nie jest podana i jest true, "StandardVerizon" jest używany (to jest wartość domyślna).
- `crossSiteAccessPolicies`: Służy do określania zasad dostępu między lokacjami dla różnych klientów. Aby uzyskać więcej informacji, zobacz [Specyfikacja pliku zasad między domenami](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) i [Udostępnianie usługi w granicach domeny](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx). Ustawienia dotyczą tylko płynnego przesyłania strumieniowego.
- `customHostNames`: Służy do konfigurowania punktu końcowego przesyłania strumieniowego w celu akceptowania ruchu kierowanego do niestandardowej nazwy hosta. Ta właściwość jest prawidłowa dla standardowych i premium `cdnEnabled`punktów końcowych przesyłania strumieniowego i można ustawić, kiedy : false.

    Własność nazwy domeny musi zostać potwierdzona przez program Media Services. Usługa Media Services weryfikuje własność `CName` nazwy domeny, wymagając, aby rekord zawierający identyfikator konta usługi Media Services jako składnik został dodany do używanej domeny. Na przykład dla "sports.contoso.com" do użycia jako niestandardowa nazwa hosta dla punktu `<accountId>.contoso.com` końcowego przesyłania strumieniowego, rekord dla musi być skonfigurowany do wskazywanie jednej z nazw hostów weryfikacji usługi Media Services. Nazwa hosta weryfikacji składa się z verifydns. \<> mediaservices-dns-zone.

    Poniżej przedstawiono oczekiwane strefy DNS, które mają być używane w rekordzie weryfikacji dla różnych regionów platformy Azure.
  
  - Ameryka Północna, Europa, Singapur, Hongkong SAR, Japonia:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - Chiny:

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Na przykład `CName` rekord mapujący "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" na "verifydns.media.azure.net" dowodzi, że identyfikator usługi media 945a4c4e-28ea-45cd-8ccb-a519f6b700ad jest właścicielem domeny contoso.com, umożliwiając w ten sposób dowolną nazwę pod contoso.com, która ma być używana jako niestandardowa nazwa hosta dla punktu końcowego przesyłania strumieniowego na tym koncie. Aby znaleźć wartość identyfikatora usługi Media Service, przejdź do [witryny Azure portal](https://portal.azure.com/) i wybierz konto usługi Media Service. Identyfikator **konta** pojawi się w prawym górnym rogu strony.

    Jeśli istnieje próba ustawienia niestandardowej nazwy hosta bez `CName` odpowiedniej weryfikacji rekordu, odpowiedź DNS zakończy się niepowodzeniem, a następnie zostanie buforowana przez pewien czas. Po wprowadzeniu odpowiedniego rekordu może upłynąć trochę czasu, aż odpowiedź w pamięci podręcznej zostanie ponownie oceniona. W zależności od dostawcy dns dla domeny niestandardowej ponowne zadośćuczynienie rekordu zajmuje od kilku minut do godziny.

    Oprócz `CName` mapy `<accountId>.<parent domain>` do `verifydns.<mediaservices-dns-zone>`, należy utworzyć `CName` inny, który mapuje niestandardową `sports.contoso.com`nazwę hosta (na przykład) do nazwy `amstest-usea.streaming.media.azure.net`hosta usługi Media Services Streaming Endpoint (na przykład).

    > [!NOTE]
    > Punkty końcowe przesyłania strumieniowego znajdujące się w tym samym centrum danych nie mogą współużytkować tej samej niestandardowej nazwy hosta.

    Obecnie usługa Media Services nie obsługuje protokołu TLS z domenami niestandardowymi.

- `maxCacheAge`- Zastępuje domyślny nagłówek kontroli pamięci podręcznej HTTP max-age ustawiony przez punkt końcowy przesyłania strumieniowego na fragmenty nośników i manifesty na żądanie. Wartość jest ustawiana w sekundach.
- `resourceState` -

    - Zatrzymane: początkowy stan punktu końcowego przesyłania strumieniowego po utworzeniu
    - Uruchamianie: przechodzi do stanu uruchomionego
    - Działa: może przesyłać strumieniowo zawartość do klientów
    - Skalowanie: jednostki skali są zwiększane lub zmniejszane
    - Zatrzymanie: przechodzi do stanu zatrzymanego
    - Usuwanie: jest usuwany

- `scaleUnits`: Zapewnia dedykowaną pojemność wyjścia, którą można kupić w odstępach co 200 Mb/s. Jeśli chcesz przejść do typu **Premium,** dostosuj `scaleUnits`.

## <a name="why-use-multiple-streaming-endpoints"></a>Dlaczego warto korzystać z wielu punktów końcowych przesyłania strumieniowego?

Pojedynczy punkt końcowy przesyłania strumieniowego może przesyłać strumieniowo wideo na żywo i na żądanie, a większość klientów używa tylko jednego punktu końcowego przesyłania strumieniowego. W tej sekcji przedstawiono kilka przykładów, dlaczego może być konieczne użycie wielu punktów końcowych przesyłania strumieniowego.

* Każda zarezerwowana jednostka pozwala na przepustowość 200 Mb/s. Jeśli potrzebujesz przepustowości większej niż 2000 Mb/s (2 Gb/s), możesz użyć drugiego punktu końcowego przesyłania strumieniowego i równoważenia obciążenia, aby zapewnić dodatkową przepustowość.

    Jednak cdn jest najlepszym sposobem, aby osiągnąć skalowanie w poziomie dla przesyłania strumieniowego zawartości, ale jeśli dostarczasz tyle zawartości, że CDN ciągnie więcej niż 2 Gb/s, można dodać dodatkowe punkty końcowe przesyłania strumieniowego (początki). W takim przypadku należy rozdać adresy URL zawartości, które są zrównoważone w dwóch punktach końcowych przesyłania strumieniowego. Takie podejście zapewnia lepsze buforowanie niż próby wysyłania żądań do każdego źródła losowo (na przykład za pośrednictwem menedżera ruchu). 
    
    > [!TIP]
    > Zazwyczaj, jeśli CDN ciągnie więcej niż 2 Gb/s, coś może być źle skonfigurowane (na przykład bez ekranowania pochodzenia).
    
* Równoważenie obciążenia różnych dostawców sieci CDN. Na przykład można skonfigurować domyślny punkt końcowy przesyłania strumieniowego, aby użyć verizon CDN i utworzyć drugi do korzystania z Akamai. Następnie dodaj pewne równoważenie obciążenia między nimi, aby osiągnąć równoważenie wielu sieci CDN. 

    Jednak klient często równoważenia obciążenia między wieloma dostawcami sieci CDN przy użyciu jednego źródła.
* Przesyłanie strumieniowe mieszanej zawartości: transmisja na żywo i wideo na żądanie. 

    Wzorce dostępu dla zawartości na żywo i na żądanie są bardzo różne. Treści na żywo zwykle otrzymują duże zapotrzebowanie na te same treści naraz. Zawartość wideo na żądanie (na przykład zawartość archiwum długiego ogona) ma niewielkie wykorzystanie tej samej zawartości. Tak więc buforowanie działa bardzo dobrze na treści na żywo, ale nie tak dobrze na zawartość długiego ogona.

    Należy wziąć pod uwagę scenariusz, w którym klienci są głównie oglądania zawartości na żywo, ale tylko od czasu do czasu oglądania zawartości na żądanie i jest obsługiwany z tego samego punktu końcowego przesyłania strumieniowego. Niskie użycie zawartości na żądanie zajmuje miejsce w pamięci podręcznej, które byłoby lepiej zapisane dla zawartości na żywo. W tym scenariuszu zaleca się obsługiwanie zawartości na żywo z jednego punktu końcowego przesyłania strumieniowego i zawartości długiego ogona z innego punktu końcowego przesyłania strumieniowego. Poprawi to wydajność zawartości wydarzenia na żywo.
    
## <a name="scaling-streaming-with-cdn"></a>Skalowanie przesyłania strumieniowego przy użyciu sieci CDN

Zobacz następujące artykuły:

- [Omówienie sieci CDN](../../cdn/cdn-overview.md)
- [Skalowanie przesyłania strumieniowego przy użyciu sieci CDN](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>Zadawaj pytania i otrzyj aktualizacje

Zapoznaj się z artykułem [społeczności usługi Azure Media Services,](media-services-community.md) aby zobaczyć różne sposoby zadawania pytań, przekazywania opinii i otrzymywać aktualizacje dotyczące usługi Media Services.

## <a name="see-also"></a>Zobacz też

[Dynamiczne pakowanie](dynamic-packaging-overview.md)

## <a name="next-steps"></a>Następne kroki

[Zarządzanie punktami końcowymi przesyłania strumieniowego](manage-streaming-endpoints-howto.md)
