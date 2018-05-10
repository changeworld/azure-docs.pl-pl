---
title: Akceleratorów rozwiązania w usłudze Azure IoT — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące akceleratorów rozwiązania IoT
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
ms.openlocfilehash: fcaf0c2d4a8be1358e4510524c8b15e063c647bf
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Często zadawane pytania dotyczące akceleratorów rozwiązania IoT

Zobacz też [połączonych fabryki specyficzne dla często zadawane pytania dotyczące](iot-suite-faq-cf.md) i [— często zadawane pytania dotyczące monitorowania zdalnego](iot-suite-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Gdzie można znaleźć kodu źródłowego dla akceleratorów rozwiązania?

Kod źródłowy jest przechowywane w następujących repozytoriów GitHub:

* [Zdalne monitorowanie akcelerator rozwiązań (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Zdalne monitorowanie akcelerator rozwiązań (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Akcelerator rozwiązań konserwacji predykcyjnej](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Połączone akcelerator rozwiązań fabryki](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Jakie zestawy SDK można użyć do opracowywania klientów urządzeń dla akceleratorów rozwiązania?

Linki do zestawów SDK urządzenia IoT inny język (C, .NET, Java, Node.js, Python) można znaleźć w [zestawów SDK programu Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdks) repozytoriów GitHub.

Jeśli korzystasz z urządzenia zestaw deweloperski, można znaleźć zasobów i próbek w [SDK zestaw deweloperski IoT](https://github.com/Microsoft/devkit-sdk) repozytorium GitHub.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>Nowa architektura mikrousług jest dostępna dla wszystkich trzech solution Accelerator?

Obecnie tylko zdalnego rozwiązanie monitorowania używa architektura mikrousług obejmuje najszerszych scenariusza.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Jakie korzyści nowej powierzając jej ich konserwację otwarte na podstawie mikrousług architektury oferuje w nową aktualizację?

W ciągu ostatnich dwóch lat znacznie powstał architektury chmury. Jako dużą wzorzec do osiągnięcia skalowalność i elastyczność, bez ograniczania szybkości programowanie związane Mikrousług. Ten wzorzec architektury jest używana w wielu usługach Microsoft wewnętrznie z dużą niezawodność i skalowalność wyników. Wprowadzamy te uczenia w praktyce, umożliwiając klientom korzystać z nich.

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Nowy akcelerator rozwiązań jest dostępna w tym samym regionie geograficznym, jak istniejące rozwiązanie?

Tak, nowe monitorowania zdalnego są dostępne w tej samej regionów geograficznych.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsuitecom"></a>Jaka jest różnica między usunięcie grupy zasobów w portalu Azure i klikając polecenie Usuń na akcelerator rozwiązań w azureiotsuite.com?

* Jeśli usuniesz akcelerator rozwiązań w [azureiotsuite.com](https://www.azureiotsuite.com/), należy usunąć wszystkie zasoby, które zostały udostępnione po utworzeniu akcelerator rozwiązań. Dodatkowe zasoby są dodawane do grupy zasobów, te zasoby są także usuwane.
* Usunięcie grupy zasobów w [portalu Azure](https://portal.azure.com), należy usunąć tylko zasoby w tej grupie zasobów. Należy również usunąć aplikacji usługi Azure Active Directory skojarzonej z akcelerator rozwiązań.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Czy można kontynuować korzystanie z mojego istniejących inwestycji w akceleratorów rozwiązania Azure IoT?

Tak. Żadne rozwiązanie występującego w dalszym ciągu działać w ramach subskrypcji platformy Azure i kod źródłowy jest nadal dostępny w witrynie GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Jak wiele wystąpień Centrum IoT można udostępnić w ramach subskrypcji?

Domyślnie można udostępnić [10 centra IoT na subskrypcję](../azure-subscription-service-limits.md#iot-hub-limits). Można utworzyć [biletu pomocy technicznej platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Aby podnieść ten limit. W rezultacie od każdego rozwiązania akceleratora przepisy nowego centrum IoT umożliwia tylko obsługę maksymalnie 10 akceleratorów rozwiązania w ramach danej subskrypcji.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Jak wiele wystąpień bazy danych Azure rozwiązania Cosmos można udostępnić w ramach subskrypcji?

50. Można utworzyć [biletu pomocy technicznej platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Aby podnieść ten limit, ale domyślnie umożliwia tylko obsługę 50 wystąpień rozwiązania Cosmos bazy danych na subskrypcję.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Ile bezpłatnych interfejsów API usługi Mapy Bing można aprowizować w ramach subskrypcji?

Dwa. W subskrypcji platformy Azure można utworzyć tylko dwa wewnętrzne transakcje poziom 1 mapy Bing dla planów organizacji. Rozwiązanie monitorowania zdalnego jest udostępniana domyślnie z planem wewnętrzny poziom 1 transakcji. W związku z tym niemodyfikowana subskrypcja pozwala aprowizować maksymalnie dwa rozwiązania do monitorowania zdalnego.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Jeśli dla DreamSpark Microsoft Azure można utworzyć akcelerator rozwiązań?

> [!NOTE]
> Microsoft Azure dla programu DreamSpark jest teraz nazywany Imagine firmy Microsoft dla uczniów lub studentów.

Obecnie nie można utworzyć akcelerator rozwiązań z [Microsoft Azure dla programu DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) konta. Można jednak utworzyć [bezpłatnego konta wersji próbnej platformy Azure](https://azure.microsoft.com/free/) w zaledwie kilka minut, umożliwiająca tworzenie akcelerator rozwiązań.

### <a name="can-i-create-a-solution-accelerator-if-i-have-cloud-solution-provider-csp-subscription"></a>Jeśli subskrypcja Cloud Solution Provider (CSP) można utworzyć akcelerator rozwiązań?

Obecnie nie można utworzyć akcelerator rozwiązań z subskrypcją Cloud Solution Provider (CSP). Można jednak utworzyć [bezpłatnego konta wersji próbnej platformy Azure](https://azure.microsoft.com/free/) w zaledwie kilka minut, umożliwiająca tworzenie akcelerator rozwiązań.

### <a name="how-do-i-delete-an-aad-tenant"></a>Jak usunąć dzierżawę usługi AAD?

Zobacz Golpe marek blogu [wskazówki usunięcia dzierżawy usługi Azure AD](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Kolejne kroki

Można również zapoznać się niektóre inne funkcje i możliwości akceleratorów rozwiązania IoT:

* [Poznaj możliwości zdalnego monitorowania akcelerator rozwiązań](iot-suite-remote-monitoring-explore.md)
* [Akceleratora rozwiązania predykcyjne konserwacji — omówienie](iot-suite-predictive-overview.md)
* [Akceleratora rozwiązań połączonych fabryki — omówienie](iot-suite-connected-factory-overview.md)
* [Zabezpieczenia IoT od podstaw](securing-iot-ground-up.md)