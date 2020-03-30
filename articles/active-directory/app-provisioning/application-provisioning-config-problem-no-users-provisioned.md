---
title: Żaden użytkownik nie jest aprowiany do aplikacji usługi Azure AD Gallery
description: Jak rozwiązywać typowe problemy napotykane, gdy nie widzisz użytkowników pojawiających się w aplikacji galerii usługi Azure AD skonfigurowanej do inicjowania obsługi administracyjnej przez użytkowników za pomocą usługi Azure AD
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
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b60261d63e1bcb75aea9d2e8a6b74902520f391
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522921"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Żaden użytkownik nie jest aprowiany do aplikacji usługi Azure AD Gallery
Po skonfigurowaniu automatycznego inicjowania obsługi administracyjnej dla aplikacji (w tym sprawdzenia, czy poświadczenia aplikacji dostarczone do usługi Azure AD w celu nawiązania połączenia z aplikacją są prawidłowe), użytkownicy i/lub grupy są aprowizowani aplikacji. Inicjowanie obsługi administracyjnej zależy od następujących elementów:

-   Którzy użytkownicy i grupy zostały **przypisane** do aplikacji. Należy zauważyć, że inicjowanie obsługi administracyjnej grup zagnieżdżonych lub grup usługi Office 365 nie jest obsługiwane. Aby uzyskać więcej informacji na temat przypisywania, zobacz [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa w usłudze Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md).
-   Określa, czy **mapowania atrybutów** są włączone i skonfigurowane do synchronizowania prawidłowych atrybutów z usługi Azure AD do aplikacji. Aby uzyskać więcej informacji na temat mapowania atrybutów, zobacz [Dostosowywanie mapowania atrybutów inicjowania obsługi administracyjnej użytkowników dla aplikacji SaaS w usłudze Azure Active Directory](customize-application-attributes.md).
-   Niezależnie od tego, czy istnieje **filtr zakresu,** który filtruje użytkowników na podstawie określonych wartości atrybutów. Aby uzyskać więcej informacji na temat filtrów zakresu, zobacz [Inicjowanie obsługi administracyjnej aplikacji oparte na atrybutach za pomocą filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
  
Jeśli zauważysz, że użytkownicy nie są aprowizacji, zapoznaj się z [dzienników inicjowania obsługi administracyjnej (w wersji zapoznawczej)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) w usłudze Azure AD. Wyszukaj wpisy dziennika dla określonego użytkownika.

Dzienniki inicjowania obsługi administracyjnej można uzyskać w witrynie Azure Portal, wybierając **dzienniki inicjowania obsługi administracyjnej** aplikacji **usługi Azure Active Directory** &gt; **Enterprise Apps** &gt; (wersja zapoznawcza) w sekcji **Działanie.** Można przeszukiwać dane inicjowania obsługi administracyjnej na podstawie nazwy użytkownika lub identyfikatora w systemie źródłowym lub systemie docelowym. Aby uzyskać szczegółowe informacje, zobacz [Inicjowanie obsługi administracyjnej dzienników (wersja zapoznawcza)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Dzienniki inicjowania obsługi administracyjnej rejestrują wszystkie operacje wykonywane przez usługę inicjowania obsługi administracyjnej, w tym wykonywanie zapytań usługi Azure AD dla przypisanych użytkowników, którzy są w zakresie inicjowania obsługi administracyjnej, wykonywanie zapytań do aplikacji docelowej pod kątem istnienia tych użytkowników, porównywanie obiektów użytkownika między systemu. Następnie dodaj, zaktualizuj lub wyłącz konto użytkownika w systemie docelowym na podstawie porównania.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Ogólne obszary problemowe z inicjowania obsługi administracyjnej do rozważenia
Poniżej znajduje się lista ogólnych obszarów problemowych, które można drążyć, jeśli masz pomysł, od czego zacząć.

- [Usługa inicjowania obsługi administracyjnej nie uruchamia się](#provisioning-service-does-not-appear-to-start)
- [Dzienniki inicjowania obsługi administracyjnej mówią, że użytkownicy są pomijani i nie są aprowizacji, nawet jeśli są one przypisane](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Usługa inicjowania obsługi administracyjnej nie uruchamia się
Jeśli ustawisz **stan inicjowania obsługi administracyjnej** **w** sekcji **\] &gt;Inicjowanie obsługi administracyjnej aplikacji aplikacji usługi Azure Active Directory &gt; Enterprise Apps &gt; \[** w portalu Azure. Jednak żadne inne szczegóły stanu są wyświetlane na tej stronie po kolejnych przeładowaniach, jest prawdopodobne, że usługa jest uruchomiona, ale nie ukończyła jeszcze początkowego cyklu. Sprawdź **dzienniki inicjowania obsługi administracyjnej (wersja zapoznawcza)** opisane powyżej, aby określić, jakie operacje wykonuje usługa i czy występują błędy.

>[!NOTE]
>Początkowy cykl może trwać od 20 minut do kilku godzin, w zależności od rozmiaru katalogu usługi Azure AD i liczby użytkowników w zakresie inicjowania obsługi administracyjnej. Kolejne synchronizacje po początkowym cyklu są szybsze, ponieważ usługa inicjowania obsługi administracyjnej przechowuje znaki wodne reprezentujące stan obu systemów po cyklu początkowym. Początkowy cykl zwiększa wydajność kolejnych synchronizacji.
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Dzienniki inicjowania obsługi administracyjnej mówią, że użytkownicy są pomijani i nie są aprowiowane, nawet jeśli są przypisane

Gdy użytkownik pojawi się jako "pominięte" w dziennikach inicjowania obsługi administracyjnej, ważne jest, aby przejrzeć **kroki** kartę dziennika, aby ustalić przyczynę. Poniżej znajdują się typowe powody i rozwiązania:

- **Skonfigurowano filtr zakresu,** **który filtruje użytkownika na podstawie wartości atrybutu**. Aby uzyskać więcej informacji na temat filtrów zakresu, zobacz [filtry zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
- **Użytkownik "nie jest skutecznie uprawniony".** Jeśli zostanie wyświetlony ten konkretny komunikat o błędzie, jest to spowodowane problemem z rekordem przypisania użytkownika przechowywanym w usłudze Azure AD. Aby rozwiązać ten problem, co likwiduje przypisanie użytkownika (lub grupy) z aplikacji i przypisz go ponownie. Aby uzyskać więcej informacji na temat przypisywania, zobacz [Przypisywanie dostępu użytkownika lub grupy](../manage-apps/assign-user-or-group-access-portal.md).
- **Brak wymaganego atrybutu lub nie jest wypełniany dla użytkownika.** Ważną rzeczą, którą należy wziąć pod uwagę podczas konfigurowania inicjowania obsługi administracyjnej jest przeglądanie i konfigurowanie mapowań atrybutów i przepływów pracy, które definiują, które właściwości użytkownika (lub grupy) przepływają z usługi Azure AD do aplikacji. Ta konfiguracja obejmuje ustawienie "właściwości pasującej", która jest używana do jednoznacznej identyfikacji i dopasowania użytkowników/grup między dwoma systemami. Aby uzyskać więcej informacji na temat tego ważnego procesu, zobacz [Dostosowywanie mapowań atrybutów inicjowania obsługi administracyjnej dla aplikacji SaaS w usłudze Azure Active Directory](customize-application-attributes.md).
- **Mapowania atrybutów dla grup:** Inicjowanie obsługi administracyjnej nazwy grupy i szczegółów grupy, oprócz członków, jeśli są obsługiwane dla niektórych aplikacji. Tę funkcję można włączyć lub wyłączyć, włączając lub wyłączając **mapowanie** obiektów grupy wyświetlanych na karcie **Inicjowanie obsługi administracyjnej.** Jeśli grupy inicjowania obsługi administracyjnej jest włączona, należy przejrzeć mapowania atrybutów, aby upewnić się, że odpowiednie pole jest używane dla "pasujące identyfikator". Pasującym identyfikatorem może być wyświetlana nazwa lub alias e-mail. Grupa i jej członkowie nie są aprowiowane, jeśli pasująca właściwość jest pusta lub nie wypełniona dla grupy w usłudze Azure AD.

## <a name="next-steps"></a>Następne kroki

[Synchronizacja usługi Azure AD Connect: opis aprowizacji deklaratywnej](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
