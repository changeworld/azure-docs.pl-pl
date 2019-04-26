---
title: Brak użytkowników są aprowizowane do aplikacji galerii usługi Azure AD | Dokumentacja firmy Microsoft
description: Jak rozwiązywać problemy z typowych problemów dotyczących sterowaną nie widzisz użytkowników znajdujących się w usłudze Azure AD aplikacji z galerii usługi została skonfigurowana dla aprowizacji użytkowników z usługą Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e74ad04f10865a830d27c1814be10eeff3ad59
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60443219"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Brak użytkowników są aprowizowane do aplikacji galerii usługi Azure AD
Po automatycznej aprowizacji została skonfigurowana dla aplikacji (w tym sprawdzanie zgodności aplikacji udostępnionych poświadczeń do usługi Azure AD connect do aplikacji), następnie użytkowników i/lub grup są aprowizowane w aplikacji. Inicjowanie obsługi administracyjnej zależy od następujących czynników:

-   Którego użytkownicy i grupy zostały **przypisane** do aplikacji. Aby uzyskać więcej informacji na temat przypisania, zobacz [przypisać użytkownika lub grupy do aplikacji przedsiębiorstwa w usłudze Azure Active Directory](assign-user-or-group-access-portal.md).
-   Określa, czy **mapowania atrybutów** są włączone i skonfigurowane do synchronizacji prawidłowe atrybuty z usługi Azure AD do aplikacji. Aby uzyskać więcej informacji na temat mapowań atrybutów, zobacz [Dostosowywanie aprowizacji atrybutu mapowania użytkownika dla aplikacji SaaS w usłudze Azure Active Directory](customize-application-attributes.md).
-   Czy istnieje **filtru określania zakresu** obecny, który jest filtrowanie użytkowników na podstawie określonej wartości atrybutu. Aby uzyskać więcej informacji na temat określania zakresu filtrów, zobacz [aprowizacja aplikacji opartych na atrybutach z filtrami zakresu](define-conditional-rules-for-provisioning-user-accounts.md).
  
Jeśli zauważysz, że użytkownicy nie są aprowizowani, sprawdź w dziennikach inspekcji w usłudze Azure AD. Wyszukaj wpisy dziennika dla określonego użytkownika.

Inicjowania obsługi dzienników inspekcji można uzyskać w witrynie Azure portal w **usługi Azure Active Directory &gt; aplikacje dla przedsiębiorstw &gt; \[Nazwa aplikacji\] &gt; dzienników inspekcji** kartę. Filtruj dzienniki na **Inicjowanie obsługi administracyjnej konta** kategorię, aby zobaczyć tylko inicjowania obsługi zdarzeń dla tej aplikacji. Możesz wyszukać użytkowników na podstawie "Pasującego Identyfikatora" który został skonfigurowany dla nich w mapowania atrybutów. Na przykład, jeśli skonfigurowano "główna nazwa użytkownika" lub "adres e-mail" jako atrybutu zgodnego po stronie usługi Azure AD, a użytkownik nie aprowizacji ma wartość "audrey@contoso.com", następnie przeszukiwanie dzienników inspekcji dla "audrey@contoso.com" i Sprawdź wpisy zwracane.

Inspekcja inicjowania obsługi administracyjnej rejestruje rekord wszystkie operacje wykonywane przez usługę aprowizacji, łącznie z zapytań usługi Azure AD dla przypisanych użytkowników, które znajdują się w zakresie udostępniania, podczas badania aplikacji docelowej, aby istnienie tych użytkowników, porównywanie obiektów użytkownika od systemu. Następnie dodaj, Aktualizuj lub wyłączyć konto użytkownika w systemie docelowym na podstawie porównania.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Ogólne obszarów problemów z inicjowania obsługi, które należy rozważyć
Poniżej znajduje się lista obszarów ogólny problem, które można rozwinąć Jeśli masz pomysł gdzie zacząć.

- [Usługa aprowizacji nie rozpoczyna się](#provisioning-service-does-not-appear-to-start)
- [Dzienniki inspekcji Załóżmy, że użytkownicy są pomijane i nie zainicjowano obsługi administracyjnej, nawet jeśli są przypisane](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Usługa aprowizacji nie rozpoczyna się
Jeśli ustawisz **stanie aprowizacji** jako **na** w **usługi Azure Active Directory &gt; aplikacje dla przedsiębiorstw &gt; \[Nazwa aplikacji\] &gt;Aprowizacji** części witryny Azure portal. Jednak żadne inne szczegóły stanu są wyświetlane na tej stronie po kolejnych ponownie ładuje, jest prawdopodobne, że usługa działa, ale jeszcze synchronizacji początkowej nie zostało ukończone. Sprawdź **dzienniki inspekcji** opisanych powyżej, aby określić, jakie operacje usługa działa, a jeśli wystąpiły żadne błędy.

>[!NOTE]
>Początkowa synchronizacja może potrwać od 20 minut do kilku godzin, w zależności od rozmiaru katalogu usługi Azure AD i liczbę użytkowników w zakresie udostępniania. Kolejne synchronizacje po synchronizacji początkowej są szybsze, usługę aprowizacji przechowuje znaki wodne, które reprezentują stanu obu systemów po synchronizacji początkowej. Synchronizacja początkowa zwiększa wydajność kolejne synchronizacje.
>


## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Dzienniki inspekcji Załóżmy, że użytkownicy są pomijane i nie zainicjowano obsługi administracyjnej, nawet jeśli są przypisane

Użytkownik jest wyświetlany jako "pominięto" w dziennikach inspekcji, jest ważne, aby przeczytać szczegółowe w wiadomości dziennika, aby ustalić przyczynę. Poniżej przedstawiono typowe przyczyny i rozwiązania:

- **Skonfigurowano filtru określania zakresu** **, jest filtrowanie użytkownika na podstawie wartości atrybutu**. Aby uzyskać więcej informacji na temat określania zakresu filtrów, zobacz [filtrami zakresu](define-conditional-rules-for-provisioning-user-accounts.md).
- **Użytkownik "nie jest skutecznie uprawnione".** Jeśli widzisz ten komunikat o błędzie określone, jest to, ponieważ wystąpił problem z rekordem przypisania użytkownika, które są przechowywane w usłudze Azure AD. Aby rozwiązać ten problem, Cofnij przypisanie użytkownika (lub grupy) z poziomu aplikacji, a następnie ponownie przypisać ją ponownie. Aby uzyskać więcej informacji na temat przypisania, zobacz [przyznać użytkownikowi lub grupie dostęp](assign-user-or-group-access-portal.md).
- **Wymagany atrybut jest brak lub nie jest wypełnione dla użytkownika.** Ważne jest, aby uwzględnić podczas konfigurowania aprowizacji jest Przejrzyj i skonfiguruj mapowania atrybutów i przepływów pracy, które określają, które użytkownik (lub grupy) właściwości przepływu z usługi Azure AD do aplikacji. Ta konfiguracja zawiera ustawienie "pasującego właściwość", która jest używany do jednoznacznego identyfikowania i dopasować użytkowników/grup między dwoma systemami. Aby uzyskać więcej informacji na temat tego procesu ważne, zobacz [Dostosowywanie aprowizacji atrybutu mapowania użytkownika dla aplikacji SaaS w usłudze Azure Active Directory](customize-application-attributes.md).
- **Mapowania atrybutów dla grup:** Inicjowanie obsługi nazwy grupy i szczegóły grupy, oprócz członków, jeśli jest obsługiwany dla niektórych aplikacji. Można włączyć lub wyłączyć tę funkcję, włączając lub wyłączając **mapowanie** dla obiektów grupy wyświetlane w **aprowizacji** kartę. Jeśli Inicjowanie obsługi administracyjnej grupy jest włączona, należy przejrzeć mapowania atrybutów, aby upewnić się, że odpowiednie pole jest on używany do dopasowywania "ID". Pasujące identyfikator może być wyświetlana nazwy albo aliasu e-mail. Grupy i jej elementów członkowskich nie są udostępnione, jeśli właściwość dopasowania jest pusty lub nie jest wypełnione dla grupy w usłudze Azure AD.

## <a name="next-steps"></a>Kolejne kroki

[Synchronizacja w programie Azure AD Connect: omówienie aprowizacji deklaratywnej](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
