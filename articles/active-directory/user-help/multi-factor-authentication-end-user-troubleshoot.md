---
title: Typowe problemy & rozwiązania z uwierzytelnianiem kont — Azure AD
description: Poznaj potencjalne problemy i rozwiązania niektórych typowych problemów z weryfikacją dwuskładnikową oraz konta służbowego.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: lizross
ms.reviewer: kexia
ms.openlocfilehash: 498c150706cd62168b7c45b1bcf36ad6c3a8b63b
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705211"
---
# <a name="common-problems-and-solutions-with-two-factor-verification-and-your-work-or-school-account"></a>Typowe problemy i rozwiązania przy użyciu weryfikacji dwuskładnikowej oraz konta służbowego

Twoja organizacja włączyła weryfikację dwuetapową, co oznacza, że logowanie za pomocą konta służbowego wymaga podania kombinacji nazwy użytkownika, hasła oraz urządzenia przenośnego lub telefonu. Twoja organizacja włączyła tę dodatkową weryfikację, ponieważ jest bardziej bezpieczna niż tylko hasło oparte na dwóch formach uwierzytelniania: coś, czego znasz Weryfikacja dwuetapowa może pomóc w zablokowaniu przez użytkownika złośliwych hakerów, ponieważ nawet jeśli mają swoje hasło, szanse się, że nie mają one również Twojego urządzenia.

Istnieją pewne typowe problemy z weryfikacją dwuetapową, które pojawiają się częściej niż dowolna z nas. W tym artykule zostaje się, jak rozwiązać typowe problemy i pewne możliwe poprawki.

>[!Important]
>Ta zawartość jest przeznaczona dla użytkowników. Jeśli jesteś administratorem, więcej informacji na temat konfigurowania i zarządzania środowiskiem usługi Azure Active Directory (Azure AD) możesz znaleźć w [dokumentacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory).
>
>Ta zawartość jest również przeznaczona wyłącznie do użytku z kontem służbowym, które jest udostępniane przez organizację (na przykład alain@contoso.com). Jeśli masz problemy z weryfikacją dwuetapową i osobistą konto Microsoft, konto skonfigurowane dla siebie (na przykład danielle@outlook.com), zobacz [Włączanie lub wyłączanie weryfikacji dwuskładnikowej w konto Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-forgot-my-mobile-device-at-home"></a>Nie pamiętam mojego urządzenia przenośnego w domu

Dzieje się tak. Twoje urządzenie przenośne zostało pozostawione w domu, a teraz nie można użyć telefonu do zweryfikowania, czy jesteś Twoim nadawcą. Jeśli wcześniej dodano kolejną metodę logowania się do konta, takiego jak telefon biurowy, powinno być możliwe korzystanie z tej metody teraz. Jeśli nigdy nie dodaliśmy dodatkowej metody weryfikacji, musisz skontaktować się z działem pomocy technicznej, aby uzyskać pomoc w powrocie do Twojego konta.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Aby zalogować się do konta służbowego przy użyciu innej metody weryfikacji

1. Zaloguj się do swojego konta normalnie i wybierz łącze **Zaloguj w innym** miejscu na stronie **weryfikacji dwuetapowej** .

    ![Zmień metodę weryfikacji logowania](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Jeśli nie widzisz **znaku w innym sposobie** linku, oznacza to, że nie zostały skonfigurowane żadne inne metody weryfikacji. Musisz skontaktować się z administratorem, aby uzyskać pomoc w logowaniu się do konta.

2. Wybierz alternatywną metodę weryfikacji i Kontynuuj proces weryfikacji dwuetapowej.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Moje urządzenie przenośne zostało zgubione lub zostało skradzione

W przypadku zgubienia lub kradzieży urządzenia przenośnego możesz zalogować się przy użyciu innej metody lub poproszeniu działu pomocy technicznej o wyczyszczenie ustawień. Zdecydowanie zalecamy poznanie przez dział pomocy technicznej, czy Twój telefon został zgubiony lub skradziony, więc można wykonać odpowiednie aktualizacje na Twoim koncie. Po wyczyszczeniu ustawień zostanie wyświetlony monit o [zarejestrowanie się w celu przeprowadzenia weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-first-time.md) przy następnym logowaniu.

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>Nie otrzymuję kodu weryfikacyjnego wysłanego do urządzenia przenośnego

Kod weryfikacyjny nie jest typowym problemem i jest zazwyczaj związany z urządzeniem przenośnym i jego ustawieniami. Niektóre możliwe czynności do wypróbowania:

- **Uruchom ponownie urządzenie przenośne.** Czasami urządzenie wymaga odświeżenia. Ponowne uruchomienie urządzenia kończy wszystkie procesy w tle, które są obecnie uruchomione i mogą powodować problemy, a także odświeża podstawowe składniki urządzenia, uruchamiając je ponownie na wypadek awarii w pewnym momencie.

- **Sprawdź, czy informacje zabezpieczające są poprawne.** Upewnij się, że informacje o metodzie weryfikacji zabezpieczeń są dokładne, a zwłaszcza numery telefonów. Jeśli umieścisz w niewłaściwym numerze telefonu, wszystkie alerty przechodzą do tej niepoprawnej liczby. Na szczęście ten użytkownik nie będzie mógł wykonywać żadnych czynności dotyczących alertów, ale również nie pomoże Ci zalogować się do konta. Aby upewnić się, że informacje są poprawne, zapoznaj się z instrukcjami w artykule [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md) .

- **Sprawdź, czy powiadomienia są włączone.** Upewnij się, że na urządzeniu przenośnym włączono powiadomienia i że została wybrana metoda powiadamiania, która umożliwia nawiązywanie połączeń telefonicznych, aplikacji uwierzytelniania i aplikacji do obsługi wiadomości (w przypadku wiadomości SMS) do wysyłania powiadomień o alertach na urządzeniu przenośnym.

- **Upewnij się, że masz sygnał urządzenia i połączenie internetowe.** Upewnij się, że Twoje połączenia telefoniczne i wiadomości SMS są przekazywane do urządzenia przenośnego. Skontaktuj się z ty i Wyślij wiadomość SMS, aby upewnić się, że otrzymujesz oba elementy. Jeśli tego nie zrobisz, najpierw sprawdź, czy urządzenie przenośne jest włączone. Jeśli urządzenie jest włączone, ale nie masz połączenia lub tekstu, prawdopodobnie wystąpił problem z siecią i musisz skontaktować się z dostawcą. Jeśli często występują problemy związane z sygnałami, zalecamy zainstalowanie [aplikacji Microsoft Authenticator](user-help-auth-app-download-install.md) i używanie jej na urządzeniu przenośnym. Aplikacja Authenticator może generować losowe kody zabezpieczeń do logowania, nie wymagając żadnego sygnału komórkowego ani połączenia internetowego.

- **Wyłącz nie przeszkadzać.** Upewnij się, że na urządzeniu przenośnym nie została włączona funkcja nie **przeszkadzać** . Gdy ta funkcja jest włączona, powiadomienia nie mogą otrzymywać alertów na urządzeniu przenośnym. Instrukcje dotyczące sposobu wyłączania tej funkcji można znaleźć w podręczniku urządzenia przenośnego.

- **Odblokuj numery telefonów** W Stany Zjednoczone połączenia głosowe pochodzące od firmy Microsoft pochodzą z następujących liczb: + 1 (866) 539 4191, + 1 (855) 330 8653 i + 1 (877) 668 6536.

- **Sprawdź ustawienia związane z baterią.** Jest to nietypowy bit na powierzchni, ale jeśli skonfigurowano optymalizację baterii w celu zatrzymywania nieużywanych aplikacji w tle, system powiadomień ma największe ryzyko. Aby spróbować rozwiązać ten problem, wyłącz optymalizację baterii dla aplikacji uwierzytelniania i aplikacji do obsługi wiadomości, a następnie spróbuj ponownie zalogować się do konta.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>Nie otrzymuję monitu o podanie drugiej informacji weryfikacyjnej

Jeśli zalogowano się na koncie służbowym przy użyciu nazwy użytkownika i hasła, ale nie został wyświetlony monit o podanie dodatkowych informacji weryfikacyjnych o zabezpieczeniach, może to oznaczać, że urządzenie nie zostało jeszcze skonfigurowane. Urządzenie przenośne musi zostać skonfigurowane do pracy z dodatkową metodą weryfikacji zabezpieczeń. Aby upewnić się, że urządzenie przenośne jest włączone i dostępne do użycia w ramach metody weryfikacji, zobacz artykuł [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md) . Jeśli wiesz, że nie skonfigurowano urządzenia ani Twojego konta, możesz to zrobić teraz, wykonując kroki opisane w artykule [Konfigurowanie mojego konta na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md) .

## <a name="i-got-a-new-phone-number-how-do-i-change-it-for-two-factor-verification"></a>Mam nowy numer telefonu, jak zmienić go na potrzeby weryfikacji dwuskładnikowej?

Jeśli masz nowy numer telefonu, musisz zaktualizować Szczegóły metody weryfikacji zabezpieczeń, aby wyświetlić komunikaty weryfikacyjne w odpowiedniej lokalizacji. Aby zaktualizować metodę weryfikacji, wykonaj kroki opisane w sekcji **Dodawanie lub zmiana numeru telefonu** w artykule [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) .

## <a name="i-got-a-new-mobile-device-how-do-i-add-it"></a>Mam nowe urządzenie przenośne, jak je dodać?

Jeśli masz nowe urządzenie przenośne, musisz skonfigurować je do pracy z weryfikacją dwuetapową. To jest rozwiązanie wieloetapowe:

1. Skonfiguruj swoje urządzenie do pracy z kontem służbowym, wykonując kroki opisane w artykule [Konfigurowanie mojego konta na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md) .

2. Zaktualizuj swoje informacje o koncie i urządzeniu na stronie **dodatkowej weryfikacji zabezpieczeń** , usuwając stare urządzenie i dodając nowe. Aby uzyskać więcej informacji, zobacz artykuł [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md) .

3. Element opcjonalny. Pobierz, zainstaluj i skonfiguruj aplikację Microsoft Authenticator na urządzeniu przenośnym, wykonując czynności opisane w artykule [pobieranie i instalowanie aplikacji Microsoft Authenticator](user-help-auth-app-download-install.md) .

4. Element opcjonalny. Włącz weryfikację dwuskładnikową dla zaufanych urządzeń, wykonując czynności opisane w sekcji **Włączanie weryfikacji dwuskładnikowej w** artykule [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) .

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Mam problemy z logowaniem się na urządzeniu przenośnym podczas podróży

Może się okazać trudne użycie metody weryfikacji powiązanej z urządzeniami przenośnymi, na przykład wiadomości SMS, podczas gdy jesteś w międzynarodowej lokalizacji. Istnieje również możliwość, że urządzenie przenośne może spowodować naliczenie opłat za roaming. W tej sytuacji zalecamy korzystanie z aplikacji Microsoft Authenticator z opcją nawiązywania połączenia z hotspotem Wi-Fi. Aby uzyskać więcej informacji o pobieraniu, instalowaniu i konfigurowaniu aplikacji Microsoft Authenticator na urządzeniu przenośnym, zobacz artykuł [pobieranie i instalowanie aplikacji Microsoft Authenticator](user-help-auth-app-download-install.md) .

## <a name="i-cant-get-my-app-passwords-to-work"></a>Nie mogę pobrać haseł aplikacji

Hasła aplikacji zastępują normalne hasło dla starszych aplikacji klasycznych, które nie obsługują weryfikacji dwuskładnikowej. Najpierw upewnij się, że hasło zostało wpisane prawidłowo. Jeśli to nie rozwiąże problemu, spróbuj utworzyć nowe hasło aplikacji dla aplikacji, wykonując czynności opisane w sekcji **Tworzenie i usuwanie haseł aplikacji za pomocą portalu Moje aplikacje** w artykule [Zarządzanie hasłami aplikacji w celu weryfikacji dwuetapowej](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) .

## <a name="why-cant-i-turn-two-factor-verification-off"></a>Dlaczego nie mogę włączyć weryfikacji dwuetapowej?

Jeśli używasz weryfikacji dwuetapowej przy użyciu konta służbowego (na przykład alain@contoso.com), najprawdopodobniej oznacza to, że Twoja organizacja zdecydowała się korzystać z tej dodatkowej funkcji zabezpieczeń. Ponieważ organizacja zdecydowała się na korzystanie z tej funkcji, nie ma możliwości jej samoistnienia. Jeśli jednak korzystasz z weryfikacji dwuetapowej przy użyciu konta osobistego, takiego jak alain@outlook.com, możesz włączyć i wyłączyć tę funkcję. Aby uzyskać instrukcje dotyczące kontrolowania weryfikacji dwuskładnikowej dla osobistych kont Microsoft, zobacz [Włączanie lub wyłączanie weryfikacji dwuskładnikowej dla konto Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nie mogę znaleźć odpowiedzi na mój problem

Jeśli wykonano te kroki, ale nadal występują problemy, skontaktuj się z działem pomocy technicznej w celu uzyskania pomocy.

## <a name="related-articles"></a>Pokrewne artykuły:

- [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md)

- [Konfigurowanie mojego konta na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md)

- [Microsoft Authenticator app FAQ (Aplikacja Microsoft Authenticator — często zadawane pytania)](user-help-auth-app-faq.md)
