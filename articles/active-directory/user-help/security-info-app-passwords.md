---
title: Konfigurowanie haseł aplikacji z poziomu stronę informacji zabezpieczających (wersja zapoznawcza) — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Konfigurowanie automatycznego generowania haseł (hasła) za pomocą dowolnej aplikacji niekorzystających z przeglądarki lub dowolną aplikację, która nie obsługuje weryfikację two-Factor Authentication w Twojej organizacji. To hasło aplikacji jest oddzielony od normalnych hasła i można skonfigurować na stronie informacje zabezpieczeń.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55dfab0c60e77b86157a005db34c37917a5e08d2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60475116"
---
# <a name="manage-app-passwords-from-your-security-info-preview-page"></a>Zarządzanie hasłami aplikacji z Twojego stronę informacji zabezpieczających (wersja zapoznawcza)
Niektóre aplikacje, takie jak Outlook 2010, nie obsługują weryfikacji dwuetapowej. To Brak obsługi oznacza, że w przypadku używania weryfikacji dwuetapowej w Twojej organizacji nie będzie działać aplikacji. Aby obejść ten problem, można utworzyć hasło wygenerowane automatycznie za pomocą każdej aplikacji niekorzystających z przeglądarki, niezależnie od normalnych hasła.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>Administrator może nie pozwalają na stosowanie haseł aplikacji. Jeśli nie widzisz **haseł aplikacji** jako opcja, nie są dostępne w Twojej organizacji.

Korzystanie z haseł aplikacji, to należy pamiętać:

- Hasła aplikacji są generowane automatycznie i tylko raz wprowadzone na aplikację.

- Istnieje limit 40 haseł na użytkownika. Jeśli spróbujesz utworzyć po osiągnięciu tego limitu, zostanie wyświetlony monit można usunąć istniejącego hasła przed uzyskaniem dostępu do utworzenia nowego.

- Używanie jednego hasła aplikacji, na każdym urządzeniu, a nie na aplikację. Na przykład utworzyć jednego hasła dla wszystkich aplikacji na komputerze przenośnym i następnie inne jednego hasła dla wszystkich aplikacji na pulpicie.

    >[!Note]
    >Klienci pakietu Office 2013 (w tym programu Outlook) obsługuje nowe protokoły uwierzytelniania i może być używany z weryfikacji dwuetapowej. Ta obsługa oznacza, że po włączeniu weryfikacji dwuetapowej już nie ma hasła aplikacji dla klientów pakietu Office 2013. Aby uzyskać więcej informacji, zobacz [sposobu działania nowoczesnego uwierzytelniania dla aplikacji klienckich pakietu Office 2013 i Office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) artykułu.

## <a name="create-new-app-passwords"></a>Tworzenie nowych haseł aplikacji
Jeśli używasz weryfikacji dwuetapowej za pomocą konta służbowego lub szkolnego, a administrator wyłączył środowisko informacje zabezpieczeń, można tworzyć i usuwać przy użyciu haseł aplikacji **zabezpieczające** strony.

>[!Note]
>Jeśli administrator nie zostało włączone środowisko informacje zabezpieczeń, należy wykonać instrukcje i informacje zawarte w [Zarządzanie hasła aplikacji na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-app-passwords.md) sekcji.

### <a name="to-create-a-new-app-password"></a>Aby utworzyć nowe hasło aplikacji
1. Zaloguj się do swojego konta firmowego lub szkolnego, a następnie przejdź do swojej https://myprofile.microsoft.com/ strony.

    ![Strona Moje profilu, pokazujący wyróżnione łączy informacje zabezpieczeń](media/security-info/securityinfo-myprofile-page.png)

2. Wybierz **zabezpieczające** z lewego okienka nawigacji lub linku w **zabezpieczające** zablokować, a następnie wybierz **Dodaj metodę** z **informacje zabezpieczające**  strony.

    ![Strona informacji zabezpieczających przy użyciu opcji Dodaj wyróżnione w — metoda](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na **Dodaj metodę** wybierz opcję **hasła aplikacji** z listy rozwijanej, a następnie wybierz **Dodaj**.

    ![Dodaj pole metody przy użyciu hasła aplikacji wybrane](media/security-info/securityinfo-myprofile-addpassword.png)

4. Wpisz nazwę aplikacji, która wymaga hasła aplikacji, a następnie wybierz **dalej**.

    ![Strona hasła aplikacji, z nazwą aplikacji](media/security-info/securityinfo-myprofile-password-appname.png)

5. Skopiuj tekst z **hasło** , wkleić hasło w obszarze hasła aplikacji (w tym przykładzie program Outlook 2010), a następnie wybierz **gotowe**.

    ![Strona hasła aplikacji, z nazwą aplikacji](media/security-info/securityinfo-myprofile-password-copytext.png)
    
    Hasło zostanie dodany, a następnie możesz pomyślnie zalogować się do aplikacji w przyszłości.

## <a name="delete-your-app-passwords"></a>Usuwanie hasła aplikacji
Jeśli nie są już potrzebne do korzystania z aplikacji, który wymaga hasła aplikacji, należy usunąć hasło skojarzonej aplikacji. Usuwanie hasła aplikacji zwolni jeden chętnych hasła — aplikacja dostępna do użytku w przyszłości.

>[!Important]
>Hasła aplikacji zostanie usunięty przez pomyłkę, czy można go cofnąć. Musisz utworzyć nowe hasło aplikacji i wprowadź go ponownie w aplikacji, wykonaj czynności w [utworzyć nowe hasła](#create-new-app-passwords) dalszej części tego artykułu.

### <a name="to-delete-an-app-password"></a>Aby usunąć hasło aplikacji

1. Na **zabezpieczające** wybierz opcję **Usuń** łącze obok **hasła aplikacji** opcji dla określonej aplikacji.

    ![Łącze, aby usunąć metodę haseł aplikacji z informacje zabezpieczające](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. Wybierz **tak** z okno dialogowe potwierdzenia, aby usunąć **hasła aplikacji**. Po hasła aplikacji zostanie usunięty, zostanie on usunięty z informacjom zabezpieczającym i znika ono z **zabezpieczające** strony.

## <a name="for-more-information"></a>Więcej informacji
- Aby uzyskać więcej informacji na temat **zabezpieczające** stronę i ustawić go tak, zobacz [Przegląd informacji zabezpieczeń](user-help-security-info-overview.md)
