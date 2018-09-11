---
title: Brak użytkowników są aprowizowane do aplikacji galerii usługi Azure AD | Dokumentacja firmy Microsoft
description: Jak rozwiązywać typowe problemy sterowaną nie widzisz użytkowników znajdujących się w usługi Azure AD aplikacji z galerii została skonfigurowana dla aprowizacji użytkowników z usługą Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/04/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 813dc8686edc94bdaad8067a3395251a91051cb3
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356960"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Brak użytkowników są aprowizowane do aplikacji galerii usługi Azure AD

Po automatycznej aprowizacji został skonfigurowany dla aplikacji (w tym sprawdzanie zgodności aplikacji udostępnionych poświadczeń do usługi Azure AD connect do aplikacji). Następnie użytkowników i/lub grup są udostępnione do aplikacji i zależy od następujących czynników:

-   Którego użytkownicy i grupy zostały **przypisane** do aplikacji. Aby uzyskać więcej informacji na temat przypisania, zobacz [przypisać użytkownika lub grupy do aplikacji przedsiębiorstwa w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

-   Określa, czy **mapowania atrybutów** są włączone i skonfigurowane do synchronizacji prawidłowe atrybuty z usługi Azure AD do aplikacji. Aby uzyskać więcej informacji na temat mapowań atrybutów, zobacz [Dostosowywanie aprowizacji atrybutu mapowania użytkownika dla aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

-   Czy istnieje **filtru określania zakresu** obecny, który jest filtrowanie użytkowników na podstawie określonej wartości atrybutu. Aby uzyskać więcej informacji na temat określania zakresu filtrów, zobacz [aprowizacja aplikacji opartych na atrybutach z filtrami zakresu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

Po stwierdzeniu, że użytkownicy nie są aprowizowani, sprawdź w dziennikach inspekcji w usłudze Azure AD i Wyszukaj wpisy dziennika dla określonego użytkownika.

Inicjowania obsługi dzienników inspekcji można uzyskać w witrynie Azure portal w **usługi Azure Active Directory &gt; aplikacje dla przedsiębiorstw &gt; \[Nazwa aplikacji\] &gt; dzienników inspekcji** kartę. Filtruj dzienniki na **Inicjowanie obsługi administracyjnej konta** kategorię, aby zobaczyć tylko inicjowania obsługi zdarzeń dla tej aplikacji. Możesz wyszukać użytkowników na podstawie "Pasującego Identyfikatora" który został skonfigurowany dla nich w mapowania atrybutów. Na przykład, jeśli skonfigurowano "główna nazwa użytkownika" lub "adres e-mail" jako atrybutu zgodnego po stronie usługi Azure AD, a użytkownik nie aprowizacji ma wartość "audrey@contoso.com". Następnie wyszukaj dzienniki inspekcji dla "audrey@contoso.com" i przeglądu, a następnie liczbę zwróconych pozycji.

Inspekcja inicjowania obsługi administracyjnej rejestruje rekord wszystkie operacje wykonywane przez usługę aprowizacji, łącznie z zapytań usługi Azure AD dla przypisanych użytkowników, które znajdują się w zakresie udostępniania, podczas badania aplikacji docelowej, aby istnienie tych użytkowników, porównywanie obiektów użytkownika od systemu. Następnie dodaj, Aktualizuj lub wyłączyć konto użytkownika w systemie docelowym na podstawie porównania.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Ogólne obszarów problemów z inicjowania obsługi, które należy rozważyć

Poniżej znajduje się lista obszarów ogólny problem, które można rozwinąć Jeśli masz pomysł gdzie zacząć.

* [Usługa aprowizacji nie rozpoczyna się](#provisioning-service-does-not-appear-to-start)
* [Dzienniki inspekcji Załóżmy, że użytkownicy są pomijane i nie zainicjowano obsługi administracyjnej, nawet jeśli są przypisane](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Usługa aprowizacji nie rozpoczyna się

Jeśli ustawisz **stanie aprowizacji** jako **na** w **usługi Azure Active Directory &gt; aplikacje dla przedsiębiorstw &gt; \[Nazwa aplikacji\] &gt;Aprowizacji** części witryny Azure portal. Jednak żadne inne szczegóły stanu są wyświetlane na tej stronie po kolejnych ponownie ładuje, jest prawdopodobne, że usługa działa, ale jeszcze synchronizacji początkowej nie zostało ukończone. Sprawdź **dzienniki inspekcji** opisanych powyżej, aby określić, jakie operacje usługa działa, a jeśli wystąpiły żadne błędy.

>[!NOTE]
>Początkowa synchronizacja może potrwać od 20 minut do kilku godzin, w zależności od rozmiaru katalogu usługi Azure AD i liczbę użytkowników w zakresie udostępniania. Kolejne synchronizacje po synchronizacji początkowej są szybsze, usługę aprowizacji przechowuje znaki wodne, które reprezentują stanu obu systemów po synchronizacji początkowej. Zwiększa to wydajność kolejne synchronizacje.
>
>

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Dzienniki inspekcji Załóżmy, że użytkownicy są pomijane i nie zainicjowano obsługi administracyjnej, nawet jeśli są przypisane

Użytkownik jest wyświetlany jako "pominięto" w dziennikach inspekcji, jest bardzo ważne, aby przeczytać szczegółowe w wiadomości dziennika, aby ustalić przyczynę. Poniżej przedstawiono typowe przyczyny i rozwiązania:

-   **Skonfigurowano filtru określania zakresu** **, jest filtrowanie użytkownika na podstawie wartości atrybutu**. Aby uzyskać więcej informacji na temat określania zakresu filtrów, zobacz <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **Użytkownik "nie jest skutecznie uprawnione".** Jeśli widzisz ten komunikat o błędzie określone, jest to, ponieważ wystąpił problem z rekordem przypisania użytkownika, które są przechowywane w usłudze Azure AD. Aby rozwiązać ten problem, Cofnij przypisanie użytkownika (lub grupy) z poziomu aplikacji, a następnie ponownie przypisać. Aby uzyskać więcej informacji na temat przypisania, zobacz <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Wymagany atrybut jest brak lub nie jest wypełnione dla użytkownika.** Ważne jest, aby uwzględnić podczas konfigurowania aprowizacji można przejrzeć i skonfigurować mapowania atrybutów i przepływów pracy, które określają, które użytkownik (lub grupy) właściwości przepływu z usługi Azure AD do aplikacji. Obejmuje to ustawienie "property pasującego" używany do jednoznacznego identyfikowania i dopasować użytkowników/grup między dwoma systemami. Aby uzyskać więcej informacji na temat tego procesu ważne, zobacz [Dostosowywanie aprowizacji atrybutu mapowania użytkownika dla aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

  * **Mapowania dla grupy atrybutów:** aprowizacji nazwę grupy i opis grupy, oprócz członków, jeśli jest obsługiwany dla niektórych aplikacji. Można włączyć lub wyłączyć tę funkcję, włączając lub wyłączając **mapowanie** dla obiektów grupy wyświetlane w **aprowizacji** kartę. Jeśli Inicjowanie obsługi administracyjnej grupy jest włączona, należy przejrzeć mapowania atrybutów, aby upewnić się, że odpowiednie pole jest on używany do dopasowywania "ID". Może to być wyświetlaną nazwę lub alias e-mail), jak grupy i jej elementów członkowskich nie można zainicjować obsługi administracyjnej, jeśli właściwość dopasowania jest pusta lub nie jest wypełnione dla grupy w usłudze Azure AD.

## <a name="next-steps"></a>Kolejne kroki
[Synchronizacja programu Azure AD Connect: opis Aprowizacja Deklaratywna](../connect/active-directory-aadconnectsync-understanding-declarative-provisioning.md)

