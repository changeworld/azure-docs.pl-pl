---
title: Zgłaszanie automatycznego inicjowania obsługi administracyjnej aplikacji SaaS
description: Dowiedz się, jak sprawdzić stan zadań automatycznego inicjowania obsługi administracyjnej konta użytkownika i jak rozwiązywać problemy z inicjowania obsługi administracyjnej poszczególnych użytkowników.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19d76f69669ffa13d1d55ffa807e6c4818b8840c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282192"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Samouczek: Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika

Usługa Azure Active Directory (Azure AD) zawiera [usługę inicjowania obsługi administracyjnej kont użytkowników,](user-provisioning.md) która pomaga zautomatyzować inicjowanie obsługi administracyjnej usuwania obsługi administracyjnej kont użytkowników w aplikacjach SaaS i innych systemach, do celów zarządzania cyklem życia tożsamości end-to-end. Usługa Azure AD obsługuje wstępnie zintegrowane łączniki inicjowania obsługi administracyjnej dla wszystkich aplikacji i systemów za pomocą samouczków inicjowania obsługi administracyjnej użytkownika [w tym miejscu.](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

W tym artykule opisano, jak sprawdzić stan zadań inicjowania obsługi administracyjnej po ich skonfigurowaniu i jak rozwiązywać problemy z inicjowania obsługi administracyjnej poszczególnych użytkowników i grup.

## <a name="overview"></a>Omówienie

Łączniki inicjowania obsługi administracyjnej są konfigurowane i konfigurowane przy użyciu [portalu Azure,](https://portal.azure.com)wykonując [dostarczoną dokumentację](../saas-apps/tutorial-list.md) dla obsługiwanej aplikacji. Po skonfigurowaniu i uruchomieniu zadania inicjowania obsługi administracyjnej można zgłaszać przy użyciu jednej z dwóch metod:

* **Portal Azure** — w tym artykule opisano przede wszystkim pobieranie informacji o raporcie z [witryny Azure Portal](https://portal.azure.com), która zawiera zarówno raport podsumowujący inicjowanie obsługi administracyjnej, jak i szczegółowe dzienniki inspekcji inicjowania obsługi administracyjnej dla danej aplikacji.
* **Interfejs API inspekcji** — usługa Azure Active Directory udostępnia również interfejs API inspekcji, który umożliwia programowe pobieranie szczegółowych dzienników inspekcji inicjowania obsługi administracyjnej. Zobacz [odwołanie do interfejsu API inspekcji usługi Azure Active Directory,](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) aby uzyskać dokumentację dotyczącą korzystania z tego interfejsu API. Ten artykuł nie obejmuje konkretnie sposobu korzystania z interfejsu API, ale szczegółowo opisuje typy zdarzeń inicjowania obsługi administracyjnej, które są rejestrowane w dzienniku inspekcji.

### <a name="definitions"></a>Definicje

W tym artykule użyto następujących terminów, zdefiniowanych poniżej:

* **System źródłowy** — repozytorium użytkowników, z których synchronizuje się usługę inicjowania obsługi administracyjnej usługi Azure AD. Usługa Azure Active Directory jest systemem źródłowym dla większości wstępnie zintegrowanych łączników inicjowania obsługi administracyjnej, jednak istnieją pewne wyjątki (na przykład: Synchronizacja przychodząca workday).
* **System docelowy** — repozytorium użytkowników, z którymi synchronizuje się usługę inicjowania obsługi administracyjnej usługi Azure AD. Zazwyczaj jest to aplikacja SaaS (przykłady: Salesforce, ServiceNow, G Suite, Dropbox dla firm), ale w niektórych przypadkach może to być system lokalny, taki jak Usługa Active Directory (przykład: Synchronizacja przychodząca w ciągu dnia roboczego z usługą Active Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Uzyskiwanie raportów inicjowania obsługi administracyjnej z witryny Azure Portal

Aby uzyskać informacje o raporcie inicjowania obsługi administracyjnej dla danej aplikacji, należy rozpocząć od uruchomienia [witryny Azure portal](https://portal.azure.com) i **dzienników inicjowania obsługi administracyjnej** **aplikacji** &gt; dla przedsiębiorstw **usługi Azure Active Directory** &gt; w sekcji **Działania.** Można również przejść do aplikacji enterprise, dla której skonfigurowano inicjowanie obsługi administracyjnej. Na przykład w przypadku inicjowania obsługi administracyjnej użytkowników do linkedin elevate, ścieżka nawigacji do szczegółów aplikacji jest:

**Usługa Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje > linkedin elevate**

W tym miejscu można uzyskać dostęp zarówno do paska postępu inicjowania obsługi administracyjnej, jak i dzienników inicjowania obsługi administracyjnej, opisanych poniżej.

## <a name="provisioning-progress-bar"></a>Pasek postępu inicjowania obsługi administracyjnej

Pasek [postępu inicjowania obsługi administracyjnej](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) jest widoczny na karcie **Inicjowanie obsługi administracyjnej** dla danej aplikacji. Znajduje się w sekcji **Bieżący stan** pod **ustawieniami**i pokazuje stan bieżącego cyklu początkowego lub przyrostowego. W tej sekcji przedstawiono również:

* Całkowita liczba użytkowników i/grup, które zostały zsynchronizowane i są obecnie w zakresie inicjowania obsługi administracyjnej między systemem źródłowym a systemem docelowym.
* Ostatni raz synchronizacja została uruchomiono. Synchronizacje zazwyczaj występują co 20-40 minut, po zakończeniu [początkowego cyklu.](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental)
* Czy cykl [początkowy](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) został zakończony.
* Czy proces inicjowania obsługi administracyjnej został umieszczony w kwarantannie i co jest przyczyną stanu kwarantanny (na przykład brak komunikacji z systemem docelowym z powodu nieprawidłowych poświadczeń administratora).

**Bieżący stan** powinien być pierwszym miejscem, w które administratorzy powinni sprawdzać kondycję operacyjną zadania inicjowania obsługi administracyjnej.

 ![Raport podsumowujący](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>Dzienniki inicjowania obsługi administracyjnej (wersja zapoznawcza)

Wszystkie działania wykonywane przez usługę inicjowania obsługi administracyjnej są rejestrowane w [dziennikach inicjowania obsługi administracyjnej](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)usługi Azure AD . Dzienniki inicjowania obsługi administracyjnej można uzyskać w witrynie Azure Portal, wybierając **dzienniki inicjowania obsługi administracyjnej** aplikacji **usługi Azure Active Directory** &gt; **Enterprise Apps** &gt; (wersja zapoznawcza) w sekcji **Działanie.** Można przeszukiwać dane inicjowania obsługi administracyjnej na podstawie nazwy użytkownika lub identyfikatora w systemie źródłowym lub systemie docelowym. Aby uzyskać szczegółowe informacje, zobacz [Inicjowanie obsługi administracyjnej dzienników (wersja zapoznawcza)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Zarejestrowane typy zdarzeń aktywności obejmują:

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Raport podsumowujący inicjowania obsługi administracyjnej i dzienniki inicjowania obsługi administracyjnej odgrywają kluczową rolę pomagając administratorom rozwiązywać różne problemy z inicjowaniem obsługi administracyjnej konta użytkownika.

Aby uzyskać wskazówki dotyczące automatycznego inicjowania obsługi administracyjnej użytkowników oparte na scenariuszach, zobacz [Problemy z konfigurowaniem i inicjowania obsługi administracyjnej użytkowników do aplikacji](../app-provisioning/application-provisioning-config-problem.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
