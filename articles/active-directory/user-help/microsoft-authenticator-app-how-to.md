---
title: Aplikacja Microsoft Authenticator dla telefonów komórkowych — usługa Azure AD | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uaktualnić do najnowszej wersji aplikacji Azure Authenticator.
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
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059823"
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Rozpoczynanie pracy z aplikacją Microsoft Authenticator
Aplikacja Microsoft Authenticator zawiera dodatkowy poziom zabezpieczeń w ramach konta służbowego (na przykład bsimon@contoso.com) lub konta Microsoft (na przykład bsimon@outlook.com).

Aplikacja działa w jednym z dwóch sposobów:

* **Powiadomienie**. Aplikacja może pomóc zapobiec nieautoryzowanemu dostępowi do konta i Zatrzymaj fałszywe transakcje, wypychanie powiadomień na smartfonie lub tablecie. Wystarczy wyświetlić powiadomienie i jeśli jest to uzasadnione, wybierz **Sprawdź**. W przeciwnym razie można wybrać **Odmów**.
* **Kod weryfikacyjny**. Aplikacja może służyć jako token oprogramowania do wygenerowania kodu weryfikacyjnego OAuth. Po wprowadzeniu nazwy użytkownika i hasła, możesz wprowadzić kod zapewnianych przez aplikację na ekranie logowania. Kod weryfikacyjny zawiera drugiej formy uwierzytelniania.

Aplikacja Microsoft Authenticator zastępuje aplikację Azure Authenticator. Aplikacja Microsoft Authenticator nadal działa, ale jeśli zdecydujesz się przejść do nowej aplikacji Microsoft Authenticator, w tym artykule mogą pomóc.  

## <a name="opt-in-for-two-step-verification"></a>Zgoda na weryfikację dwuetapową

Aplikacja Microsoft Authenticator nie działa przez siebie. Skonfiguruj każdy z konta, aby monitować o drugiej metody weryfikacji, po zalogowaniu się przy użyciu nazwy użytkownika i hasła.

Konto służbowe lub szkolne zwykle nie uzyskasz wybierz tę funkcję samodzielnie. Zamiast tego administrator zabezpieczeń powoduje w Twoim imieniu, a następnie powiadamia użytkownika zarejestrowania metod weryfikacji konta. Jeśli ten scenariusz ma zastosowanie do użytkownika, zapoznaj się z [co usługi Azure Multi-Factor Authentication oznacza dla mnie](multi-factor-authentication-end-user.md).

To konto osobiste musisz skonfigurować weryfikację dwuetapową dla siebie. Jeśli masz konto Microsoft, te kroki są dostępne w [o weryfikacji dwuetapowej](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

Umożliwia także Microsoft Authenticator z kontami firm innych niż Microsoft. One może wywołać funkcję coś innego niż weryfikacji dwuetapowej, ale można go znaleźć w obszarze zabezpieczeń i ustawień logowania.

## <a name="install-the-app"></a>Zainstaluj aplikację
Aplikacja Microsoft Authenticator jest dostępna dla systemów [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) i [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

## <a name="add-accounts-to-the-app"></a>Dodaj konta do aplikacji
Dla każdego konta, które chcesz dodać do aplikacji Microsoft Authenticator użyj jednej z następujących procedur:

### <a name="add-a-personal-microsoft-account-to-the-app"></a>Dodaj osobiste konto Microsoft do aplikacji

W przypadku osobistego konta Microsoft (jedno, którego używasz do logowania do usługi Outlook.com, Xbox, Skype, itp.) jest wszystko, co należy zrobić, zaloguj się do swojego konta w aplikacji Microsoft Authenticator.

### <a name="add-a-work-or-school-account-to-the-app-using-the-qr-code-scanner"></a>Dodaj konto służbowe lub szkolne do aplikacji przy użyciu skanera kodów QR
1. Przejdź do ekranu ustawienia weryfikacji zabezpieczeń.  Aby uzyskać informacje, jak uzyskać dostęp do tego ekranu, zobacz [zmiana ustawień zabezpieczeń](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).
2. Zaznacz pole wyboru obok pozycji **aplikacji Authenticator** polecenie **Konfiguruj**.

    ![Przycisk Konfiguruj, na ekranie ustawień weryfikacji zabezpieczeń](./media/microsoft-authenticator-app-how-to/azureauthe.png)

    Wyświetlenie ekranu przy użyciu kodu QR na nim.

    ![Ekran, który zawiera kod QR](./media/microsoft-authenticator-app-how-to/barcode2.png)
3. Otwórz aplikację Microsoft Authenticator. Na **kont** ekranu, wybierz opcję **+**, a następnie określ, czy chcesz dodać konto służbowe.
4. Używanie aparatu, aby zeskanować kod QR, a następnie wybierz **gotowe** aby zamknąć ekran kodu QR.

    Jeśli aparat fotograficzny nie działa prawidłowo, możesz to zrobić [ręcznie wprowadzić kod QR i adres URL](#add-an-account-to-the-app-manually).

5. Gdy aplikacja będzie wyświetlana nazwa konta z 6 cyfrowym kodem podrzędne, wszystko będzie gotowe.

    ![ekran kont](./media/microsoft-authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Ręcznie dodaj konto do aplikacji
1. Przejdź do ekranu ustawienia weryfikacji zabezpieczeń.  Aby uzyskać informacje, jak uzyskać dostęp do tego ekranu, zobacz [zmiana ustawień zabezpieczeń](multi-factor-authentication-end-user-manage-settings.md).
2. Wybierz **skonfigurować**.

    ![Przycisk Konfiguruj, na ekranie ustawień weryfikacji zabezpieczeń](./media/microsoft-authenticator-app-how-to/azureauthe.png)

    Wyświetlenie ekranu przy użyciu kodu QR na nim.  Należy pamiętać, kod i adres URL.

    ![Ekran, który zawiera kod QR i adres URL](./media/microsoft-authenticator-app-how-to/barcode2.png)
3. Otwórz aplikację Microsoft Authenticator. Na **kont** ekranu, wybierz opcję **+**, a następnie określ, czy chcesz dodać konto służbowe.

4. Skaner, wybierz **wprowadź kod ręcznie**.

    ![Ekran skanowanie kodu QR](./media/microsoft-authenticator-app-how-to/scan2.png)
5. Wprowadź kod i adres URL w odpowiednich polach w aplikacji, a następnie wybierz **Zakończ**.

    ![Ekran do wprowadzania kod i adres URL](./media/microsoft-authenticator-app-how-to/manual.png)

6. Gdy aplikacja będzie wyświetlana nazwa konta z 6 cyfrowym kodem podrzędne, wszystko będzie gotowe.

    ![ekran kont](./media/microsoft-authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>Dodaj konto z aplikacją przy użyciu odcisku palca lub rozpoznawanie twarzy możliwości urządzenia
Twoja organizacja może wymagać numeru PIN w celu ukończenia żądania weryfikacji. Aplikację Microsoft Authenticator, można użyć urządzenia możliwości rozpoznawanie twarzy lub odcisku palca zamiast numeru PIN. Aby skonfigurować tę funkcjonalność na swojej pierwszej weryfikacji w aplikacji, zobaczysz opcję użycia funkcji Touch ID (dla systemu iOS) lub odcisku palca identyfikacji zamiast tego. 

Aby skonfigurować funkcję Touch ID dla Microsoft Authenticator, należy wykonać żądanie normalne weryfikacji za pomocą numeru PIN. Microsoft Authenticator zostanie automatycznie ustawiony dla urządzeń, które obsługują użycia funkcji Touch ID. 

![Weryfikacja instalacji funkcji Touch ID.](./media/microsoft-authenticator-app-how-to/touchid1.png)

Od tego punktu do przodu, po, musisz zweryfikować logowania, wybierz powiadomienie wypychane odebrane, a skanowanie jego linii papilarnych, zamiast wprowadzać numer PIN.

![Powiadomienie push](./media/microsoft-authenticator-app-how-to/touchid2.png)

## <a name="use-the-app-when-you-sign-in"></a>Po zalogowaniu przy użyciu aplikacji

Po dodaniu konta do aplikacji monit może wykonać testów weryfikacyjnych, aby upewnić się, że wszystko zostało skonfigurowane prawidłowo. Po tym gotowe! Nie będzie konieczne wykonywanie dodatkowych czynności, aż do następnego logowania.

Jeśli została wybrana opcja używania kodów weryfikacyjnych w aplikacji, możesz uruchomić je wyświetlić na stronie głównej. Tak, aby zawsze mieć nowy kod gdy będą potrzebne zmiany co 30 sekund. Ale nie trzeba podejmować żadnych działań, które z nich, dopóki Zaloguj się i monit o wprowadzenie kodu weryfikacyjnego.  
