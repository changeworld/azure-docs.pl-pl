---
title: Problem podczas konfiguracji, inicjowanie obsługi użytkowników dla aplikacji w galerii Azure AD | Dokumentacja firmy Microsoft
description: Sposoby rozwiązywania typowych problemów, które muszą ponieść podczas konfigurowania Inicjowanie obsługi użytkowników do aplikacji już wyświetlane w galerii aplikacji usługi Azure AD
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
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 637a2af17cf3b36392e0a694df8c10a55d616675
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333027"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problem podczas konfiguracji, inicjowanie obsługi użytkowników dla aplikacji w galerii Azure AD

Konfigurowanie [użytkownika automatycznego inicjowania obsługi administracyjnej](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) dla aplikacji (jeśli są obsługiwane), wymaga, aby uzyskać szczegółowe instrukcje występować do przygotowania aplikacji do automatycznego inicjowania obsługi. Następnie można użyć portalu Azure, aby skonfigurować usługę inicjowania obsługi administracyjnej, aby zsynchronizować konta użytkowników do aplikacji.

Zawsze należy zacząć od znajdowanie samouczek ustawień dotyczących przygotowywania Inicjowanie obsługi administracyjnej dla aplikacji. Następnie wykonaj te kroki, aby skonfigurować aplikację i usługi Azure AD, aby utworzyć połączenie inicjowania obsługi administracyjnej. Lista samouczków aplikacji można znaleźć w folderze [listy samouczki dotyczące integracji aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

## <a name="how-to-see-if-provisioning-is-working"></a>Jak sprawdzić, czy działa inicjowania obsługi administracyjnej 

Po skonfigurowaniu usługi większości wgląd w funkcjonowanie usługi może być pobierana dwóch miejscach:

-   **Dzienniki inspekcji** — dzienniki inspekcji inicjowania obsługi administracyjnej, rekord wszystkich operacji wykonywanych przez usługę inicjowania obsługi administracyjnej, łącznie z zapytań usługi Azure AD dla przypisanych użytkowników, którzy znajdują się w zakresie udostępniania. Wyślij zapytanie do aplikacji docelowej istnienie tych użytkowników, porównanie obiektów użytkownika między systemem. Następnie dodać, zaktualizować lub wyłączyć konto użytkownika w systemie docelowym na podstawie porównania. Inicjowania obsługi administracyjnej dzienników inspekcji można uzyskać w portalu Azure w **usługi Azure Active Directory &gt; aplikacje przedsiębiorstwa &gt; \[Nazwa aplikacji\] &gt; dzienniki inspekcji** kartę. Odfiltruj dzienniki **Inicjowanie obsługi konta** kategorię, aby zobaczyć tylko inicjowania obsługi zdarzeń dla danej aplikacji.

-   **Inicjowanie obsługi administracyjnej stan —** Uruchom podsumowanie ostatniego inicjowania obsługi dla danej aplikacji można przeglądać w **usługi Azure Active Directory &gt; aplikacje przedsiębiorstwa &gt; \[Nazwa aplikacji\] &gt;Inicjowania obsługi administracyjnej** sekcji w dolnej części ekranu w obszarze Ustawienia usługi. Ta sekcja zawiera podsumowanie liczby użytkowników (i/lub grup) są aktualnie synchronizowane między tymi dwoma systemami, a jeśli wystąpiły żadne błędy. Szczegóły błędu można w dziennikach inspekcji. Należy pamiętać, że stan inicjowania obsługi administracyjnej nie można wypełnić do momentu zakończenia jednej pełnej wstępnej synchronizacji od usługi Azure AD i aplikacji.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Ogólnych problemów z inicjowaniem obsługi administracyjnej do rozważenia

Poniżej przedstawiono listę obszarów problemów ogólne, które można przejść do szczegółów Jeśli wiadomo, gdzie można uruchomić.

* [Inicjowanie obsługi usługi nie rozpoczyna się](#provisioning-service-does-not-appear-to-start)
* [Nie można zapisać konfiguracji z powodu poświadczeń aplikacji nie działa](#can’t-save-configuration-due-to-app-credentials-not-working)
* [Dzienniki inspekcji, że użytkownicy są "pominięte" nieudostępniane, nawet jeśli są przypisane](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Inicjowanie obsługi usługi nie rozpoczyna się

Jeśli ustawisz **stan inicjowania obsługi administracyjnej** jako **na** w **usługi Azure Active Directory &gt; aplikacje przedsiębiorstwa &gt; \[Nazwa aplikacji\] &gt;Inicjowania obsługi administracyjnej** części portalu Azure. Jednak inne szczegóły stanu są wyświetlane na tej stronie po kolejne ponowne załadowanie. Istnieje prawdopodobieństwo, że usługa jest uruchomiona, ale nie ukończono jeszcze wstępnej synchronizacji. Sprawdź **dzienniki inspekcji** opisane powyżej, aby określić, jakie operacje usługi jest wykonywane, i czy wystąpiły żadne błędy.

>[!NOTE]
>Początkowa synchronizacja może zająć od 20 minut do kilku godzin, zależnie od rozmiaru katalogu usługi Azure AD i liczbę użytkowników w zakresie obsługi. Kolejne synchronizacje po początkowej synchronizacji można szybciej, jak znaki wodne, przedstawiające stan obu systemów po początkowej synchronizacji, poprawa wydajności kolejne synchronizacje magazyny inicjowania obsługi usługi.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Nie można zapisać konfiguracji z powodu poświadczeń aplikacji nie działa

Aby Inicjowanie obsługi administracyjnej do pracy usługa Azure AD wymaga prawidłowe poświadczenia, które zezwolenie na połączenia z zarządzania użytkownik podał, aplikacja interfejsu API. Jeśli te poświadczenia nie działają lub nie znasz wat, są one, przejrzyj samouczek dotyczące konfigurowania tej aplikacji, opisanych powyżej.

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Dzienniki inspekcji, że użytkownicy są pomijane nieudostępniane, nawet jeśli są przypisane

Gdy użytkownik jest wyświetlany jako "pominięte" w dziennikach inspekcji, jest bardzo ważne, aby przeczytać rozszerzonej szczegółowe informacje w wiadomości dziennika, aby określić przyczynę. Poniżej przedstawiono typowe przyczyny i rozwiązania:

-   **Skonfigurowano zakresu filtru** **który jest filtrowanie użytkownika na podstawie wartości atrybutu**. Aby uzyskać więcej informacji na filtrami zakresów, zobacz <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **Użytkownik "nie jest skutecznie uprawnione".** Jeśli zostanie wyświetlony ten komunikat o błędzie, to, że występuje problem z rekordem przypisania użytkownika, które są przechowywane w usłudze Azure AD. Aby rozwiązać ten problem, Usuń przypisanie użytkownika (lub grupy) z aplikacji, a następnie ponownie przypisać. Aby uzyskać więcej informacji na przypisanie, zobacz <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Lub nie jest wypełnione dla użytkownika jest wymagany atrybut.** Ważne jest, aby uwzględnić podczas konfigurowania udostępniania można przejrzeć i skonfigurować mapowanie atrybutów i przepływów pracy, określających, które użytkownik (lub grupa) właściwości przepływu z usługi Azure AD do aplikacji. Dotyczy to również ustawienie "zgodnej właściwości" używany do jednoznacznego identyfikowania i odpowiada użytkowników/grupy między tymi dwoma systemami. Aby uzyskać więcej informacji na ten proces ważne, zobacz <https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>.

   * **Mapowania dla grupy atrybutów:** inicjowania obsługi administracyjnej Nazwa grupy i szczegóły grupy, oprócz członków, jeśli są obsługiwane w przypadku niektórych aplikacji. Można włączyć lub wyłączyć tę funkcję przez włączenie lub wyłączenie **mapowania** dla obiektów grupy pokazano **inicjowania obsługi administracyjnej** kartę. Jeśli Inicjowanie obsługi grup jest włączone, należy przejrzeć mapowań atrybutów do upewnij się, że odpowiednie pole jest używany przez "Pasujących ID". Może to być wyświetlaną nazwę lub alias e-mail), jak grupy i jej elementów członkowskich nie można zainicjować obsługi administracyjnej Jeśli zgodnej właściwości jest pusta lub nie jest wypełnione dla grupy w usłudze Azure AD.

## <a name="next-steps"></a>Kolejne kroki
[Automatyzowanie użytkownika alokowania i anulowania alokowania do aplikacji SaaS w usłudze Azure Active Directory](active-directory-saas-app-provisioning.md)
