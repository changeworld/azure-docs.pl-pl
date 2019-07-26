---
title: Skonfiguruj informacje zabezpieczające, aby użyć klucza zabezpieczeń (wersja zapoznawcza) — Azure Active Directory | Microsoft Docs
description: Jak skonfigurować informacje zabezpieczające, aby zweryfikować Twoją tożsamość przy użyciu klucza zabezpieczeń szybkiej tożsamości online (FIDO2).
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d2f06b054e433c0320019548c56539d102beaad
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68386501"
---
# <a name="set-up-security-info-to-use-a-security-key-preview"></a>Skonfiguruj informacje zabezpieczające, aby użyć klucza zabezpieczeń (wersja zapoznawcza)

Kluczy zabezpieczeń można używać jako metody logowania bezhasło w organizacji. Klucz zabezpieczeń to urządzenie fizyczne, które jest używane z unikatowym numerem PIN do logowania się do konta służbowego. Ponieważ klucze zabezpieczeń wymagają posiadania urządzenia fizycznego i czegoś tylko ty, jest on traktowany jako silniejszy Metoda uwierzytelniania niż nazwa użytkownika i hasło.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Important]
>Ta zawartość jest przeznaczona dla użytkowników. Jeśli jesteś administratorem, więcej informacji na temat konfigurowania i zarządzania środowiskiem usługi Azure Active Directory (Azure AD) możesz znaleźć w [dokumentacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory).

## <a name="what-is-a-security-key"></a>Co to jest klucz zabezpieczeń?

Obecnie obsługujemy kilka projektów i dostawców kluczy zabezpieczeń przy użyciu metody uwierzytelniania bezhasło w [trybie szybkiej tożsamości online (Fido)](https://fidoalliance.org/fido2/) (FIDO2). Ta metoda umożliwia jednokrotne zalogowanie się do konta służbowego w celu uzyskania dostępu do wszystkich zasobów opartych na chmurze i obsługiwanych przeglądarek.

Administrator lub Twoja organizacja dostarczy klucz zabezpieczeń, jeśli będzie potrzebny dla konta służbowego. Istnieją różne typy kluczy zabezpieczeń, których można użyć, na przykład klucz USB, który można podłączyć do urządzenia lub klawisza NFC, który został wybrany w czytniku NFC. Więcej informacji o kluczu zabezpieczeń, w tym o tym, jaki typ jest, można znaleźć w dokumentacji producenta.

> [!Note]
> Jeśli nie możesz użyć klucza zabezpieczeń FIDO2, istnieją inne metody uwierzytelniania bezhasła, których można użyć, takich jak aplikacja Microsoft Authenticator lub funkcja Windows Hello. Aby uzyskać więcej informacji na temat aplikacji Microsoft Authenticator, zobacz [co to jest aplikacja Microsoft Authenticator?](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview). Aby uzyskać więcej informacji na temat usługi Windows Hello, zobacz [Omówienie funkcji Windows Hello](https://www.microsoft.com/windows/windows-hello).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem rejestrowania klucza zabezpieczeń należy dokładnie sprawdzić, czy:

- Administrator włączył tę funkcję do użycia w organizacji.

- Korzystasz z urządzenia z systemem Windows 10 lub nowszym, wersja 1903 i korzystanie z przeglądarki Microsoft Edge.

- Uzyskano fizyczny klucz zabezpieczeń od administratora lub Twojej organizacji. Klucz zabezpieczeń musi być zarówno FIDO2, jak i zgodny z firmą Microsoft. Jeśli masz pytania dotyczące klucza zabezpieczeń i tego, czy jest on zgodny, skontaktuj się z działem pomocy technicznej Twojej organizacji.

## <a name="register-your-security-key"></a>Rejestrowanie klucza zabezpieczeń

Przed zalogowaniem się do konta służbowego przy użyciu klucza należy przygotować klucz zabezpieczeń do pracy z systemem Windows i unikatowym numerem PIN.

1. Przejdź do strony **mój profil** w https://myprofile.microsoft.com obszarze i zaloguj się, jeśli jeszcze tego nie zrobiono.

2. Wybierz pozycję **Informacje o zabezpieczeniach**, wybierz pozycję **Dodaj metodę**, a następnie wybierz pozycję **klucz zabezpieczeń** z listy rozwijanej **Dodaj metodę** .

    ![Pole dodawania metody z wybranym kluczem zabezpieczeń](media/security-info/security-info-security-key-add-method.png)

3. Wybierz pozycję **Dodaj**, a następnie wybierz typ klucza zabezpieczeń, czyli **urządzenie USB** lub **urządzenie NFC**.

    ![Wybierz, czy masz klucz zabezpieczeń USB lub NFC](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > Jeśli nie masz pewności, jakiego typu klucz zabezpieczeń masz, zapoznaj się z dokumentacją producenta. Jeśli nie masz pewności co do producenta, skontaktuj się z działem pomocy technicznej Twojej organizacji w celu uzyskania pomocy.

4. Klucz zabezpieczeń jest dostępny fizycznie, a następnie w polu **klucz zabezpieczeń** wybierz pozycję **dalej**.

    ![Pole rejestracji uruchamiania klucza zabezpieczeń](media/security-info/security-info-security-key-start-setup.png)

    Zostanie wyświetlone nowe okno umożliwiające przeprowadzenie kroków konfigurowania nowej metody logowania.

5. W polu **Konfigurowanie nowej metody logowania** wybierz pozycję **dalej**, a następnie:

    - Jeśli klucz zabezpieczeń jest urządzeniem USB, Wstaw klucz zabezpieczeń do portu USB urządzenia.

    - Jeśli Twój klucz zabezpieczeń jest urządzeniem NFC, naciśnij swój klucz zabezpieczeń do czytnika.

6. Wpisz unikatowy numer PIN klucza zabezpieczeń w polu **zabezpieczenia systemu Windows** , a następnie wybierz przycisk **OK**.

    Powrócisz do **Ustawienia nowej metody logowania** .

7. Wybierz opcję **Dalej**.

8. Wróć do strony **informacje zabezpieczające** wpisz nazwę rozpoznawaną później dla nowego klucza zabezpieczeń, a następnie wybierz przycisk **dalej**.

    ![Strona informacje zabezpieczające, nadawanie nazwy kluczowi zabezpieczeń](media/security-info/security-info-security-key-name.png)

    Twój klucz zabezpieczeń jest zarejestrowany i gotowy do użycia w celu logowania się do konta służbowego.

9. Wybierz pozycję **gotowe** , aby zamknąć pole **klucz zabezpieczeń** .

    Strona **informacje zabezpieczające** zostanie zaktualizowana informacjami o kluczu zabezpieczeń.

    ![Strona z informacjami o zabezpieczeniach z pokazanymi wszystkimi zarejestrowanymi metodami](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>Usuwanie klucza zabezpieczeń z informacji zabezpieczających

Jeśli nie chcesz już używać klucza zabezpieczeń, możesz usunąć klucz z informacji zabezpieczających. Ponieważ spowoduje to zatrzymanie klucza zabezpieczeń z użyciem konta służbowego, klucz zabezpieczeń kontynuuje przechowywanie danych i poświadczeń. Aby usunąć dane i poświadczenia z samego klucza zabezpieczeń, należy postępować zgodnie z instrukcjami w sekcji Resetowanie [zabezpieczeń zgodnych z firmą Microsoft](#reset-your-security-key) w tym artykule.

1. Wybierz łącze **Usuń** z klucza zabezpieczeń do usunięcia.

2. W polu **Usuń klucz zabezpieczeń** wybierz pozycję **OK** .

    Klucz zabezpieczeń został usunięty i nie będzie już można go używać do logowania się do konta służbowego.

>[!Important]
>Jeśli ten klucz zabezpieczeń został usunięty przez pomyłkę, należy zarejestrować go ponownie, korzystając z instrukcji przedstawionych w sekcji [jak zarejestrować klucz zabezpieczeń](#register-your-security-key) w tym artykule.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Zarządzanie ustawieniami klucza zabezpieczeń z ustawień systemu Windows

Ustawieniami klucza zabezpieczeń można zarządzać z poziomu aplikacji **Ustawienia systemu Windows** , w tym resetowania klucza zabezpieczeń i tworzenia nowego numeru PIN klucza zabezpieczeń.

### <a name="reset-your-security-key"></a>Zresetuj klucz zabezpieczeń

Aby usunąć wszystkie informacje o koncie przechowywanych w fizycznym kluczu zabezpieczeń, należy przywrócić klucz z powrotem do domyślnych ustawień fabrycznych. Zresetowanie klucza zabezpieczeń spowoduje usunięcie wszystkich danych z klucza, co pozwoli na rozpoczęcie od nowa.

>[!IMPORTANT]
>Zresetowanie klucza zabezpieczeń spowoduje usunięcie wszystkich danych z klucza i zresetowanie go do domyślnych ustawień fabrycznych.
>
> **Wszystkie dane i poświadczenia zostaną wyczyszczone.**

#### <a name="to-reset-your-security-key"></a>Aby zresetować klucz zabezpieczeń

1. Otwórz aplikację Ustawienia systemu Windows, wybierz pozycję **konta**, wybierz pozycję **Opcje logowania**, wybierz pozycję **klucz zabezpieczeń**, a następnie wybierz pozycję **Zarządzaj**.

2. Wstaw swój klucz zabezpieczeń do portu USB lub naciśnij swój czytnik NFC, aby zweryfikować swoją tożsamość.

3. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie w zależności od konkretnego producenta klucza zabezpieczeń. Jeśli klucz producenta nie znajduje się na liście instrukcji na ekranie, zapoznaj się z witryną producenta, aby uzyskać więcej informacji.

4. Wybierz pozycję **Zamknij** , aby zamknąć ekran **Zarządzanie** .

### <a name="create-a-new-security-key-pin"></a>Utwórz nowy numer PIN klucza zabezpieczeń

Nowy numer PIN klucza zabezpieczeń można utworzyć dla klucza zabezpieczeń.

#### <a name="to-create-a-new-security-key-pin"></a>Aby utworzyć nowy numer PIN klucza zabezpieczeń

1. Otwórz aplikację Ustawienia systemu Windows, wybierz pozycję **konta**, wybierz pozycję **Opcje logowania**, wybierz pozycję **klucz zabezpieczeń**, a następnie wybierz pozycję **Zarządzaj**.

2. Włóż klucz zabezpieczeń do portu USB lub naciśnij swój czytnik NFC, aby zweryfikować swoją tożsamość. 5
3. Wybierz pozycję **Dodaj** z obszaru **numeru PIN klucza zabezpieczeń** , wpisz i potwierdź nowy numer PIN klucza zabezpieczeń, a następnie wybierz przycisk **OK**.

    Klucz zabezpieczeń jest aktualizowany przy użyciu nowego numeru PIN klucza zabezpieczeń, który będzie używany z kontem służbowym. Jeśli zdecydujesz się ponownie zmienić kod PIN, możesz wybrać przycisk **Zmień** . 6
4. Wybierz pozycję **Zamknij** , aby zamknąć ekran **Zarządzanie** .

## <a name="additional-security-info-methods"></a>Dodatkowe metody informacji zabezpieczających

Dostępne są dodatkowe opcje dotyczące sposobu, w jaki Twoja organizacja kontaktuje się z Twoją tożsamością, w oparciu o to, co you're próbę wykonania. Dostępne są następujące opcje:

- **Aplikacja uwierzytelniania.** Pobierz aplikację Authenticator i użyj jej do uzyskania powiadomienia o zatwierdzeniu lub losowo wygenerowanego kodu zatwierdzenia na potrzeby weryfikacji dwuetapowej lub resetowania hasła. Instrukcje krok po kroku dotyczące konfigurowania i używania aplikacji Microsoft Authenticator można znaleźć w temacie [set up Security Info to use the Authenticator App](security-info-setup-auth-app.md).

- **Tekst urządzenia przenośnego.** Wprowadź numer urządzenia przenośnego i uzyskaj tekst kodu, który będzie używany do weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikowania tożsamości za pomocą wiadomości tekstowych (SMS), zobacz [Konfigurowanie informacji zabezpieczających do używania wiadomości](security-info-setup-text-msg.md)SMS.

- **Urządzenie przenośne lub połączenie telefoniczne.** Wprowadź numer urządzenia przenośnego i otrzymaj połączenie telefoniczne w celu weryfikacji dwuetapowej lub resetowania hasła. Aby uzyskać instrukcje krok po kroku dotyczące weryfikowania tożsamości przy użyciu numeru telefonu, zobacz [Konfigurowanie informacji zabezpieczających do korzystania z połączeń telefonicznych](security-info-setup-phone-number.md).

- **Adres e-mail.** Wprowadź służbowy adres e-mail, aby uzyskać wiadomość e-mail na potrzeby resetowania hasła. Ta opcja jest niedostępna w przypadku weryfikacji dwuetapowej. Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania poczty e-mail, zobacz [Konfigurowanie informacji zabezpieczających do używania poczty e-mail](security-info-setup-email.md).

- **Pytania zabezpieczające.** Odpowiedz na niektóre pytania zabezpieczające utworzone przez administratora w organizacji. Ta opcja jest dostępna tylko do resetowania haseł, a nie do weryfikacji dwuetapowej. Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania pytań zabezpieczających, zobacz artykuł [Konfigurowanie informacji zabezpieczających do używania pytań zabezpieczających](security-info-setup-questions.md) .

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat metod uwierzytelniania bezhasła, zapoznaj się z tematem " [usługa Azure AD" rozpoczyna publiczną wersję zapoznawczą kluczy zabezpieczeń FIDO2, włączając](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) w to Blog dotyczący logowania bezhasła lub zapoznaj się z [aplikacją Microsoft Authenticator?](https://docs.microsoft.com/azure/active-directory/user-help8user-help-auth-app-overview) [ Artykuły dotyczące omówienia systemu Windows Hello](https://www.microsoft.com/windows/windows-hello) .

- Aby uzyskać bardziej szczegółowe informacje o [kluczach zabezpieczeń zgodnych z firmą Microsoft](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key).

- Zagubione lub zapomniane hasło możesz zresetować w [portalu resetowania hasła](https://passwordreset.microsoftonline.com/) lub wykonując kroki przedstawione w artykule [Resetowanie hasła służbowego](user-help-reset-password.md).

- Wskazówki i pomoc dotyczące rozwiązywania problemów z logowaniem znajdują się w artykule [Nie można zalogować się na konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
