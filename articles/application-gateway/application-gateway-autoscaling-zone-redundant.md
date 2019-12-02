---
title: Skalowanie automatyczne i strefowo nadmiarowa brama aplikacji (wersja 2)
description: W tym artykule wprowadzono Standard_v2 aplikacji platformy Azure i WAF_v2 jednostki SKU, które obejmują funkcję skalowania automatycznego i strefowo nadmiarowe.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/09/2019
ms.author: victorh
ms.openlocfilehash: 66978f313f5cb3881f8befc61289d7de0f4214cb
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668147"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Skalowanie automatyczne i strefowo nadmiarowa brama aplikacji (wersja 2) 

Application Gateway i Zapora aplikacji sieci Web (WAF) są również dostępne w ramach Standard_v2 i WAF_v2 jednostki SKU. Jednostka SKU v2 oferuje ulepszenia wydajności i dodaje obsługę krytycznych nowych funkcji, takich jak Skalowanie automatyczne, nadmiarowość stref i obsługa statycznych adresów VIP. Istniejące funkcje w ramach jednostki SKU w warstwie Standardowa i WAF nadal są obsługiwane w nowej jednostce SKU v2 z kilkoma wyjątkami wymienionymi w sekcji [porównanie](#differences-with-v1-sku) .

Nowa jednostka SKU w wersji 2 obejmuje następujące udoskonalenia:

- **Skalowanie**automatyczne: wdrożenia Application Gateway lub WAF w ramach jednostki SKU skalowania automatycznego można skalować w górę lub w dół w zależności od zmiany wzorców obciążenia ruchu sieciowego. Dzięki skalowaniu automatycznemu nie trzeba również wybierać rozmiaru wdrożenia ani liczby wystąpień podczas aprowizowania usługi. Ta jednostka SKU oferuje prawdziwą elastyczność. W Standard_v2 i WAF_v2 jednostki SKU Application Gateway mogą działać zarówno w stałej pojemności (wyłączone Skalowanie automatyczne), jak i w trybie włączonym skalowaniem automatycznym. Tryb stałej wydajności jest przydatny w scenariuszach z spójnymi i przewidywalnymi obciążeniami. Tryb skalowania automatycznego jest korzystny w aplikacjach, które zobaczą wariancję ruchu aplikacji.
- **Nadmiarowość stref**: wdrożenie Application Gateway lub WAF może obejmować wiele strefy dostępności, eliminując konieczność udostępniania oddzielnych wystąpień Application Gateway w każdej strefie za pomocą Traffic Manager. Można wybrać jedną strefę lub wiele stref, w których są wdrożone Application Gateway wystąpienia, co sprawia, że jest to bardziej odporne na awarie strefy. Pula zaplecza dla aplikacji może być podobnie dystrybuowana w strefach dostępności.

  Nadmiarowość stref jest dostępna tylko w przypadku, gdy dostępne są strefy platformy Azure. W innych regionach obsługiwane są wszystkie inne funkcje. Aby uzyskać więcej informacji, zobacz [co to jest strefy dostępności na platformie Azure?](../availability-zones/az-overview.md#services-support-by-region)
- **Statyczny adres VIP**: jednostka SKU Application Gateway v2 obsługuje wyłącznie statyczny typ adresu VIP. Dzięki temu wirtualne adresy IP skojarzone z bramą aplikacji nie ulegają zmianie w cyklu życia wdrożenia nawet po ponownym uruchomieniu.  W wersji 1 nie ma statycznego adresu VIP, dlatego do App Services za pośrednictwem bramy aplikacji należy użyć adresu URL usługi Application Gateway zamiast adresu IP.
- Ponowne **Zapisywanie nagłówka**: Application Gateway umożliwia dodawanie, usuwanie lub aktualizowanie nagłówków żądań i odpowiedzi HTTP z jednostką SKU v2. Aby uzyskać więcej informacji, zobacz [Zapisywanie nagłówków HTTP przy użyciu Application Gateway](rewrite-http-headers.md)
- **Integracja Key Vault (wersja zapoznawcza)** : Application Gateway v2 obsługuje integrację z usługą Key Vault (w publicznej wersji zapoznawczej) dla certyfikatów serwera, które są dołączone do odbiorników z obsługą protokołu HTTPS. Aby uzyskać więcej informacji, zobacz [kończenie połączeń SSL z certyfikatami Key Vault](key-vault-certs.md).
- Kontroler transferu danych w **usłudze Azure Kubernetes (wersja zapoznawcza)** : kontroler transferu danych przychodzących Application Gateway v2 umożliwia korzystanie z Application Gateway platformy Azure jako ruchu przychodzącego dla usługi Azure Kubernetes Service (AKS) znanej jako klaster AKS. Aby uzyskać więcej informacji, zobacz [stronę dokumentacji](https://azure.github.io/application-gateway-kubernetes-ingress/).
- **Ulepszenia wydajności**: jednostka SKU v2 oferuje maksymalnie pięciokrotną lepszą wydajność odciążania protokołu SSL w porównaniu z jednostką SKU Standard/WAF.
- **Szybsze wdrażanie i aktualizowanie czasu** Jednostka SKU v2 zapewnia szybszy czas wdrożenia i aktualizacji w porównaniu do jednostki SKU Standard/WAF. Obejmuje to również zmiany konfiguracji WAF.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Obsługiwane regiony

Jednostka SKU Standard_v2 i WAF_v2 jest dostępna w następujących regionach: Północno-środkowe stany USA, Południowo-środkowe stany USA, zachodnie stany USA, zachodnie Stany Zjednoczone 2, Wschodnie stany USA, USA 2, środkowe stany USA, Europa Północna, Europa Zachodnia, Azja Południowo-Wschodnia, Francja Zachodnie Zjednoczone Królestwo środkowa, Stany Zjednoczone, Japonia Wschodnia, Japonia Zachodnia, Australia Wschodnia , Australia Południowo-Wschodnia, Brazylia Południowa, Kanada środkowa, Kanada Wschodnia, Azja Wschodnia, Korea środkowa, Korea Południowa, Południowe Zjednoczone Królestwo, Indie Środkowe, Indie Zachodnie i Indie Południowe.

## <a name="pricing"></a>Cennik

W przypadku jednostki SKU v2 model cenowy jest oparty na zużyciu i nie jest już dołączony do liczby wystąpień ani rozmiarów. Cennik wersji 2 jednostki SKU ma dwa składniki:

- **Stała cena** — cena godzinowa (lub częściowa godzina) do aprowizacji Standard_v2 lub WAF_v2 bramy. Należy pamiętać, że 0 dodatkowe wystąpienia w dalszym ciągu zapewniają wysoką dostępność usługi, która jest zawsze dostępna ze stałą ceną.
- **Cena jednostkowa wydajności** — koszt oparty na zużyciu jest naliczany wraz z kosztem stałym. Opłata za jednostkę wydajności jest też obliczana godzinowo lub częściowo godzinowo. Jednostka wydajności ma trzy wymiary: jednostkę obliczeniową, połączenia trwałe i przepływność. Jednostka obliczeniowa jest miarą użycia wydajności procesora. Czynniki wpływające na jednostkę obliczeniową to połączenia TLS/s, obliczenia ponownego zapisu adresów URL i przetwarzanie reguł WAF. Połączenie trwałe to miara ustanowionych połączeń TCP z bramą aplikacji w danym interwale rozliczeniowym. Przepływność jest średnimi Megabitami/s przetworzonymi przez system w danym interwale rozliczeniowym.  Rozliczenia odbywają się na poziomie jednostki pojemności dla wszystkiego powyżej liczby wystąpień zarezerwowanych.

Każda jednostka pojemności składa się z maksymalnie: 1 jednostki obliczeniowej lub 2500 połączeń trwałych lub przepływności 2,22 MB/s.

Wskazówki dotyczące jednostek obliczeniowych:

- **Standard_v2** — każda jednostka obliczeniowa jest w stanie około 50 połączeń na sekundę z certyfikatem klucza RSA 2048-bitowym.
- **WAF_v2** — każda jednostka obliczeniowa może obsłużyć około 10 współbieżnych żądań na sekundę w przypadku 70-30% ruchu z 70% żądań mniejszych niż 2 KB Get/post i pozostałych. Obecnie nie ma to wpływ na wydajność WAF.

> [!NOTE]
> Każde wystąpienie może obecnie obsługiwać około 10 jednostek pojemności.
> Liczba żądań, które może obsłużyć jednostka obliczeniowa, zależy od różnych kryteriów, takich jak rozmiar klucza certyfikatu TLS, algorytm wymiany kluczy, ponowne zapisywanie nagłówka i wielkość żądania przychodzącego WAF. Zalecamy przeprowadzanie testów aplikacji w celu określenia liczby żądań na jednostkę obliczeniową. Jednostka pojemności i jednostka obliczeniowa zostaną udostępnione jako Metryka przed rozpoczęciem rozliczania.

W poniższej tabeli przedstawiono przykładowe ceny i służą tylko do celów ilustracyjnych.

**Cennik w regionie Wschodnie stany USA**:

|              Nazwa jednostki SKU                             | Stała cena ($/godz.)  | Cena jednostkowa wydajności ($/CU-hr)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0,20             | 0,0080                          |
| WAF_v2                                            |    0,36             | 0,0144                          |

Aby uzyskać więcej informacji o cenach, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/application-gateway/). Opłaty są naliczane według harmonogramu od 1 lipca 2019.

**Przykład 1**

Standard_v2 Application Gateway jest inicjowana bez skalowania automatycznego w trybie skalowania ręcznego ze stałą pojemnością pięciu wystąpień.

Stała cena = 744 (godziny) * $0,20 = $148,8 <br>
Jednostki pojemności = 744 (godz.) * 10 jednostek wydajności na wystąpienie * 5 wystąpień * $0,008 za godzinę jednostki pojemności = $297,6

Łączna cena = $148,8 + $297,6 = $446,4

**Przykład 2**

Application Gateway standard_v2 jest inicjowana przez miesiąc, z zerowymi wystąpieniami i w tym momencie otrzymuje 25 nowych połączeń SSL/s, średnia z 8,88-MB/s transfer danych. Przy założeniu, że połączenia są krótkotrwałe, cena będzie:

Stała cena = 744 (godziny) * $0,20 = $148,8

Cena jednostkowa wydajności = 744 (godz.) * Max (25/50 jednostek obliczeniowych dla połączeń/s, jednostka pojemności 8.88/2.22 na potrzeby przepływności) * $0,008 = 744 * 4 * 0,008 = $23,81

Łączna cena = $148.8 + 23.81 = $172,61

Jak widać, opłaty są naliczane tylko za cztery jednostki pojemności, a nie dla całego wystąpienia. 

> [!NOTE]
> Funkcja Max zwraca największą wartość w parze wartości.


**Przykład 3**

Application Gateway standard_v2 jest inicjowana przez miesiąc z co najmniej pięcioma wystąpieniami. Przy założeniu, że ruch i połączenia są krótkotrwałe, cena będzie następująca:

Stała cena = 744 (godziny) * $0,20 = $148,8

Cena jednostki wydajności = 744 (godz.) * Max (0/50 jednostek obliczeniowych dla połączeń/s, 0/2.22 Jednostka pojemności dla przepływności) * $0,008 = 744 * 50 * 0,008 = $297,60

Łączna cena = $148.80 + 297.60 = $446,4

W tym przypadku opłaty są naliczane w całości z pięciu wystąpień nawet wtedy, gdy nie ma żadnego ruchu.

**Przykład 4**

Application Gateway standard_v2 jest inicjowana przez miesiąc, z co najmniej pięcioma wystąpieniami, ale ten czas jest średnim 125-MB/s i 25 połączeniami SSL na sekundę. Przy założeniu, że ruch i połączenia są krótkotrwałe, cena będzie następująca:

Stała cena = 744 (godziny) * $0,20 = $148,8

Cena jednostkowa wydajności = 744 (godz.) * Max (25/50 jednostek obliczeniowych dla połączeń/s, jednostka pojemności 125/2.22 dla przepływności) * $0,008 = 744 * 57 * 0,008 = $339,26

Łączna cena = $148.80 + 339.26 = $488,06

W tym przypadku opłaty są naliczane za całe pięć wystąpień oraz siedem jednostek pojemności (czyli 7/10 wystąpienia).  

**Przykład 5**

WAF_v2 Application Gateway jest inicjowany przez miesiąc. W tym czasie otrzymujesz 25 nowych połączeń SSL/s, średnia z 8,88-MB/s transmisji danych i wysyła żądania 80 na sekundę. Przy założeniu, że połączenia są krótkotrwałe i obliczenia jednostek obliczeniowych dla aplikacji obsługują 10 RPS pliku na jednostkę obliczeniową, cena będzie:

Stała cena = 744 (godziny) * $0,36 = $267,84

Cena jednostkowa wydajności = 744 (godz.) * Max (maksymalna liczba jednostek obliczeniowych (25/50 dla połączeń/s, 80/10 WAF RPS pliku), 8.88/2.22 jednostki pojemności dla przepływności) * $0,0144 = 744 * 8 * 0,0144 = $85,71

Łączna cena = $267,84 + $85,71 = $353,55

> [!NOTE]
> Funkcja Max zwraca największą wartość w parze wartości.

## <a name="scaling-application-gateway-and-waf-v2"></a>Skalowanie Application Gateway i WAF v2

Application Gateway i WAF można skonfigurować do skalowania w dwóch trybach:

- **Skalowanie** automatyczne — z włączonym skalowaniem automatycznym jednostki SKU Application Gateway i WAF v2 są skalowane w górę lub w dół na podstawie wymagań dotyczących ruchu aplikacji. Ten tryb oferuje lepszą elastyczność aplikacji i eliminuje konieczność odgadnięcia rozmiaru bramy aplikacji lub liczby wystąpień. Ten tryb pozwala także zaoszczędzić koszt przez niewymaganie, aby brama działała na szczytowej pojemności dla przewidywanego maksymalnego obciążenia ruchem. Należy określić minimalną i opcjonalną maksymalną liczbę wystąpień. Minimalna pojemność zapewnia, że Application Gateway i WAF v2 nie spadnie poniżej minimalnej określonej liczby wystąpień, nawet w przypadku braku ruchu. Każde wystąpienie liczy się jako 10 dodatkowych jednostek pojemności zarezerwowanych. Zero oznacza brak zarezerwowanej pojemności i ma charakter wyłącznie Skalowanie automatyczne. Należy pamiętać, że zero dodatkowych wystąpień w dalszym ciągu zapewnia wysoką dostępność usługi, która jest zawsze dostępna ze stałą ceną. Opcjonalnie można również określić maksymalną liczbę wystąpień, która zapewnia, że Application Gateway nie będzie skalowana poza określoną liczbę wystąpień. Opłaty są naliczane za ilość ruchu obsługiwanego przez bramę. Liczba wystąpień może być z zakresu od 0 do 125. Wartość domyślna maksymalna liczba wystąpień to 20, jeśli nie zostanie określona.
- **Ręczne** — możesz alternatywnie wybrać tryb ręczny, w którym Brama nie będzie automatycznie skalowana. W tym trybie, jeśli ruch jest większy niż obsługiwane Application Gateway lub WAF, może to spowodować utratę ruchu. W trybie ręcznym określenie liczby wystąpień jest obowiązkowe. Liczba wystąpień może się różnić od 1 do 125 wystąpień.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Porównanie funkcji między jednostką SKU V1 i jednostką SKU v2

Poniższa tabela zawiera porównanie funkcji dostępnych w poszczególnych jednostkach SKU.

|                                                   | jednostka SKU w wersji 1   | jednostka SKU v2   |
| ------------------------------------------------- | -------- | -------- |
| Skalowanie automatyczne                                       |          | &#x2713; |
| Nadmiarowość stref                                   |          | &#x2713; |
| Statyczny adres VIP                                        |          | &#x2713; |
| Kontroler danych przychodzących usługi Azure Kubernetes Service (AKS) |          | &#x2713; |
| Integracja magazynu kluczy Azure                       |          | &#x2713; |
| Zapisz ponownie nagłówki HTTP (S)                           |          | &#x2713; |
| Routing oparty na adresach URL                                 | &#x2713; | &#x2713; |
| Hostowanie wielu witryn                             | &#x2713; | &#x2713; |
| Przekierowywanie ruchu                               | &#x2713; | &#x2713; |
| Zapora aplikacji internetowej (WAF)                    | &#x2713; | &#x2713; |
| Reguły niestandardowe zapory aplikacji internetowej                                  |          | &#x2713; |
| Kończenie żądań protokołu Secure Sockets Layer (SSL)            | &#x2713; | &#x2713; |
| Kompleksowe szyfrowanie SSL                         | &#x2713; | &#x2713; |
| Koligacja sesji                                  | &#x2713; | &#x2713; |
| Niestandardowe strony błędów                                | &#x2713; | &#x2713; |
| Obsługa protokołu WebSocket                                 | &#x2713; | &#x2713; |
| Obsługa protokołu HTTP/2                                    | &#x2713; | &#x2713; |
| Opróżnianie połączeń                               | &#x2713; | &#x2713; |

> [!NOTE]
> Jednostka SKU skalowania w wersji 2 obsługuje teraz [domyślne sondy kondycji](application-gateway-probe-overview.md#default-health-probe) w celu automatycznego monitorowania kondycji wszystkich zasobów w puli zaplecza oraz wyróżniania tych członków zaplecza, które są uznawane za złej kondycji. Domyślna sonda kondycji jest automatycznie konfigurowana dla frontonów, które nie mają niestandardowej konfiguracji sondowania. Aby dowiedzieć się więcej, zobacz [sondy kondycji w usłudze Application Gateway](application-gateway-probe-overview.md).

## <a name="differences-with-v1-sku"></a>Różnice w wersji 1 SKU

|Występują|Szczegóły|
|--|--|
|Certyfikat uwierzytelniania|Nieobsługiwane.<br>Aby uzyskać więcej informacji, zobacz [Omówienie kompleksowej usługi SSL z Application Gateway](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Mieszanie Standard_v2 i Application Gateway standardowych w tej samej podsieci|Brak obsługi|
|Trasa zdefiniowana przez użytkownika (UDR) w podsieci Application Gateway|Brak obsługi|
|SIECIOWEJ grupy zabezpieczeń dla zakresu portów przychodzących| -65200 do 65535 dla Standard_v2 jednostki SKU<br>-65503 do 65534 dla standardowej jednostki SKU.<br>Aby uzyskać więcej informacji, zobacz [często zadawane pytania](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Dzienniki wydajności w usłudze Diagnostyka Azure|Nieobsługiwane.<br>Należy używać metryk platformy Azure.|
|Rozliczenia|Rozliczenia zaplanowane do rozpoczęcia od 1 lipca 2019.|
|Tryb FIPS|Te nie są obecnie obsługiwane.|
|Tryb tylko ILB|Nie jest to obecnie obsługiwane. Tryb publiczny i ILB razem są obsługiwane.|
|Integracja z programem webwatch|Nieobsługiwane.|
|Integracja Azure Security Center|Jeszcze niedostępne.

## <a name="migrate-from-v1-to-v2"></a>Migrowanie z wersji 1 do wersji 2

Skrypt Azure PowerShell jest dostępny w galerii programu PowerShell, aby ułatwić migrację z poziomu wersji V1 Application Gateway/WAF do jednostki SKU skalowania automatycznego w wersji 2. Ten skrypt ułatwia skopiowanie konfiguracji z bramy v1. Migracja ruchu nadal jest odpowiedzialna. Aby uzyskać więcej informacji, zobacz [Migrowanie platformy Azure Application Gateway z wersji 1 do wersji 2](migrate-v1-v2.md).

## <a name="next-steps"></a>Następne kroki

- [Szybki Start: bezpośredni ruch internetowy za pomocą usługi Azure Application Gateway — Azure Portal](quick-create-portal.md)
- [Tworzenie automatycznego skalowania, nadmiarowej bramy aplikacji z zastrzeżonym wirtualnym adresem IP przy użyciu Azure PowerShell](tutorial-autoscale-ps.md)
- Dowiedz się więcej o [Application Gateway](overview.md).
- Dowiedz się więcej o [zaporze platformy Azure](../firewall/overview.md).
