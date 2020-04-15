---
title: Co nowego w usłudze Azure Application Gateway
description: Dowiedz się, co nowego w usłudze Azure Application Gateway, takie jak najnowsze informacje o wersji, znane problemy, poprawki błędów, przestarzałe funkcje i nadchodzące zmiany.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 03/24/2020
ms.author: victorh
ms.openlocfilehash: 70014b5ab37a07e01eaa2db3d729b7d8af520842
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311773"
---
# <a name="whats-new-in-azure-application-gateway"></a>Co nowego w usłudze Azure Application Gateway?

Usługa Azure Application Gateway jest aktualizowana na bieżąco. Aby być na bieżąco z najnowszymi wydarzeniami, w tym artykule znajdziesz informacje na temat:

- Najnowsze wydania
- Znane problemy
- Poprawki błędów
- Funkcje uznane za przestarzałe

## <a name="new-features"></a>Nowe funkcje

|Funkcja  |Opis  |Data dodania  |
|---------|---------|---------|
| Trasy zdefiniowane przez użytkownika (UDR) w wersji 2 (wersja zapoznawcza) |Trasy zdefiniowane przez użytkownika są teraz obsługiwane w niektórych scenariuszach na jednostkach SKU bramy aplikacji w wersji 2. Aby uzyskać więcej informacji, zobacz [Omówienie konfiguracji bramy aplikacji](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet). |Marzec 2020 r. |
|Zmiany w plikach cookie koligacji |Gdy koligacja oparta na plikach cookie jest włączona, Brama aplikacji wstrzykuje inny identyczny plik cookie o nazwie *ApplicationGatewayAffinityCORS* oprócz istniejącego pliku cookie ApplicationGatewayAffinity. *ApplicationGatewayAffinityCORS* ma dwa dodatkowe atrybuty dodane do niego (*SameSite = Brak; Secure*), dzięki czemu sesje lepkie są utrzymywane nawet w przypadku żądań cross-origin. Aby uzyskać więcej informacji, zobacz [Koligacja oparta na pliku cookie bramy aplikacji.](configuration-overview.md#cookie-based-affinity) |Luty 2020 r. |
|Ulepszenia sondy |Dzięki niestandardowym udoskonaleniom sondy w jednostce SKU bramy aplikacji w wersji 2 firma Microsoft uprościła [konfigurację sondy,](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku)ułatwiliśmy [testy kondycji zaplecza na żądanie](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe) i dodaliśmy więcej informacji [diagnostycznych](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages) ułatwiających rozwiązywanie problemów ze zdrowiem wewnętrznej bazy danych.  |Październik 2019 r. |
|Więcej danych |Dodaliśmy następujące nowe metryki ułatwiające monitorowanie jednostki SKU bramy aplikacji w wersji 2: [metryki związane z chronometracie,](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics)stan odpowiedzi wewnętrznej bazy danych, odebrane bajty, wysłane bajty, protokół TLS klienta i bieżące jednostki obliczeniowe. Zobacz [metryki obsługiwane przez jednostkę SKU bramy aplikacji w wersji 2](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |Sierpień 2019 r. |
|Reguły niestandardowe zapory aplikacji internetowej |Brama aplikacji WAF_v2 obsługuje teraz tworzenie reguł niestandardowych. Zobacz [Reguły niestandardowe bramy aplikacji](custom-waf-rules-overview.md). |Czerwiec 2019 r. |
|Automatyczne skalowanie, nadmiarowość stref, statyczna obsługa VIP GA |Ogólna dostępność dla jednostki SKU w wersji 2, która obsługuje skalowanie automatyczne, nadmiarowość stref, zwiększa wydajność, statyczne adresy VIP, przechowalnia kluczy, przepisywanie nagłówków. Zobacz [dokumentację skalowania automatycznego bramy aplikacji](application-gateway-autoscaling-zone-redundant.md). |Kwiecień 2019 r. |
|Integracja z magazynem kluczy |Brama aplikacji obsługuje teraz integrację z usługą Key Vault (w publicznej wersji zapoznawczej) dla certyfikatów serwera dołączonych do odbiorników obsługujących protokół HTTPS. Zobacz [Zakończenie protokołu TLS z certyfikatami Usługi Key Vault](key-vault-certs.md). |Kwiecień 2019 r. |
|Nagłówek CRUD/Przepisuje     |Teraz można przepisać nagłówki HTTP. Zobacz [Samouczek: Tworzenie bramy aplikacji i przepisywanie nagłówków HTTP, aby](tutorial-http-header-rewrite-powershell.md) uzyskać więcej informacji.|Grudzień 2018 r.|
|Lista konfiguracji i wykluczeń WAF     |Dodaliśmy więcej opcji, które pomogą Ci skonfigurować waf i zmniejszyć liczbę fałszywych alarmów. Aby uzyskać więcej informacji, zobacz [Limity rozmiaru zapory żądań aplikacji sieci Web i listy wykluczeń](application-gateway-waf-configuration.md).|Grudzień 2018 r.|
|Automatyczne skalowanie, nadmiarowość stref, statyczna obsługa VIP      |Dzięki jednostce SKU w wersji 2 istnieje wiele ulepszeń, takich jak skalowanie automatyczne, zwiększona wydajność i inne. Zobacz [Co to jest brama aplikacji platformy Azure?](overview.md)|Wrzesień 2018 r.|
|Opróżnianie połączeń     |Opróżnianie połączeń umożliwia wdzięku usunięcie elementów członkowskich z pul zaplecza. Aby uzyskać więcej informacji, zobacz [Opróżnianie połączenia](features.md#connection-draining).|Wrzesień 2018 r.|
|Niestandardowe strony błędów     |W niestandardowych stronach błędów można utworzyć stronę błędu w formacie pozostałych witryn. Aby to włączyć, zobacz [Tworzenie niestandardowych stron błędów bramy aplikacji](custom-error.md).|Wrzesień 2018 r.|
|Ulepszenia wskaźników     |Można uzyskać lepszy widok stanu bramy aplikacji z rozszerzonych metryk. Aby włączyć metryki w bramie aplikacji, zobacz [Kondycja zaplecza, dzienniki diagnostyczne i metryki bramy aplikacji](application-gateway-diagnostics.md).|Czerwiec 2018 r.|

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat bramy aplikacji platformy Azure, zobacz [Co to jest brama aplikacji platformy Azure?](overview.md)
