---
title: What's new in Azure Application Gateway
description: Aby dowiedzieć się o nowych za pomocą usługi Azure Application Gateway, takich jak najnowsze informacje o wersji, znanych problemów i poprawek błędów przestarzałe funkcje i nadchodzących zmian.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/1/2019
ms.author: victorh
ms.openlocfilehash: 053eac95a81492e79aee824b02383206c6702a84
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58806688"
---
# <a name="whats-new-in-azure-application-gateway"></a>What's new in Azure Application Gateway?

Usługa Azure Application Gateway jest aktualizowany na bieżąco. Aby uzyskać najnowsze informacje o najnowszych zmianach, w tym artykule przedstawiono informacje o:

- Zainstalowane najnowsze wersje
- Znane problemy
- Poprawki błędów
- Przestarzałe funkcje

## <a name="new-features"></a>Nowe funkcje

|Cecha  |Opis  |Data dodania  |
|---------|---------|---------|
|Nagłówek CRUD/modyfikacji oprogramowania     |Teraz można przepisać nagłówków HTTP. Zobacz [samouczka: Tworzenie bramy aplikacji i ponownie zapisuje nagłówki HTTP](tutorial-http-header-rewrite-powershell.md) Aby uzyskać więcej informacji.|Grudzień 2018 r.|
|Lista konfiguracji i wyłączenie zapory aplikacji sieci Web     |Dodaliśmy więcej opcji ułatwiające konfigurowanie zapory aplikacji internetowych i redukować liczbę fałszywych alarmów. Zobacz [limity rozmiaru żądanie zapory aplikacji sieci Web i listy wykluczeń](application-gateway-waf-configuration.md) Aby uzyskać więcej informacji.|Grudzień 2018 r.|
|Skalowanie automatyczne nadmiarowości strefy, statycznych adresów VIP pomocy technicznej (wersja zapoznawcza)     |Jednostki SKU w wersji 2 i istnieje wiele udoskonaleń, takich jak automatyczne skalowanie, lepszą wydajność i nie tylko. Zobacz [co to jest Azure Application Gateway?](overview.md#autoscaling-public-preview) Aby uzyskać więcej informacji.|Wrzesień 2018 r.|
|Opróżnianie połączeń     |Opróżniania połączenia pozwala bezpiecznie usunąć członków puli zaplecza. Aby uzyskać więcej informacji, zobacz [opróżniania połączenia](overview.md#connection-draining).|Wrzesień 2018 r.|
|Niestandardowe strony błędów     |Za pomocą strony błędów niestandardowych można utworzyć stronę błędu w formacie rest, witryny sieci Web. Aby włączyć tę opcję, zobacz [strony błędów niestandardowych Utwórz bramę aplikacji](custom-error.md).|Wrzesień 2018 r.|
|Ulepszenia metryki     |Możesz uzyskać lepszą widok stanu usługi Application Gateway przy użyciu rozszerzonych metryk. Aby włączyć metryki dotyczące Twojej bramy Application Gateway, zobacz [kondycja zaplecza, dzienniki diagnostyczne i metryki dla usługi Application Gateway](application-gateway-diagnostics.md).|Czerwiec 2018 r.|

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi Azure Application Gateway, zobacz [co to jest Azure Application Gateway?](overview.md)