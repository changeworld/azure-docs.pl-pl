---
title: Konfiguruj weryfikację dwuetapową — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Gdy firma umożliwia skonfigurowanie usługi Azure Multi-Factor Authentication, pojawi się zarejestrować na potrzeby weryfikacji dwuetapowej. Dowiedz się, jak je skonfigurować.
services: active-directory
keywords: jak używać platformy azure, active directory w chmurze, samouczek dotyczący usługi active directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: 46f83a6a-dbdd-4375-8dc4-e7ea77c16357
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2df72d03bae8987de4998276a0be0f3ce1ec0333
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65230039"
---
# <a name="set-up-my-account-for-two-step-verification"></a>Skonfiguruj moje konto na potrzeby weryfikacji dwuetapowej
Weryfikacja dwuetapowa była kroku dodatkowe zabezpieczenia, która pomaga chronić Twoje konto, co utrudnia innym osobom włamanie się. Jeśli czytasz ten artykuł, prawdopodobnie masz wiadomość e-mail od firmowego lub szkolnego administratora o usłudze uwierzytelnianie wieloskładnikowe. Lub może być zalogowania się i stało się komunikat z prośbą do skonfigurowania dodatkowej weryfikacji zabezpieczeń. Jeśli tak jest rzeczywiście, **nie możesz zalogować przed ukończeniem procesu automatycznej rejestracji**.

Ten artykuł pomoże Ci skonfigurować Twoje **konto służbowe**. Jeśli chcesz włączyć weryfikację dwuetapową własne, osobiste konto Microsoft, zobacz [o weryfikacji dwuetapowej](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="set-up-your-account"></a>Konfigurowanie konta

Przy pomocy technicznej Twojej firmy wymaga rozpocząć korzystanie z weryfikacji dwuetapowej, zobaczysz ekran informujący **administrator wymaga skonfigurowania tego konta w dodatkowej weryfikacji zabezpieczeń**:

![Konfiguracja](./media/multi-factor-authentication-end-user-first-time/first.png)

Aby rozpocząć, wybierz **teraz skonfigurować.**

Jeśli nie widzisz taki ekran po zalogowaniu, postępuj zgodnie z instrukcjami w [Zarządzanie ustawieniami weryfikacji dwuetapowej](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page) można znaleźć na stronie ustawień, na której możesz zarządzać opcje weryfikacji.

## <a name="decide-how-you-want-to-verify-your-sign-ins"></a>Zdecyduj, jak chcesz zweryfikować logowań

Pierwsze pytanie w procesie rejestracji jest o tym, jak chcesz się z Tobą skontaktować. Przyjrzyj się opcje w tabeli, a następnie użyj linków, aby przejść do kroków instalacji dla poszczególnych metod.

| Metoda kontaktu | Opis |
| --- | --- |
| [Aplikacja mobilna](#use-a-mobile-app-as-the-contact-method) |- **Odbieraj powiadomienia dotyczące weryfikacji.** Ta opcja wypycha powiadomienie do aplikacji authenticator na smartfonie lub tablecie. Wyświetlić powiadomienie i, jeśli jest to uzasadnione, wybierz **Uwierzytelnij** w aplikacji. Pracy lub nauki może wymagać wprowadzania numeru PIN przed uwierzytelniania.<br>- **Użyj kodu weryfikacyjnego.** W tym trybie aplikacja uwierzytelniania generuje kod weryfikacyjny, który jest aktualizowany co 30 sekund. Wprowadź kod weryfikacyjny najbardziej aktualne w interfejsie logowania.<br>Aplikacja Microsoft Authenticator jest dostępna dla systemów [Android](https://go.microsoft.com/fwlink/?linkid=866594) i [iOS](https://go.microsoft.com/fwlink/?linkid=866594).|
| [Połączenie z telefonem komórkowym lub tekst](#use-your-mobile-phone-as-the-contact-method) |- **Połączenie telefoniczne** powoduje automatyczne połączenie głosowe na numer telefonu, należy podać. Odebranie połączenia i naciśnięcie przycisku # na klawiaturze telefonu w celu uwierzytelnienia.<br>- **Wiadomość SMS** kończy wiadomość SMS z kodem weryfikacyjnym. Następujący wiersz w tekście Odpowiedz na wiadomość SMS lub wprowadź kod weryfikacyjny udostępniane w interfejsie logowania. |
| [Z telefonem biurowym](#use-your-office-phone-as-the-contact-method) |Przełącza automatyczne połączenie głosowe na numer telefonu, których udzielasz. Odebrać połączenie i naciska klawisz # na klawiaturze telefonu w celu uwierzytelnienia. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Korzystanie z aplikacji mobilnej jako metody kontaktu
Za pomocą tej metody wymaga, zainstaluj aplikację authenticator na telefonie lub tablecie. Kroki opisane w tym artykule opierają się na aplikację Microsoft Authenticator, która jest dostępna dla [Android](https://go.microsoft.com/fwlink/?Linkid=825072) i [iOS](https://go.microsoft.com/fwlink/?Linkid=825073).

>[!NOTE]
>Nie trzeba korzystać z aplikacji Microsoft Authenticator. Jeśli już używasz innej aplikacji wystawcy uwierzytelnienia, można nadal z niego korzystać.

1. Wybierz **aplikacji mobilnej** z listy rozwijanej.
2. Wybierz opcję **Odbieraj powiadomienia dotyczące weryfikacji** lub **Użyj kodu weryfikacyjnego**, a następnie wybierz **Konfigurowanie**.

   ![Ekran weryfikacji zabezpieczeń](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)

3. Na telefonie lub tablecie, Otwórz aplikację, a następnie wybierz pozycję **+** w celu dodania konta. (Na urządzeniach z systemem Android, wybierz trzy kropki, a następnie **Dodaj konto**.)
4. Określ, czy chcesz dodać konto służbowe. Zostanie otwarty ze skanera kodów QR na telefonie. Jeśli aparat fotograficzny nie działa prawidłowo, możesz wybrać, aby ręcznie wprowadzić informacje o firmie. Aby uzyskać więcej informacji, zobacz [ręcznie dodać konto](#add-an-account-manually).  
5. Zeskanuj obraz kodu QR, który pojawił się ekran konfigurowania aplikacji mobilnej.  Wybierz **gotowe** aby zamknąć ekran kodu QR.  

   ![Ekran kod QR](./media/multi-factor-authentication-end-user-first-time/scan2.png)

6. Po zakończeniu aktywacji przez telefon, wybierz **skontaktuj się ze mną**.  W tym kroku wysyła powiadomienie lub kod weryfikacyjny na Twój telefon. Wybierz **Sprawdź**.  
7. Jeśli Twoja firma wymaga zastosowania numeru PIN do zatwierdzania weryfikacji logowania, wprowadź go.

   ![Pole wprowadzania numeru PIN](./media/multi-factor-authentication-end-user-first-time/scan3.png)

8. Po zakończeniu wprowadzania numeru PIN, wybierz **Zamknij**. W tym momencie weryfikacja zostanie pomyślnie.
9. Zaleca się, że wprowadzić numer telefonu komórkowego na wypadek utraty dostępu do aplikacji mobilnej. Określ swój kraj/region z listy rozwijanej, a następnie wprowadź swój numer telefonu komórkowego w polu obok nazwy kraju/regionu. Wybierz opcję **Dalej**.
10. W tym momencie monit o ustawienie hasła aplikacji dla aplikacji niekorzystających z przeglądarki, takich jak Outlook 2010 lub starszej lub natywna aplikacja poczty e-mail na urządzeniach firmy Apple. Jest to spowodowane niektóre aplikacje nie obsługują weryfikacji dwuetapowej. Jeśli nie używasz tych aplikacji, kliknij przycisk **gotowe** i Pomiń pozostałe czynności.
11. Jeśli używasz tych aplikacji, Kopiuj hasło aplikacji podany i wkleić go do aplikacji, zamiast regularnego hasła. Można użyć tego samego hasła aplikacji dla wielu aplikacji. Aby uzyskać więcej informacji [Pomoc dotycząca haseł aplikacji].
12. Kliknij przycisk **Gotowe**.

### <a name="add-an-account-manually"></a>Ręcznie dodaj konto
Jeśli chcesz dodać konto do aplikacji mobilnej ręcznie, zamiast korzystać z czytnika QR, wykonaj następujące kroki.

1. Wybierz **ręcznie wprowadź konto** przycisku.  
2. Wprowadź kod i adres URL, które zostały podane na tej samej stronie, która pokazuje kod kreskowy. Tych informacji znajduje się w **kodu** i **adresu URL** pól w aplikacji mobilnej.

    ![Konfiguracja](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. Po zakończeniu aktywacji wybierz **skontaktuj się ze mną**. W tym kroku wysyła powiadomienie lub kod weryfikacyjny na Twój telefon. Wybierz **Sprawdź**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Użyć telefonu komórkowego jako metody kontaktu
1. Wybierz **numer telefonu uwierzytelniania** z listy rozwijanej.  

    ![Konfiguracja](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. Wybierz swój kraj/region z listy rozwijanej, a następnie wprowadź swój numer telefonu komórkowego.
3. Wybierz metodę, której chcesz użyć telefonu komórkowego — wiadomość SMS lub połączenie za pomocą.
4. Wybierz **skontaktuj się ze mną** Aby zweryfikować swój numer telefonu. W zależności od wybranego trybu możemy wysłać wiadomość SMS lub Zadzwonimy do Ciebie. Postępuj zgodnie z instrukcjami na ekranie, a następnie wybierz **Sprawdź**.
5. W tym momencie monit o ustawienie hasła aplikacji dla aplikacji niekorzystających z przeglądarki, takich jak Outlook 2010 lub starszej lub natywna aplikacja poczty e-mail na urządzeniach firmy Apple. Jest to spowodowane niektóre aplikacje nie obsługują weryfikacji dwuetapowej. Jeśli nie używasz tych aplikacji, kliknij przycisk **gotowe** i Pomiń pozostałe czynności.
6. Jeśli używasz tych aplikacji, Kopiuj hasło aplikacji podany i wkleić go do aplikacji, zamiast regularnego hasła. Można użyć tego samego hasła aplikacji dla wielu aplikacji. Aby uzyskać więcej informacji [Pomoc dotycząca haseł aplikacji].
7. Kliknij przycisk **Gotowe**.

## <a name="use-your-office-phone-as-the-contact-method"></a>Stosowania telefonu biurowego jako metody kontaktu
1. Wybierz **telefon biurowy** z listy rozwijanej  

    ![Konfiguracja](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. Pola numeru telefonu jest automatycznie wypełniane przy użyciu Twoich informacji kontaktowych firmy. Jeśli liczba jest nieprawidłowa lub nieistniejąca, skontaktuj się z administratorem, aby wprowadzić zmiany.
3. Wybierz **skontaktuj się ze mną** można zweryfikować Twojego numeru telefonu numer, a firma Microsoft będzie wywoływać numer. Postępuj zgodnie z instrukcjami na ekranie, a następnie wybierz **Sprawdź**.
4. W tym momencie monit o ustawienie hasła aplikacji dla aplikacji niekorzystających z przeglądarki, takich jak Outlook 2010 lub starszej lub natywna aplikacja poczty e-mail na urządzeniach firmy Apple. Jest to spowodowane niektóre aplikacje nie obsługują weryfikacji dwuetapowej. Jeśli nie używasz tych aplikacji, kliknij przycisk **gotowe** i Pomiń pozostałe czynności.
5. Jeśli używasz tych aplikacji, Kopiuj hasło aplikacji podany i wkleić go do aplikacji, zamiast regularnego hasła. Można użyć tego samego hasła aplikacji dla wielu aplikacji. Aby uzyskać więcej informacji, zobacz [co to są hasła aplikacji](multi-factor-authentication-end-user-app-passwords.md).
6. Kliknij przycisk **Gotowe**.

## <a name="next-steps"></a>Kolejne kroki
* Zmień opcje preferowanych i [Zarządzanie ustawieniami weryfikacji dwuetapowej](multi-factor-authentication-end-user-manage-settings.md)
* Konfigurowanie [haseł aplikacji](multi-factor-authentication-end-user-app-passwords.md) macierzysty dla aplikacji dla urządzeń, które nie obsługują weryfikacji dwuetapowej.
* Zapoznaj się z [aplikacji Microsoft Authenticator](user-help-auth-app-download-install.md) szybkie, bezpieczne uwierzytelnianie, nawet wtedy, gdy nie masz usługi komórki.
