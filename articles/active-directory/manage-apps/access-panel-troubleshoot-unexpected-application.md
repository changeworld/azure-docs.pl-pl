---
title: Jak aplikacje pojawiają się na panelu dostępu | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów, dlaczego widać aplikację w panelu dostępu
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.reviewr: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fa8ea75cc7fda05326c802c25a91d025b66b5ce
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65784412"
---
# <a name="how-applications-appear-on-the-access-panel"></a>Jak aplikacje pojawiają się na panelu dostępu

Panel dostępu jest portal sieci web, która pozwala użytkownikom przy użyciu konta służbowego w usłudze Azure Active Directory (Azure AD) do wyświetlania i uruchamiania aplikacji opartych na chmurze, że administrator usługi Azure AD udzielił im dostępu do. Te aplikacje są skonfigurowane w imieniu użytkownika w portalu usługi Azure AD. Administrator może aprowizować bezpośrednio aplikacji do użytkownika lub do grupy użytkowników jest częścią skutkuje aplikacji znajdujących się na panelu dostępu użytkownika.

## <a name="general-issues-to-check-first"></a>Zagadnienia ogólne w celu sprawdzenia

-   Jeśli aplikacja została usunięta z użytkownika lub grupę, do których użytkownik jest członkiem, spróbuj zarejestrować i pomniejszać ponownie do panelu dostępu użytkownika po kilku minutach aby zobaczyć, jeśli aplikacja zostanie usunięta.

-   Jeśli licencja została usunięta z użytkownikowi lub grupie użytkownik jest członkiem to może zająć dużo czasu, w zależności od rozmiaru i złożoności grupy zmian, które ma zostać wykonane. Zezwalaj na dodatkowy czas, przed zalogowaniem się do panelu dostępu.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemy związane z przypisaniem aplikacji do użytkowników

Użytkownik może być widoczny aplikację w swoim panelu dostępu, ponieważ miał zostały wcześniej przypisane do niego. Poniżej przedstawiono kilka sposobów, aby sprawdzić:

-   [Sprawdź, jeśli użytkownik jest przypisany do aplikacji](#check-if-a-user-is-assigned-to-the-application)

-   [Sprawdź, czy użytkownik jest w ramach licencji, związane z aplikacji](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Sprawdź, jeśli użytkownik jest przypisany do aplikacji

Aby sprawdzić, jeśli użytkownik jest przypisany do aplikacji, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

6. **Wyszukiwanie** dla nazwy aplikacji w danym.

7. Kliknij przycisk **użytkowników i grup**.

8. Sprawdź, czy użytkownik ma przypisaną aplikacji.

   * Jeśli chcesz usunąć użytkownika z aplikacji, **kliknij wiersz** użytkownika i wybierz **Usuń**.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Sprawdź, czy użytkownik jest w ramach licencji, związane z aplikacji

Aby sprawdzić przypisanych licencji użytkownika, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5. Kliknij przycisk **wszyscy użytkownicy**.

6. **Wyszukiwanie** dla użytkownika, jesteś zainteresowany i **kliknij wiersz** do wybrania.

7. Kliknij przycisk **licencji** aby zobaczyć, które obecnie licencji użytkownika został przypisany.

   * Jeśli użytkownik jest przypisany do licencji usługi Office, to umożliwia aplikacjom pierwszy Office innych firm są wyświetlane na panelu dostępu użytkownika.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemy związane z przypisaniem aplikacji do grup

Użytkownik może być widoczny aplikację w swoim panelu dostępu, ponieważ są one częścią grupy, która ma przypisane aplikacji. Poniżej przedstawiono kilka sposobów, aby sprawdzić:

-   [Sprawdzanie członkostwa grupy użytkownika](#check-a-users-group-memberships)

-   [Sprawdź, czy użytkownik jest członkiem grupy przypisane do licencji](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>Sprawdzanie członkostwa grupy użytkownika

Aby sprawdzić członkostwa w grupie, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5. Kliknij przycisk **wszyscy użytkownicy**.

6. **Wyszukiwanie** dla użytkownika, jesteś zainteresowany i **kliknij wiersz** do wybrania.

7. Kliknij przycisk **grup.**

8. Sprawdź, czy użytkownik jest częścią grupy przypisane do aplikacji.

   * Jeśli chcesz usunąć użytkownika z grupy, **kliknij wiersz** grupy i wybierz pozycję Usuń.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Sprawdź, czy użytkownik jest członkiem grupy przypisane do licencji

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5. Kliknij przycisk **wszyscy użytkownicy**.

6. **Wyszukiwanie** dla użytkownika, jesteś zainteresowany i **kliknij wiersz** do wybrania.

7. Kliknij przycisk **grup.**

8. Kliknij wiersz określonej grupy.

9. Kliknij przycisk **licencji** aby zobaczyć, który licencji grupy został przypisany do niego.

   * Jeśli grupa jest przypisana do licencji usługi Office, to może umożliwiać niektórych aplikacji pierwszy Office innych firm są wyświetlane na panelu dostępu użytkownika.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Jeśli te kroki rozwiązywania problemów nie Rozwiąż ten problem

Otwórz bilet pomocy technicznej, używając następujących informacji, jeśli jest dostępny:

-   Identyfikator błędu korelacji

-   Nazwy UPN (adres e-mail użytkownika)

-   Identyfikator dzierżawy

-   Typ przeglądarki

-   Występuje, strefę czasową i przedziału czasu/czasu podczas błędu

-   Ślady narzędzia fiddler

## <a name="next-steps"></a>Kolejne kroki
[Managing Applications with Azure Active Directory (Zarządzanie aplikacjami za pomocą usługi Azure Active Directory)](what-is-application-management.md)
