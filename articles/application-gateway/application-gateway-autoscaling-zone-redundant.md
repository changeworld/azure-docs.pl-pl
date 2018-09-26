---
title: Skalowanie automatyczne i strefowo nadmiarowe Application Gateway na platformie Azure (publiczna wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące limitów rozmiarów żądania zapory aplikacji sieci web i listy wykluczeń w usłudze Application Gateway przy użyciu witryny Azure portal.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 09/26/2018
ms.author: victorh
ms.openlocfilehash: ab1c9405042de02183b8742fa940a3a5a482923a
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165233"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>Skalowanie automatyczne i brama aplikacji strefowo nadmiarowy (publiczna wersja zapoznawcza)

Usługa Application Gateway i zapory aplikacji sieci Web (WAF) są teraz dostępne w publicznej wersji zapoznawczej w ramach nowej jednostki SKU, która oferuje ulepszenia wydajności i dodaje obsługę krytyczne nowe funkcje, takie jak skalowanie automatyczne, nadmiarowości strefy i obsługę statycznych adresów VIP. Istniejących funkcji w ramach jednostki SKU jest ogólnie dostępna w dalszym ciągu być obsługiwane w nowej jednostki SKU z pewnymi wyjątkami, wymienione w sekcji znanych ograniczeń polecenia. Nowe jednostki SKU obejmują następujące ulepszenia:

- **Skalowanie automatyczne**: wzorce obciążenia usługa Application Gateway i zapory aplikacji sieci Web wdrożenia w ramach automatycznego skalowania, jednostki SKU można skalować w górę lub w dół zależnie od zmieniających się ruchu. Skalowanie automatyczne spowoduje również usunięcie wymaganie, aby wybrać rozmiar lub wystąpienie liczba wdrożeń podczas inicjowania obsługi. Dzięki temu jednostka SKU oferuje elastyczność wartość true. W ramach nowej jednostki SKU bramy Application Gateway mogą działać zarówno w stałym pojemności (skalowanie automatyczne wyłączone), a także w trybie z włączonym skalowaniem automatycznym. Tryb stałych pojemności jest przydatne w scenariuszach ze spójnego i przewidywalnego obciążenia. Tryb skalowania automatycznego jest korzystne w aplikacjach, które są wyświetlone mnóstwo wariancja w ruchu aplikacji.
   
   > [!NOTE]
   > Automatyczne skalowanie nie jest obecnie dostępna w ramach jednostki SKU zapory aplikacji sieci Web. Konfigurowanie zapory aplikacji sieci Web przy użyciu trybu stałych pojemności, zamiast trybu skalowania automatycznego.
- **Strefa nadmiarowości**: wdrożenie usługi Application Gateway lub zapory aplikacji sieci Web może obejmować wielu strefach dostępności, usuwając konieczność do aprowizacji i uruchamiaj osobnych wystąpień bramy Application Gateway w każdej strefie przy użyciu usługi Traffic Manager. Możesz wybrać jedną strefę lub wieloma strefami wdrożonym wystąpień bramy Application Gateway, dlatego zapewnianie odporności błąd strefy. Pula zaplecza dla aplikacji można rozpowszechniać podobnie w różnych strefach dostępności.
- **Ulepszenia wydajności**: skalowanie automatyczne jednostka SKU oferuje maksymalnie 5 X lepsza wydajność odciążania protokołu SSL w porównaniu z ogólnie dostępnymi jednostkami SKU.
- **Krótszy czas wdrożenia i zaktualizuj** skalowania automatycznego jednostki SKU zapewnia krótszy czas wdrażania i aktualizacji w porównaniu z ogólnie dostępnymi jednostkami SKU.
- **Statyczny adres VIP**: wyłącznie bramy application gateway VIP typu statycznego adresu VIP obsługuje teraz. Daje to gwarancję, że adres VIP skojarzony z usługą application gateway nie zmienia się nawet po ponownym uruchomieniu.

> [!IMPORTANT]
> Skalowanie automatyczne i strefowo nadmiarowe usługa application gateway jednostkę SKU jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest dostarczane bez umowy dotyczącej poziomu usług i nie jest zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane lub mogą mieć ograniczone możliwości. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Obsługiwane regiony
Automatyczne skalowanie jednostek SKU jest dostępna w regionach wschodnie stany USA 2, środkowe stany USA, zachodnie stany USA 2, Francja środkowa, Europa Zachodnia i Azja południowo-wschodnia.

## <a name="pricing"></a>Cennik
W trakcie okresu zapoznawczego nie ma opłat. Użytkownik jest naliczana zasobów innych niż bramy aplikacji, takich jak usługi Key Vault, maszyny wirtualne itp. 

## <a name="known-issues-and-limitations"></a>Znane problemy i ograniczenia

|Problem|Szczegóły|
|--|--|
|Rozliczenia|Nie ma obecnie nie rozliczeń.|
|Tryb FIPS, WebSocket|Te nie są obecnie obsługiwane.|
|Trybie tylko do wewnętrznego modułu równoważenia obciążenia|To nie jest obecnie obsługiwane. Publiczne i tryb wewnętrznego modułu równoważenia obciążenia ze sobą jest obsługiwany.|
|Skalowanie automatyczne zapory aplikacji sieci Web|Zapora aplikacji sieci Web nie obsługuje trybu skalowania automatycznego. Tryb stałych pojemności jest obsługiwany.|

## <a name="next-steps"></a>Kolejne kroki
- [Tworzenie automatycznego skalowania, strefa nadmiarowe usługa application gateway z zarezerwowanym wirtualnym adresem IP przy użyciu programu Azure PowerShell](tutorial-autoscale-ps.md)
- Dowiedz się więcej o [Application Gateway](overview.md).
- Dowiedz się więcej o [zapory usługi Azure](../firewall/overview.md). 

