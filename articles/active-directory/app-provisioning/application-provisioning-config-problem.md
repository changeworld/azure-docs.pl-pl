---
title: Problem z konfigurowaniem inicjowania obsługi administracyjnej przez użytkowników aplikacji usługi Azure AD Gallery
description: Jak rozwiązywać typowe problemy napotykane podczas konfigurowania inicjowania obsługi administracyjnej użytkowników do aplikacji już wymienionej w Galerii aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3955b96e4a7edfc79a229d927523bdd4473409d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522768"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problem z konfigurowaniem inicjowania obsługi administracyjnej przez użytkownika aplikacji usługi Azure AD Gallery

Konfigurowanie [automatycznego inicjowania obsługi administracyjnej](user-provisioning.md) dla aplikacji (gdzie jest obsługiwane), wymaga, aby określone instrukcje były przestrzegane w celu przygotowania aplikacji do automatycznego inicjowania obsługi administracyjnej. Następnie można użyć witryny Azure Portal, aby skonfigurować usługę inicjowania obsługi administracyjnej do synchronizowania kont użytkowników z aplikacją.

Należy zawsze rozpocząć od znalezienia samouczka konfiguracji specyficzne dla konfigurowania inicjowania obsługi administracyjnej dla aplikacji. Następnie wykonaj te kroki, aby skonfigurować zarówno aplikację, jak i usługę Azure AD, aby utworzyć połączenie inicjowania obsługi administracyjnej. Listę samouczków aplikacji można znaleźć na [liście samouczków dotyczących integracji aplikacji SaaS z usługą Azure Active Directory](../saas-apps/tutorial-list.md).

## <a name="how-to-see-if-provisioning-is-working"></a>Jak sprawdzić, czy inicjowanie obsługi administracyjnej działa 

Po skonfigurowaniu usługi większość informacji na temat działania usługi można wyciągnąć z dwóch miejsc:

-   **Inicjowanie obsługi administracyjnej dzienników (w wersji zapoznawczej)** — [dzienniki inicjowania obsługi administracyjnej](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) rejestrują wszystkie operacje wykonywane przez usługę inicjowania obsługi administracyjnej, w tym wykonywanie zapytań usługi Azure AD dla przypisanych użytkowników, którzy są w zakresie inicjowania obsługi administracyjnej. Kwerenda aplikacji docelowej dla istnienia tych użytkowników, porównując obiekty użytkownika między systemem. Następnie dodaj, zaktualizuj lub wyłącz konto użytkownika w systemie docelowym na podstawie porównania. Dzienniki inicjowania obsługi administracyjnej można uzyskać w witrynie Azure Portal, wybierając **dzienniki inicjowania obsługi administracyjnej** aplikacji **usługi Azure Active Directory** &gt; **Enterprise Apps** &gt; (wersja zapoznawcza) w sekcji **Działanie.**

-   **Aktualny stan –** Podsumowanie ostatniego uruchomienia inicjowania obsługi administracyjnej dla danej aplikacji można zobaczyć w sekcji **Inicjowanie obsługi administracyjnej &gt; nazw &gt; \[\] &gt;aplikacji aplikacji usługi Azure Active Directory Enterprise Apps** w dolnej części ekranu w obszarze ustawienia usługi. Sekcja Bieżący stan pokazuje, czy cykl inicjowania obsługi administracyjnej rozpoczął inicjowanie obsługi administracyjnej kont użytkowników. Możesz obserwować postęp cyklu, zobaczyć, ilu użytkowników i grup zostało aprowizowanych i zobacz, ile ról zostało utworzonych. Jeśli występują błędy, szczegóły można znaleźć w [Dzienniki inicjowania obsługi administracyjnej (... /reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Ogólne obszary problemowe z aprowizacji do rozważenia

Poniżej znajduje się lista ogólnych obszarów problemowych, które można drążyć, jeśli masz pomysł, od czego zacząć.

* [Usługa inicjowania obsługi administracyjnej nie uruchamia się](#provisioning-service-does-not-appear-to-start)
* Nie można zapisać konfiguracji z powodu niedziałających poświadczeń aplikacji
* [Dzienniki inicjowania obsługi administracyjnej mówią, że użytkownicy są "pomijani" i nie są aprowizacji, nawet jeśli są one przypisane](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Usługa inicjowania obsługi administracyjnej nie uruchamia się

Jeśli ustawisz **stan inicjowania obsługi administracyjnej** **w** sekcji **\] &gt;Inicjowanie obsługi administracyjnej aplikacji aplikacji usługi Azure Active Directory &gt; Enterprise Apps &gt; \[** w portalu Azure. Jednak żadne inne szczegóły stanu nie są wyświetlane na tej stronie po kolejnych przeładowaniach. Jest prawdopodobne, że usługa jest uruchomiona, ale nie ukończyła jeszcze początkowego cyklu. Sprawdź **dzienniki inicjowania obsługi administracyjnej** opisane powyżej, aby określić, jakie operacje wykonuje usługa i czy występują błędy.

>[!NOTE]
>Początkowy cykl może trwać od 20 minut do kilku godzin, w zależności od rozmiaru katalogu usługi Azure AD i liczby użytkowników w zakresie inicjowania obsługi administracyjnej. Kolejne synchronizacje po początkowym cyklu być szybsze, jak usługa inicjowania obsługi administracyjnej przechowuje znaki wodne, które reprezentują stan obu systemów po cyklu początkowym, zwiększając wydajność kolejnych synchronizacji.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Nie można zapisać konfiguracji z powodu niedziałających poświadczeń aplikacji

Aby inicjowanie obsługi administracyjnej działało, usługa Azure AD wymaga prawidłowych poświadczeń, które umożliwiają łączenie się z interfejsem API zarządzania użytkownikami dostarczonym przez tę aplikację. Jeśli te poświadczenia nie działają lub nie wiesz, co to są, przejrzyj samouczek konfigurowania tej aplikacji, opisany wcześniej.

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Dzienniki inicjowania obsługi administracyjnej mówią, że użytkownicy są pomijani i nie są aprowiowane, nawet jeśli są przypisane

Gdy użytkownik pojawi się jako "pominięte" w dziennikach inicjowania obsługi administracyjnej, jest bardzo ważne, aby odczytać rozszerzone szczegóły w komunikacie dziennika, aby ustalić przyczynę. Poniżej znajdują się typowe powody i rozwiązania:

- **Skonfigurowano filtr zakresu,** **który filtruje użytkownika na podstawie wartości atrybutu**. Aby uzyskać więcej informacji, zobacz [aprowowanie aplikacji oparte na atrybutach za pomocą filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

- **Użytkownik "nie jest skutecznie uprawniony".** Jeśli zostanie wyświetlony ten konkretny komunikat o błędzie, jest to spowodowane problemem z rekordem przypisania użytkownika przechowywanym w usłudze Azure AD. Aby rozwiązać ten problem, odwuń przypisanie użytkownika (lub grupy) z aplikacji i ponownie przypisz go ponownie. Aby uzyskać więcej informacji, zobacz [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md).

- **Brak wymaganego atrybutu lub nie jest wypełniany dla użytkownika.** Ważną rzeczą, którą należy wziąć pod uwagę podczas konfigurowania inicjowania obsługi administracyjnej, należy przejrzeć i skonfigurować mapowania atrybutów i przepływy pracy, które definiują, które właściwości użytkownika (lub grupy) przepływają z usługi Azure AD do aplikacji. Obejmuje to ustawienie "właściwości dopasowania", które mają służyć do jednoznacznej identyfikacji i dopasowania użytkowników/grup między dwoma systemami. Aby uzyskać więcej informacji na temat tego ważnego procesu, zobacz [Dostosowywanie mapowania atrybutów inicjowania obsługi administracyjnej przez użytkowników](../app-provisioning/customize-application-attributes.md).

  * **Mapowania atrybutów dla grup:** Inicjowanie obsługi administracyjnej nazwy grupy i szczegółów grupy, oprócz członków, jeśli są obsługiwane dla niektórych aplikacji. Tę funkcję można włączyć lub wyłączyć, włączając lub wyłączając **mapowanie** obiektów grupy wyświetlanych na karcie **Inicjowanie obsługi administracyjnej.** Jeśli grupy inicjowania obsługi administracyjnej jest włączona, należy przejrzeć mapowania atrybutów, aby upewnić się, że odpowiednie pole jest używane dla "pasujące identyfikator". Może to być wyświetlana nazwa lub alias e-mail), ponieważ grupa i jej członkowie nie mogą być aprowizacji, jeśli pasujące właściwość jest pusta lub nie wypełnione dla grupy w usłudze Azure AD.

## <a name="next-steps"></a>Następne kroki
[Automatyzacja aprowizacji i cofania aprowizacji użytkowników dla aplikacji SaaS przy użyciu usługi Azure Active Directory](user-provisioning.md)
