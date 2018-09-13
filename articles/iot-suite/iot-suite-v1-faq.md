---
title: Pakiet Azure IoT Suite — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące Pakietu IoT
services: ''
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
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: 7e7c4affee64a945900c02b6375ba4df5d085183
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35757808"
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Często zadawane pytania dotyczące Pakietu IoT

Zobacz też dotyczące połączonej fabryki [— często zadawane pytania](../iot-accelerators/iot-accelerators-faq-cf.md).

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>Gdzie znaleźć kod źródłowy wstępnie skonfigurowanego rozwiązania

Kod źródłowy znajduje się w następujących repozytoriów GitHub:
* [Wstępnie skonfigurowanego rozwiązania monitorowania zdalnego][lnk-remote-monitoring-github]
* [Rozwiązanie do konserwacji predykcyjnej, wstępnie skonfigurowane][lnk-predictive-maintenance-github]
* [Połączonej fabryki wstępnie skonfigurowanego rozwiązania](https://github.com/Azure/azure-iot-connected-factory)

### <a name="how-do-i-update-to-the-latest-version-of-the-remote-monitoring-preconfigured-solution-that-uses-the-iot-hub-device-management-features"></a>Jak zaktualizować do najnowszej wersji wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego korzystającej z funkcjami zarządzania urządzeniami usługi IoT Hub?

* Jeśli wdrożono wstępnie skonfigurowane rozwiązanie z https://www.azureiotsuite.com/ lokacji zawsze wdraża nowe wystąpienie klasy najnowsza wersja rozwiązania.
* Jeśli wdrożono wstępnie skonfigurowanego rozwiązania przy użyciu wiersza polecenia, należy zaktualizować istniejące wdrożenie za pomocą nowego kodu. Zobacz [wdrożenie w chmurze] [ lnk-cloud-deployment] w usłudze GitHub [repozytorium][lnk-remote-monitoring-github].

### <a name="how-can-i-add-support-for-a-new-device-method-to-the-remote-monitoring-preconfigured-solution"></a>Jak dodać obsługę nowej metody urządzenia do wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego

Zobacz sekcję [obsługę nowa metoda w symulatorze] [ lnk-add-method] w [dostosowanie wstępnie skonfigurowanego rozwiązania] [ lnk-customize] artykułu.

### <a name="the-simulated-device-is-ignoring-my-desired-property-changes-why"></a>Symulowane urządzenie ignoruje Moje zmiany żądanych właściwości Dlaczego?
W przypadku wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego, symulowane urządzenie kod używa tylko **Desired.Config.TemperatureMeanValue** i **Desired.Config.TelemetryInterval** żądane właściwości do aktualizowania zgłoszonych właściwości. Wszystkie inne żądania zmiany żądanych właściwości są ignorowane.

### <a name="my-device-does-not-appear-in-the-list-of-devices-in-the-solution-dashboard-why"></a>Urządzenie nie ma na liście urządzeń na pulpicie nawigacyjnym rozwiązania, dlaczego?

Na liście urządzeń na pulpicie nawigacyjnym rozwiązania korzysta z zapytania, aby zwrócić listę urządzeń. Obecnie zapytanie nie może zwrócić więcej niż 10 tysięcy urządzeń. Postaraj się kryteria wyszukiwania dla zapytania bardziej restrykcyjne.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Jaka jest różnica między usunięciem grupy zasobów w witrynie Azure Portal a usunięciem wstępnie skonfigurowanego rozwiązania w witrynie azureiotsuite.com?

* Jeśli usuniesz wstępnie skonfigurowanego rozwiązania w [azureiotsuite.com][lnk-azureiotsuite], możesz usunąć wszystkie zasoby, których przeprowadzono aprowizację podczas tworzenia wstępnie skonfigurowanego rozwiązania. Dodatkowe zasoby są dodawane do grupy zasobów, te zasoby są także usuwane. 
* W przypadku usunięcia grupy zasobów w [witryny Azure portal][lnk-azure-portal], możesz usunąć tylko zasoby w tej grupie zasobów. Musisz również usunąć aplikację usługi Azure Active Directory skojarzone z wstępnie skonfigurowanym rozwiązaniem w [witryny Azure portal][lnk-azure-portal].

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Ile wystąpień usługi IoT Hub można aprowizować w ramach subskrypcji?

Domyślnie można aprowizować [10 centrów IoT na subskrypcję][link-azuresublimits]. Możesz utworzyć [bilet pomocy technicznej] [ link-azuresupportticket] Aby podnieść ten limit. W rezultacie ponieważ każde wstępnie skonfigurowane rozwiązanie aprowizuje nowe Centrum IoT Hub, można aprowizować tylko więcej niż 10 wstępnie skonfigurowanych rozwiązań w ramach danej subskrypcji. 

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Ile wystąpień usługi Azure Cosmos DB można aprowizować w ramach subskrypcji?

50. Możesz utworzyć [bilet pomocy technicznej] [ link-azuresupportticket] Aby podnieść ten limit, ale domyślnie można aprowizować tylko 50 wystąpień usługi Cosmos DB na subskrypcję. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Ile bezpłatnych interfejsów API usługi Mapy Bing można aprowizować w ramach subskrypcji?

Dwa. Można utworzyć tylko dwa wewnętrzne transakcje poziom 1 map Bing za plany Enterprise z subskrypcją platformy Azure. Rozwiązania do monitorowania zdalnego jest domyślnie aprowizowane przy użyciu planu wewnętrzne transakcje poziomu 1. W związku z tym niemodyfikowana subskrypcja pozwala aprowizować maksymalnie dwa rozwiązania do monitorowania zdalnego.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>Mam wdrożone rozwiązanie do monitorowania zdalnego z mapą statyczną. Jak dodać interaktywną mapę Bing?

1. Pobierz Twojego interfejsu API usługi mapy Bing dla QueryKey przedsiębiorstwa z [witryny Azure portal][lnk-azure-portal]: 
   
   1. Przejdź do grupy zasobów, w przypadku usługi interfejsu API map Bing dla przedsiębiorstw w [witryny Azure portal][lnk-azure-portal].
   2. Kliknij przycisk **wszystkie ustawienia**, następnie **zarządzanie kluczami**. 
   3. Możesz zobaczyć dwa klucze: **MasterKey** i **QueryKey**. Skopiuj wartość dla **QueryKey**.
      
      > [!NOTE]
      > Nie masz konta interfejsu API usługi Mapy Bing dla przedsiębiorstw? Utwórz go w [witryny Azure portal] [ lnk-azure-portal] przez kliknięcie + nowe, wyszukując frazę interfejsu API usługi mapy Bing dla przedsiębiorstw i postępuj zgodnie z monitami, aby utworzyć.
      > 
      > 
2. Uzyskaj najnowszy kod ze [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github].
3. Przeprowadź wdrożenie lokalne lub wdrożenie, postępując zgodnie ze wskazówkami wdrożenia wiersza polecenia, w folderze /docs/ w repozytorium w chmurze. 
4. Po wykonaniu wdrożenia lokalnego lub wdrożenia w chmurze poszukaj w folderze głównym pliku *.user.config utworzonego podczas wdrażania. Otwórz ten plik w edytorze tekstu. 
5. Zmień następujący wiersz, aby uwzględnić wartość skopiowaną z Twojej **QueryKey**: 
   
   `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Czy można utworzyć wstępnie skonfigurowane rozwiązanie w przypadku korzystania z platformy Microsoft Azure dla programu DreamSpark?

> [!NOTE]
> Platforma Microsoft Azure dla programu DreamSpark jest teraz nazywany Microsoft Imagine dla studentów.

Obecnie nie można utworzyć wstępnie skonfigurowane rozwiązanie z [Microsoft Azure dla programu DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) konta. Jednakże, możesz utworzyć [konto bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/free/) w zaledwie kilka minut, który pozwala utworzyć wstępnie skonfigurowanego rozwiązania.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>Wstępnie skonfigurowane rozwiązanie można utworzyć, jeśli mam obecnie subskrypcję Cloud Solution Provider (CSP)?

Obecnie nie można utworzyć wstępnie skonfigurowane rozwiązanie z subskrypcji Cloud Solution Provider (CSP). Jednakże, możesz utworzyć [konto bezpłatnej wersji próbnej platformy Azure] [ lnk-30daytrial] w zaledwie kilka minut, który pozwala utworzyć wstępnie skonfigurowanego rozwiązania.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Jak usunąć dzierżawę usługi Azure AD?

Zobacz wpis w blogu Erica [Walkthrough of Deleting dzierżawy usługi Azure AD][lnk-delete-aad-tennant].

### <a name="next-steps"></a>Kolejne kroki

Możesz także wypróbować niektóre inne funkcje i możliwości wstępnie skonfigurowanych rozwiązań Pakietu IoT:

* [Omówienie rozwiązania do konserwacji predykcyjnej, wstępnie skonfigurowane][lnk-predictive-overview]
* [Omówienie rozwiązania połączonej fabryki wstępnie skonfigurowanego](../iot-accelerators/iot-accelerators-connected-factory-overview.md)
* [Zabezpieczenia IoT od podstaw][lnk-security-groundup]

[lnk-predictive-overview]:../iot-accelerators/iot-accelerators-predictive-overview.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx
[lnk-cloud-deployment]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
[lnk-add-method]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md#add-support-for-a-new-method-to-the-simulator
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-predictive-maintenance-github]: https://github.com/Azure/azure-iot-predictive-maintenance
