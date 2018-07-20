---
title: 'Instrukcje: Konfigurowanie funkcji zapisywania zwrotnego haseł dla usługi Azure AD SSPR'
description: Użyj usługi Azure AD i Azure AD Connect do zapisywania zwrotnego haseł do katalogu lokalnego
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: e613ff742096077fe1765d4b855b6c7d409cc228
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158951"
---
# <a name="how-to-configure-password-writeback"></a>Instrukcje: Konfigurowanie funkcji zapisywania zwrotnego haseł

Firma Microsoft zaleca, aby używać funkcji automatycznej aktualizacji [program Azure AD Connect](./../connect/active-directory-aadconnect-get-started-express.md) podczas korzystania z funkcji zapisywania zwrotnego haseł.

W następujących krokach założono, program Azure AD Connect została już skonfigurowana w danym środowisku, za pomocą [Express](./../connect/active-directory-aadconnect-get-started-express.md) lub [niestandardowe](./../connect/active-directory-aadconnect-get-started-custom.md) ustawienia.

1. Aby skonfigurować i włączyć funkcję zapisywania zwrotnego haseł, zaloguj się do serwera usługi Azure AD Connect i uruchomić **program Azure AD Connect** Kreatora konfiguracji.
2. Na **powitalnej** wybierz opcję **Konfiguruj**.
3. Na **dodatkowe zadania** wybierz opcję **Dostosowywanie opcji synchronizacji**, a następnie wybierz pozycję **dalej**.
4. Na **nawiązywanie połączenia z usługi Azure AD** strony, wprowadź poświadczenia administratora globalnego, a następnie wybierz pozycję **dalej**.
5. Na **łączenie katalogów** i **domen/jednostek organizacyjnych** filtrowanie stron, wybierz **dalej**.
6. Na **funkcje opcjonalne** strony, zaznacz pole obok pozycji **funkcji zapisywania zwrotnego haseł** i wybierz **dalej**.
   ![Włączanie zapisywania zwrotnego haseł w programie Azure AD Connect][Writeback]
7. Na **wszystko gotowe do skonfigurowania** wybierz opcję **Konfiguruj** i poczekaj na zakończenie procesu.
8. Po wyświetleniu konfiguracji Zakończ wybierz **zakończenia**.

Typowe zadania dotyczące rozwiązywania problemów związanych z zapisywaniem zwrotnym haseł, zobacz sekcję dotyczącą [Rozwiązywanie problemów z zapisywaniem zwrotnym haseł](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) w naszym artykuł dotyczący rozwiązywania problemów.

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
7. W obszarze **uprawnienia**, zaznacz następujące pola:
    * **Resetowanie hasła**
    * **Zmień hasło**
    * **Zapis lockoutTime**
    * **Zapis pwdLastSet**
8. Wybierz **Zastosuj/OK** Aby zastosować zmiany i zamknąć okna dialogowe.

## <a name="next-steps"></a>Kolejne kroki

[Co to jest funkcja zapisywania zwrotnego haseł?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Włączanie zapisywania zwrotnego haseł w programie Azure AD Connect"
