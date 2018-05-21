---
title: Pakiet Azure IoT — często zadawane pytania | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 4c8ecdae8d84028986b4b3fba94486ce8eb7f1bc
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Często zadawane pytania dotyczące Pakietu IoT

Zobacz też konkretnym połączonych fabryki [— często zadawane pytania](../iot-accelerators/iot-accelerators-faq-cf.md).

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>Gdzie można znaleźć kodu źródłowego dla wstępnie skonfigurowanych rozwiązań?

Kod źródłowy jest przechowywane w następujących repozytoriów GitHub:
* [Zdalne monitorowanie wstępnie skonfigurowane rozwiązanie][lnk-remote-monitoring-github]
* [Rozwiązanie do konserwacji predykcyjnej wstępnie][lnk-predictive-maintenance-github]
* [Fabryka połączonych wstępnie skonfigurowane rozwiązanie](https://github.com/Azure/azure-iot-connected-factory)

### <a name="how-do-i-update-to-the-latest-version-of-the-remote-monitoring-preconfigured-solution-that-uses-the-iot-hub-device-management-features"></a>Jak zaktualizować do najnowszej wersji zdalnego wstępnie skonfigurowane rozwiązanie monitorowania, który korzysta z funkcji zarządzania urządzeniami Centrum IoT

* W przypadku wdrożenia wstępnie skonfigurowane rozwiązanie z https://www.azureiotsuite.com/ lokacji, zawsze wdraża nowe wystąpienie klasy najnowsza wersja rozwiązania.
* Jeśli wdrożono wstępnie skonfigurowane rozwiązanie przy użyciu wiersza polecenia, należy zaktualizować istniejące wdrożenie o nowy kod. Zobacz [wdrożenie w chmurze] [ lnk-cloud-deployment] w serwisie GitHub [repozytorium][lnk-remote-monitoring-github].

### <a name="how-can-i-add-support-for-a-new-device-method-to-the-remote-monitoring-preconfigured-solution"></a>Jak dodać obsługę nowej metody urządzenie do zdalnego wstępnie skonfigurowane rozwiązanie monitorowania

Zobacz sekcję [dodać obsługę nowej metody do symulatora] [ lnk-add-method] w [dostosować wstępnie skonfigurowane rozwiązanie] [ lnk-customize] artykułu.

### <a name="the-simulated-device-is-ignoring-my-desired-property-changes-why"></a>Symulowane urządzenie ignoruje zmiany żądanej właściwości, dlaczego?
W zdalnym wstępnie skonfigurowane rozwiązanie monitorowania, symulowane urządzenie kod używa tylko **Desired.Config.TemperatureMeanValue** i **Desired.Config.TelemetryInterval** żądanego właściwości Zaktualizuj właściwości zgłoszony. Wszystkie inne żądanej właściwości żądania zmiany są ignorowane.

### <a name="my-device-does-not-appear-in-the-list-of-devices-in-the-solution-dashboard-why"></a>Urządzenie nie ma na liście urządzeń na pulpicie nawigacyjnym rozwiązania, dlaczego?

Na liście urządzeń na pulpicie nawigacyjnym rozwiązania używa zapytania do zwrócenia listy urządzeń. Obecnie kwerendy nie może zwrócić więcej niż 10 tys urządzeń. Spróbuj bardziej restrykcyjne kryteria wyszukiwania dla zapytania.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Jaka jest różnica między usunięciem grupy zasobów w witrynie Azure Portal a usunięciem wstępnie skonfigurowanego rozwiązania w witrynie azureiotsuite.com?

* Jeśli usuniesz wstępnie skonfigurowane rozwiązanie w [azureiotsuite.com][lnk-azureiotsuite], należy usunąć wszystkie zasoby, które zostały udostępnione po utworzeniu wstępnie skonfigurowane rozwiązanie. Dodatkowe zasoby są dodawane do grupy zasobów, te zasoby są także usuwane. 
* Usunięcie grupy zasobów w [portalu Azure][lnk-azure-portal], należy usunąć tylko zasoby w tej grupie zasobów. Należy również usunąć aplikacji usługi Azure Active Directory skojarzonej z wstępnie skonfigurowane rozwiązanie w [portalu Azure][lnk-azure-portal].

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Jak wiele wystąpień Centrum IoT można udostępnić w ramach subskrypcji?

Domyślnie można udostępnić [10 centra IoT na subskrypcję][link-azuresublimits]. Można utworzyć [biletu pomocy technicznej platformy Azure] [ link-azuresupportticket] Aby podnieść ten limit. W związku z tym ponieważ każde wstępnie skonfigurowane rozwiązanie postanowienia nowego centrum IoT można udostępnić tylko maksymalnie 10 wstępnie skonfigurowanych rozwiązań w ramach danej subskrypcji. 

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Jak wiele wystąpień bazy danych Azure rozwiązania Cosmos można udostępnić w ramach subskrypcji?

50. Można utworzyć [biletu pomocy technicznej platformy Azure] [ link-azuresupportticket] Aby podnieść ten limit, ale domyślnie umożliwia tylko obsługę 50 wystąpień rozwiązania Cosmos bazy danych na subskrypcję. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Ile bezpłatnych interfejsów API usługi Mapy Bing można aprowizować w ramach subskrypcji?

Dwa. W subskrypcji platformy Azure można utworzyć tylko dwa wewnętrzne transakcje poziom 1 mapy Bing dla planów organizacji. Rozwiązanie monitorowania zdalnego jest udostępniana domyślnie z planem wewnętrzny poziom 1 transakcji. W związku z tym niemodyfikowana subskrypcja pozwala aprowizować maksymalnie dwa rozwiązania do monitorowania zdalnego.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>Mam wdrożone rozwiązanie do monitorowania zdalnego z mapą statyczną. Jak dodać interaktywną mapę Bing?

1. Pobierz interfejsu API map Bing dla QueryKey przedsiębiorstwa z [portalu Azure][lnk-azure-portal]: 
   
   1. Przejdź do grupy zasobów, w przypadku interfejsu API map Bing dla przedsiębiorstwa w [portalu Azure][lnk-azure-portal].
   2. Kliknij przycisk **wszystkie ustawienia**, następnie **zarządzanie kluczami**. 
   3. Można zobaczyć dwa klucze: **umożliwia** i **QueryKey**. Skopiuj wartość **QueryKey**.
      
      > [!NOTE]
      > Nie masz konta interfejsu API usługi Mapy Bing dla przedsiębiorstw? Utworzyć w [portalu Azure] [ lnk-azure-portal] przez kliknięcie przycisku + nowe, wyszukiwanie interfejsu API map Bing dla przedsiębiorstwa i postępuj zgodnie z monitami, aby utworzyć.
      > 
      > 
2. Rozwiń najnowsze kod z [Azure IoT — zdalnego-monitorowania][lnk-remote-monitoring-github].
3. Uruchom lokalnie lub w chmurze, wdrażania, postępując zgodnie ze wskazówkami wdrożenia wiersza polecenia w folderze /docs/ w repozytorium. 
4. Po wykonaniu wdrożenia lokalnego lub wdrożenia w chmurze poszukaj w folderze głównym pliku *.user.config utworzonego podczas wdrażania. Otwórz ten plik w edytorze tekstu. 
5. Zmień poniższy wiersz, aby uwzględnić wartość skopiowany z Twojej **QueryKey**: 
   
   `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Czy można utworzyć wstępnie skonfigurowane rozwiązanie w przypadku korzystania z platformy Microsoft Azure dla programu DreamSpark?

> [!NOTE]
> Microsoft Azure dla programu DreamSpark jest teraz nazywany Imagine firmy Microsoft dla uczniów lub studentów.

Obecnie nie można utworzyć wstępnie skonfigurowane rozwiązanie o [Microsoft Azure dla programu DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) konta. Można jednak utworzyć [bezpłatnego konta wersji próbnej platformy Azure](https://azure.microsoft.com/free/) w zaledwie kilka minut, umożliwiająca tworzenie wstępnie skonfigurowanego rozwiązania.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>Jeśli subskrypcja Cloud Solution Provider (CSP) można utworzyć wstępnie skonfigurowane rozwiązanie?

Obecnie nie można utworzyć wstępnie skonfigurowane rozwiązanie z subskrypcją Cloud Solution Provider (CSP). Można jednak utworzyć [bezpłatnego konta wersji próbnej platformy Azure] [ lnk-30daytrial] w zaledwie kilka minut, umożliwiająca tworzenie wstępnie skonfigurowanego rozwiązania.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Jak usunąć dzierżawa usługi Azure AD?

Zobacz Golpe marek blogu [wskazówki usunięcia dzierżawy usługi Azure AD][lnk-delete-aad-tennant].

### <a name="next-steps"></a>Kolejne kroki

Możesz także wypróbować niektóre inne funkcje i możliwości wstępnie skonfigurowanych rozwiązań Pakietu IoT:

* [Omówienie rozwiązania konserwacji predykcyjnej wstępnie][lnk-predictive-overview]
* [Fabryka połączonych wstępnie Omówienie rozwiązania](../iot-accelerators/iot-accelerators-connected-factory-overview.md)
* [Zabezpieczenia IoT od podstaw][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

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
