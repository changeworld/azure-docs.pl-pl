---
title: Co nowego w usłudze Azure Application Gateway
description: Dowiedz się, co nowego w usłudze Azure Application Gateway, takich jak Najnowsze informacje o wersji, znane problemy, poprawki błędów, przestarzałe funkcje i nadchodzące zmiany.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: 6755825aa123b3d7d1eed23291790ff3dc477ac5
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382484"
---
# <a name="whats-new-in-azure-application-gateway"></a>Co nowego w usłudze Azure Application Gateway?

Usługa Azure Application Gateway jest regularnie aktualizowana. Aby uzyskać najnowsze informacje o najnowszych zmianach, w tym artykule przedstawiono informacje o:

- Zainstalowane najnowsze wersje
- Znane problemy
- Poprawki błędów
- Przestarzałe funkcje

## <a name="new-features"></a>Nowe funkcje

|Cecha  |Opis  |Data dodania  |
|---------|---------|---------|
|Więcej metryk |Dodaliśmy następujące nowe metryki, które ułatwiają monitorowanie jednostki SKU bramy Application w wersji 2: [Metryki dotyczące chronometrażu](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics), stan odpowiedzi zaplecza, bajty odebrane, Wysłane bajty, protokół TLS klienta i bieżące jednostki obliczeniowe. Zobacz [metryki obsługiwane przez jednostkę SKU Application Gateway v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |2019 sierpnia |
|Reguły niestandardowe zapory aplikacji internetowej |Application Gateway WAF_v2 obsługuje teraz Tworzenie reguł niestandardowych. Zobacz [Application Gateway reguł niestandardowych](custom-waf-rules-overview.md). |Czerwiec 2019 |
|Skalowanie automatyczne, nadmiarowość stref, obsługa statycznych adresów VIP |Ogólna dostępność dla jednostki SKU w wersji 2, która obsługuje skalowanie automatyczne, nadmiarowość stref, podnoszenie wydajności, statyczne adresy VIP, Key Vault, ponowne zapisywanie nagłówka. Zobacz [Application Gateway dokumentacji skalowania](application-gateway-autoscaling-zone-redundant.md)automatycznego. |Kwiecień 2019 |
|Integracja Key Vault |Application Gateway teraz obsługuje integrację z Key Vault (w publicznej wersji zapoznawczej) dla certyfikatów serwera, które są dołączone do odbiorników z obsługą protokołu HTTPS. Zobacz [kończenie połączeń SSL z certyfikatami Key Vault](key-vault-certs.md). |Kwiecień 2019 |
|Nagłówek CRUD/ponowne zapisywanie     |Teraz można ponownie napisać nagłówki HTTP. Zobacz [samouczek: Utwórz bramę aplikacji i ponownie Napisz nagłówki](tutorial-http-header-rewrite-powershell.md) http, aby uzyskać więcej informacji.|Grudzień 2018 r.|
|WAF konfiguracja i lista wykluczeń     |Dodaliśmy więcej opcji, które ułatwiają konfigurowanie WAF i zmniejszanie fałszywych wyników pozytywnych. Aby uzyskać więcej informacji [, zobacz limity rozmiaru żądań zapory aplikacji sieci Web i listy wykluczeń](application-gateway-waf-configuration.md) .|Grudzień 2018 r.|
|Skalowanie automatyczne, nadmiarowość stref, obsługa statycznych adresów VIP      |W przypadku wersji 2 jednostki SKU wprowadzono wiele udoskonaleń, takich jak Skalowanie automatyczne, zwiększona wydajność i wiele innych. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Application Gateway?](overview.md) .|Wrzesień 2018 r.|
|Opróżnianie połączenia     |Opróżnianie połączeń pozwala bezpiecznie usunąć członków z pul zaplecza. Aby uzyskać więcej informacji, zobacz [opróżnianie połączenia](overview.md#connection-draining).|Wrzesień 2018 r.|
|Niestandardowe strony błędów     |Ze stronami błędów niestandardowych można utworzyć stronę błędu w formacie reszty witryn sieci Web. Aby włączyć tę funkcję, zobacz [tworzenie Application Gateway niestandardowych stron błędów](custom-error.md).|Wrzesień 2018 r.|
|Udoskonalenia metryk     |Lepszy widok stanu Application Gateway można uzyskać przy użyciu ulepszonych metryk. Aby włączyć metryki na Application Gateway, zobacz temat [kondycja zaplecza, dzienniki diagnostyczne i metryki dla Application Gateway](application-gateway-diagnostics.md).|Czerwiec 2018 r.|

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure Application Gateway, zobacz [co to jest usługa azure Application Gateway?](overview.md)
