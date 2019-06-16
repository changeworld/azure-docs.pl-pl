---
title: Akceleratory rozwiązań IoT — często zadawane pytania — Azure | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące akceleratorów rozwiązań IoT
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: b2f08e811217572e09a254e9ab3306ab954b14b5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61447973"
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

Jeśli używasz urządzenia Mxchip znajdziesz zasoby i przykłady w [zestawu SDK usługi IoT DevKit](https://github.com/Microsoft/devkit-sdk) repozytorium GitHub.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>Architektura mikrousług jest dostępna dla wszystkich trzech solution Accelerator?

Obecnie tylko rozwiązania do zdalnego monitorowania używa architektura mikrousług obejmuje największą scenariusza.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Jakie korzyści są dostępne nowe open source opartych na mikrousługach architektury w nowej aktualizacji?

W ciągu ostatnich dwóch lat znacznie powstał architektury w chmurze. Mikrousługi mają uznana doskonałe wzorzec do osiągnięcia skali i elastyczności, bez obniżania oczekiwanego poziomu prędkość opracowywania. Ten wzorzec architektury jest używana w kilku usługach Microsoft wewnętrznie z doskonałą niezawodność i skalowalność wyników. Microsoft jest umieszczenie te informacje w praktyce w akceleratorów rozwiązań, tak aby klienci mogli korzystać z nich.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Jestem administratorem usługi i chcę zmienić katalog mapowanie między moją subskrypcją i określony dzierżawy usługi Azure AD. Jak wykonać to zadanie?

Zobacz [dodać istniejącą subskrypcję do katalogu usługi Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Chcę zmienić administratora usługi lub administratora współpracującego z poziomu konta

Zobacz artykuł pomocy technicznej [Zmienianie administratora usługi i administratora współpracującego z poziomu konta](https://azure.microsoft.com/support/changing-service-admin-and-co-admin).

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Dlaczego widzę ten błąd? "Twoje konto nie ma odpowiednich uprawnień do utworzenia rozwiązania. Należy skontaktować się z administratorem konta lub spróbuj przy użyciu innego konta."

Przyjrzyj się poniższym diagramie, aby uzyskać wskazówki:

![Schemat blokowy uprawnień](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Jeśli nadal widzą błąd po upewnieniu się, jesteś administratorem globalnym dzierżawy usługi Azure AD i współadministratorem subskrypcji, poproś administratora konta, Usuń użytkownika i przypisać odpowiednie uprawnienia w tej kolejności. Najpierw dodaj użytkownika jako administrator globalny, a następnie dodać użytkowników jako współadministrator subskrypcji platformy Azure. Jeśli problemy będą się powtarzać, skontaktuj się z [Pomoc i obsługa techniczna](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Dlaczego widzę ten błąd, gdy mam subskrypcję platformy Azure? "Subskrypcja platformy Azure jest wymagana do utworzenia wstępnie skonfigurowanych rozwiązań. Możesz można utworzyć bezpłatne konto próbne w zaledwie kilka minut."

Jeśli masz pewność, że masz subskrypcję platformy Azure, zweryfikuj mapowanie dla Twojej subskrypcji dzierżawy i sprawdź, czy na liście rozwijanej wybrano poprawny dzierżawy. Jeśli została zweryfikowana dzierżawy jest poprawna, postępuj zgodnie z powyższym diagramie i sprawdź poprawność mapowania subskrypcji i tej dzierżawy usługi Azure AD.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Gdzie można znaleźć informacje o poprzednią wersję rozwiązania do zdalnego monitorowania?

Poprzednią wersję akceleratora rozwiązania monitorowania zdalnego jest znane jako rozwiązanie IoT zdalnego monitorowania pakietu wstępnie skonfigurowane. Można znaleźć dokumentację zarchiwizowane w [ https://docs.microsoft.com/previous-versions/azure/iot-suite/ ](https://docs.microsoft.com/previous-versions/azure/iot-suite/).

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Nowy akcelerator rozwiązań jest dostępna w tym samym regionie geograficznym co istniejące rozwiązanie?

Tak, nowe monitorowania zdalnego jest dostępna w tych samych regionach geograficznych.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Jaka jest różnica między usunięciem grupy zasobów w witrynie Azure portal a usunięciem akceleratora rozwiązań w azureiotsolutions.com?

* Jeśli usuniesz akcelerator rozwiązań w [azureiotsolutions.com](https://www.azureiotsolutions.com/), możesz usunąć wszystkie zasoby, które zostały wdrożone, podczas tworzenia akcelerator rozwiązań. Dodatkowe zasoby są dodawane do grupy zasobów, te zasoby są także usuwane.
* W przypadku usunięcia grupy zasobów w [witryny Azure portal](https://portal.azure.com), możesz usunąć tylko zasoby w tej grupie zasobów. Należy również usunąć aplikację usługi Azure Active Directory skojarzone z akceleratora rozwiązań.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Czy mogę nadal korzystać z moich istniejących inwestycji w akceleratorów rozwiązań Azure IoT?

Tak. Wszystkie rozwiązania, które obecnie istnieje w dalszym ciągu działać w ramach subskrypcji platformy Azure i kod źródłowy pozostaje dostępna w witrynie GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Ile wystąpień usługi IoT Hub można aprowizować w ramach subskrypcji?

Domyślnie można aprowizować [10 centrów IoT na subskrypcję](../azure-subscription-service-limits.md#iot-hub-limits). Możesz utworzyć [bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Aby podnieść ten limit. W rezultacie od każdego przepisy akcelerator rozwiązań nowego centrum IoT pozwala aprowizować maksymalnie 10 akceleratorów rozwiązań w ramach danej subskrypcji.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Ile wystąpień usługi Azure Cosmos DB można aprowizować w ramach subskrypcji?

50\. Możesz utworzyć [bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Aby podnieść ten limit, ale domyślnie można aprowizować tylko 50 wystąpień usługi Cosmos DB na subskrypcję.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Ile bezpłatnych interfejsów API usługi Mapy Bing można aprowizować w ramach subskrypcji?

Dwa. Można utworzyć tylko dwa wewnętrzne transakcje poziom 1 map Bing za plany Enterprise z subskrypcją platformy Azure. Rozwiązanie monitorowania zdalnego jest domyślnie aprowizowane przy użyciu planu wewnętrzne transakcje poziomu 1. Co w efekcie pozwala aprowizować maksymalnie dwie zdalne monitorowanie rozwiązań w ramach subskrypcji bez żadnych modyfikacji.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Czy mogę utworzyć akceleratora rozwiązań, jeśli mam Microsoft Azure dla programu DreamSpark?

> [!NOTE]
> Platforma Microsoft Azure dla programu DreamSpark jest teraz nazywany Microsoft Imagine dla studentów.

Obecnie nie można utworzyć za pomocą akceleratora rozwiązań [Microsoft Azure dla programu DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) konta. Jednakże, możesz utworzyć [konto bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/free/) w zaledwie kilka minut umożliwiająca tworzenie akcelerator rozwiązań.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Jak usunąć dzierżawę usługi Azure AD?

Zobacz wpis w blogu Erica [Walkthrough of Deleting dzierżawy usługi Azure AD](https://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Kolejne kroki

Możesz także wypróbować niektóre inne funkcje i możliwości akceleratorów rozwiązań IoT:

* [Poznaj możliwości akceleratora rozwiązania monitorowania zdalnego](quickstart-remote-monitoring-deploy.md)
* [Omówienie akceleratora rozwiązań do konserwacji zapobiegawczej](iot-accelerators-predictive-overview.md)
* [Wdrażanie akceleratora rozwiązania połączonej fabryki](quickstart-connected-factory-deploy.md)
* [Zabezpieczenia IoT od podstaw](/azure/iot-fundamentals/iot-security-ground-up)
