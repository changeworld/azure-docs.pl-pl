---
title: Użytkownicy nie są aprowizowany do aplikacji w galerii Azure AD | Dokumentacja firmy Microsoft
description: Sposoby rozwiązywania typowych problemów, które muszą ponieść Jeśli nie widzisz użytkowników znajdujących się w moduł usługi Azure AD galerii aplikacji została skonfigurowana dla Inicjowanie obsługi użytkowników z usługą Azure AD
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: asteen
ms.openlocfilehash: a36d60b8915ae0b46226bb2127829a8f1767daba
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
ms.locfileid: "26616296"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Użytkownicy nie są aprowizowany do aplikacji w galerii Azure AD

Po automatycznego inicjowania obsługi administracyjnej został skonfigurowany dla aplikacji (w tym sprawdzanie zgodności aplikacji udostępnionych poświadczeń do usługi Azure AD do łączenia się z aplikacją). Następnie użytkowników i grupy są udostępnione do aplikacji i zależy od następujących czynności:

-   Które użytkowników i grup **przypisane** do aplikacji. Aby uzyskać więcej informacji na przypisanie, zobacz [przypisać użytkownika lub grupy do aplikacji przedsiębiorstwa w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

-   Określa, czy **mapowania atrybutów** są włączone i skonfigurowane do synchronizacji prawidłowe atrybuty z usługi Azure AD do aplikacji. Aby uzyskać więcej informacji o mapowaniu atrybutu, zobacz [Dostosowywanie inicjowania obsługi administracyjnej atrybutu mapowania użytkowników dla aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

-   Czy istnieje **zakresu filtru** obecna, który jest filtrowania użytkowników na podstawie określonej wartości atrybutu. Aby uzyskać więcej informacji na filtrami zakresów, zobacz [udostępniania aplikacji na podstawie atrybutów z filtrami zakresów](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

Po stwierdzeniu, że użytkownicy nie jest zainicjowana, sprawdź w dziennikach inspekcji w usłudze Azure AD i Wyszukaj wpisy dziennika dla określonego użytkownika.

Inicjowania obsługi administracyjnej dzienników inspekcji można uzyskać w portalu Azure w **usługi Azure Active Directory &gt; aplikacje przedsiębiorstwa &gt; \[Nazwa aplikacji\] &gt; dzienniki inspekcji** kartę. Odfiltruj dzienniki **Inicjowanie obsługi konta** kategorię, aby zobaczyć tylko inicjowania obsługi zdarzeń dla danej aplikacji. Można wyszukiwać użytkowników na podstawie "Dopasowania Identyfikatora" skonfigurowanego dla nich w mapowań atrybutów. Na przykład, jeśli skonfigurowane "główna nazwa użytkownika" lub "adres e-mail" jako zgodnego atrybut po stronie usługi Azure AD, a użytkownik nie udostępniania ma wartość "audrey@contoso.com". Następnie wyszukaj w dziennikach inspekcji "audrey@contoso.com" i przeglądu, a następnie wpisy zwracane.

Dzienniki inspekcji inicjowania obsługi administracyjnej, rekord wszystkie operacje wykonywane przez usługę inicjowania obsługi administracyjnej, łącznie z zapytań usługi Azure AD dla przypisanych użytkowników, które znajdują się w zakresie udostępniania zapytań aplikacji docelowej istnienie tych użytkowników, porównanie obiektów użytkownika między systemem. Następnie dodać, zaktualizować lub wyłączyć konto użytkownika w systemie docelowym na podstawie porównania.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Ogólne obszarów problemów z inicjowaniem obsługi administracyjnej do rozważenia

Poniżej przedstawiono listę obszarów problemów ogólne, które można przejść do szczegółów Jeśli wiadomo, gdzie można uruchomić.

* [Inicjowanie obsługi usługi nie rozpoczyna się](#provisioning-service-does-not-appear-to-start)
* [Dzienniki inspekcji, że użytkownicy są pominięte nieudostępniane, nawet jeśli są przypisane](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Inicjowanie obsługi usługi nie rozpoczyna się

Jeśli ustawisz **stan inicjowania obsługi administracyjnej** jako **na** w **usługi Azure Active Directory &gt; aplikacje przedsiębiorstwa &gt; \[Nazwa aplikacji\] &gt;inicjowania obsługi administracyjnej** części portalu Azure. Jednak żaden inny stan są wyświetlane szczegóły na tej stronie po kolejne ponowne załadowanie, istnieje duże prawdopodobieństwo, że usługa jest uruchomiona, ale nie ukończono jeszcze wstępnej synchronizacji. Sprawdź **dzienniki inspekcji** opisane powyżej, aby określić, jakie operacje usługi jest wykonywane, i czy wystąpiły żadne błędy.

>[!NOTE]
>Początkowa synchronizacja może zająć od 20 minut do kilku godzin, zależnie od rozmiaru katalogu usługi Azure AD i liczbę użytkowników w zakresie obsługi. Kolejne synchronizacje po początkowej synchronizacji są szybsze, jak znaki wodne, przedstawiające stan obu systemów po początkowej synchronizacji magazyny inicjowania obsługi usługi. Poprawia to wydajność kolejne synchronizacje.
>
>

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Dzienniki inspekcji, że użytkownicy są pomijane nieudostępniane, nawet jeśli są przypisane

Gdy użytkownik jest wyświetlany jako "pominięte" w dziennikach inspekcji, jest bardzo ważne, aby przeczytać rozszerzonej szczegółowe informacje w wiadomości dziennika, aby określić przyczynę. Poniżej przedstawiono typowe przyczyny i rozwiązania:

-   **Skonfigurowano zakresu filtru** **który jest filtrowanie użytkownika na podstawie wartości atrybutu**. Aby uzyskać więcej informacji na filtrami zakresów, zobacz <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **Użytkownik "nie jest skutecznie uprawnione".** Jeśli zostanie wyświetlony ten komunikat o błędzie, to, że występuje problem z rekordem przypisania użytkownika, które są przechowywane w usłudze Azure AD. Aby rozwiązać ten problem, Usuń przypisanie użytkownika (lub grupy) z aplikacji, a następnie ponownie przypisać. Aby uzyskać więcej informacji na przypisanie, zobacz <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Lub nie jest wypełnione dla użytkownika jest wymagany atrybut.** Ważne jest, aby uwzględnić podczas konfigurowania udostępniania można przejrzeć i skonfigurować mapowanie atrybutów i przepływów pracy, określających, które użytkownik (lub grupa) właściwości przepływu z usługi Azure AD do aplikacji. Dotyczy to również ustawienie "zgodnej właściwości" używany do jednoznacznego identyfikowania i odpowiada użytkowników/grupy między tymi dwoma systemami. Aby uzyskać więcej informacji na ten proces ważne, zobacz [Dostosowywanie inicjowania obsługi administracyjnej atrybutu mapowania użytkowników dla aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

  * **Mapowania dla grupy atrybutów:** inicjowania obsługi administracyjnej Nazwa grupy i szczegóły grupy, oprócz członków, jeśli są obsługiwane w przypadku niektórych aplikacji. Można włączyć lub wyłączyć tę funkcję przez włączenie lub wyłączenie **mapowania** dla obiektów grupy pokazano **inicjowania obsługi administracyjnej** kartę. Jeśli Inicjowanie obsługi grup jest włączone, należy przejrzeć mapowań atrybutów do upewnij się, że odpowiednie pole jest używany przez "Pasujących ID". Może to być wyświetlaną nazwę lub alias e-mail), jak grupy i jej elementów członkowskich nie można zainicjować obsługi administracyjnej Jeśli zgodnej właściwości jest pusta lub nie jest wypełnione dla grupy w usłudze Azure AD.

## <a name="next-steps"></a>Następne kroki
[Synchronizacja programu Azure AD Connect: opis Aprowizacją deklaratywną](active-directory-aadconnectsync-understanding-declarative-provisioning.md)

