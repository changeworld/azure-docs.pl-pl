---
title: Zdalne monitorowanie akcelerator rozwiązań — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące monitorowania zdalnego akcelerator rozwiązań
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: a65f2a90aedf5aa50016a6c5e5019094f202bf55
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Często zadawane pytania dotyczące monitorowania zdalnego akcelerator rozwiązań

Zobacz też ogólne [— często zadawane pytania](iot-accelerators-faq.md).

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Ile kosztuje udostępnić nowe rozwiązanie monitorowania zdalnego?

Nowy akcelerator rozwiązań udostępnia dwie opcje wdrożenia:

* A *podstawowe* — opcja przeznaczona dla deweloperów wyszukiwanie niższe koszty rozwoju lub klienci, którzy chcą do kompilacji pokaz lub weryfikacji koncepcji.
* A *standardowe* — opcja przeznaczona dla przedsiębiorstw, która pragnie wdrożyć infrastrukturę gotowe do produkcji.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Jak I upewnij się, że moje kosztów I utrzymywanie niskich podczas opracowywania I Moje rozwiązanie?

Oprócz zapewnienia dwa wdrożenia zróżnicowanych, nowe rozwiązanie monitorowania zdalnego ma ustawienie, aby włączyć lub wyłączyć wszystkie symulowanego urządzenia, na żądanie. Wyłączanie symulacji zmniejsza danych pozyskanych w rozwiązaniu i w związku z tym całkowity koszt.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Jaka jest różnica między opcjami wdrażania w wersji podstawowej i standardowej? Jak wybieranie opcji dwa wdrożenia?

Każdej opcji wdrażania odnosi się do różnych potrzeb. Podstawy wdrażania jest przeznaczona do rozpoczęcia pracy i rozwój fazy weryfikacji koncepcji i małych wdrożeń pilotażowych. Usługa udostępnia udoskonalone architektura minimalne zasoby potrzebne i niższe koszty. Standardowe wdrożenie jest przeznaczony do tworzenia i dostosowywania rozwiązania gotowe do produkcji i umożliwia wdrożenie z należy pamiętać, że niezbędne elementy. Niezawodności i skalowania aplikacji mikrousług są tworzone jako kontenery Docker i wdrażane za pomocą usługi orchestrator (Kubernetes domyślnie). Orchestrator jest odpowiedzialny za wdrożenie skalowania i zarządzania aplikacji. Należy wybrać opcję na podstawie Twoich potrzeb bieżącej. Można użyć jednego, innych lub obu w zależności od etapu Twojego projektu.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Jak skonfigurować dynamicznej mapy na pulpicie nawigacyjnym?

Aby uzyskać więcej informacji, zobacz [klucza uaktualnienia mapy Aby wyświetlić urządzenia na mapie dynamiczne](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="next-steps"></a>Kolejne kroki

Możesz także wypróbować niektóre inne funkcje i możliwości akceleratorów rozwiązań IoT:

* [Poznaj możliwości zdalnego monitorowania akcelerator rozwiązań](iot-accelerators-remote-monitoring-explore.md)
* [Omówienie akceleratora rozwiązań do konserwacji zapobiegawczej](../iot-suite/iot-suite-predictive-overview.md)
* [Akceleratora rozwiązań połączonych fabryki — omówienie](iot-accelerators-connected-factory-overview.md)
* [Zabezpieczenia IoT od podstaw](../iot-suite/securing-iot-ground-up.md)
