---
title: Jak aplikacje pojawiają się na panelu dostępu | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z powodu aplikacji jest wyświetlane w panelu dostępu
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
ms.reviewr: japere
ms.openlocfilehash: a67ce71ab49d00d7d9425714ad43cd4a6ee842f3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333534"
---
# <a name="how-applications-appear-on-the-access-panel"></a>Jak aplikacje pojawiają się na panelu dostępu

Panel dostępu jest portalu sieci web, która pozwala użytkownikom przy użyciu konta służbowego w usłudze Azure Active Directory (Azure AD) do wyświetlania i uruchamiania aplikacji opartej na chmurze, czy administrator usługi Azure AD udzielił im dostępu do. Te aplikacje są skonfigurowane w imieniu użytkownika w portalu usługi Azure AD. Administrator może udostępnić użytkownikowi aplikację bezpośrednio lub do grupy użytkownik wchodzi w skład powodujące aplikacji znajdujących się w panelu dostępu użytkownika.

## <a name="general-issues-to-check-first"></a>Ogólne problemy, aby sprawdzić w pierwszej kolejności

-   Jeśli aplikacja została usunięta z użytkownika lub grupę, których użytkownik jest członkiem, spróbuj zarejestrować i wylogowywanie ponownie do panelu dostępu użytkownika po kilku minutach można sprawdzić, czy aplikacja jest usunięte.

-   Jeśli licencji został usunięty z użytkownikiem lub grupą użytkownik jest członkiem to może potrwać dłuższy czas, w zależności od rozmiaru i złożoności grupy zmian wprowadzanych. Zezwalaj na dodatkowy czas przed zalogowaniem się do panelu dostępu.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemy związane z przypisywaniem aplikacji dla użytkowników

Użytkownik może być widoczny aplikacji na ich Panel dostępu, ponieważ miał zostały uprzednio przypisane do niego. Poniżej przedstawiono kilka sposobów, aby sprawdzić:

-   [Sprawdź, czy użytkownik jest przypisany do aplikacji](#check-if-a-user-is-assigned-to-the-application)

-   [Sprawdź, czy użytkownik jest w ramach licencji, związanych z aplikacją](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Sprawdź, czy użytkownik jest przypisany do aplikacji

Aby sprawdzić, czy użytkownik jest przypisany do aplikacji, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

6.  **Wyszukiwanie** nazwy w aplikacji.

7.  Kliknij przycisk **użytkowników i grup**.

8.  Sprawdź, czy użytkownika jest przypisany do aplikacji.

  * Jeśli chcesz usunąć użytkownika z aplikacji, **kliknij wiersz** użytkownika i wybierz **usunąć**.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Sprawdź, czy użytkownik jest w ramach licencji, związanych z aplikacją

Aby sprawdzić przypisane licencje użytkownika, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika planuje się i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **licencji** aby zobaczyć, które obecnie licencje użytkownika został przypisany.

   * Jeśli użytkownik jest przypisany do licencji pakietu Office, to umożliwia aplikacjom pierwszy Office firm są wyświetlane w panelu dostępu użytkownika.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemy związane z przypisywanie aplikacji do grupy

Użytkownik może być widoczny aplikacji na ich Panel dostępu, ponieważ są one częścią grupy przypisanej do aplikacji. Poniżej przedstawiono kilka sposobów, aby sprawdzić:

-   [Sprawdzanie członkostwa w grupach użytkownika](#check-a-users-group-memberships)

-   [Sprawdź, czy użytkownik jest członkiem grupy przypisane do licencji](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>Sprawdzanie członkostwa w grupach użytkownika

Aby sprawdzić członkostwa w grupie, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika planuje się i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **grup.**

8.  Sprawdź, czy użytkownika jest częścią grupy przypisane do aplikacji.

   * Jeśli chcesz usunąć użytkownika z grupy, **kliknij wiersz** grupy i wybierz opcję Usuń.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Sprawdź, czy użytkownik jest członkiem grupy przypisane do licencji

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika planuje się i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **grup.**

8.  Kliknij wiersz określonej grupy.

9.  Kliknij przycisk **licencji** aby zobaczyć, które licencje grupy został przypisany do niej.

  * Jeśli ta grupa jest przypisana do licencji pakietu Office, może to włączenie pewnych aplikacji pierwszy Office firm są wyświetlane w panelu dostępu użytkownika.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Jeśli te kroki rozwiązywania problemów nie Rozwiąż problem

Otwórz bilet pomocy technicznej następujące informacje, jeśli są dostępne:

-   Identyfikator błędu korelacji

-   Nazwa UPN (adres e-mail użytkownika)

-   Identyfikator dzierżawy

-   Typ przeglądarki

-   Strefa czasowa i przedziału czasu/czasu podczas błąd występuje

-   Ślady fiddler

## <a name="next-steps"></a>Kolejne kroki
[Managing Applications with Azure Active Directory (Zarządzanie aplikacjami za pomocą usługi Azure Active Directory)](manage-apps/what-is-application-management.md)
