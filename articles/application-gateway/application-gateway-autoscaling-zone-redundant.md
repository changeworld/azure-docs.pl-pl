---
title: Skalowanie automatyczne i strefowo nadmiarowe Application Gateway na platformie Azure (publiczna wersja zapoznawcza)
description: W tym artykule przedstawiono SKU v2 aplikacji na platformie Azure, w tym funkcji skalowania automatycznego i strefowo nadmiarowe.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 3/6/2019
ms.author: victorh
ms.openlocfilehash: 4410dd9e61fe5b585ca5b245dbf33dbf8c38e701
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "59010230"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>Skalowanie automatyczne i brama aplikacji strefowo nadmiarowy (publiczna wersja zapoznawcza)

Usługa Application Gateway i zapory aplikacji sieci Web (WAF) są teraz dostępne w publicznej wersji zapoznawczej w ramach nowej jednostki SKU v2, oferuje ulepszenia wydajności, która dodaje obsługę krytyczne nowe funkcje, takie jak skalowanie automatyczne, nadmiarowości strefy i obsługę statycznych adresów VIP. Istniejących funkcji w ramach jednostki SKU jest ogólnie dostępna w dalszym ciągu być obsługiwane w nowej jednostki SKU w wersji 2 z pewnymi wyjątkami, wymienione w sekcji znanych ograniczeń polecenia. Nowe jednostki SKU w wersji 2 obejmują następujące ulepszenia:

- **Skalowanie automatyczne**: Usługa Application Gateway i zapory aplikacji sieci Web wdrożenia w ramach jednostki SKU skalowania automatycznego można skalować w górę lub dół zależności od zmieniających się wzorców obciążenia ruchu. Dzięki skalowaniu automatycznemu nie trzeba również wybierać rozmiaru wdrożenia ani liczby wystąpień podczas aprowizowania usługi. Ta jednostka SKU oferuje elastyczność wartość true. W ramach nowej jednostki SKU bramy Application Gateway mogą działać zarówno w stałym pojemności (skalowanie automatyczne wyłączone), jak i w trybie z włączonym skalowaniem automatycznym. Tryb stałych pojemności jest przydatne w scenariuszach ze spójnego i przewidywalnego obciążenia. Tryb skalowania automatycznego jest korzystne w aplikacjach, które Zobacz mnóstwo wariancji w ruchu aplikacji.

- **Strefa nadmiarowości**: Wdrożenie usługi Application Gateway lub zapory aplikacji sieci Web może obejmować wielu strefach dostępności, usuwając konieczność do aprowizacji i pokrętła osobnych wystąpień bramy Application Gateway w każdej strefie przy użyciu usługi Traffic Manager. Możesz wybrać jedną strefę lub wieloma strefami wdrożonym wystąpień bramy Application Gateway, dlatego zapewnianie odporności błąd strefy. Pula zaplecza dla aplikacji można rozpowszechniać podobnie w różnych strefach dostępności.
- **Ulepszenia wydajności**: Skalowanie automatyczne, jednostki SKU oferty maksymalnie 5 X, lepsze SSL offload wydajność w porównaniu z ogólnie dostępnymi jednostkami SKU.
- **Krótszy czas wdrożenia i zaktualizuj** skalowania automatycznego jednostki SKU zapewnia krótszy czas wdrażania i aktualizacji w porównaniu z ogólnie dostępnymi jednostkami SKU.
- **Statyczny adres VIP**: Adres VIP bramy aplikacji obsługuje teraz typu statycznego adresu VIP wyłącznie. Daje to gwarancję, że adres VIP skojarzony z usługą application gateway nie zmienia się nawet po ponownym uruchomieniu.

> [!IMPORTANT]
> Jednostka SKU autoskalowanej i strefowo nadmiarowej bramy aplikacji jest aktualnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

> [!NOTE]
> Skalowanie automatyczne i strefowo nadmiarowe usługa application gateway jednostkę SKU obsługuje teraz [domyślnej funkcji badania kondycji](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe) automatyczne monitorowanie kondycji wszystkich zasobów w puli zaplecza i usuwanie dowolnego zasobu z puli uznawana za złą. #Identyfikator sondy kondycji domyślne można automatycznie skonfigurować dla tych zaplecza, dla których nie skonfigurowano żadnej konfiguracji niestandardowej sondy. Aby dowiedzieć się więcej, zobacz [sondy kondycji w usłudze application gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Porównanie funkcji jednostki SKU v1 i v2 jednostki SKU

W poniższej tabeli porównano funkcje dostępne w każdej jednostki SKU.

|                                                   | Jednostka SKU V1   | Jednostka SKU w wersji 2   |
| ------------------------------------------------- | -------- | -------- |
| Skalowanie automatyczne                                       |          | &#x2713; |
| Nadmiarowości strefy                                   |          | &#x2713; |
| &nbsp;Statyczny adres VIP&nbsp;&nbsp;                      |          | &#x2713; |
| Routing oparty na adresach URL                                 | &#x2713; | &#x2713; |
| Hostowanie wielu witryn                             | &#x2713; | &#x2713; |
| Przekierowywanie ruchu                               | &#x2713; | &#x2713; |
| Zapora aplikacji internetowej                    | &#x2713; | &#x2713; |
| Kończenie żądań protokołu Secure Sockets Layer (SSL)            | &#x2713; | &#x2713; |
| End-to-end szyfrowania SSL                         | &#x2713; | &#x2713; |
| Koligacja sesji                                  | &#x2713; | &#x2713; |
| Niestandardowe strony błędów                                | &#x2713; | &#x2713; |
| Ponownie zapisuje nagłówki HTTP (S)                           |          | &#x2713; |
| Obsługa protokołu WebSocket                                 | &#x2713; | &#x2713; |
| Obsługa protokołu HTTP/2                                    | &#x2713; | &#x2713; |
| Opróżnianie połączeń                               | &#x2713; | &#x2713; |
| Usługa Azure kontrolera danych przychodzących rozwiązania Kubernetes Service (AKS) |          | &#x2713; |

## <a name="supported-regions"></a>Obsługiwane regiony

Automatyczne skalowanie jednostek SKU jest dostępna w następujących regionach: Północna środkowe stany USA, południowo-środkowe stany USA, zachodnie stany USA, zachodnie stany USA 2, wschodnie stany USA, wschodnie stany USA 2, środkowe stany USA, Europa Północna, Europa Zachodnia, Azja południowo-wschodnia, Francja środkowa, zachodnie Zjednoczone Królestwo, Japonia Wschodnia, Japonia, część zachodnia.

## <a name="pricing"></a>Cennik

W trakcie okresu zapoznawczego nie ma opłat. Opłaty naliczane są za zasobów innych niż bramy aplikacji, takich jak usługi Key Vault, maszyny wirtualne i tak dalej.

## <a name="known-issues-and-limitations"></a>Znane problemy i ograniczenia

|Problem|Szczegóły|
|--|--|
|Certyfikat uwierzytelniania|Nieobsługiwane.<br>Aby uzyskać więcej informacji, zobacz [omówienie kompleksowej usługi SSL z usługą Application Gateway](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Mieszanie Standard_v2 i standardowa usługa Application Gateway w tej samej podsieci|Nieobsługiwane|
|Zdefiniowaną przez użytkownika (UDR) w podsieci bramy aplikacji|Nieobsługiwane|
|Sieciowa grupa zabezpieczeń dla zakresu portów dla ruchu przychodzącego| -65200 do 65535, aby uzyskać Standard_v2 jednostki SKU<br>-65503 do 65534 dla standardowej jednostki SKU.<br>Aby uzyskać więcej informacji, zobacz [— często zadawane pytania](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Dzienniki wydajności w usłudze Diagnostyka Azure|Nieobsługiwane.<br>Metryki platformy Azure powinna być używana.|
|Rozliczenia|Nie ma obecnie nie rozliczeń.|
|Tryb FIPS|Te nie są obecnie obsługiwane.|
|Trybie tylko do wewnętrznego modułu równoważenia obciążenia|To nie jest obecnie obsługiwane. Publiczne i tryb wewnętrznego modułu równoważenia obciążenia ze sobą jest obsługiwany.|
|Sieć, obserwator integracji|Nie są obsługiwane w publicznej wersji zapoznawczej.|

## <a name="next-steps"></a>Kolejne kroki
- [Tworzenie automatycznego skalowania, strefa nadmiarowe usługa application gateway z zarezerwowanym wirtualnym adresem IP przy użyciu programu Azure PowerShell](tutorial-autoscale-ps.md)
- Dowiedz się więcej o [Application Gateway](overview.md).
- Dowiedz się więcej o [zapory usługi Azure](../firewall/overview.md).
