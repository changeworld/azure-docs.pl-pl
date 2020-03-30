---
title: Akceleratory rozwiązań IoT — często zadawane pytania - Azure | Dokumenty firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące akceleratorów rozwiązań IoT. Zawiera łącza do repozytoriów GitHub.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 7a2b167f90b4ec79f5fa515ded1c676ca7085e72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647717"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Często zadawane pytania dotyczące akceleratorów rozwiązań IoT

Zobacz też często zadawane pytania [dotyczące specyficznego dla połączonego systemu fabrycznego](iot-accelerators-faq-cf.md) i [często zadawane pytania dotyczące zdalnego monitorowania.](iot-accelerators-faq-rm-v2.md)

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Gdzie można znaleźć kod źródłowy akceleratorów rozwiązań?

Kod źródłowy jest przechowywany w następujących repozytoriach GitHub:

* [Akcelerator rozwiązań do zdalnego monitorowania (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Akcelerator rozwiązań do zdalnego monitorowania (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Akcelerator rozwiązań do konserwacji predykcyjnej](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Akcelerator rozwiązań Connected Factory](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Jakich sków można używać do tworzenia klientów urządzeń dla akceleratorów rozwiązań?

Łącza do zestawów SDK urządzeń IoT w języku [microsoft azure ioT sdks można](https://github.com/Azure/azure-iot-sdks) znaleźć w zestawach SDK ioT platformy Microsoft Azure w różnych językach (C, .NET, Java, Node.js, Python) w repozytoriach zestawów IoT usługi Microsoft Azure.

Jeśli używasz urządzenia DevKit, można znaleźć zasoby i przykłady w repozytorium [GitHub IoT DevKit SDK.](https://github.com/Microsoft/devkit-sdk)

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>Czy nowa architektura mikrousług jest dostępna dla wszystkich trzech akceleratorów rozwiązań?

Obecnie tylko rozwiązanie zdalnego monitorowania używa architektury mikrousług, ponieważ obejmuje najszerszy scenariusz.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Jakie zalety zapewnia nowa architektura oparta na mikrousługach typu open source w nowej aktualizacji?

W ciągu ostatnich dwóch lat architektura chmury znacznie ewoluowała. Mikrousługi pojawiły się jako wielki wzorzec, aby osiągnąć skalę i elastyczność, bez utraty szybkości programowania. Ten wzorzec architektury jest używany w kilku usługach firmy Microsoft wewnętrznie z dużą niezawodnością i skalowalnością. Firma Microsoft wprowadza te nauki w życie w akceleratorach rozwiązań, aby klienci mogli z nich korzystać.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Jestem administratorem usługi i chcę zmienić mapowanie katalogu między moją subskrypcją a określoną dzierżawą usługi Azure AD. Jak wykonać to zadanie?

Zobacz [Dodawanie istniejącej subskrypcji do katalogu usługi Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Chcę zmienić administratora usługi lub współadministratora po zalogowaniu się za pomocą konta instytucji

Zobacz artykuł pomocy technicznej [Zmiana administratora usługi i współadministratora po zalogowaniu się za pomocą konta instytucji](https://azure.microsoft.com/support/changing-service-admin-and-co-admin).

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Dlaczego widzę ten błąd? "Twoje konto nie ma odpowiednich uprawnień do tworzenia rozwiązania. Skontaktuj się z administratorem konta lub spróbuj użyć innego konta."

Zapoznaj się z poniższym diagramem, aby uzyskać wskazówki:

![Schemat blokowy uprawnień](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Jeśli błąd będzie nadal widoczny po weryfikacji, że jesteś globalnym administratorem dzierżawy usługi Azure AD i współadministratorem subskrypcji, niech administrator konta usunie użytkownika i ponownie przypisze niezbędne uprawnienia w tej kolejności. Najpierw dodaj użytkownika jako administratora globalnego, a następnie dodaj użytkownika jako współadministratora subskrypcji platformy Azure. Jeśli problemy będą się powtarzać, skontaktuj się z [pomocą & pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Dlaczego ten błąd jest taki, gdy mam subskrypcję platformy Azure? "Do tworzenia wstępnie skonfigurowanych rozwiązań wymagana jest subskrypcja platformy Azure. Możesz utworzyć bezpłatne konto próbne w ciągu zaledwie kilku minut."

Jeśli masz pewność, że masz subskrypcję platformy Azure, sprawdź poprawność mapowania dzierżawy dla subskrypcji i sprawdź, czy w rozwijanie jest wybrana właściwa dzierżawa. Jeśli masz sprawdzone dzierżawy jest poprawna, wykonaj poprzedni diagram i sprawdź poprawność mapowania subskrypcji i tej dzierżawy usługi Azure AD.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Gdzie można znaleźć informacje o poprzedniej wersji rozwiązania do zdalnego monitorowania?

Poprzednia wersja akceleratora rozwiązań do zdalnego monitorowania była znana jako wstępnie skonfigurowane rozwiązanie do zdalnego monitorowania pakietu IoT Suite. Zarchiwizowana dokumentacja znajduje się pod adresem [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/).

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Czy nowy akcelerator rozwiązań jest dostępny w tym samym regionie geograficznym co istniejące rozwiązanie?

Tak, nowe zdalne monitorowanie jest dostępne w tych samych regionach geograficznych.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Jaka jest różnica między usunięciem grupy zasobów w witrynie Azure portal a kliknięciem przycisku usuń w akceleratorze rozwiązań w azureiotsolutions.com?

* Jeśli usuniesz akcelerator rozwiązań w [azureiotsolutions.com,](https://www.azureiotsolutions.com/)usuniesz wszystkie zasoby, które zostały wdrożone podczas tworzenia akceleratora rozwiązań. Jeśli dodano dodatkowe zasoby do grupy zasobów, te zasoby są również usuwane.
* Jeśli usuniesz grupę zasobów w [witrynie Azure portal,](https://portal.azure.com)tylko te zasoby z tej grupy zasobów zostaną usunięte. Należy również usunąć aplikację usługi Azure Active Directory skojarzoną z akceleratorem rozwiązań.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Czy nadal mogę korzystać z istniejących inwestycji w akceleratory rozwiązań IoT platformy Azure?

Tak. Każde rozwiązanie, które istnieje dzisiaj nadal działa w ramach subskrypcji platformy Azure, a kod źródłowy pozostaje dostępny w usłudze GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Ile wystąpień usługi IoT Hub można aprowizować w ramach subskrypcji?

Domyślnie można aprowizować [10 centrów IoT na subskrypcję.](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits) Można utworzyć [bilet pomocy technicznej platformy Azure,](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby podnieść ten limit. W rezultacie, ponieważ każdy akcelerator rozwiązań aprowizacji nowego Centrum IoT, można aprowizować tylko do 10 akceleratorów rozwiązań w danej subskrypcji.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Ile wystąpień usługi Azure Cosmos DB można aprowizować w ramach subskrypcji?

Pięćdziesiąt. Można utworzyć [bilet pomocy technicznej platformy Azure,](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby podnieść ten limit, ale domyślnie można aprowizować tylko 50 wystąpień usługi Cosmos DB na subskrypcję.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Ile bezpłatnych interfejsów API usługi Mapy Bing można aprowizować w ramach subskrypcji?

Dwa. W ramach subskrypcji platformy Azure można utworzyć tylko dwie mapy Bing poziomu 1 transakcji wewnętrznych na poziomie 1 dla przedsiębiorstw. Rozwiązanie zdalnego monitorowania jest domyślnie aprowiowane za pomocą planu poziomu 1 transakcji wewnętrznych. W rezultacie można aprowizować tylko do dwóch rozwiązań zdalnego monitorowania w ramach subskrypcji bez żadnych modyfikacji.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Czy mogę utworzyć akcelerator rozwiązań, jeśli mam platformę Microsoft Azure for DreamSpark?

> [!NOTE]
> Platforma Microsoft Azure for DreamSpark jest teraz znana jako Microsoft Imagine dla studentów.

Obecnie nie można utworzyć akceleratora rozwiązań za pomocą konta [Microsoft Azure for DreamSpark.](https://azure.microsoft.com/pricing/member-offers/imagine/) Można jednak utworzyć [bezpłatne konto próbne dla platformy Azure](https://azure.microsoft.com/free/) w ciągu zaledwie kilku minut, które umożliwia utworzenie akceleratora rozwiązań.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Jak usunąć dzierżawę usługi Azure AD?

Zobacz wpis w blogu Erica Golpe [Przewodnik po usunięciu dzierżawy usługi Azure AD](https://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Następne kroki

Możesz także wypróbować niektóre inne funkcje i możliwości akceleratorów rozwiązań IoT:

* [Poznaj możliwości akceleratora rozwiązań do zdalnego monitorowania](quickstart-remote-monitoring-deploy.md)
* [Omówienie akceleratora rozwiązań do konserwacji zapobiegawczej](iot-accelerators-predictive-overview.md)
* [Wdrażanie akceleratora rozwiązań Connected Factory](quickstart-connected-factory-deploy.md)
* [Zabezpieczenia IoT od podstaw](/azure/iot-fundamentals/iot-security-ground-up)
