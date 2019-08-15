---
title: Konfigurowanie weryfikacji dwuetapowej — Azure Active Directory | Microsoft Docs
description: Gdy firma skonfiguruje usługę Azure MFA Authentication, zostanie wyświetlony monit o zarejestrowanie się w celu weryfikacji dwuetapowej. Dowiedz się, jak ją skonfigurować.
services: active-directory
keywords: Jak korzystać z usługi Azure Directory, usługi Active Directory w chmurze, samouczka usługi Active Directory
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
ms.openlocfilehash: 75bc067bfe8a98ef2337f368243b3221be1677d6
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949905"
---
# <a name="set-up-my-account-for-two-step-verification"></a>Konfigurowanie mojego konta na potrzeby weryfikacji dwuetapowej
Weryfikacja dwuetapowa to dodatkowy krok zabezpieczeń, który pomaga chronić Twoje konto przez utrudnienie innym osobom. Jeśli czytasz ten artykuł, prawdopodobnie otrzymasz wiadomość e-mail od administratora służbowego dotyczącego uwierzytelniania wieloskładnikowego. Lub być może podjęto próbę zalogowania się i otrzymasz komunikat z prośbą o skonfigurowanie dodatkowej weryfikacji zabezpieczeń. W takim przypadku **nie można zalogować się do momentu zakończenia procesu automatycznej rejestracji**.

Ten artykuł ułatwia skonfigurowanie **konta służbowego**. Jeśli chcesz włączyć weryfikację dwuetapową dla własnego, osobistego konto Microsoft, zobacz [Informacje o weryfikacji](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)dwuetapowej.

## <a name="set-up-your-account"></a>Skonfiguruj konto

Gdy dział pomocy technicznej Twojej firmy wymaga rozpoczęcia korzystania z weryfikacji dwuetapowej, zobaczysz ekran z informacją, że **administrator wymaga skonfigurowania tego konta w celu przeprowadzenia dodatkowej weryfikacji zabezpieczeń**:

![Konfiguracja](./media/multi-factor-authentication-end-user-first-time/first.png)

Aby rozpocząć, wybierz pozycję **Skonfiguruj teraz.**

Jeśli nie widzisz ekranu takiego jak podczas logowania, postępuj zgodnie z instrukcjami w sekcji [Zarządzanie ustawieniami weryfikacji](multi-factor-authentication-end-user-manage-settings.md#using-the-additional-security-verification-page) dwuetapowej, aby znaleźć stronę Ustawienia, na której można zarządzać opcjami weryfikacji.

## <a name="decide-how-you-want-to-verify-your-sign-ins"></a>Zdecyduj, jak chcesz zweryfikować logowania

Na pierwszym pytaniu w procesie rejestracji można skontaktować się z Tobą. Zapoznaj się z opcjami w tabeli i użyj linków, aby przejść do kroków instalacji dla każdej z tych metod.

| Metoda kontaktu | Opis |
| --- | --- |
| [Aplikacja mobilna](#use-a-mobile-app-as-the-contact-method) |- **Otrzymuj powiadomienia o weryfikacji.** Ta opcja powoduje wypchnięcie powiadomienia do aplikacji Authenticator na telefonie Smartphone lub tablecie. Wyświetl powiadomienie i, jeśli jest ono wiarygodne, wybierz pozycję **Uwierzytelnij** w aplikacji. Przed uwierzytelnieniem może być wymagane wprowadzenie numeru PIN przez użytkownika lub szkołę.<br>- **Użyj kodu weryfikacyjnego.** W tym trybie aplikacja Authenticator generuje kod weryfikacyjny, który aktualizuje co 30 sekund. Wprowadź najbardziej aktualny kod weryfikacyjny w interfejsie logowania.<br>Aplikacja Microsoft Authenticator jest dostępna dla systemów [Android](https://go.microsoft.com/fwlink/?linkid=866594) i [iOS](https://go.microsoft.com/fwlink/?linkid=866594).|
| [Połączenie lub tekst telefonu komórkowego](#use-your-mobile-phone-as-the-contact-method) |- **Połączenie telefoniczne** umieszcza automatyczne połączenie głosowe z podanym numerem telefonu. Odpowiedz na wywołanie i naciśnij klawisz # na klawiaturze telefonu, aby przeprowadzić uwierzytelnianie.<br>- **Wiadomość SMS** zawiera wiadomość tekstową zawierającą kod weryfikacyjny. Po wyświetleniu monitu w tekście należy odpowiedzieć na wiadomość tekstową lub wprowadzić kod weryfikacyjny podany w interfejsie logowania. |
| [Połączenie telefoniczne biura](#use-your-office-phone-as-the-contact-method) |Umieszcza automatyczne połączenie głosowe z podanym numerem telefonu. Odpowiedz na wywołanie i naciśnij klawisz # na klawiaturze telefonu w celu uwierzytelnienia. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Korzystanie z aplikacji mobilnej jako metody kontaktu
Użycie tej metody wymaga zainstalowania aplikacji uwierzytelniania na telefonie lub tablecie. Kroki opisane w tym artykule są oparte na aplikacji Microsoft Authenticator dostępnej dla systemów [Android](https://go.microsoft.com/fwlink/?Linkid=825072) i [iOS](https://go.microsoft.com/fwlink/?Linkid=825073).

>[!NOTE]
>Nie musisz używać aplikacji Microsoft Authenticator. Jeśli używasz już innej aplikacji do uwierzytelniania, możesz nadal z niej korzystać.

1. Z listy rozwijanej wybierz opcję **aplikacja mobilna** .
2. Wybierz opcję **Odbierz powiadomienia dla weryfikacji** lub **Użyj kodu weryfikacyjnego**, a następnie wybierz pozycję **Konfiguruj**.

   ![Ekran dodatkowej weryfikacji zabezpieczeń](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)

3. Na telefonie lub tablecie Otwórz aplikację i wybierz pozycję **+** , aby dodać konto. (Na urządzeniach z systemem Android wybierz trzy kropki, a następnie **Dodaj konto**).
4. Określ, że chcesz dodać konto służbowe. Zostanie otwarty skaner kodu QR na telefonie. Jeśli aparat nie działa prawidłowo, możesz wybrać ręczne wprowadzanie informacji o firmie. Aby uzyskać więcej informacji, zobacz [Ręczne dodawanie konta](#add-an-account-manually).  
5. Zeskanuj obraz kodu QR, który pojawił się na ekranie w celu skonfigurowania aplikacji mobilnej.  Wybierz pozycję **gotowe** , aby zamknąć ekran kod QR.  

   ![Ekran kodu QR](./media/multi-factor-authentication-end-user-first-time/scan2.png)

6. Po zakończeniu aktywacji na telefonie wybierz pozycję **kontakt ze mną**.  Ten krok powoduje wysłanie do telefonu powiadomienia lub kodu weryfikacyjnego. Wybierz pozycję **Weryfikuj**.  
7. Jeśli firma wymaga numeru PIN do zatwierdzania weryfikacji logowania, wprowadź ją.

   ![Pole do wprowadzania numeru PIN](./media/multi-factor-authentication-end-user-first-time/scan3.png)

8. Po zakończeniu wprowadzania numeru PIN wybierz pozycję **Zamknij**. W tym momencie weryfikacja powinna zakończyć się pomyślnie.
9. Zalecamy wprowadzenie numeru telefonu komórkowego na wypadek utraty dostępu do aplikacji mobilnej. Określ swój kraj/region z listy rozwijanej, a następnie wprowadź numer telefonu komórkowego w polu obok nazwy kraju/regionu. Wybierz opcję **Dalej**.
10. W tym momencie zostanie wyświetlony monit o skonfigurowanie haseł aplikacji dla aplikacji niekorzystających z przeglądarki, takich jak Outlook 2010 lub starsza lub Natywna aplikacja poczty e-mail na urządzeniach firmy Apple. Wynika to z faktu, że niektóre aplikacje nie obsługują weryfikacji dwuetapowej. Jeśli nie używasz tych aplikacji, kliknij przycisk **gotowe** i pomiń pozostałe kroki.
11. Jeśli używasz tych aplikacji, skopiuj podane hasło aplikacji i wklej je do aplikacji zamiast zwykłego hasła. Możesz użyć tego samego hasła aplikacji dla wielu aplikacji. Aby uzyskać więcej informacji, [pomoc przy hasłach aplikacji].
12. Kliknij przycisk **Gotowe**.

### <a name="add-an-account-manually"></a>Ręczne dodawanie konta
Jeśli chcesz ręcznie dodać konto do aplikacji mobilnej, zamiast korzystać z czytnika QR, wykonaj następujące czynności.

1. Wybierz przycisk **Wprowadź konto ręcznie** .  
2. Wprowadź kod i adres URL, które są podane na tej samej stronie, na której jest wyświetlany kod kreskowy. Te informacje są umieszczane w polach **kod** i **adres URL** w aplikacji mobilnej.

    ![Konfiguracja](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. Po zakończeniu aktywacji wybierz pozycję **skontaktuj się ze mną**. Ten krok powoduje wysłanie do telefonu powiadomienia lub kodu weryfikacyjnego. Wybierz pozycję **Weryfikuj**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Używanie telefonu komórkowego jako metody kontaktu
1. Z listy rozwijanej wybierz pozycję **telefon uwierzytelniania** .  

    ![Konfiguracja](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. Wybierz swój kraj/region z listy rozwijanej, a następnie wprowadź numer telefonu komórkowego.
3. Wybierz metodę, która ma być używana z telefonem komórkowym — tekst lub połączenie.
4. Wybierz pozycję **kontakt ze mną** , aby zweryfikować swój numer telefonu. W zależności od wybranego trybu wyślemy Ci tekst lub dzwonimy do Ciebie. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, a następnie wybierz pozycję **Weryfikuj**.
5. W tym momencie zostanie wyświetlony monit o skonfigurowanie haseł aplikacji dla aplikacji niekorzystających z przeglądarki, takich jak Outlook 2010 lub starsza lub Natywna aplikacja poczty e-mail na urządzeniach firmy Apple. Wynika to z faktu, że niektóre aplikacje nie obsługują weryfikacji dwuetapowej. Jeśli nie używasz tych aplikacji, kliknij przycisk **gotowe** i pomiń pozostałe kroki.
6. Jeśli używasz tych aplikacji, skopiuj podane hasło aplikacji i wklej je do aplikacji zamiast zwykłego hasła. Możesz użyć tego samego hasła aplikacji dla wielu aplikacji. Aby uzyskać więcej informacji, [pomoc przy hasłach aplikacji].
7. Kliknij przycisk **Gotowe**.

## <a name="use-your-office-phone-as-the-contact-method"></a>Używanie telefonu biurowego jako metody kontaktu
1. Z listy rozwijanej wybierz pozycję **telefon biurowy**  

    ![Konfiguracja](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. Pole numer telefonu jest automatycznie wypełniane informacjami kontaktowymi firmy. Jeśli liczba jest nieprawidłowa lub jej brak, poprosimy administratora o wprowadzenie zmian.
3. Wybierz pozycję **kontakt ze mną** , aby zweryfikować numer telefonu, a my pomożemy numer. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, a następnie wybierz pozycję **Weryfikuj**.
4. W tym momencie zostanie wyświetlony monit o skonfigurowanie haseł aplikacji dla aplikacji niekorzystających z przeglądarki, takich jak Outlook 2010 lub starsza lub Natywna aplikacja poczty e-mail na urządzeniach firmy Apple. Wynika to z faktu, że niektóre aplikacje nie obsługują weryfikacji dwuetapowej. Jeśli nie używasz tych aplikacji, kliknij przycisk **gotowe** i pomiń pozostałe kroki.
5. Jeśli używasz tych aplikacji, skopiuj podane hasło aplikacji i wklej je do aplikacji zamiast zwykłego hasła. Możesz użyć tego samego hasła aplikacji dla wielu aplikacji. Aby uzyskać więcej informacji, zobacz [co to są hasła aplikacji](multi-factor-authentication-end-user-app-passwords.md).
6. Kliknij przycisk **Gotowe**.

## <a name="next-steps"></a>Następne kroki
* Zmień preferowane opcje i [Zarządzaj ustawieniami weryfikacji](multi-factor-authentication-end-user-manage-settings.md) dwuetapowej
* Skonfiguruj [hasła aplikacji](multi-factor-authentication-end-user-app-passwords.md) dla natywnych aplikacji urządzeń, które nie obsługują weryfikacji dwuetapowej.
* Zapoznaj się z [aplikacją Microsoft Authenticator](user-help-auth-app-download-install.md) , aby uzyskać szybkie i bezpieczne uwierzytelnianie nawet wtedy, gdy nie masz usługi komórkowej.
