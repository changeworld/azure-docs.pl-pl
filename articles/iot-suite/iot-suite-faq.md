---
title: Pakiet Azure IoT — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące Pakietu IoT
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
ms.openlocfilehash: 49e94e771deb4582b922400d81e8388faf164f40
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Często zadawane pytania dotyczące Pakietu IoT

Zobacz też [połączone — często zadawane pytania dotyczące fabryki](iot-suite-faq-cf.md) i [— często zadawane pytania dotyczące monitorowania zdalnego](iot-suite-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>Gdzie można znaleźć kodu źródłowego dla wstępnie skonfigurowanych rozwiązań?

Kod źródłowy jest przechowywane w następujących repozytoriów GitHub:

* [Zdalne monitorowanie wstępnie skonfigurowane rozwiązanie (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Zdalne monitorowanie wstępnie skonfigurowane rozwiązanie (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Rozwiązanie do konserwacji predykcyjnej wstępnie](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Fabryka połączonych wstępnie skonfigurowane rozwiązanie](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-preconfigured-solutions"></a>Jakie zestawy SDK można użyć do opracowywania klientów urządzeń wstępnie skonfigurowanych rozwiązań?

Linki do zestawów SDK urządzenia IoT inny język (C, .NET, Java, Node.js, Python) można znaleźć w [zestawów SDK programu Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdks) repozytoriów GitHub.

Jeśli korzystasz z urządzenia zestaw deweloperski, można znaleźć zasobów i próbek w [SDK zestaw deweloperski IoT](https://github.com/Microsoft/devkit-sdk) repozytorium GitHub.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-preconfigured-solutions"></a>Nowa architektura mikrousług jest dostępna dla wszystkich trzech wstępnie skonfigurowanych rozwiązań?

Obecnie tylko zdalnego rozwiązanie monitorowania używa architektura mikrousług obejmuje najszerszych scenariusza.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Jakie korzyści nowej powierzając jej ich konserwację otwarte na podstawie mikrousług architektury oferuje w nową aktualizację?

W ciągu ostatnich dwóch lat znacznie powstał architektury chmury. Jako dużą wzorzec do osiągnięcia skalowalność i elastyczność, bez ograniczania szybkości programowanie związane Mikrousług. Ten wzorzec architektury jest używana w wielu usługach Microsoft wewnętrznie z dużą niezawodność i skalowalność wyników. Wprowadzamy te uczenia w praktyce, umożliwiając klientom korzystać z nich.

### <a name="is-the-new-preconfigured-solution-available-in-the-same-geographic-region-as-the-existing-solution"></a>To nowe rozwiązanie wstępnie skonfigurowane w tym samym regionie geograficznym, jak istniejące rozwiązanie?

Tak, nowe monitorowania zdalnego są dostępne w tej samej regionów geograficznych.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Jaka jest różnica między usunięciem grupy zasobów w witrynie Azure Portal a usunięciem wstępnie skonfigurowanego rozwiązania w witrynie azureiotsuite.com?

* Jeśli usuniesz wstępnie skonfigurowane rozwiązanie w [azureiotsuite.com](https://www.azureiotsuite.com/), należy usunąć wszystkie zasoby, które zostały udostępnione po utworzeniu wstępnie skonfigurowane rozwiązanie. Dodatkowe zasoby są dodawane do grupy zasobów, te zasoby są także usuwane.
* Usunięcie grupy zasobów w [portalu Azure](https://portal.azure.com), należy usunąć tylko zasoby w tej grupie zasobów. Należy również usunąć aplikacji usługi Azure Active Directory skojarzonej z wstępnie skonfigurowanych rozwiązań.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-suite"></a>Czy można kontynuować korzystanie z mojego istniejących inwestycji w pakiet IoT Azure?

Tak. Żadne rozwiązanie występującego w dalszym ciągu działać w ramach subskrypcji platformy Azure i kod źródłowy jest nadal dostępny w witrynie GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Jak wiele wystąpień Centrum IoT można udostępnić w ramach subskrypcji?

Domyślnie można udostępnić [10 centra IoT na subskrypcję](../azure-subscription-service-limits.md#iot-hub-limits). Można utworzyć [biletu pomocy technicznej platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Aby podnieść ten limit. W związku z tym ponieważ każde wstępnie skonfigurowane rozwiązanie postanowienia nowego centrum IoT można udostępnić tylko maksymalnie 10 wstępnie skonfigurowanych rozwiązań w ramach danej subskrypcji.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Jak wiele wystąpień bazy danych Azure rozwiązania Cosmos można udostępnić w ramach subskrypcji?

50. Można utworzyć [biletu pomocy technicznej platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Aby podnieść ten limit, ale domyślnie umożliwia tylko obsługę 50 wystąpień rozwiązania Cosmos bazy danych na subskrypcję.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Ile bezpłatnych interfejsów API usługi Mapy Bing można aprowizować w ramach subskrypcji?

Dwa. W subskrypcji platformy Azure można utworzyć tylko dwa wewnętrzne transakcje poziom 1 mapy Bing dla planów organizacji. Rozwiązanie monitorowania zdalnego jest udostępniana domyślnie z planem wewnętrzny poziom 1 transakcji. W związku z tym niemodyfikowana subskrypcja pozwala aprowizować maksymalnie dwa rozwiązania do monitorowania zdalnego.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Czy można utworzyć wstępnie skonfigurowane rozwiązanie w przypadku korzystania z platformy Microsoft Azure dla programu DreamSpark?

> [!NOTE]
> Microsoft Azure dla programu DreamSpark jest teraz nazywany Imagine firmy Microsoft dla uczniów lub studentów.

Obecnie nie można utworzyć wstępnie skonfigurowane rozwiązanie o [Microsoft Azure dla programu DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) konta. Można jednak utworzyć [bezpłatnego konta wersji próbnej platformy Azure](https://azure.microsoft.com/free/) w zaledwie kilka minut, umożliwiająca tworzenie wstępnie skonfigurowanego rozwiązania.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>Jeśli subskrypcja Cloud Solution Provider (CSP) można utworzyć wstępnie skonfigurowane rozwiązanie?

Obecnie nie można utworzyć wstępnie skonfigurowane rozwiązanie z subskrypcją Cloud Solution Provider (CSP). Można jednak utworzyć [bezpłatnego konta wersji próbnej platformy Azure](https://azure.microsoft.com/free/) w zaledwie kilka minut, umożliwiająca tworzenie wstępnie skonfigurowanego rozwiązania.

### <a name="how-do-i-delete-an-aad-tenant"></a>Jak usunąć dzierżawę usługi AAD?

Zobacz Golpe marek blogu [wskazówki usunięcia dzierżawy usługi Azure AD](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Kolejne kroki

Możesz także wypróbować niektóre inne funkcje i możliwości wstępnie skonfigurowanych rozwiązań Pakietu IoT:

* [Poznaj możliwości zdalnego wstępnie skonfigurowane rozwiązanie monitorowania](iot-suite-remote-monitoring-explore.md)
* [Omówienie rozwiązania konserwacji predykcyjnej wstępnie](iot-suite-predictive-overview.md)
* [Fabryka połączonych wstępnie Omówienie rozwiązania](iot-suite-connected-factory-overview.md)
* [Zabezpieczenia IoT od podstaw](securing-iot-ground-up.md)