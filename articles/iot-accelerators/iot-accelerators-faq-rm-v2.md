---
title: Zdalne monitorowanie akcelerator rozwiązań — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące zdalnego monitorowania akceleratora rozwiązań
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 59b52163ba7f5abd53c60264f10f5352e3e4f6cd
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49067792"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Często zadawane pytania dotyczące zdalnego monitorowania akceleratora rozwiązań

Zobacz też ogólne [— często zadawane pytania](iot-accelerators-faq.md).

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Ile kosztuje aprowizować nowe rozwiązanie monitorowania zdalnego?

Nowy akcelerator rozwiązań udostępnia dwie opcje wdrożenia:

* A *podstawowe* opcja przeznaczona dla deweloperów szukających niższe koszty tworzenia lub klienci, którzy chcą tworzyć obsługi demonstracji i weryfikacji koncepcji.
* A *standardowa* opcja przeznaczona dla przedsiębiorstw, które chcą wdrożyć infrastrukturę gotowe do produkcji.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Jak mogę upewnij się, że mogę niskich Moje kosztów, podczas gdy tworzyć moje rozwiązanie?

Oprócz zapewniania dwa wdrożenia zróżnicowanej, nowe rozwiązanie monitorowania zdalnego ma ustawienia, aby włączyć lub wyłączyć wszystkie symulowane urządzenia, na żądanie. Wyłączanie symulacji zmniejsza dane pozyskane w rozwiązaniu i dlatego całkowity koszt.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Jaka jest różnica między opcjami wdrożenia podstawowa i standardowa? Jak określić jedną z opcji wdrażania dwóch

Każdej opcji wdrażania odnosi się do różnych potrzeb. Podstawowe wdrożenie zaprojektowano do rozpoczęcia pracy i opracowanie Weryfikacja koncepcji i małych pilotażowych. Usprawniona architektura zapewnia minimalne wymagane zasoby i niższe koszty. Standardowe wdrożenie jest przeznaczony do tworzenia i dostosowywania rozwiązania gotowe do produkcji i wdrożenia przy użyciu wymaganych elementów, należy pamiętać, że. Dla niezawodności i skali mikrousługi aplikacji są kompilowane jako kontenery platformy Docker i wdrażane za pomocą programu orchestrator (Kubernetes domyślnie). Program orchestrator jest odpowiedzialny za wdrażania, skalowania i zarządzania aplikacji. Należy wybrać opcję zgodnie z potrzebami bieżącego. Można na przykład jeden, drugi lub jako kombinację obu tych w zależności od etapu Twojego projektu.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Jak skonfigurować dynamiczne mapy na pulpicie nawigacyjnym?

Aby uzyskać więcej informacji, zobacz [uaktualnienia mapy klawisz, aby wyświetlić urządzenia na mapie dynamiczne](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Gdzie można znaleźć informacje o poprzednią wersję rozwiązania do zdalnego monitorowania?

Poprzednią wersję akceleratora rozwiązania monitorowania zdalnego jest znane jako rozwiązanie IoT zdalnego monitorowania pakietu wstępnie skonfigurowane. Można znaleźć dokumentację zarchiwizowane w [ https://docs.microsoft.com/previous-versions/azure/iot-suite/ ](https://docs.microsoft.com/previous-versions/azure/iot-suite/).

### <a name="next-steps"></a>Kolejne kroki

Możesz także wypróbować niektóre inne funkcje i możliwości akceleratorów rozwiązań IoT:

* [Poznaj możliwości akceleratora rozwiązania monitorowania zdalnego](quickstart-remote-monitoring-deploy.md)
* [Omówienie akceleratora rozwiązań do konserwacji zapobiegawczej](iot-accelerators-predictive-overview.md)
* [Wdrażanie akceleratora rozwiązania połączonej fabryki](quickstart-connected-factory-deploy.md)
* [Zabezpieczenia IoT od podstaw](/azure/iot-fundamentals/iot-security-ground-up)
