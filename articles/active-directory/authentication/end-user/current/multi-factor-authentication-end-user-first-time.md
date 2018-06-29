---
title: Skonfigurowaną weryfikację dwuetapową — usługi Azure AD | Dokumentacja firmy Microsoft
description: Gdy firma skonfiguruje Azure Multi-Factor Authentication, pojawi się monit zalogowania się do weryfikacji dwuetapowej. Dowiedz się, jak je skonfigurować.
services: multi-factor-authentication
keywords: jak używać usługi azure directory, usługi active directory w chmurze, samouczek usługi active directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.reviewer: richagi
ms.assetid: 46f83a6a-dbdd-4375-8dc4-e7ea77c16357
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: lizross
ms.custom: end-user
ms.openlocfilehash: 85227a243ba5293c425ddd54d0be3e47a7406382
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102505"
---
# <a name="set-up-my-account-for-two-step-verification"></a>Skonfiguruj moje konto na potrzeby weryfikacji dwuetapowej
Weryfikacja dwuetapowa to dodatkowe zabezpieczenia krok, który pomaga chronić Twoje konto dokonując przeszkodę dla innych osób włamanie się. Jeśli czytasz ten artykuł, prawdopodobnie masz wiadomość e-mail od firmy lub szkoły administratorem temat uwierzytelniania wieloskładnikowego. Lub może być próbował zarejestrować i otrzymano komunikat z pytaniem do skonfigurowania dodatkowej weryfikacji zabezpieczeń. Jeśli tak, jest **nie można zalogować przed ukończeniem procesu automatycznej rejestracji**.

Ten artykuł pomoże Ci skonfigurować Twoje **konto służbowe**. Jeśli chcesz włączyć weryfikację dwuetapową dla własnego, osobistego konta Microsoft, zobacz [o weryfikacji dwuetapowej](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="set-up-your-account"></a>Skonfiguruj konto

Gdy działem pomocy technicznej firmy, należy uruchomić przy użyciu weryfikacji dwuetapowej, pojawi się ekran z informacją, **administrator wymaga skonfigurowania tego konta na dodatkowej weryfikacji zabezpieczeń**:

![Konfigurowanie](./media/multi-factor-authentication-end-user-first-time/first.png)

Aby rozpocząć, wybierz **teraz skonfigurować.**

Jeśli nie pojawia się ekran podobny do tego po zalogowaniu, postępuj zgodnie z instrukcjami [zarządzać ustawieniami na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page) można znaleźć na stronie ustawień umożliwiającej zarządzanie opcji weryfikacji.

## <a name="decide-how-you-want-to-verify-your-sign-ins"></a>Zdecyduj, jak chcesz zweryfikować sesje logowania

Pierwsze pytanie w procesie rejestracji jest jak mamy się z Tobą skontaktować. Spójrz na Opcje tabeli i przejdź do procedury konfiguracji dla każdej metody za pomocą łączy.

| Metoda kontaktu | Opis |
| --- | --- |
| [Aplikacji mobilnej](#use-a-mobile-app-as-the-contact-method) |- **Odbieranie powiadomień o weryfikacji.** Ta opcja wypycha powiadomienie do aplikacji uwierzytelniania na smartfonie lub tablecie. Wyświetlić powiadomienie i, jeśli jest to uzasadnione, wybierz **Uwierzytelnij** w aplikacji. Konto służbowe może wymagać wprowadzenia kodu PIN przed uwierzytelniania.<br>- **Użyj kodu weryfikacyjnego.** W tym trybie aplikacja uwierzytelniania generuje kod weryfikacyjny, który aktualizuje co 30 sekund. Wprowadź kod weryfikacyjny najbardziej aktualne w interfejsie logowania.<br>Jest dostępna dla aplikacji Microsoft Authenticator [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594), i [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071). |
| [Telefonu komórkowego połączenia lub wiadomości SMS](#use-your-mobile-phone-as-the-contact-method) |- **Połączenie telefoniczne** umieszcza wykonywane automatyczne połączenie głosowe do należy podać numer telefonu. Odebranie połączenia i naciśnięcie przycisku # na klawiaturze telefonu w celu uwierzytelnienia.<br>- **Wiadomość SMS** kończy się wiadomość tekstową zawierającą kod weryfikacyjny. Następujący wiersz w tekście Odpowiedz na wiadomość SMS lub wprowadź kod weryfikacyjny Podany w interfejsie logowania. |
| [Z telefonem biurowym](#use-your-office-phone-as-the-contact-method) |Umieszcza wykonywane automatyczne połączenie głosowe pod numer telefonu, podane przez użytkownika. Odebrać połączenie i naciska klawisz # na klawiaturze telefonu w celu uwierzytelnienia. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Użyj aplikacji mobilnej jako metody kontaktu
Za pomocą tej metody wymaga zainstalowania aplikacji uwierzytelniania na swój telefon lub tablet. Kroki opisane w tym artykule opierają się na aplikacji Authenticator firmy Microsoft, który jest dostępny dla [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), i [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

1. Wybierz **aplikacji mobilnej** z listy rozwijanej.
2. Wybierz opcję **otrzymywać powiadomienia na potrzeby weryfikacji** lub **Użyj kodu weryfikacyjnego**, a następnie wybierz pozycję **Konfigurowanie**.

   ![Dodatkowe zabezpieczenia weryfikacji ekranu](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)

3. Na swój telefon lub tablet, Otwórz aplikację i wybierz **+** w celu dodania konta. (Na urządzeniach z systemem Android, wybierz wielokropek, a następnie **Dodaj konto**.)
4. Określ, czy chcesz dodać konta służbowego. Otwiera skaner kodów QR w telefonie. Jeśli aparatu nie działa prawidłowo, można wybrać ręcznie wprowadzić informacje o Twojej firmie. Aby uzyskać więcej informacji, zobacz [ręcznie dodać konto](#add-an-account-manually).  
5. Skanuj obraz kodu QR, który pojawił się z ekranem do konfigurowania aplikacji mobilnej.  Wybierz **gotowe** aby zamknąć ekran kod QR.  

   ![Ekran kod QR](./media/multi-factor-authentication-end-user-first-time/scan2.png)

6. Po zakończeniu aktywacji przez telefon, wybierz **kontaktu mnie**.  Ten krok wysyła powiadomienie lub kod weryfikacyjny na Twój telefon. Wybierz **Sprawdź**.  
7. Jeśli zatwierdzanie weryfikacji logowania firmy wymaga numeru PIN, wprowadź go.

   ![Pole wprowadzania numeru PIN](./media/multi-factor-authentication-end-user-first-time/scan3.png)

8. Po zakończeniu wprowadzania numeru PIN, wybierz **Zamknij**. W tym momencie weryfikacja zostanie pomyślnie.
9. Firma Microsoft zaleca, możesz wprowadzić numer telefonu komórkowego na wypadek utraty dostępu do aplikacji mobilnej. Określ w Twoim kraju z listy rozwijanej, a następnie wprowadź numer telefonu komórkowego w polu obok nazwy kraju. Wybierz opcję **Dalej**.
10. W tym momencie monit o ustawienie hasła aplikacji dla aplikacji korzystających z przeglądarki, takich jak Outlook 2010 lub starszy lub natywna aplikacja poczty e-mail na urządzeniach Apple. Jest to spowodowane niektóre aplikacje nie obsługują weryfikacji dwuetapowej. Nie należy używać tych aplikacji, kliknij przycisk **gotowe** i Pomiń pozostałe czynności.
11. Jeśli używasz tych aplikacji Kopiuj hasło aplikacji podane i wklej go do aplikacji zamiast prawidłowe hasło. Dla wielu aplikacji, można użyć tego samego hasła aplikacji. Aby uzyskać więcej informacji [pomoc przy użyciu haseł aplikacji].
12. Kliknij przycisk **Gotowe**.

### <a name="add-an-account-manually"></a>Ręcznie dodaj konto
Jeśli chcesz dodać konto do aplikacji mobilnej ręcznie, zamiast używać czytnika QR, wykonaj następujące kroki.

1. Wybierz **ręcznie wprowadź konto** przycisku.  
2. Wprowadź kod i adres URL, które znajdują się na tej samej stronie, pokazujący kod kreskowy. Te informacje o przechodzi **kod** i **adres URL** pola w aplikacji mobilnej.

    ![Konfigurowanie](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. Po zakończeniu aktywacji wybierz **kontaktu mnie**. Ten krok wysyła powiadomienie lub kod weryfikacyjny na Twój telefon. Wybierz **Sprawdź**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Użyć telefonu komórkowego jako metody kontaktu
1. Wybierz **numer telefonu uwierzytelniania** z listy rozwijanej.  

    ![Konfigurowanie](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. Wybierz swój kraj z listy rozwijanej, a następnie wprowadź numer telefonu komórkowego.
3. Wybierz metodę, której chcesz użyć do użycia z telefonem komórkowym - SMS lub połączenie.
4. Wybierz **kontaktu mnie** Aby zweryfikować swój numer telefonu. W zależności od wybrany tryb możemy wysłać tekstu lub zadzwonić do Ciebie. Postępuj zgodnie z instrukcjami na ekranie, a następnie wybierz **Sprawdź**.
5. W tym momencie monit o ustawienie hasła aplikacji dla aplikacji korzystających z przeglądarki, takich jak Outlook 2010 lub starszy lub natywna aplikacja poczty e-mail na urządzeniach Apple. Jest to spowodowane niektóre aplikacje nie obsługują weryfikacji dwuetapowej. Nie należy używać tych aplikacji, kliknij przycisk **gotowe** i Pomiń pozostałe czynności.
6. Jeśli używasz tych aplikacji Kopiuj hasło aplikacji podane i wklej go do aplikacji zamiast prawidłowe hasło. Dla wielu aplikacji, można użyć tego samego hasła aplikacji. Aby uzyskać więcej informacji [pomoc przy użyciu haseł aplikacji].
7. Kliknij przycisk **Gotowe**.

## <a name="use-your-office-phone-as-the-contact-method"></a>Użyj jako metody kontaktu Telefon biurowy
1. Wybierz **telefon biurowy** z listy rozwijanej  

    ![Konfigurowanie](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. Pole numeru telefonu jest automatycznie wypełniany swoje informacje kontaktowe firmy. Jeśli liczba jest nieprawidłowa lub nieistniejąca, skontaktuj się z administratorem, aby wprowadzić zmiany.
3. Wybierz **kontaktu mnie** można zweryfikować telefonu numer i firma Microsoft będzie wywoływać numer. Postępuj zgodnie z instrukcjami na ekranie, a następnie wybierz **Sprawdź**.
4. W tym momencie monit o ustawienie hasła aplikacji dla aplikacji korzystających z przeglądarki, takich jak Outlook 2010 lub starszy lub natywna aplikacja poczty e-mail na urządzeniach Apple. Jest to spowodowane niektóre aplikacje nie obsługują weryfikacji dwuetapowej. Nie należy używać tych aplikacji, kliknij przycisk **gotowe** i Pomiń pozostałe czynności.
5. Jeśli używasz tych aplikacji Kopiuj hasło aplikacji podane i wklej go do aplikacji zamiast prawidłowe hasło. Dla wielu aplikacji, można użyć tego samego hasła aplikacji. Aby uzyskać więcej informacji, zobacz [co to są hasła aplikacji](../../../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-app-passwords.md).
6. Kliknij przycisk **Gotowe**.

## <a name="next-steps"></a>Kolejne kroki
* Zmienianie opcji i [zarządzać ustawieniami na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-manage-settings.md)
* Konfigurowanie [hasła aplikacji](../../../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-app-passwords.md) dla aplikacji natywnych urządzenia, które nie obsługują weryfikacji dwuetapowej.
* Zapoznaj się z [aplikacji Microsoft Authenticator](../../../../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) Aby szybko i bezpiecznie uwierzytelniania nawet wtedy, gdy nie ma usługi komórki.
