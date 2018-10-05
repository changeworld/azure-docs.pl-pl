---
title: 'Instrukcje: Konfigurowanie funkcji zapisywania zwrotnego haseł dla usługi Azure AD SSPR'
description: Użyj usługi Azure AD i Azure AD Connect do zapisywania zwrotnego haseł do katalogu lokalnego
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 57446f068945d74351a77f21e16874d9e24ddcab
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801934"
---
# <a name="how-to-configure-password-writeback"></a>Instrukcje: Konfigurowanie funkcji zapisywania zwrotnego haseł

Firma Microsoft zaleca, aby używać funkcji automatycznej aktualizacji [program Azure AD Connect](../hybrid/how-to-connect-install-express.md) podczas korzystania z funkcji zapisywania zwrotnego haseł.

W następujących krokach założono, program Azure AD Connect została już skonfigurowana w danym środowisku, za pomocą [Express](../hybrid/how-to-connect-install-express.md) lub [niestandardowe](../hybrid/how-to-connect-install-custom.md) ustawienia.

1. Aby skonfigurować i włączyć zapisywanie zwrotne haseł, zaloguj się na serwerze programu Azure AD Connect i uruchom kreatora konfiguracji programu **Azure AD Connect**.
2. Na **stronie powitalnej** wybierz pozycję **Konfiguruj**.
3. Na stronie **Zadania dodatkowe** wybierz pozycję **Dostosuj opcje synchronizacji**, a następnie wybierz pozycję **Dalej**.
4. Na stronie **Łączenie z usługą Azure AD** wprowadź poświadczenia administratora globalnego, a następnie wybierz pozycję **Dalej**.
5. Na stronach filtrowania **Łączenie katalogów** i **Domena/jednostka OU** wybierz pozycję **Dalej**.
6. Na stronie **Funkcje opcjonalne** zaznacz pole obok pozycji **Zapisywanie zwrotne haseł** i wybierz pozycję **Dalej**.
   ![Włączanie zapisywania zwrotnego haseł w programie Azure AD Connect][Writeback]
7. Na stronie **Wszystko gotowe do skonfigurowania** wybierz pozycję **Konfiguruj** i poczekaj na zakończenie procesu.
8. Po ukończeniu konfiguracji wybierz pozycję **Zakończ**.

Typowe zadania dotyczące rozwiązywania problemów związanych z zapisywaniem zwrotnym haseł, zobacz sekcję dotyczącą [Rozwiązywanie problemów z zapisywaniem zwrotnym haseł](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) w naszym artykuł dotyczący rozwiązywania problemów.

> [!WARNING]
> Zapisywanie zwrotne haseł przestaną działać dla klientów, którzy korzystają z wersji Azure AD Connect 1.0.8641.0 i gdy starsze [Azure Access Control service (ACS) została wycofana w dniu 7 listopada 2018](../develop/active-directory-acs-migration.md). Usługa Azure AD Connect wersje 1.0.8641.0 i starsze nie będzie już umożliwiać funkcji zapisywania zwrotnego haseł w tym czasie, ponieważ są one zależne od usługi ACS do obsługi tej funkcji.
>
> Aby uniknąć przerw w działaniu usługi, uaktualnianie z poprzedniej wersji programu Azure AD Connect do nowszej wersji, zobacz artykuł [program Azure AD Connect: uaktualnianie z poprzedniej wersji do najnowszej wersji](../hybrid/how-to-upgrade-previous-version.md)
>

## <a name="active-directory-permissions"></a>Uprawnienia usługi Active Directory

Konto określone w narzędziu Azure AD Connect muszą mieć następujące elementy ustawione, jeśli mają być uwzględnione w zakresie funkcji samoobsługowego resetowania HASEŁ:

* **Resetowanie hasła** 
* **Zmień hasło** 
* **Uprawnienia do zapisu** na `lockoutTime`
* **Uprawnienia do zapisu** na `pwdLastSet`
* **Rozszerzone prawa** systemie:
   * Obiekt główny *każdej domeny* w tym lesie
   * Użytkownik jednostki organizacyjne (OU) mają być uwzględnione w zakresie funkcji samoobsługowego resetowania HASEŁ

Jeśli nie masz pewności co konta opisem konta odwołuje się do, Otwórz do usługi Azure Active Directory Connect interfejsu użytkownika konfiguracji i wybierz **wyświetlanie bieżącej konfiguracji** opcji. Konta, które należy dodać uprawnienia do znajduje się w obszarze **synchronizację katalogów**.

Jeśli możesz ustawić te uprawnienia kontom usług ma każdego lasu może Zarządzanie hasłami w imieniu kont użytkownika, w tym lesie. 

> [!IMPORTANT]
> Jeśli nie udzielisz tych uprawnień, następnie, mimo że zapisywania zwrotnego wydaje się być prawidłowo skonfigurowany, użytkownicy będą napotykać błędy podczas prób zarządzania swoich haseł lokalnych w chmurze.
>

> [!NOTE]
> Może potrwać do godziny lub dłużej tych uprawnień można replikować do wszystkich obiektów w katalogu.
>

Aby skonfigurować odpowiednie uprawnienia do zapisywania zwrotnego haseł występuje, wykonaj następujące czynności:

1. Otwórz użytkownicy usługi Active Directory i komputerów za pomocą konta mającego uprawnienia administratora w odpowiedniej domenie.
2. Z **widoku** menu, upewnij się, że **zaawansowane funkcje** jest włączona.
3. W lewym panelu, kliknij prawym przyciskiem myszy obiekt, który reprezentuje katalog główny domeny, a następnie wybierz pozycję **właściwości** > **zabezpieczeń** > **zaawansowane**.
4. Z **uprawnienia** zaznacz **Dodaj**.
5. Wybierz konto, które uprawnienia są stosowane do (z konfiguracji usługi Azure AD Connect).
6. W **dotyczy** listy rozwijanej wybierz **potomne użytkownika** obiektów.
7. W obszarze **uprawnienia**, zaznacz pola dla następujących opcji:
    * **Resetowanie hasła**
    * **Zmień hasło**
    * **Zapis lockoutTime**
    * **Zapis pwdLastSet**
8. Wybierz **Zastosuj/OK** Aby zastosować zmiany i zamknąć okna dialogowe.

## <a name="next-steps"></a>Kolejne kroki

[Co to jest funkcja zapisywania zwrotnego haseł?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Włączanie zapisywania zwrotnego haseł w programie Azure AD Connect"
