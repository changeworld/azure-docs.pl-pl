---
title: Skalowanie automatyczne i strefowo nadmiarowe Application Gateway na platformie Azure
description: W tym artykule przedstawiono Standard_v2 aplikacji platformy Azure i jednostki SKU, WAF_v2, która zawiera funkcje automatycznego skalowania i strefowo nadmiarowe.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 5/22/2019
ms.author: victorh
ms.openlocfilehash: 8e17c5e34ec3e2397c3054b1d0e0d97dbf410db2
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65986869"
---
# <a name="autoscaling-and-zone-redundant-application-gateway"></a>Skalowanie automatyczne i strefowo nadmiarowe Application Gateway 

Usługa Application Gateway i zapory aplikacji sieci Web (WAF) są również dostępne w obszarze Standard_v2 i WAF_v2 jednostki SKU. Jednostka SKU w wersji 2 oferuje ulepszenia wydajności i dodaje obsługę krytyczne nowe funkcje, takie jak skalowanie automatyczne, nadmiarowości strefy i obsługę statycznych adresów VIP. Istniejących funkcji w obszarze standardowa i jednostki SKU zapory aplikacji sieci Web w dalszym ciągu być obsługiwane w nowej jednostki SKU w wersji 2 z pewnymi wyjątkami, na liście [porównania](#differences-with-v1-sku) sekcji.

Nowe jednostki SKU w wersji 2 obejmuje następujące ulepszenia:

- **Skalowanie automatyczne**: Usługa Application Gateway i zapory aplikacji sieci Web wdrożenia w ramach jednostki SKU skalowania automatycznego można skalować w górę lub dół zależności od zmieniających się wzorców obciążenia ruchu. Dzięki skalowaniu automatycznemu nie trzeba również wybierać rozmiaru wdrożenia ani liczby wystąpień podczas aprowizowania usługi. Ta jednostka SKU oferuje elastyczność wartość true. Standard_v2 i jednostki SKU WAF_v2 usługa Application Gateway mogą działać zarówno w stałym pojemności (skalowanie automatyczne wyłączone), jak i w trybie z włączonym skalowaniem automatycznym. Tryb stałych pojemności jest przydatne w scenariuszach ze spójnego i przewidywalnego obciążenia. Tryb skalowania automatycznego jest korzystne w aplikacjach, które Zobacz wariancja w ruchu aplikacji.
- **Strefa nadmiarowości**: Usługa Application Gateway lub wdrażania zapory aplikacji sieci Web może obejmować wielu strefach dostępności konieczność aprowizowanie osobnych wystąpień bramy Application Gateway w każdej strefie przy użyciu usługi Traffic Manager. Możesz wybrać jedną strefę lub wieloma strefami wdrożonym wystąpień bramy Application Gateway, co czyni ją na bardziej odporne na awarie strefy. Pula zaplecza dla aplikacji można rozpowszechniać podobnie w różnych strefach dostępności.

  Nadmiarowości strefy jest dostępna tylko, której strefy platformy Azure są dostępne. W innych regionach wszystkie inne funkcje są obsługiwane. Aby uzyskać więcej informacji, zobacz [co to są strefy dostępności na platformie Azure?](../availability-zones/az-overview.md#services-support-by-region)
- **Statyczny adres VIP**: Application gateway w wersji 2 jednostki SKU obsługuje statycznego adresu VIP wpisz wyłącznie. Daje to gwarancję, że adres VIP skojarzony z usługą application gateway nie zmienia się do zarządzania cyklem życia wdrożenia, nawet po ponownym uruchomieniu.
- **Napisz ponownie nagłówek**: Usługa Application Gateway umożliwia dodać, usunąć lub zaktualizować nagłówki żądania i odpowiedzi HTTP z jednostką SKU v2. Aby uzyskać więcej informacji, zobacz [ponownie zapisuje nagłówki protokołu HTTP z usługą Application Gateway](rewrite-http-headers.md)
- **Integracji magazynu kluczy (wersja zapoznawcza)**: Brama aplikacji w wersji 2 obsługuje integrację z usługą Key Vault (w publicznej wersji zapoznawczej) dla serwera certyfikatów, które są dołączone do odbiorników obsługujące protokół HTTPS. Aby uzyskać więcej informacji, zobacz [kończenia żądań SSL z certyfikatami usługi Key Vault](key-vault-certs.md).
- **Usługa Azure Kubernetes Service kontroler danych przychodzących w (wersja zapoznawcza)**: Usługa Application Gateway kontroler danych przychodzących w wersji 2 umożliwia usługi Azure Application Gateway ma być używany jako transferu danych przychodzących dla usługi Azure Kubernetes Service (AKS) nazywany klastrem AKS. Aby uzyskać więcej informacji, zobacz [stronę z dokumentacją dotyczącą](https://azure.github.io/application-gateway-kubernetes-ingress/).
- **Ulepszenia wydajności**: V2, jednostki SKU oferty maksymalnie 5 X, lepsze SSL offload wydajność w porównaniu z jednostki SKU Standard/zapory aplikacji sieci Web.
- **Krótszy czas wdrożenia i zaktualizuj** jednostki SKU w wersji 2 oferuje skrócić czas wdrażania i aktualizacji w porównaniu do jednostki SKU Standard/zapory aplikacji sieci Web. Obejmuje to zmian w konfiguracji zapory aplikacji sieci Web.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Obsługiwane regiony

Standard_v2 i WAF_v2 jednostki SKU jest dostępna w następujących regionach: Północna środkowe stany USA, południowo-środkowe stany USA, zachodnie stany USA, zachodnie stany USA 2, wschodnie stany USA, wschodnie stany USA 2, środkowe stany USA, Europa Północna, Europa Zachodnia, Azja południowo-wschodnia, Francja środkowa, zachodnie Zjednoczone Królestwo, Japonia Wschodnia, Japonia, część zachodnia. W przyszłości zostaną dodane dodatkowe regiony.

## <a name="pricing"></a>Cennik

Z jednostką SKU v2 model rozliczania usług jest wymuszany przez użycie i nie jest już dołączony do wystąpienia liczby i rozmiarów. Cena jednostki SKU v2 ma dwa składniki:

- **Stała cena** — jest to co godzinę (lub niepełna godzina) cenę aprowizowanie Standard_v2 lub WAF_v2 bramy.
- **Cena jednostki pojemności** — jest to koszt na podstawie użycia, która jest rozliczana oprócz koszt stały. Opłata za jednostkę pojemności jest również obliczane co godzinę lub częściowe co godzinę. Istnieją trzy wymiary do jednostki pojemności — obliczeniowych jednostki, połączeń trwałych i przepływności. Jednostka to miara mocy procesora używane obliczeń. Czynniki wpływające na jednostek obliczeniowych są połączeń TLS na sekundę, ponowne zapisywanie adresów URL obliczeń i przetwarzanie reguł zapory aplikacji sieci Web. Trwałe połączenie jest miarą ustanowionych połączeń TCP do usługi application gateway w danym interwale rozliczeń. Przepływność jest średnia megabitów na sekundę przetwarzanych przez system w danym interwale rozliczeń.

Każda jednostka pojemności składa się z co najwyżej: 1 obliczeniowe o jednostkę lub połączeń trwałych 2500 lub 2.22 MB/s przepływności.

Obliczenia wskazówki jednostki:

- **Standard_v2** — każda jednostka obliczeniowa jest w stanie około 50 połączeń na sekundę przy użyciu certyfikatu TLS klucza szyfrowania RSA 2048-bitowych.
- **WAF_v2** — każdego obliczenia jednostki może obsługiwać około 10 równoczesnych żądań na sekundę dla 70 30% kombinacji ruchu przy użyciu 70% żądań w mniej niż 2 KB GET/POST, a pozostałe wyższy. Zapora aplikacji sieci Web jest nie wpływ na wydajność, rozmiar odpowiedzi obecnie.

> [!NOTE]
> Każde wystąpienie aktualnie obsługuje około 10 jednostek pojemności.
> Liczba żądań, które może obsłużyć jednostek obliczeniowych, zależy od różnych kryteriów, takich jak rozmiar klucza certyfikatu TLS, algorytm wymiany kluczy, ponownego nagłówka, a w przypadku przychodzących rozmiar żądania zapory aplikacji sieci Web. Firma Microsoft zaleca wykonywać testy aplikacji w celu ustalenia liczby żądań według jednostek obliczeniowych. Jednostka obliczeniowa i jednostki pojemności zostanie udostępniona jako metryki przed rozliczeń rozpoczyna się.

**Ceny w wschodnie stany USA**:

|              Nazwa jednostki SKU                             | Stała cena ($/ godz.)  | Cena pojemności ($/ CU-godz.)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0.20             | 0.0080                          |
| WAF_v2                                            |    0.36             | 0.0144                          |

Aby uzyskać więcej informacji o cenach, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/application-gateway/). Rozliczenia jest zaplanowane do uruchomienia w dniu 1 lipca 2019 r.

**Przykład 1**

Standard_v2 bramy aplikacji był zaopatrzony bez skalowania automatycznego w trybie skalowanie ręczne stałą wydajność pięć wystąpień.

Stała cena = 744(hours) * 0,20 USD = 148.8 $ <br>
Jednostki pojemności = 744 (godziny) 10 jednostki pojemności dla każdego wystąpienia * pięć wystąpień * $0.008 za godzinę używania jednostki pojemności = 297.6 $

Łączna cena = 148.8 $ + $297.6 = 446.4 $

**Przykład 2**

Standard_v2 Application Gateway jest przygotowany przez jeden miesiąc, a w tym czasie odbierze 25 nowe SSL połączeń na sekundę, średnia 8.88 MB/s transferu danych. Przy założeniu, że połączenia są krótkie krótkotrwałe, będzie cen:

Stała cena = 744(hours) * 0,20 USD = 148.8 $

Cena jednostki pojemności = 744(hours) * Max (jednostka obliczeniowa 25/50 dla połączeń na sekundę, 8.88/2.22 pojemność jednostki przepływności) * $0.008 = 744 * 4 * 0.008 = 23.81 $

Łączna cena = $148. 23.81 8 + = $172.61

**Przykład 3**

WAF_v2 bramy aplikacji są aprowizowane w miesiącu. W tym czasie odbierze 25 nowe SSL połączeń na sekundę, średnia 8.88 MB/s transferu danych i jest 80 żądań na sekundę. Przy założeniu, że połączenia są krótkie krótkotrwałe, a cen obliczania jednostki obliczeniowej dla aplikacji obsługiwane przez 10 RPS poszczególnych jednostek obliczeniowych, będzie:

Stała cena = 744(hours) * $0.36 = 267.84 $

Cena jednostki pojemności = 744(hours) * Max (Max(25/50 for connections/sec, 80/10 WAF RPS) jednostek obliczeniowych, 8.88/2.22 pojemność jednostki przepływności) * $0.0144 = 744 * 8 * 0.0144 = 85.71 $

Łączna cena = 267.84 $ + $85.71 = 353.55 $

## <a name="scaling-application-gateway-and-waf-v2"></a>Skalowanie usługa Application Gateway i zapory aplikacji sieci Web w wersji 2

Usługa Application Gateway i zapory aplikacji sieci Web można skonfigurować do skalowania w dwóch trybach:

- **Skalowanie automatyczne** — z włączonym skalowaniem automatycznym, usługa Application Gateway i zapory aplikacji sieci Web w wersji 2, jednostki SKU skalować w górę lub w dół w zależności od wymagań ruchu aplikacji. W tym trybie oferuje lepsze elastyczność aplikacji i eliminuje potrzebę odgadnięcia licznik rozmiaru lub wystąpienia bramy aplikacji. Ten tryb umożliwia obniżenie kosztów przez nie wymaga, by było uruchamiane bram szczytu aprowizowana pojemność dla przewidywanego ruchu maksymalnego obciążenia. Klienci muszą określać minimalną i opcjonalnie maksymalną liczbą wystąpień. Pojemności minimalnej gwarantuje, że usługa Application Gateway i zapory aplikacji sieci Web w wersji 2 nie spadnie poniżej minimalnej liczby wystąpień określony, nawet przy braku ruchu sieciowego. Opłata jest naliczana dla tej minimalnej pojemności, nawet w przypadku braku dowolnego ruchu. Możesz również opcjonalnie określić maksymalną liczbę wystąpień, który zapewnia, że Application Gateway nie skalować poza określoną liczbę wystąpień. Nadal będzie naliczana ilości ruch przez bramę. Liczbę wystąpień może należeć do zakresu od 0 do 125. Domyślna wartość maksymalna liczba wystąpień to 20, jeśli nie określono.
- **Ręczne** — może też ręcznego trybu, w których brama nie będzie skalowania automatycznego. W tym trybie Jeśli istnieje więcej ruchu niż co usługa Application Gateway i zapory aplikacji sieci Web może obsługiwać, jego może spowodować utratę ruchu. W trybie ręczne określenie liczby wystąpień jest obowiązkowy. Liczba wystąpień może się różnić od 1 do 125 wystąpień.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Porównanie funkcji jednostki SKU v1 i v2 jednostki SKU

W poniższej tabeli porównano funkcje dostępne w każdej jednostki SKU.

|                                                   | Jednostka SKU V1   | Jednostka SKU w wersji 2   |
| ------------------------------------------------- | -------- | -------- |
| Skalowanie automatyczne                                       |          | &#x2713; |
| Nadmiarowości strefy                                   |          | &#x2713; |
| Statyczny adres VIP                                        |          | &#x2713; |
| Usługa Azure kontrolera danych przychodzących rozwiązania Kubernetes Service (AKS) |          | &#x2713; |
| Integracja magazynu kluczy Azure                       |          | &#x2713; |
| Ponownie zapisuje nagłówki HTTP (S)                           |          | &#x2713; |
| Routing oparty na adresach URL                                 | &#x2713; | &#x2713; |
| Hostowanie wielu witryn                             | &#x2713; | &#x2713; |
| Przekierowywanie ruchu                               | &#x2713; | &#x2713; |
| Zapora aplikacji internetowej                    | &#x2713; | &#x2713; |
| Kończenie żądań protokołu Secure Sockets Layer (SSL)            | &#x2713; | &#x2713; |
| End-to-end szyfrowania SSL                         | &#x2713; | &#x2713; |
| Koligacja sesji                                  | &#x2713; | &#x2713; |
| Niestandardowe strony błędów                                | &#x2713; | &#x2713; |
| Obsługa protokołu WebSocket                                 | &#x2713; | &#x2713; |
| Obsługa protokołu HTTP/2                                    | &#x2713; | &#x2713; |
| Opróżnianie połączenia                               | &#x2713; | &#x2713; |

> [!NOTE]
> Skalowanie automatyczne w wersji 2, obsługuje teraz jednostki SKU [sondy kondycji domyślne](application-gateway-probe-overview.md#default-health-probe) automatyczne monitorowanie kondycji wszystkich zasobów w puli zaplecza i wyróżnianie tych członków wewnętrznej bazy danych, które jest uznawana za złą. Domyślna sonda kondycji jest automatycznie konfigurowany dla zaplecza, które nie mają żadnej konfiguracji niestandardowej sondy. Aby dowiedzieć się więcej, zobacz [sondy kondycji w usłudze application gateway](application-gateway-probe-overview.md).

## <a name="differences-with-v1-sku"></a>Różnice z jednostką SKU v1

|Różnica|Szczegóły|
|--|--|
|Certyfikat uwierzytelniania|Nieobsługiwane.<br>Aby uzyskać więcej informacji, zobacz [omówienie kompleksowej usługi SSL z usługą Application Gateway](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Mieszanie Standard_v2 i standardowa usługa Application Gateway w tej samej podsieci|Nieobsługiwane|
|Zdefiniowaną przez użytkownika (UDR) w podsieci bramy aplikacji|Nieobsługiwane|
|Sieciowa grupa zabezpieczeń dla zakresu portów dla ruchu przychodzącego| -65200 do 65535, aby uzyskać Standard_v2 jednostki SKU<br>-65503 do 65534 dla standardowej jednostki SKU.<br>Aby uzyskać więcej informacji, zobacz [— często zadawane pytania](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Dzienniki wydajności w usłudze Diagnostyka Azure|Nieobsługiwane.<br>Metryki platformy Azure powinna być używana.|
|Informacje billingowe|Rozliczenia zaplanowane do uruchomienia w dniu 1 lipca 2019 r.|
|Tryb FIPS|Te nie są obecnie obsługiwane.|
|Trybie tylko do wewnętrznego modułu równoważenia obciążenia|To nie jest obecnie obsługiwane. Publiczne i tryb wewnętrznego modułu równoważenia obciążenia ze sobą jest obsługiwany.|
|Sieć, obserwator integracji|Nieobsługiwane.|
|Integracja z Centrum pomocy technicznej platformy Azure|Nie jest jeszcze dostępna.

## <a name="next-steps"></a>Kolejne kroki

- [Szybki start: Ruchem internetowym bezpośredniego przy użyciu usługi Azure Application Gateway — witryna Azure portal](quick-create-portal.md)
- [Tworzenie automatycznego skalowania, strefa nadmiarowe usługa application gateway z zarezerwowanym wirtualnym adresem IP przy użyciu programu Azure PowerShell](tutorial-autoscale-ps.md)
- Dowiedz się więcej o [Application Gateway](overview.md).
- Dowiedz się więcej o [zapory usługi Azure](../firewall/overview.md).