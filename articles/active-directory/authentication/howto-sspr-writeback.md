---
title: 'Instrukcje: Konfigurowanie funkcji zapisywania zwrotnego haseł dla usługi Azure AD SSPR — usługi Azure Active Directory'
description: Użyj usługi Azure AD i Azure AD Connect do zapisywania zwrotnego haseł do katalogu lokalnego
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17a2661883dd069e8cb719672f6b92442f1a8a0a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60357520"
---
# <a name="how-to-configure-password-writeback"></a>Porady: Konfigurowanie zapisywania zwrotnego haseł

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
> Aby uniknąć przerw w działaniu usługi, uaktualnianie z poprzedniej wersji programu Azure AD Connect do nowszej wersji, zobacz artykuł [program Azure AD Connect: Uaktualnianie z poprzedniej wersji do najnowszej](../hybrid/how-to-upgrade-previous-version.md)
>

## <a name="licensing-requirements-for-password-writeback"></a>Wymagania licencyjne dla funkcji zapisywania zwrotnego haseł

**Samoobsługowe hasło Resetowanie/zmiana/odblokowywanie przy użyciu funkcji zapisywania zwrotnego w środowisku lokalnym jest funkcją premium usługi Azure AD**. Aby uzyskać więcej informacji na temat licencjonowania, zobacz [usługi Azure Active Directory ceny witryny](https://azure.microsoft.com/pricing/details/active-directory/).

Aby użyć funkcji zapisywania zwrotnego haseł, musi mieć jeden z następujących licencji, które są przypisane w dzierżawie usługi:

* Usługa Azure AD — warstwa Premium P1
* Usługa Azure AD — warstwa Premium P2
* Pakiet Enterprise Mobility + Security E3 lub A3
* Pakiet Enterprise Mobility + Security E5 lub A5
* Rozwiązania Microsoft 365 E3 lub A3
* Rozwiązania Microsoft 365 E5 lub A5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> Licencjonowanie plany usługi Office 365 autonomiczny *nie obsługują "Samoobsługi hasło Resetowanie/zmiana/odblokowywanie przy użyciu funkcji zapisywania zwrotnego w środowisku lokalnym"* i wymagają jednego z poprzednich planów dla tej funkcji do pracy.
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
6. W **dotyczy** listy rozwijanej wybierz **obiektów użytkowników podrzędny**.
7. W obszarze **uprawnienia**, zaznacz pola dla następujących opcji:
    * **Zmień hasło**
    * **Resetowanie hasła**
8. W obszarze **właściwości**, zaznacz pola dla następujących opcji:
    * **Zapis lockoutTime**
    * **Zapis pwdLastSet**
9. Wybierz **Zastosuj/OK** Aby zastosować zmiany i zamknąć okna dialogowe.

## <a name="next-steps"></a>Kolejne kroki

[Co to jest funkcja zapisywania zwrotnego haseł?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Włączanie zapisywania zwrotnego haseł w programie Azure AD Connect"
