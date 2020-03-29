---
title: Jak aplikacje pojawiają się w panelu dostępu | Dokumenty firmy Microsoft
description: Rozwiązywanie problemów z pojawianiem się aplikacji w Panelu dostępu
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784412"
---
# <a name="how-applications-appear-on-the-access-panel"></a>Sposób, w jaki aplikacje są wyświetlane w panelu dostępu

Panel dostępu to portal internetowy, który umożliwia użytkownikowi z kontem służbowym w usłudze Azure Active Directory (Azure AD) wyświetlanie i uruchamianie aplikacji opartych na chmurze, do których administrator usługi Azure AD przyznał im dostęp. Te aplikacje są konfigurowane w imieniu użytkownika w portalu usługi Azure AD. Administrator może aprowizować aplikację bezpośrednio lub do grupy, w wyniku czego aplikacja pojawia się w Panelu dostępu użytkownika.

## <a name="general-issues-to-check-first"></a>Kwestie ogólne do sprawdzenia w pierwszej kolejności

-   Jeśli aplikacja została usunięta z użytkownika lub grupy, do zespołu, do się, spróbuj zalogować się i ponownie zalogować do panelu dostępu użytkownika po kilku minutach, aby sprawdzić, czy aplikacja została usunięta.

-   Jeśli licencja została usunięta z użytkownika lub grupy, użytkownik jest członkiem tego może zająć dużo czasu, w zależności od rozmiaru i złożoności grupy dla zmian, które mają być wprowadzone. Przed zalogowaniem się do Panelu dostępu pozostaw na dodatkowy czas.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemy związane z przypisywaniem aplikacji użytkownikom

Użytkownik może widzieć aplikację w panelu dostępu, ponieważ został wcześniej do niej przypisany. Oto kilka sposobów sprawdzenia:

-   [Sprawdź, czy użytkownik jest przypisany do aplikacji](#check-if-a-user-is-assigned-to-the-application)

-   [Sprawdź, czy użytkownik ma licencję związaną z aplikacją](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Sprawdź, czy użytkownik jest przypisany do aplikacji

Aby sprawdzić, czy użytkownik jest przypisany do aplikacji, wykonaj następujące kroki:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

6. **Wyszukaj** nazwę danej aplikacji.

7. kliknij pozycję **Użytkownicy i grupy**.

8. Sprawdź, czy użytkownik jest przypisany do aplikacji.

   * Jeśli chcesz usunąć użytkownika z aplikacji, **kliknij wiersz** użytkownika i wybierz polecenie **Usuń**.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Sprawdź, czy użytkownik ma licencję związaną z aplikacją

Aby sprawdzić przypisane licencje użytkownika, wykonaj następujące czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5. kliknij pozycję **Wszyscy użytkownicy**.

6. **Wyszukaj** użytkownika, którego jesteś zainteresowany, i **kliknij wiersz,** aby wybrać.

7. kliknij **pozycję Licencje,** aby zobaczyć, które licencje użytkownik aktualnie ma przypisane.

   * Jeśli użytkownik jest przypisany do licencji pakietu Office, umożliwia to wyświetlanie aplikacji pakietu Office pierwszej firmy w Panelu dostępu użytkownika.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemy związane z przypisywaniem aplikacji do grup

Użytkownik może widzieć aplikację w panelu dostępu, ponieważ jest częścią grupy, do którą przypisano aplikację. Oto kilka sposobów sprawdzenia:

-   [Sprawdzanie członkostwa w grupie użytkownika](#check-a-users-group-memberships)

-   [Sprawdzanie, czy użytkownik jest członkiem grupy przypisanej do licencji](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>Sprawdzanie członkostwa w grupie użytkownika

Aby sprawdzić przynależność do grupy, wykonaj następujące czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5. kliknij pozycję **Wszyscy użytkownicy**.

6. **Wyszukaj** użytkownika, którego jesteś zainteresowany, i **kliknij wiersz,** aby wybrać.

7. kliknij pozycję **Grupy.**

8. Sprawdź, czy użytkownik jest częścią grupy przypisanej do aplikacji.

   * Jeśli chcesz usunąć użytkownika z grupy, **kliknij wiersz** grupy i wybierz pozycję Usuń.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Sprawdzanie, czy użytkownik jest członkiem grupy przypisanej do licencji

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5. kliknij pozycję **Wszyscy użytkownicy**.

6. **Wyszukaj** użytkownika, którego jesteś zainteresowany, i **kliknij wiersz,** aby wybrać.

7. kliknij pozycję **Grupy.**

8. kliknij wiersz określonej grupy.

9. kliknij **pozycję Licencje,** aby zobaczyć, które licencje grupa została do niej przypisana.

   * Jeśli grupa jest przypisana do licencji pakietu Office, może to umożliwić niektórym aplikacjom pakietu Office pierwszej strony pojawianie się w Panelu dostępu użytkownika.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Jeśli te kroki rozwiązywania problemów nie rozwiążą problemu

otwórz bilet pomocy technicznej z następującymi informacjami, jeśli są dostępne:

-   Identyfikator błędu korelacji

-   UPN (adres e-mail użytkownika)

-   Identyfikator dzierżawy

-   Typ przeglądarki

-   Strefa czasowa i czas/ramy czasowe podczas wystąpienia błędu

-   Ślady skrzypka

## <a name="next-steps"></a>Następne kroki
[Zarządzanie aplikacjami za pomocą usługi Azure Active Directory](what-is-application-management.md)
