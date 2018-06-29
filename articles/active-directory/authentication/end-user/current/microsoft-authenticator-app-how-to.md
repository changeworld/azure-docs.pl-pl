---
title: Aplikacja Microsoft Authenticator dla telefonów komórkowych — usługi Azure AD | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uaktualnić do najnowszej wersji Azure Authenticator.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/17/2017
ms.author: lizross
ms.reviewer: librown
ms.custom: H1Hack27Feb2017, end-user
ms.openlocfilehash: 8241dcaaf5623a22f4fc485f021766276472fb51
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102494"
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Wprowadzenie do aplikacji Microsoft Authenticator
Aplikacja Microsoft Authenticator zawiera dodatkowy poziom zabezpieczeń w ramach Twojego konta służbowego (na przykład bsimon@contoso.com) lub konta Microsoft (na przykład bsimon@outlook.com).

Aplikacja działa w jednym z dwóch sposobów:

* **Powiadomienia**. Aplikacja może pomóc zapobiec nieautoryzowanemu dostępowi do konta i Zatrzymaj fałszywe transakcje przez wypychanie powiadomień do smartfonie lub tablecie. Wystarczy wyświetlić powiadomienie, a jeśli uzasadnione, wybierz **Sprawdź**. W przeciwnym razie można wybrać **Odmów**.
* **Kod weryfikacyjny**. Aplikacja może służyć jako token oprogramowania do wygenerowania kodu weryfikacyjnego OAuth. Po wprowadzeniu nazwy użytkownika i hasła, musisz wprowadzić kod dostarczone przez aplikację do ekranu logowania. Kod weryfikacyjny zapewnia drugiej formy uwierzytelniania.

Aplikacja Microsoft Authenticator zastępuje aplikacji Azure Authenticator. Aplikacja Azure Authenticator nadal działa, ale jeśli zdecydujesz się przejść do nowej aplikacji Authenticator firmy Microsoft, w tym artykule może pomóc.  

## <a name="opt-in-for-two-step-verification"></a>Zgódź się na potrzeby weryfikacji dwuetapowej

Aplikacja Microsoft Authenticator nie działa przez samego siebie. Skonfiguruj każdy swoje konta, aby monitować o drugiej metody weryfikacji po zalogowaniu się przy użyciu nazwy użytkownika i hasła.

Dla konta firmowego lub szkolnego zwykle nie pobieraj wybierz tę funkcję dla siebie. Zamiast tego administrator zabezpieczeń zdecyduje się w Twoim imieniu i następnie powiadamia rejestracji metody weryfikacji dla Twojego konta. Jeśli ten scenariusz ma zastosowanie do Ciebie, zapoznaj się z [co oznacza Azure Multi-Factor Authentication dla mnie](multi-factor-authentication-end-user.md).

Osobiste konta musisz skonfigurowaną weryfikację dwuetapową dla siebie. Jeśli masz konto Microsoft, te kroki są dostępne w [o weryfikacji dwuetapowej](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

Można również używać Authenticator firmy Microsoft z kont innych niż Microsoft. Funkcja one wywołać inną niż weryfikację dwuetapową, ale można go znaleźć w ramach zabezpieczeń lub ustawienia logowania.

## <a name="install-the-app"></a>Zainstaluj aplikację
Jest dostępna dla aplikacji Microsoft Authenticator [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594), i [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

## <a name="add-accounts-to-the-app"></a>Dodaj konta do aplikacji
Dla każdego konta, które chcesz dodać do aplikacji Microsoft Authenticator użyj jednej z następujących procedur:

### <a name="add-a-personal-microsoft-account-to-the-app"></a>Dodawanie osobistego konta Microsoft do aplikacji

Osobistego konta Microsoft (jeden, którego używasz do logowania do usługi Outlook.com, Xbox, Skype, itp.) musisz wykonać będzie zalogować się do konta w aplikacji Authenticator firmy Microsoft.

### <a name="add-a-work-or-school-account-to-the-app-using-the-qr-code-scanner"></a>Dodaj konto służbowe do aplikacji przy użyciu skaner kodów QR
1. Przejdź do ekranu ustawienia weryfikacji zabezpieczeń.  Aby uzyskać informacje dotyczące sposobu uzyskiwania do tego ekranu, zobacz [zmiana ustawień zabezpieczeń](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).
2. Zaznacz pole wyboru obok pozycji **aplikacji uwierzytelniania** następnie wybierz **Konfiguruj**.

    ![Przycisk Konfiguruj na ekranie ustawień weryfikacji zabezpieczeń](./media/microsoft-authenticator-app-how-to/azureauthe.png)

    Spowoduje to wyświetlenie ekranu kod QR.

    ![Ekran, który zawiera kod QR](./media/microsoft-authenticator-app-how-to/barcode2.png)
3. Otwórz aplikację uwierzytelniania firmy Microsoft. Na **kont** ekranu wybierz **+**, a następnie określ, czy chcesz dodać konta służbowego.
4. Umożliwia aparatu Zeskanuj kod QR, a następnie wybierz **gotowe** aby zamknąć ekran kod QR.

    Jeśli aparatu nie działa prawidłowo, możesz [ręcznie wprowadzić kod QR i adres URL](#add-an-account-to-the-app-manually).

5. Jeśli aplikacja zawiera nazwę konta z sześciocyfrowy kod podrzędne, wszystko będzie gotowe.

    ![Ekran kont](./media/microsoft-authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Ręcznie dodaj konto do aplikacji
1. Przejdź do ekranu ustawienia weryfikacji zabezpieczeń.  Aby uzyskać informacje dotyczące sposobu uzyskiwania do tego ekranu, zobacz [zmiana ustawień zabezpieczeń](multi-factor-authentication-end-user-manage-settings.md).
2. Wybierz **skonfigurować**.

    ![Przycisk Konfiguruj na ekranie ustawień weryfikacji zabezpieczeń](./media/microsoft-authenticator-app-how-to/azureauthe.png)

    Spowoduje to wyświetlenie ekranu kod QR.  Należy pamiętać, kod i adres URL.

    ![Ekran, który zawiera kod QR i adres URL](./media/microsoft-authenticator-app-how-to/barcode2.png)
3. Otwórz aplikację uwierzytelniania firmy Microsoft. Na **kont** ekranu wybierz **+**, a następnie określ, czy chcesz dodać konta służbowego.

4. Skaner, wybierz **ręcznie wprowadzić kod**.

    ![Ekran skanowania kod QR](./media/microsoft-authenticator-app-how-to/scan2.png)
5. Wprowadź kod i adres URL w odpowiednich polach w aplikacji, a następnie wybierz **Zakończ**.

    ![Ekran wprowadzić kod i adres URL](./media/microsoft-authenticator-app-how-to/manual.png)

6. Jeśli aplikacja zawiera nazwę konta z sześciocyfrowy kod podrzędne, wszystko będzie gotowe.

    ![Ekran kont](./media/microsoft-authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>Dodaj konto do aplikacji przy użyciu możliwości rozpoznawanie twarzy lub linii papilarnych urządzenia
Twoja organizacja może wymagać numeru PIN w celu ukończenia żądania weryfikacji. Aplikacja Microsoft Authenticator może używać urządzenia możliwości rozpoznawanie twarzy lub linii papilarnych zamiast numeru PIN. Aby to skonfigurować na pierwszym weryfikacji w aplikacji, zobaczysz opcję, aby używać funkcji Touch ID (dla systemu iOS) lub linii papilarnych identyfikacji zamiast tego. 

Aby skonfigurować funkcję Touch ID do Authenticator firmy Microsoft, należy wykonać żądanie normalne weryfikacji przy użyciu numeru PIN. Microsoft Authenticator zostanie automatycznie ustawiony dla urządzeń, które obsługują użycia funkcji Touch ID. 

![Weryfikacja instalacji funkcji Touch ID](./media/microsoft-authenticator-app-how-to/touchid1.png)

Od tego punktu do przodu, gdy jest wymagane, aby sprawdzić, logowanie, wybierz powiadomień wypychanych odebrane i skanowania odcisku palca zamiast wprowadzania numeru PIN.

![Powiadomienie push](./media/microsoft-authenticator-app-how-to/touchid2.png)

## <a name="use-the-app-when-you-sign-in"></a>Po zalogowaniu się przy użyciu tej aplikacji

Po dodaniu konta w aplikacji może zostać wyświetlony o przeprowadzenie testów weryfikacyjnych, aby upewnić się, czy wszystko zostało poprawnie skonfigurowane. Po wykonaniu tej gotowe! Nie trzeba wykonywanie wszelkich innych dopiero po następnym zalogowaniu.

Jeśli wybrano opcję Użyj kodów weryfikacji w aplikacji, możesz uruchomić je wyświetlić na stronie głównej. Ich zmiana co 30 sekund, aby zawsze mieć nowy kod gdy będziesz potrzebować. Ale nie trzeba wykonywać żadnych czynności, które z nich, dopóki Zaloguj się i wyświetlony monit o podanie kodu weryfikacyjnego.  
