---
title: Dodawanie osobistych kont Microsoft do aplikacji Microsoft Authenticator — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak dodać osobistych kont Microsoft, takich jak Outlook.com lub Xbox LIVE w aplikacji Microsoft Authenticator dla weryfikacji two-Factor Authentication.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.openlocfilehash: d3fa60dedb3d8319e80eebf5d29ea1defa509bdb
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468550"
---
# <a name="add-your-personal-microsoft-accounts"></a>Dodawanie osobistych kont Microsoft
Dodaj osobistych kont Microsoft, takich jak Outlook.com, i Xbox LIVE w aplikacji Microsoft Authenticator dla procesu standardowa two-Factor Authentication weryfikacji i metodę logowania bez hasła telefonu.

- **Metoda standardowa weryfikacji two-Factor Authentication.** Wpisz nazwę użytkownika i hasło na urządzeniu, w przypadku logowania się do, a następnie wybierz czy aplikację Microsoft Authenticator wyśle powiadomienie, lub jeśli chcesz skopiować kod weryfikacyjny skojarzone z **kont** ekranu Aplikacja Microsoft Authenticator.

- **Bez hasła metodę logowania.** Wpisz swoją nazwę użytkownika na urządzeniu, w przypadku logowania się do osobistego konta Microsoft, a następnie używania urządzenia przenośnego, aby sprawdzić, czy chodzi o Ciebie przy użyciu linii papilarnych, powierzchni lub numeru PIN. W przypadku tej metody nie trzeba wprowadzić hasło.

>[!Important]
>Przed dodaniem konta należy pobrać i zainstalować aplikację Microsoft Authenticator. Jeśli użytkownik jeszcze nie, wykonaj kroki opisane w [pobrać i zainstalować aplikację](microsoft-authenticator-app-how-to.md) artykułu.

## <a name="add-your-personal-microsoft-account"></a>Dodaj osobiste konto Microsoft
Możesz dodać Twojego osobistego konta Microsoft, włączając weryfikacji two-Factor Authentication, a następnie, dodając je do aplikacji.

>[!Note]
>Jeśli zamierzasz używać tylko logowanie za pomocą telefonu bez hasła dla Twojego osobistego konta Microsoft, nie trzeba włączyć weryfikację two-Factor Authentication. Jako dodatkowego zabezpieczenia konta, zalecamy włączenie weryfikacji two-Factor Authentication.

### <a name="turn-on-two-factor-verification"></a>Włącz weryfikację two-Factor Authentication

1. Na komputerze, przejdź do swojej [podstawy zabezpieczeń](https://account.microsoft.com/security) strony i zaloguj się przy użyciu osobistego konta Microsoft. Na przykład alain@outlook.com.

2. W dolnej części **podstawy zabezpieczeń** wybierz **więcej opcji zabezpieczeń** łącza.

    ![Strona podstawy zabezpieczeń z wyróżnionym linkiem "więcej opcji zabezpieczeń"](./media/microsoft-authenticator-app-add-personal-account/more-security-options-link.png)

3. Przejdź do **weryfikacji dwuetapowej** sekcji, a następnie wybierz opcję włączyć funkcję **na**. Można również wyłączyć ją tutaj, jeśli nie chcesz jej używać z konta osobistego.

### <a name="add-your-microsoft-account-to-the-app"></a>Dodaj konto Microsoft do aplikacji

1. Otwórz aplikację Microsoft Authenticator na swoim urządzeniu przenośnym.

2. Wybierz **Dodaj konto** z **dostosowywanie i kontroli** ikonę w prawym górnym rogu.

    ![Strona konta, z wyróżnioną ikoną Dostosuj i kontrola](./media/microsoft-authenticator-app-add-personal-account/customize-and-control-icon.png)

3. W **Dodaj konto** wybierz **konto osobiste**.

4. Zaloguj się do konta osobistego, przy użyciu adresu e-mail odpowiednie (takie jak alain@outlook.com), a następnie wybierz **dalej**.

    >[!Note]
    >Jeśli nie masz osobiste konto Microsoft, możesz ją utworzyć w tym miejscu.

5. Wprowadź hasło, a następnie wybierz **Zaloguj**.

    Twojego osobistego konta zostanie dodany do aplikacji Microsoft Authenticator.

## <a name="next-steps"></a>Kolejne kroki

- Po dodaniu konta do aplikacji, można zalogować się przy użyciu aplikacji Authenticator na urządzeniu. Aby uzyskać więcej informacji, zobacz [Zaloguj się przy użyciu aplikacji](microsoft-authenticator-app-phone-signin-faq.md).

- Dla urządzeń z systemem iOS można również wykonać kopię zapasową poświadczeń konta usługi i powiązane ustawienia aplikacji, takie jak kolejność Twoje konta z chmurą. Aby uzyskać więcej informacji, zobacz [tworzenia kopii zapasowych i odzyskiwanie przy użyciu aplikacji Microsoft Authenticator](microsoft-authenticator-app-backup-and-recovery.md).
