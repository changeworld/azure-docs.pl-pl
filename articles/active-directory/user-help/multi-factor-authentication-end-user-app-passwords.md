---
title: Jak zarządzać hasłami aplikacji — Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o hasłach aplikacji i jakie są używane do w odniesieniu do weryfikacji dwuetapowej.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6790fa1cbb10999a751b31bcb27db2edcb67b4a
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517763"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Zarządzanie hasłami aplikacji weryfikacji dwuetapowej

Niektóre aplikacje korzystające z przeglądarki, takich jak Outlook 2010, nie obsługuje weryfikację dwuetapową. Ten brak obsługi oznacza, że jeśli używasz weryfikacji dwuetapowej, aplikacja nie będzie działać. Aby obejść ten problem, można utworzyć hasło wygenerowane automatycznie za pomocą każdej aplikacji niekorzystających z przeglądarki, niezależnie od normalnych hasła.

Korzystanie z haseł aplikacji, to należy pamiętać:

- Hasła aplikacji są generowane automatycznie i tylko raz wprowadzone na aplikację.

- Istnieje limit 40 haseł na użytkownika. Jeśli spróbujesz utworzyć po osiągnięciu tego limitu, zostanie wyświetlony monit można usunąć istniejącego hasła przed uzyskaniem dostępu do utworzenia nowego.

- Używanie jednego hasła aplikacji, na każdym urządzeniu, a nie na aplikację. Na przykład utworzyć jednego hasła dla wszystkich aplikacji na komputerze przenośnym i następnie inne jednego hasła dla wszystkich aplikacji na pulpicie.

    >[!Note]
    >Klienci pakietu Office 2013 (w tym programu Outlook) obsługuje nowe protokoły uwierzytelniania i może być używany z weryfikacji dwuetapowej. Ta obsługa oznacza, że po włączeniu weryfikacji dwuetapowej już nie ma hasła aplikacji dla klientów pakietu Office 2013. Aby uzyskać więcej informacji, zobacz [sposobu działania nowoczesnego uwierzytelniania dla aplikacji klienckich pakietu Office 2013 i Office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) artykułu.

## <a name="where-to-create-and-delete-your-app-passwords"></a>Gdzie można tworzyć i usuwać haseł aplikacji

Podczas rejestracji weryfikacji dwuetapowej początkowej otrzymuje hasła aplikacji. Jeśli potrzebujesz więcej niż jeden hasło, można utworzyć dodatkowe haseł, w oparciu o sposobie korzystania z weryfikacji dwuetapowej:

- **Weryfikacja dwuetapowa jest używany z pracy lub nauki i portalu MyApps.** Tworzenie i usuwanie hasła aplikacji zgodnie z instrukcjami tworzenia i usuwanie hasła aplikacji przy użyciu portalu MyApps części tego artykułu. Aby uzyskać więcej informacji na temat portalu MyApps i jak z niej korzystać, zobacz [co to jest portal MyApps, w usłudze Azure Active Directory?](active-directory-saas-access-panel-introduction.md).

- **Weryfikacja dwuetapowa jest używany z pracy lub konta służbowego i portalu usługi Office 365.** Tworzenie i usuwanie hasła aplikacji przy użyciu instrukcji w [tworzenie i usuwanie hasła aplikacji przy użyciu portalu usługi Office 365](#create-and-delete-app-passwords-using-the-office-365-portal) dalszej części tego artykułu.

- **Weryfikacja dwuetapowa jest używany z osobistego konta Microsoft.** Tworzenie i usuwanie hasła aplikacji przy użyciu [podstawy zabezpieczeń](https://account.microsoft.com/account/) strony za pomocą osobistego konta Microsoft. Aby uzyskać więcej informacji, zobacz [haseł aplikacji i weryfikacji dwuetapowej](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification) artykułu.

## <a name="create-and-delete-app-passwords-using-the-myapps-portal"></a>Tworzenie i usuwanie hasła aplikacji przy użyciu portalu MyApps
Można tworzyć i usuwanie hasła aplikacji za pośrednictwem portalu MyApps.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Aby utworzyć hasła aplikacji przy użyciu portalu MyApps

1. Zaloguj się do witryny [https://myapps.microsoft.com](https://myapps.microsoft.com).

2. Wybierz nazwę w prawym górnym rogu, a następnie wybierz **profilu**.

3. Wybierz **dodatkowej weryfikacji zabezpieczeń**.

   ![Wybierz opcję dodatkowa weryfikacja zabezpieczeń — zrzut ekranu](./media/multi-factor-authentication-end-user-app-passwords/myapps1.png)

4. Wybierz **haseł aplikacji**.

   ![Wybierz aplikację haseł — zrzut ekranu](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Kliknij pozycję **Utwórz**.

6. Wpisz nazwę dla hasła aplikacji, a następnie wybierz **dalej**.

7. Kopiuj hasło aplikacji do Schowka i wklej go w swojej aplikacji.
   
    ![Utwórz hasło aplikacji](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Aby usunąć hasła aplikacji przy użyciu portalu MyApps

1. Przejdź do swojego profilu, a następnie wybierz **dodatkowa weryfikacja zabezpieczeń**.

2. Wybierz **haseł aplikacji**, a następnie wybierz pozycję **Usuń** obok hasło aplikacji do usunięcia.

   ![Usuwanie hasła aplikacji](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

3. Wybierz **tak** aby upewnić się, aby usunąć hasło, a następnie wybierz **Zamknij**.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Tworzenie i usuwanie hasła aplikacji przy użyciu portalu usługi Office 365

Jeśli używasz weryfikacji dwuetapowej za pomocą pracy lub konta służbowego i aplikacji usługi Office 365, można tworzyć i usuwanie hasła aplikacji przy użyciu portalu usługi Office 365. Użytkownik nie może przekraczać 40 haseł aplikacji w dowolnym momencie. Jeśli potrzebujesz innego hasła aplikacji po osiągnięciu tego limitu, musisz usunąć istniejące hasła aplikacji.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Aby utworzyć hasła aplikacji przy użyciu portalu usługi Office 365

1. Zaloguj się do swojego konta firmowego lub szkolnego.

2. Przejdź do [ https://portal.office.com ](https://portal.office.com), wybierz opcję **ustawienia** ikonę w prawym górnym rogu **portalu usługi Office 365** strony, a następnie rozwiń **dodatkowe zabezpieczenia Weryfikacja**.

    ![Portal przedstawiający Office rozwinięty obszar weryfikacji zabezpieczeń](media/security-info/security-info-o365password.png)

3. Zaznacz tekst, który jest wyświetlany komunikat, **tworzenie haseł aplikacji i zarządzanie nimi** otworzyć **haseł aplikacji** strony.

4. Wybierz **Utwórz**, wpisz przyjazną nazwę dla aplikacji, który wymaga hasła aplikacji, a następnie wybierz **dalej**.

5. Wybierz **Kopiuj hasło do Schowka**, a następnie wybierz pozycję **Zamknij**.

6. Umożliwia Zaloguj się do aplikacji niekorzystających z przeglądarki hasło skopiowane aplikacji. Musisz tylko raz wprowadzić to hasło, a następnie zapamiętywane jest w przyszłości.

### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>Aby usunąć hasła aplikacji przy użyciu portalu usługi Office 365

1. Zaloguj się do swojego konta firmowego lub szkolnego.

2. Przejdź do [ https://portal.office.com ](https://portal.office.com), wybierz opcję **ustawienia** ikonę w prawym górnym rogu **portalu usługi Office 365** strony, a następnie wybierz pozycję **dodatkowe zabezpieczenia Weryfikacja**.

3. Zaznacz tekst, który jest wyświetlany komunikat, **tworzenie haseł aplikacji i zarządzanie nimi** otworzyć **haseł aplikacji** strony.

4. Wybierz **Usuń** hasła aplikacji, można usunąć, wybierz **tak** w okno dialogowe z potwierdzeniem, a następnie wybierz **Zamknij**.

    Usunięto hasło aplikacji.

5. Wykonaj kroki tworzenia hasła aplikacji, aby utworzyć nowe hasło aplikacji.

## <a name="if-your-app-passwords-arent-working-properly"></a>Jeśli Twoje hasła aplikacji nie działają prawidłowo

Upewnij się, że hasło została wpisana poprawnie. Jeśli wiesz, że wprowadzone hasło jest prawidłowe, możesz zalogować się ponownie i Utwórz nowe hasło aplikacji. Jeśli żadna z tych opcji nie rozwiąże problemu, skontaktuj się z działem pomocy technicznej Twojej firmy, dzięki czemu można ich usunąć istniejące hasła aplikacji, co pozwala utworzyć zupełnie nowym. 

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie ustawieniami weryfikacji dwuetapowej](multi-factor-authentication-end-user-manage-settings.md)

- Wypróbuj [aplikacji Microsoft Authenticator](user-help-auth-app-download-install.md) Aby zweryfikować swoje logowania za pomocą aplikacji powiadomień, nie odbiera teksty lub wywołania.
