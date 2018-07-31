---
title: Skonfigurować hasła aplikacji przy użyciu informacji zabezpieczających — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Konfigurowanie automatycznego generowania haseł (hasła) każdej aplikacji niekorzystających z przeglądarki, za pomocą oddzielić od normalnych hasła, przy użyciu informacji zabezpieczających.
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 56d207dc47b9e055782568f1c4a4cedc4d19a288
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2018
ms.locfileid: "39348682"
---
# <a name="manage-app-passwords-using-security-info-preview"></a>Zarządzanie hasłami aplikacji przy użyciu informacji zabezpieczających (wersja zapoznawcza)

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Niektóre aplikacje korzystające z przeglądarki, takich jak Outlook 2010, nie obsługuje weryfikację dwuetapową. Ten brak obsługi oznacza, że jeśli używasz weryfikacji dwuetapowej, aplikacja nie będzie działać. Aby obejść ten problem, można utworzyć hasło wygenerowane automatycznie za pomocą każdej aplikacji niekorzystających z przeglądarki, niezależnie od normalnych hasła.

Korzystanie z haseł aplikacji, to należy pamiętać:

- Hasła aplikacji są generowane automatycznie i tylko raz wprowadzone na aplikację.

- Istnieje limit 40 haseł na użytkownika. Jeśli spróbujesz utworzyć po osiągnięciu tego limitu, zostanie wyświetlony monit można usunąć istniejącego hasła przed uzyskaniem dostępu do utworzenia nowego.

- Używanie jednego hasła aplikacji, na każdym urządzeniu, a nie na aplikację. Na przykład utworzyć jednego hasła dla wszystkich aplikacji na komputerze przenośnym i następnie inne jednego hasła dla wszystkich aplikacji na pulpicie.

    >[!Note]
    >Klienci pakietu Office 2013 (w tym programu Outlook) obsługuje nowe protokoły uwierzytelniania i może być używany z weryfikacji dwuetapowej. Ta obsługa oznacza, że po włączeniu weryfikacji dwuetapowej już nie ma hasła aplikacji dla klientów pakietu Office 2013. Aby uzyskać więcej informacji, zobacz [sposobu działania nowoczesnego uwierzytelniania dla aplikacji klienckich pakietu Office 2013 i Office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) artykułu.

## <a name="create-and-delete-app-passwords-using-security-info"></a>Tworzenie i usuwanie hasła aplikacji przy użyciu informacji zabezpieczających

Czy używasz weryfikacji dwuetapowej przy użyciu konta służbowego i administrator wyłączył środowisko informacje zabezpieczeń, można tworzyć i usuwać haseł aplikacji przy użyciu portalu Moje aplikacje.

Jeśli administrator nie zostało włączone środowisko informacje zabezpieczeń, należy wykonać instrukcje i informacje zawarte w [Zarządzanie hasła aplikacji na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-app-passwords.md) sekcji.

### <a name="to-create-app-passwords-using-the-my-apps-portal"></a>Aby utworzyć hasła aplikacji przy użyciu portalu Moje aplikacje

1. Zaloguj się do swojego konta firmowego lub szkolnego.

2. Przejdź do myapps.microsoft.com, wybierz nazwę w prawym górnym rogu strony, a następnie wybierz **profilu**.

3. W **Zarządzanie kontem** wybierz opcję **Edytuj informacje zabezpieczające**.

    ![Ekran profil z wyróżnionym linkiem informacje zabezpieczeń edycji](media/security-info/security-info-profile.png)

4. W **zabezpieczyć swoje konto** ekranu, wybierz opcję **Dodaj informacje zabezpieczające**.

    ![Ekran informacje zabezpieczeń z istniejących, można edytować informacje](media/security-info/security-info-edit-add-info.png)

5. W **Dodaj informacje zabezpieczające** ekranu, wybierz opcję **hasła aplikacji**.

6. W **utworzyć hasło aplikacji** ekranu, wpisz nazwę dla hasła aplikacji, a następnie wybierz **dalej**.

    ![Ekran, gdzie nazwa hasło aplikacji](media/security-info/security-info-name-app-password.png)

7. Wybierz **kopiowania** Kopiuj hasło do Schowka, a następnie wybierz pozycję **dalej**.

    ![Ekran przy użyciu hasła aplikacji na potrzeby kopiowania](media/security-info/security-info-create-app-password.png)
    
8. Upewnij się, że hasło aplikacji jest wyświetlany na **zabezpieczyć swoje konto** ekranu.

    ![Nadal bezpiecznego ekranu, przy użyciu hasła aplikacji](media/security-info/security-info-keep-secure-app-password.png)

### <a name="to-delete-app-passwords-using-the-my-apps-portal"></a>Aby usunąć hasła aplikacji przy użyciu portalu Moje aplikacje

1. Na **zabezpieczyć swoje konto** ekranu, wybierz opcję **X** obok hasło aplikacji do usunięcia.

    ![Nadal bezpiecznego ekranu, czy usunąć hasło aplikacji](media/security-info/security-info-keep-secure-delete-app-password.png)

2. W **hasło aplikacji Usuń** ekranu, wybierz opcję **Usuń**.

    ![Usuń ekranie hasła aplikacji](media/security-info/security-info-keep-secure-delete-app-password2.png)

## <a name="next-steps"></a>Kolejne kroki

- Jeśli musisz zaktualizować swoje informacje zabezpieczające, postępuj zgodnie z instrukcjami [Zarządzanie informacjom zabezpieczającym](security-info-manage-settings.md) artykułu.

- Aby uzyskać więcej ogólnych informacji na temat informacji zabezpieczających, i co można zrobić, zobacz [Przegląd informacji zabezpieczeń](user-help-security-info-overview.md) 