---
title: Akceleratory rozwiązań IoT platformy Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące akceleratorów rozwiązań IoT
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 66494ca6bd9b4d8276469e85f4c6bb435ae245f7
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44716404"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Często zadawane pytania dotyczące akceleratorów rozwiązań IoT

Zobacz też [połączone specyficzne dla fabryka — często zadawane pytania](iot-accelerators-faq-cf.md) i [— często zadawane pytania dotyczące monitorowania zdalnego](iot-accelerators-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Gdzie znaleźć kod źródłowy dla akceleratorów rozwiązań

Kod źródłowy znajduje się w następujących repozytoriów GitHub:

* [Akcelerator rozwiązań Monitorowanie zdalne (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Akcelerator rozwiązań zdalnego monitorowania (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Predykcyjne akcelerator rozwiązań konserwacji](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Akcelerator rozwiązań usługi połączonej fabryki](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Jakie zestawy SDK można używać do tworzenia urządzeń klientów Akceleratory rozwiązań?

Można znaleźć linki do zestawów SDK urządzeń IoT innym języku (C, .NET, Java, Node.js, Python) w [Microsoft Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks) repozytoriów GitHub.

Jeśli używasz urządzenia Mxchip znajdziesz zasoby i próbek w [zestawu SDK usługi IoT DevKit](https://github.com/Microsoft/devkit-sdk) repozytorium GitHub.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>Architektura mikrousług jest dostępna dla wszystkich trzech solution Accelerator?

Obecnie tylko rozwiązania do zdalnego monitorowania używa architektura mikrousług obejmuje największą scenariusza.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Jakie korzyści są dostępne nowe open source opartych na mikrousługach architektury w nowej aktualizacji?

W ciągu ostatnich dwóch lat znacznie powstał architektury w chmurze. Mikrousługi mają uznana doskonałe wzorzec do osiągnięcia skali i elastyczności, bez obniżania oczekiwanego poziomu prędkość opracowywania. Ten wzorzec architektury jest używana w kilku usługach Microsoft wewnętrznie z doskonałą niezawodność i skalowalność wyników. Firma Microsoft korzystają z tych uczenia w praktyce, dzięki czemu nasze klienci mogli korzystać z nich.

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Nowy akcelerator rozwiązań jest dostępna w tym samym regionie geograficznym co istniejące rozwiązanie?

Tak, nowe monitorowania zdalnego jest dostępna w tych samych regionach geograficznych.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsuitecom"></a>Jaka jest różnica między usunięciem grupy zasobów w witrynie Azure portal a usunięciem akceleratora rozwiązań w witrynie azureiotsuite.com?

* Jeśli usuniesz akcelerator rozwiązań w [azureiotsuite.com](https://www.azureiotsolutions.com/), możesz usunąć wszystkie zasoby, których przeprowadzono aprowizację podczas tworzenia akcelerator rozwiązań. Dodatkowe zasoby są dodawane do grupy zasobów, te zasoby są także usuwane.
* W przypadku usunięcia grupy zasobów w [witryny Azure portal](https://portal.azure.com), możesz usunąć tylko zasoby w tej grupie zasobów. Należy również usunąć aplikację usługi Azure Active Directory skojarzone z akceleratora rozwiązań.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Czy mogę nadal korzystać z moich istniejących inwestycji w akceleratorów rozwiązań Azure IoT?

Tak. Wszystkie rozwiązania, które obecnie istnieje w dalszym ciągu działać w ramach subskrypcji platformy Azure i kod źródłowy pozostaje dostępna w witrynie GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Ile wystąpień usługi IoT Hub można aprowizować w ramach subskrypcji?

Domyślnie można aprowizować [10 centrów IoT na subskrypcję](../azure-subscription-service-limits.md#iot-hub-limits). Możesz utworzyć [bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Aby podnieść ten limit. W rezultacie od każdego przepisy akcelerator rozwiązań nowego centrum IoT pozwala aprowizować maksymalnie 10 akceleratorów rozwiązań w ramach danej subskrypcji.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Ile wystąpień usługi Azure Cosmos DB można aprowizować w ramach subskrypcji?

50. Możesz utworzyć [bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Aby podnieść ten limit, ale domyślnie można aprowizować tylko 50 wystąpień usługi Cosmos DB na subskrypcję.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Ile bezpłatnych interfejsów API usługi Mapy Bing można aprowizować w ramach subskrypcji?

Dwa. Można utworzyć tylko dwa wewnętrzne transakcje poziom 1 map Bing za plany Enterprise z subskrypcją platformy Azure. Rozwiązanie monitorowania zdalnego jest domyślnie aprowizowane przy użyciu planu wewnętrzne transakcje poziomu 1. Co w efekcie pozwala aprowizować maksymalnie dwie zdalne monitorowanie rozwiązań w ramach subskrypcji bez żadnych modyfikacji.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Czy mogę utworzyć akceleratora rozwiązań, jeśli mam Microsoft Azure dla programu DreamSpark?

> [!NOTE]
> Platforma Microsoft Azure dla programu DreamSpark jest teraz nazywany Microsoft Imagine dla studentów.

Obecnie nie można utworzyć za pomocą akceleratora rozwiązań [Microsoft Azure dla programu DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) konta. Jednakże, możesz utworzyć [konto bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/free/) w zaledwie kilka minut umożliwiająca tworzenie akcelerator rozwiązań.

### <a name="how-do-i-delete-an-aad-tenant"></a>Jak usunąć dzierżawę usługi AAD?

Zobacz wpis w blogu Erica [Walkthrough of Deleting dzierżawy usługi Azure AD](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Kolejne kroki

Możesz także wypróbować niektóre inne funkcje i możliwości akceleratorów rozwiązań IoT:

* [Poznaj możliwości akceleratora rozwiązania monitorowania zdalnego](quickstart-remote-monitoring-deploy.md)
* [Omówienie akceleratora rozwiązań do konserwacji zapobiegawczej](iot-accelerators-predictive-overview.md)
* [Wdrażanie akceleratora rozwiązania połączonej fabryki](quickstart-connected-factory-deploy.md)
* [Zabezpieczenia IoT od podstaw](/azure/iot-fundamentals/iot-security-ground-up)
