---
title: Skalowanie automatyczne i strefowo nadmiarowa brama aplikacji (wersja 2)
description: W tym artykule przedstawiono jednostkę SKU Standard_v2 aplikacji azure i WAF_v2, która obejmuje skalowanie automatyczne i funkcje nadmiarowe strefy.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: victorh
ms.openlocfilehash: 7feb0f00c5431048d19d4ad6cb3860f6eb8ed052
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312702"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Skalowanie automatyczne i strefowo nadmiarowa brama aplikacji (wersja 2) 

Brama aplikacji i zapora aplikacji sieci Web (WAF) są również dostępne w Standard_v2 i WAF_v2 jednostce SKU. Jednostka SKU w wersji 2 oferuje ulepszenia wydajności i dodaje obsługę nowych krytycznych funkcji, takich jak skalowanie automatyczne, nadmiarowość stref i obsługa statycznych adresów VIP. Istniejące funkcje w ramach standardu i WAF SKU nadal są obsługiwane w nowej jednostce SKU w wersji 2, z kilkoma wyjątkami wymienionymi w sekcji [porównania.](#differences-with-v1-sku)

Nowa jednostka SKU w wersji 2 zawiera następujące ulepszenia:

- **Skalowanie automatyczne:** Wdrożenia bramy aplikacji lub WAF w ramach jednostki SKU skalowania automatycznego można skalować w górę lub w dół na podstawie zmiany wzorców obciążenia ruchu. Dzięki skalowaniu automatycznemu nie trzeba również wybierać rozmiaru wdrożenia ani liczby wystąpień podczas aprowizowania usługi. Ta jednostka SKU oferuje prawdziwą elastyczność. W Standard_v2 i WAF_v2 SKU brama aplikacji może działać zarówno w trybie o stałej pojemności (skalowanie automatyczne wyłączone), jak i w trybie skalowania automatycznego. Tryb stałej pojemności jest przydatny w scenariuszach z spójnymi i przewidywalnymi obciążeniami. Tryb skalowania automatycznego jest korzystny w aplikacjach, które widzą odchylenia w ruchu aplikacji.
- **Nadmiarowość stref:** Wdrożenie bramy aplikacji lub WAF może obejmować wiele stref dostępności, eliminując konieczność aprowizowania oddzielnych wystąpień bramy aplikacji w każdej strefie za pomocą Menedżera ruchu. Można wybrać pojedynczą strefę lub wiele stref, w których są wdrażane wystąpienia bramy aplikacji, co sprawia, że jest bardziej odporna na awarie strefy. Pula wewnętrznej bazy danych dla aplikacji może być podobnie rozłożona między strefami dostępności.

  Nadmiarowość stref jest dostępna tylko wtedy, gdy są dostępne strefy platformy Azure. W innych regionach wszystkie inne funkcje są obsługiwane. Aby uzyskać więcej informacji, zobacz [Co to są strefy dostępności na platformie Azure?](../availability-zones/az-overview.md#services-support-by-region)
- **Statyczne VIP:** SKU bramy aplikacji w wersji 2 obsługuje wyłącznie statyczny typ adresu VIP. Gwarantuje to, że adres VIP skojarzony z bramą aplikacji nie zmienia się w cyklu życia wdrożenia, nawet po ponownym uruchomieniu.  Nie ma statycznego adresu VIP w wersji 1, więc należy użyć adresu URL bramy aplikacji zamiast adresu IP dla routingu nazwy domeny do usług aplikacji za pośrednictwem bramy aplikacji.
- **Przepisanie nagłówka:** Brama aplikacji umożliwia dodawanie, usuwanie lub aktualizowanie nagłówków żądań i odpowiedzi HTTP za pomocą jednostki SKU w wersji 2. Aby uzyskać więcej informacji, zobacz [Przepisywanie nagłówków HTTP za pomocą bramy aplikacji](rewrite-http-headers.md)
- **Integracja magazynu kluczy:** Brama aplikacji w wersji 2 obsługuje integrację z magazynem kluczy dla certyfikatów serwera dołączonych do odbiorników obsługujących protokół HTTPS. Aby uzyskać więcej informacji, zobacz [Zakończenie protokołu TLS z certyfikatami Usługi Key Vault](key-vault-certs.md).
- **Kontroler transferu danych przychodzących usługi Azure Kubernetes: Kontroler**transferu danych przychodzących bramy aplikacji w wersji 2 umożliwia bramę aplikacji platformy Azure jako ruch przychodzący usługi Azure Kubernetes Service (AKS) znanej jako klaster AKS. Aby uzyskać więcej informacji, zobacz [Co to jest kontroler transferu danych przychodzących bramy aplikacji?](ingress-controller-overview.md).
- **Ulepszenia wydajności:** Jednostka SKU w wersji 2 oferuje do 5-krotnie lepszą wydajność odciążania TLS w porównaniu z jednostką SKU Standard/WAF.
- **Szybsze wdrażanie i czas aktualizacji** Jednostka SKU w wersji 2 zapewnia szybsze wdrożenie i czas aktualizacji w porównaniu do jednostki SKU standard/WAF. Obejmuje to również zmiany konfiguracji WAF.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Obsługiwane regiony

SKU Standard_v2 i WAF_v2 jest dostępna w następujących regionach: Północno-środkowe stany USA, Południowo-środkowe stany USA, Zachodnie stany USA, Zachodnie stany USA 2, Wschodnie stany USA, Wschodnie STANY USA 2, Środkowe STANY USA, Europa Północna, Europa Zachodnia, Azja Południowo-Wschodnia, Francja Środkowa, Wielka Brytania Zachodnia, Japonia Wschodnia, Japonia Wschodnia, Japonia Zachodnia, Australia Wschodnia, Brazylia Południowa, Kanada Środkowa, Kanada Wschodnia, Korea Wschodnia, Korea Środkowa, Korea Południowa , Wielka Brytania Południowa, Indie Środkowe, Indie Zachodnie, Indie Południowe.

## <a name="pricing"></a>Cennik

W przypadku jednostki SKU w wersji 2 model cenowy jest oparty na zużyciu i nie jest już dołączony do liczby wystąpień lub rozmiarów. Cennik SKU w wersji 2 ma dwa składniki:

- **Stała cena** — jest to cena godzinowa (lub częściowa) w celu udostępnienia Standard_v2 lub WAF_v2 bramy. Należy pamiętać, że 0 dodatkowych minimalnych wystąpień nadal zapewnia wysoką dostępność usługi, która jest zawsze wliczona w cenę ustaloną.
- **Cena jednostkowa pojemności** — jest to koszt oparty na zużyciu, który jest naliczany oprócz kosztu stałego. Opłata jednostkowa pojemności jest również obliczane co godzinę lub częściowo. Istnieją trzy wymiary jednostki pojemności - jednostka obliczeniowa, połączenia trwałe i przepływność. Jednostka obliczeniowa jest miarą zużywanej pojemności procesora. Czynniki wpływające na jednostkę obliczeniową to połączenia TLS/s, obliczenia ponownego zapisu adresu URL i przetwarzanie reguł WAF. Połączenie trwałe jest miarą nawiązanych połączeń TCP z bramą aplikacji w danym przedziale rozliczeniowym. Przepływność jest średnią megabitów/s przetwarzane przez system w danym przedziale rozliczeniowym.  Rozliczenia są wykonywane na poziomie jednostki pojemności dla niczego powyżej liczby wystąpień zarezerwowanych.

Każda jednostka pojemności składa się co najwyżej z: 1 jednostki obliczeniowej lub 2500 połączeń trwałych lub przepustowości 2,22 Mb/s.

Wskazówki dotyczące jednostek obliczeniowych:

- **Standard_v2** — każda jednostka obliczeniowa może łączyć się około 50 połączeń na sekundę za pomocą certyfikatu TLS klucza RSA 2048-bitowego.
- **WAF_v2** — każda jednostka obliczeniowa może obsługiwać około 10 równoczesnych żądań na sekundę dla 70-30% kombinacji ruchu z 70% żądań mniej niż 2 KB GET/POST i pozostają wyższe. Aktualnie rozmiar odpowiedzi nie ma wpływu na wydajność WAF.

> [!NOTE]
> Każde wystąpienie może obecnie obsługiwać około 10 jednostek pojemności.
> Liczba żądań, które może obsłużyć jednostka obliczeniowa, zależy od różnych kryteriów, takich jak rozmiar klucza certyfikatu TLS, algorytm wymiany kluczy, przepisywanie nagłówków oraz w przypadku rozmiaru żądania przychodzącego WAF. Zaleca się wykonywanie testów aplikacji w celu określenia szybkości żądania na jednostkę obliczeniową. Zarówno jednostka pojemności, jak i jednostka obliczeniowa zostaną udostępnione jako metryka przed rozpoczęciem rozliczeń.

W poniższej tabeli przedstawiono przykładowe ceny i służą wyłącznie do celów ilustracyjnych.

**Ceny w USA Wschód:**

|              Nazwa jednostki SKU                             | Stała cena ($/h)  | Pojemność Cena jednostkowa ($/CU-hr)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0,20             | 0.0080                          |
| WAF_v2                                            |    0.36             | 0.0144                          |

Aby uzyskać więcej informacji o cenach, zobacz [stronę cennika](https://azure.microsoft.com/pricing/details/application-gateway/). 

**Przykład 1**

Standard_v2 bramy aplikacji jest aprowizowana bez skalowania automatycznego w trybie ręcznego skalowania o stałej pojemności pięciu wystąpień.

Stała cena = 744(godziny) * $0.20 = $148.8 <br>
Jednostki pojemności = 744 (godziny) * 10 jednostek pojemności na wystąpienie * 5 wystąpień * $0.008 za godzinę jednostkową pojemności = $297.6

Całkowita cena = $148.8 + $297.6 = $446.4

**Przykład 2**

Standard_v2 bramy aplikacji jest aprowizowana przez miesiąc, z zerowym minimalnym wystąpieniem i w tym czasie otrzymuje 25 nowych połączeń TLS/s, średnio 8,88 Mb/s transferu danych. Zakładając, że połączenia są krótkotrwałe, twoja cena będzie:

Stała cena = 744(godziny) * $0.20 = $148.8

Cena jednostkowa pojemności = 744(godziny) * Max (jednostka obliczeniowa 25/50 dla połączeń/s, jednostka pojemności 8,88/2,22 dla przepustowości) * $0.008 = 744 * 4 * 0.008 = $23.81

Cena całkowita = $148.8+23.81 = $172.61

Jak widać, są rozliczane tylko dla czterech jednostek pojemności, a nie dla całego wystąpienia. 

> [!NOTE]
> Funkcja Max zwraca największą wartość w parze wartości.


**Przykład 3**

Standard_v2 bramy aplikacji jest aprowizowana przez miesiąc, z co najmniej pięcioma wystąpieniami. Zakładając, że nie ma ruchu i połączenia są krótkotrwałe, cena będzie:

Stała cena = 744(godziny) * $0.20 = $148.8

Cena jednostkowa pojemności = 744 (godziny) * Max (jednostka obliczeniowa 0/50 dla połączeń/s, jednostka pojemności 0/2,22 dla przepustowości) * $0.008 = 744 * 50 * 0.008 = $297.60

Cena całkowita = $148.80+297.60 = $446.4

W takim przypadku są rozliczane za całość z pięciu wystąpień, mimo że nie ma ruchu.

**Przykład 4**

Standard_v2 bramy aplikacji jest aprowizowana przez miesiąc, z co najmniej pięcioma wystąpieniami, ale tym razem jest średnio 125 Mbps transferu danych i 25 połączeń TLS na sekundę. Zakładając, że nie ma ruchu i połączenia są krótkotrwałe, cena będzie:

Stała cena = 744(godziny) * $0.20 = $148.8

Cena jednostkowa pojemności = 744(godziny) * Max (jednostka obliczeniowa 25/50 dla połączeń/s, jednostka pojemności 125/2,22 dla przepustowości) * $0.008 = 744 * 57 * 0.008 = $339.26

Cena całkowita = $148.80+339.26 = $488.06

W takim przypadku są rozliczane za pełne pięć wystąpień plus siedem jednostek pojemności (co stanowi 7/10 wystąpienia).  

**Przykład 5**

WAF_v2 bramy aplikacji jest aprowizowana przez miesiąc. W tym czasie otrzymuje 25 nowych połączeń TLS/s, średnio 8,88 Mb/s transferu danych i wykonuje 80 żądań na sekundę. Zakładając, że połączenia są krótkotrwałe, a obliczanie jednostki obliczeniowej dla aplikacji obsługuje 10 RPS na jednostkę obliczeniową, cena będzie wynosić:

Stała cena = 744(godziny) * $0.36 = $267.84

Cena jednostkowa pojemności = 744(godziny) * Max (jednostka obliczeniowa Max(25/50 dla połączeń/s, 80/10 WAF RPS), 8,88/2,22 jednostki pojemności dla przepustowości) * $0.0144 = 744 * 8 * 0.0144 = $85.71

Całkowita cena = $267.84 + $85.71 = $353.55

> [!NOTE]
> Funkcja Max zwraca największą wartość w parze wartości.

## <a name="scaling-application-gateway-and-waf-v2"></a>Skalowanie bramy aplikacji i waf w wersji 2

Brama aplikacji i WAF można skonfigurować do skalowania w dwóch trybach:

- **Skalowanie automatyczne** — po włączeniu skalowania automatycznego bramy aplikacji i jednostek SKU WAF v2 skalują się w górę lub w dół na podstawie wymagań ruchu aplikacji. Ten tryb oferuje lepszą elastyczność aplikacji i eliminuje konieczność odgadnięcia rozmiaru bramy aplikacji lub liczby wystąpień. Ten tryb umożliwia również zaoszczędzenie kosztów, nie wymagając od bramy uruchamiania z maksymalną pojemnością aprowizowana dla przewidywanego maksymalnego obciążenia ruchem. Należy określić minimalną i opcjonalnie maksymalną liczbę wystąpień. Minimalna pojemność gwarantuje, że brama aplikacji i WAF w wersji 2 nie spadają poniżej minimalnej liczby wystąpień określonych, nawet w przypadku braku ruchu. Każde wystąpienie liczy się jako 10 dodatkowych zarezerwowanych jednostek pojemności. Zero oznacza brak pojemności zarezerwowanej i ma charakter czysto automatyczny. Należy pamiętać, że zero dodatkowych minimalnych wystąpień nadal zapewnia wysoką dostępność usługi, która jest zawsze wliczona w cenę ustaloną. Można również opcjonalnie określić maksymalną liczbę wystąpień, co zapewnia, że brama aplikacji nie skaluje się poza określoną liczbę wystąpień. Nadal będą naliczane naliczane płatności za ilość ruchu obsługiwanego przez bramę. Liczba wystąpień może wynosić od 0 do 125. Wartość domyślna dla maksymalnej liczby wystąpień wynosi 20, jeśli nie określono.
- **Ręcznie** — można alternatywnie wybrać tryb ręczny, w którym brama nie będzie automatycznie skalować. W tym trybie, jeśli istnieje większy ruch niż brama aplikacji lub WAF może obsłużyć, może to spowodować utratę ruchu. W trybie ręcznym określanie liczby wystąpień jest obowiązkowe. Liczba wystąpień może się różnić od 1 do 125 wystąpień.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Porównanie funkcji między jednostką SKU w wersji 1 i sku w wersji 2

W poniższej tabeli porównano funkcje dostępne dla każdej jednostki SKU.

|                                                   | Jednostka SKU w wersji 1   | Jednostka SKU w wersji 2   |
| ------------------------------------------------- | -------- | -------- |
| Skalowanie automatyczne                                       |          | &#x2713; |
| Nadmiarowość stref                                   |          | &#x2713; |
| Statyczny VIP                                        |          | &#x2713; |
| Kontroler transferu danych przychodzących usługi Azure Kubernetes (AKS) |          | &#x2713; |
| Integracja magazynu kluczy Azure                       |          | &#x2713; |
| Przepisywanie nagłówków HTTP(S)                           |          | &#x2713; |
| Routing oparty na adresach URL                                 | &#x2713; | &#x2713; |
| Hostowanie wielu witryn                             | &#x2713; | &#x2713; |
| Przekierowanie ruchu                               | &#x2713; | &#x2713; |
| Zapora aplikacji internetowej                    | &#x2713; | &#x2713; |
| Reguły niestandardowe zapory aplikacji internetowej                                  |          | &#x2713; |
| Zakończenie usługi SSL (Transport Layer Security(TLS)/Secure Sockets Layer (SSL)            | &#x2713; | &#x2713; |
| Kompleksowe szyfrowanie TLS                         | &#x2713; | &#x2713; |
| Koligacja sesji                                  | &#x2713; | &#x2713; |
| Niestandardowe strony błędów                                | &#x2713; | &#x2713; |
| Obsługa protokołu WebSocket                                 | &#x2713; | &#x2713; |
| Obsługa protokołu HTTP/2                                    | &#x2713; | &#x2713; |
| Opróżnianie połączeń                               | &#x2713; | &#x2713; |

> [!NOTE]
> Skalowanie automatyczne w wersji 2 SKU obsługuje teraz [domyślne sondy kondycji,](application-gateway-probe-overview.md#default-health-probe) aby automatycznie monitorować kondycję wszystkich zasobów w puli zaplecza i wyróżniać te elementy członkowskie zaplecza, które są uważane za złej kondycji. Domyślna sonda kondycji jest automatycznie konfigurowana dla zaplecza, które nie mają żadnej niestandardowej konfiguracji sondy. Aby dowiedzieć się więcej, zobacz [sondy kondycji w bramie aplikacji](application-gateway-probe-overview.md).

## <a name="differences-with-v1-sku"></a>Różnice z sku w wersji 1

|Różnica|Szczegóły|
|--|--|
|Certyfikat uwierzytelniania|Bez pomocy technicznej.<br>Aby uzyskać więcej informacji, zobacz [Omówienie end-to end TLS z bramą aplikacji](ssl-overview.md#end-to-end-tls-with-the-v2-sku).|
|Mieszanie Standard_v2 i standardowej bramy aplikacji w tej samej podsieci|Nieobsługiwane|
|Trasa zdefiniowana przez użytkownika (UDR) w podsieci bramy aplikacji|Obsługiwane (konkretne scenariusze). W wersji zapoznawczej.<br> Aby uzyskać więcej informacji na temat obsługiwanych scenariuszy, zobacz [Omówienie konfiguracji bramy aplikacji](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet).|
|NSG dla zakresu portów przychodzących| - od 65200 do 65535 dla Standard_v2 SKU<br>- 65503 do 65534 dla standardowej jednostki SKU.<br>Aby uzyskać więcej informacji, zobacz często zadawane [pytania](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Dzienniki wydajności w diagnostyce platformy Azure|Bez pomocy technicznej.<br>Należy użyć metryk platformy Azure.|
|Rozliczenia|Rozliczenie planowane jest na 1 lipca 2019 r.|
|Tryb FIPS|Obecnie nie są one obsługiwane.|
|Tryb tylko ilb|Obecnie nie jest to obsługiwane. Tryb publiczny i ILB razem jest obsługiwany.|
|Integracja z netwatcher|Bez pomocy technicznej.|
|Integracja z usługą Azure Security Center|Nie jest jeszcze dostępna.

## <a name="migrate-from-v1-to-v2"></a>Migrowanie z wersji 1 do wersji 2

Skrypt programu Azure PowerShell jest dostępny w galerii programu PowerShell, aby ułatwić migrację z bramy aplikacji w wersji 1/WAF do jednostki SKU skalowania automatycznego w wersji 2. Ten skrypt ułatwia kopiowanie konfiguracji z bramy w wersji 1. Migracja ruchu jest nadal twoim obowiązkiem. Aby uzyskać więcej informacji, zobacz [Migrowanie bramy aplikacji platformy Azure z wersji 1 do wersji 2](migrate-v1-v2.md).

## <a name="next-steps"></a>Następne kroki

- [Szybki start: bezpośredni ruch internetowy w usłudze Azure Application Gateway — Azure Portal](quick-create-portal.md)
- [Tworzenie skalowania automatycznego, bramy aplikacji nadmiarowej strefy z zastrzeżonym wirtualnym adresem IP przy użyciu programu Azure PowerShell](tutorial-autoscale-ps.md)
- Dowiedz się więcej o [bramie aplikacji](overview.md).
- Dowiedz się więcej o [Zaporze platformy Azure](../firewall/overview.md).
