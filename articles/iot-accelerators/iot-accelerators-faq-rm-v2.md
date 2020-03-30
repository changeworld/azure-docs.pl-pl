---
title: — często zadawane pytania dotyczące akceleratora rozwiązań do zdalnego monitorowania — Azure | Dokumenty firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące akceleratorów rozwiązań do zdalnego monitorowania.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: c410ac7f41adb623a7198320a1edced097778569
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73826251"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Często zadawane pytania dotyczące akceleratora rozwiązań do zdalnego monitorowania

Zobacz też ogólne często zadawane [pytania](iot-accelerators-faq.md).

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Ile kosztuje aprowerozsądowanie nowego rozwiązania do zdalnego monitorowania?

Nowy akcelerator rozwiązań oferuje dwie opcje wdrażania:

* *Podstawowa* opcja przeznaczona dla deweloperów poszukujących niższych kosztów rozwoju lub klientów, którzy chcą zbudować demo lub dowód koncepcji.
* *Standardowa* opcja przeznaczona dla przedsiębiorstw, które chcą wdrożyć infrastrukturę gotową do produkcji.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Jak mogę zapewnić, że mogę obniżyć koszty podczas opracowywania rozwiązania?

Oprócz zapewnienia dwóch zróżnicowanych wdrożeń, nowe rozwiązanie do zdalnego monitorowania ma ustawienie umożliwiające włączenie lub wyłączenie wszystkich symulowanych urządzeń na żądanie. Wyłączenie symulacji zmniejsza ilość danych pochłoniętych w rozwiązaniu, a tym samym całkowity koszt.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Jaka jest różnica między podstawowymi i standardowymi opcjami wdrażania? Jak zdecydować między dwiema opcjami wdrażania?

Każda opcja wdrażania odpowiada na różne potrzeby. Podstawowe wdrożenie ma na celu rozpoczęcie i rozwój PoC i małych pilotów. Zapewnia usprawniony architektury z minimalnych niezbędnych zasobów i niższych kosztów. Wdrożenie standardowe jest przeznaczony do tworzenia i dostosowywania rozwiązania gotowe do produkcji i zapewnia wdrożenie z elementów niezbędnych do realizacji tego. Dla niezawodności i skali mikrousług aplikacji są tworzone jako kontenery platformy Docker i wdrażane przy użyciu programu orchestrator (Kubernetes domyślnie). Koordynator jest odpowiedzialny za wdrażanie, skalowanie i zarządzanie aplikacją. Należy wybrać opcję w zależności od bieżących potrzeb. Można użyć jednego, drugiego lub kombinacji obu w zależności od etapu projektu.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Jak skonfigurować mapę dynamiczną na pulpicie nawigacyjnym?

Aby uzyskać więcej informacji, zobacz [Klucz mapy uaktualniania, aby wyświetlić urządzenia na mapie dynamicznej](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Gdzie można znaleźć informacje o poprzedniej wersji rozwiązania do zdalnego monitorowania?

Poprzednia wersja akceleratora rozwiązań do zdalnego monitorowania była znana jako wstępnie skonfigurowane rozwiązanie do zdalnego monitorowania pakietu IoT Suite. Zarchiwizowana dokumentacja znajduje się pod adresem [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/).

### <a name="next-steps"></a>Następne kroki

Możesz także wypróbować niektóre inne funkcje i możliwości akceleratorów rozwiązań IoT:

* [Poznaj możliwości akceleratora rozwiązań do zdalnego monitorowania](quickstart-remote-monitoring-deploy.md)
* [Omówienie akceleratora rozwiązań do konserwacji zapobiegawczej](iot-accelerators-predictive-overview.md)
* [Wdrażanie akceleratora rozwiązań Connected Factory](quickstart-connected-factory-deploy.md)
* [Zabezpieczenia IoT od podstaw](/azure/iot-fundamentals/iot-security-ground-up)
