---
title: Problem z logowaniem do aplikacji galerii usługi Azure AD, skonfigurować hasło logowania jednokrotnego | Dokumentacja firmy Microsoft
description: Jak rozwiązywać problemy związane z aplikacją galerii usługi Azure AD, który jest skonfigurowany dla hasła logowania jednokrotnego.
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 742df882fb64e09ff63ef2eceb5514ca070dc227
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190322"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Problemy z logowania za pomocą galerii aplikacji usługi Azure AD skonfigurowane na potrzeby logowania jednokrotnego

Panel dostępu jest portalem sieci web. Umożliwia użytkownikom, którzy pracy usługi Azure Active Directory (Azure AD) lub kont aplikacji w chmurze, do których mają uprawnienia dostępu do służbowych. Użytkownicy, którzy mają wersje usługi Azure AD umożliwia również grupami samoobsługi i możliwości zarządzania aplikacjami za pomocą panelu dostępu.

Panel dostępu jest oddzielony od witryny Azure portal. Użytkownicy nie muszą subskrypcji platformy Azure za pomocą panelu dostępu.

Aby użyć opartego na hasłach logowanie jednokrotne (SSO) w panelu dostępu, rozszerzenie panelu dostępu musi być zainstalowany w przeglądarce. Rozszerzenie pobiera automatycznie po wybraniu aplikacji, która jest skonfigurowana dla opartego na hasłach logowania jednokrotnego.

## <a name="browser-requirements-for-access-panel"></a>Wymagania dotyczące przeglądarki dla panelu dostępu

Panel dostępu wymaga przeglądarki, która obsługuje język JavaScript i CSS została włączona.

Następujące przeglądarki obsługują opartego na hasłach logowania jednokrotnego:

- Program Internet Explorer 8, 9, 10 i 11, Windows 7 lub nowszy

- Dla programu Chrome w systemie Windows 7 lub nowszym lub System MacOS x lub nowszych

- Firefox 26.0 lub nowsze, Windows XP z dodatkiem SP2 lub nowszej lub w systemie Mac OS X 10.6 lub nowsze

>[!NOTE]
>Rozszerzenie logowania jednokrotnego opartego na hasłach stają się dostępne dla programu Microsoft Edge w systemie Windows 10 po pomoc techniczna dotycząca dodano rozszerzenia przeglądarki Microsoft Edge.

## <a name="install-the-access-panel-browser-extension"></a>Zainstaluj rozszerzenie przeglądarki panelu dostępu

Wykonaj następujące kroki:

1. Otwórz [panelu dostępu](https://myapps.microsoft.com) w obsługiwanej przeglądarki i zaloguj się jako użytkownik w usłudze Azure AD.

2. Wybierz aplikację, hasło logowania jednokrotnego z włączonym, w panelu dostępu.

3. Po wyświetleniu monitu wybierz **Zainstaluj teraz**.

4. Nastąpi przekierowanie do łącza pobierania w zależności od przeglądarki. Wybierz **Dodaj** można zainstalować rozszerzenia przeglądarki.

5. Po wyświetleniu monitu wybierz **Włącz** lub **Zezwalaj**.

6. Po zakończeniu instalacji uruchom ponownie przeglądarkę.

7.  Zaloguj się do panelu dostępu i zobacz, jeśli hasło logowania jednokrotnego z włączonym aplikacji może zostać uruchomiony.

Można także bezpośrednio pobrać rozszerzenia dla programu Chrome i Firefox za pośrednictwem tych linków:

-   [Rozszerzenie panelu dostępu dla programu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozszerzenie panelu dostępu w przeglądarce Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Konfigurowanie zasad grupy dla programu Internet Explorer

Można skonfigurować zasady grupy, która pozwala na zdalnie zainstalować rozszerzenie panelu dostępu dla programu Internet Explorer na komputerach użytkowników.

Poniżej przedstawiono wymagania wstępne:

-   [Usług domenowych Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) muszą zostać skonfigurowane, i komputerów użytkowników muszą być przyłączone do domeny.

-   Masz "Edytuj ustawienia" uprawnień do edycji obiektu zasad grupy (GPO). Domyślnie członkowie następujące grupy zabezpieczeń mają to uprawnienie: Administratorzy domeny, Administratorzy przedsiębiorstwa i Twórcy-właściciele zasad grupy. [Dowiedz się więcej](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Aby skonfigurować zasady grupy i wdrożyć ją dla użytkowników, zobacz [sposób wdrażania rozszerzenia Panelu dostępu dla programu Internet Explorer przy użyciu zasad grupy](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy).

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Rozwiązywanie problemów z panelu dostępu w programie Internet Explorer

Aby uzyskać dostęp do narzędzia do diagnostyki i instrukcje dotyczące konfigurowania rozszerzenia, zobacz [Rozwiązywanie problemów z rozszerzeniem panelu dostępu dla programu Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Skonfigurować hasło logowania jednokrotnego dla aplikacji z galerii usługi Azure AD

Aby skonfigurować aplikację z galerii usługi Azure AD, należy wykonać następujące czynności:

-   Dodawanie aplikacji z galerii usługi Azure AD
-   [Konfigurowanie aplikacji na potrzeby hasła logowania jednokrotnego](#configure-the-app-for-password-sso)
-   [Przypisywanie użytkowników do aplikacji](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

Wykonaj następujące kroki:

1. Otwórz [witryny Azure portal](https://portal.azure.com) i zaloguj się jako administrator globalny lub współadministratora.

2. Wybierz **wszystkich usług** w górnej części okienka nawigacji po lewej stronie, aby otworzyć rozszerzenia usługi Azure AD.

3. Typ **usługi Azure Active Directory** w filtru pole wyszukiwania, a następnie wybierz **usługi Azure Active Directory**.

4. Wybierz **aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.

5. Wybierz **Dodaj** w prawym górnym rogu **aplikacje dla przedsiębiorstw** okienka.

6. W **Dodaj z galerii** sekcji, wpisz nazwę aplikacji w **wprowadź nazwę** pole.

7. Wybierz aplikację, którą chcesz skonfigurować na potrzeby logowania jednokrotnego.

8. *Opcjonalnie:* Przed dodaniem aplikacji, można zmienić jej nazwę w **nazwa** pole.

9. Kliknij przycisk **Dodaj** Aby dodać aplikację.

   Po krótkim opóźnieniu będziesz mieć możliwość Zobacz okienko konfiguracji aplikacji.

### <a name="configure-the-app-for-password-sso"></a>Konfigurowanie aplikacji na potrzeby hasła logowania jednokrotnego

Wykonaj następujące kroki:

1. Otwórz [witryny Azure portal](https://portal.azure.com/) i zaloguj się jako administrator globalny lub współadministratora.

2. Wybierz **wszystkich usług** w górnej części okienka nawigacji po lewej stronie, aby otworzyć rozszerzenia usługi Azure AD.

3. Typ **usługi Azure Active Directory** w filtru pole wyszukiwania, a następnie wybierz **usługi Azure Active Directory**.

4. Wybierz **aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.

5. Wybierz **wszystkie aplikacje** Aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz aplikacji, które mają używać **filtru** formant w górnej części **listę wszystkich aplikacji**. Ustaw **Pokaż** opcję "Wszystkie aplikacje".

6. Wybierz aplikację, którą chcesz skonfigurować na potrzeby logowania jednokrotnego.

7. Po załadowaniu aplikacji wybierz **logowanie jednokrotne** w okienku po lewej stronie aplikacji.

8. Wybierz **opartego na hasłach logowania jednokrotnego** trybu.

9. Przypisywanie użytkowników do aplikacji.

10. Można również podać poświadczenia dla użytkowników. (W przeciwnym razie użytkownicy otrzymują monit o podanie poświadczeń przy uruchamianiu aplikacji.) Aby to zrobić, wybierz wiersze użytkowników. Następnie wybierz pozycję **poświadczenia aktualizacji** i wprowadź nazwę użytkownika i hasła.

### <a name="assign-users-to-the-app"></a>Przypisywanie użytkowników do aplikacji

Aby przypisać użytkowników do aplikacji bezpośrednio, wykonaj następujące kroki:

1. Otwórz [witryny Azure portal](https://portal.azure.com/) i zaloguj się jako administrator globalny.

2. Wybierz **wszystkich usług** w ból nawigacji po lewej stronie, aby otworzyć rozszerzenia usługi Azure AD.

3. Typ **usługi Azure Active Directory** w filtru pole wyszukiwania, a następnie wybierz **usługi Azure Active Directory**.

4. Wybierz **aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.

5. Wybierz **wszystkie aplikacje** Aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz aplikacji, które mają używać **filtru** formant w górnej części **listę wszystkich aplikacji**. Ustaw **Pokaż** opcję "Wszystkie aplikacje".

6. Z listy wybierz aplikację, która ma zostać przypisany użytkownikowi.

7. Po załadowaniu aplikacji wybierz **użytkowników i grup** z aplikacji w okienku nawigacji po lewej stronie.

8. Wybierz **Dodaj** w górnej części **użytkowników i grup** liście, aby otworzyć **Dodaj przydziału** okienka.

9. Wybierz **użytkowników i grup** w **Dodaj przydziału** okienka.

10. W **wyszukiwanie według nazwy lub adresu e-mail** wpisz pełną nazwę lub użytkownik, który ma zostać przypisany adres e-mail.

11. Umieść kursor nad użytkownika na liście. Zaznacz pole wyboru obok zdjęcie w profilu lub logo, które można dodać tego użytkownika dla użytkownika **wybrane** listy.

12. *Opcjonalnie:* Aby dodać innego użytkownika, wpisz inną nazwę lub adres e-mail w **wyszukiwanie według nazwy lub adresu e-mail** , a następnie wybierz pole wyboru, aby dodać tego użytkownika do **wybrane** listy.

13. Gdy skończysz, Wybieranie użytkowników, kliknij przycisk **wybierz** je dodać do listy użytkowników i grupy, którzy zostali przypisani do aplikacji.

14. *Opcjonalnie:* Kliknij przycisk **wybierz rolę** w **Dodaj przydziału** okienku wybierz rolę do przypisania do użytkowników, które zostały wybrane.

15. Wybierz **przypisać** można przypisać ją do wybranych użytkowników.

    Po krótkim opóźnieniu użytkownicy będą mogli uzyskiwać dostęp do tych aplikacji z poziomu panelu dostępu.

## <a name="request-support"></a>Żądania pomocy technicznej 
Jeśli otrzymasz komunikat o błędzie podczas konfigurowania logowania jednokrotnego i przypisywać użytkowników, otwórz bilet pomocy technicznej. Zawierać tak dużo następujące informacje, jak to możliwe:

-   Identyfikator błędu korelacji
-   Nazwy UPN (adres e-mail użytkownika)
-   Identyfikator dzierżawy
-   Typ przeglądarki
-   Strefę czasową i czas i czas ramki w momencie wystąpienia błędu
-   Ślady narzędzia fiddler

## <a name="next-steps"></a>Kolejne kroki
[Udostępnij logowanie jednokrotne do aplikacji serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
