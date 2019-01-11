---
title: Skalowanie automatyczne i strefowo nadmiarowe Application Gateway na platformie Azure (publiczna wersja zapoznawcza)
description: Ten artykuł zawiera informacje dotyczące limitów rozmiarów żądania zapory aplikacji sieci web i listy wykluczeń w usłudze Application Gateway przy użyciu witryny Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 1/10/2019
ms.author: victorh
ms.openlocfilehash: f5885fd2ac76550990c9a56a1d200bbe11555918
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2019
ms.locfileid: "54213760"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>Skalowanie automatyczne i brama aplikacji strefowo nadmiarowy (publiczna wersja zapoznawcza)

Usługa Application Gateway i zapory aplikacji sieci Web (WAF) są teraz dostępne w publicznej wersji zapoznawczej w ramach nowej jednostki SKU v2, oferuje ulepszenia wydajności, która dodaje obsługę krytyczne nowe funkcje, takie jak skalowanie automatyczne, nadmiarowości strefy i obsługę statycznych adresów VIP. Istniejących funkcji w ramach jednostki SKU jest ogólnie dostępna w dalszym ciągu być obsługiwane w nowej jednostki SKU w wersji 2 z pewnymi wyjątkami, wymienione w sekcji znanych ograniczeń polecenia. Nowe jednostki SKU w wersji 2 obejmują następujące ulepszenia:

- **Skalowanie automatyczne**: Usługa Application Gateway i zapory aplikacji sieci Web wdrożenia w ramach jednostki SKU skalowania automatycznego można skalować w górę lub dół zależności od zmieniających się wzorców obciążenia ruchu. Dzięki skalowaniu automatycznemu nie trzeba również wybierać rozmiaru wdrożenia ani liczby wystąpień podczas aprowizowania usługi. Dzięki temu jednostka SKU oferuje elastyczność wartość true. W ramach nowej jednostki SKU bramy Application Gateway mogą działać zarówno w stałym pojemności (skalowanie automatyczne wyłączone), jak i w trybie z włączonym skalowaniem automatycznym. Tryb stałych pojemności jest przydatne w scenariuszach ze spójnego i przewidywalnego obciążenia. Tryb skalowania automatycznego jest korzystne w aplikacjach, które Zobacz mnóstwo wariancji w ruchu aplikacji.
   
   > [!NOTE]
   > Automatyczne skalowanie nie jest obecnie dostępna w ramach jednostki SKU zapory aplikacji sieci Web. Konfigurowanie zapory aplikacji sieci Web przy użyciu trybu stałych pojemności, zamiast trybu skalowania automatycznego.
- **Strefa nadmiarowości**: Wdrożenie usługi Application Gateway lub zapory aplikacji sieci Web może obejmować wielu strefach dostępności, usuwając konieczność do aprowizacji i pokrętła osobnych wystąpień bramy Application Gateway w każdej strefie przy użyciu usługi Traffic Manager. Możesz wybrać jedną strefę lub wieloma strefami wdrożonym wystąpień bramy Application Gateway, dlatego zapewnianie odporności błąd strefy. Pula zaplecza dla aplikacji można rozpowszechniać podobnie w różnych strefach dostępności.
- **Ulepszenia wydajności**: Skalowanie automatyczne, jednostki SKU oferty maksymalnie 5 X, lepsze SSL offload wydajność w porównaniu z ogólnie dostępnymi jednostkami SKU.
- **Krótszy czas wdrożenia i zaktualizuj** skalowania automatycznego jednostki SKU zapewnia krótszy czas wdrażania i aktualizacji w porównaniu z ogólnie dostępnymi jednostkami SKU.
- **Statyczny adres VIP**: Adres VIP bramy aplikacji obsługuje teraz typu statycznego adresu VIP wyłącznie. Dzięki temu adres VIP skojarzony z usługą Application Gateway nie zmienia się nawet po ponownym uruchomieniu.

> [!IMPORTANT]
> Jednostka SKU autoskalowanej i strefowo nadmiarowej bramy aplikacji jest aktualnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Obsługiwane regiony
Automatyczne skalowanie jednostek SKU jest dostępna w wschodnie stany USA 2, środkowe stany USA, zachodnie stany USA 2, północno-środkowe stany USA, zachodnie stany USA, południowo-środkowe stany USA, Francja środkowa, Europa Zachodnia, Europa Północna, zachodnie Zjednoczone Królestwo, Południowo-Wschodnia i Japonia, część wschodnia.

## <a name="pricing"></a>Cennik
W trakcie okresu zapoznawczego nie ma opłat. Opłata jest naliczana za zasobów innych niż bramy aplikacji, takich jak usługi Key Vault, maszyny wirtualne itp. 

## <a name="known-issues-and-limitations"></a>Znane problemy i ograniczenia

|Problem|Szczegóły|
|--|--|
|Certyfikat uwierzytelniania|Nieobsługiwane.<br>Aby uzyskać więcej informacji, zobacz [omówienie kompleksowej usługi SSL z usługą Application Gateway](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Mieszanie Standard_v2 i standardowa usługa Application Gateway w tej samej podsieci|Nieobsługiwane.<br>Ponadto jeśli włączono automatyczne skalowanie, jedną podsieć może mieć tylko jedną bramę aplikacji.|
|Zdefiniowaną przez użytkownika (UDR) w podsieci bramy aplikacji|Nieobsługiwane|
|Sieciowa grupa zabezpieczeń dla zakresu portów dla ruchu przychodzącego| -65200 do 65535, aby uzyskać Standard_v2 jednostki SKU<br>-65503 do 65534 dla standardowej jednostki SKU.<br>Aby uzyskać więcej informacji, zobacz [— często zadawane pytania](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Dzienniki wydajności w usłudze Diagnostyka Azure|Nieobsługiwane.<br>Metryki platformy Azure powinna być używana.|
|Rozliczenia|Nie ma obecnie nie rozliczeń.|
|Tryb FIPS, WebSocket|Te nie są obecnie obsługiwane.|
|Trybie tylko do wewnętrznego modułu równoważenia obciążenia|To nie jest obecnie obsługiwane. Publiczne i tryb wewnętrznego modułu równoważenia obciążenia ze sobą jest obsługiwany.|
|Skalowanie automatyczne zapory aplikacji sieci Web|Zapora aplikacji sieci Web nie obsługuje trybu skalowania automatycznego. Tryb stałych pojemności jest obsługiwany.|

## <a name="next-steps"></a>Kolejne kroki
- [Tworzenie automatycznego skalowania, strefa nadmiarowe usługa application gateway z zarezerwowanym wirtualnym adresem IP przy użyciu programu Azure PowerShell](tutorial-autoscale-ps.md)
- Dowiedz się więcej o [Application Gateway](overview.md).
- Dowiedz się więcej o [zapory usługi Azure](../firewall/overview.md). 

