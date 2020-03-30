---
title: Konfigurowanie klucza zabezpieczeń jako metody weryfikacji — Azure AD
description: Jak skonfigurować stronę informacje zabezpieczające (wersja zapoznawcza), aby zweryfikować swoją tożsamość, aby użyć klucza zabezpieczeń Szybka tożsamość (FIDO2) jako metody weryfikacji.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: curtand
ms.openlocfilehash: ee868ded0dc3a46f2ec20d0141aa2576cbf71f45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062340"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>Konfigurowanie klucza zabezpieczeń jako metody weryfikacji

Klucze zabezpieczeń można używać jako metody logowania bez hasła w organizacji. Klucz zabezpieczeń to urządzenie fizyczne używane z unikatowym kodem PIN do logowania się na konto służbowe. Ponieważ klucze bezpieczeństwa wymagają mieć urządzenie fizyczne i coś, co tylko wiesz, jest uważany za silniejszą metodę weryfikacji niż nazwa użytkownika i hasło.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> Jeśli nie widzisz opcji klucza zabezpieczeń, możliwe, że twoja organizacja nie zezwala na używanie tej opcji do weryfikacji. W takim przypadku musisz wybrać inną metodę lub skontaktować się z działem pomocy technicznej organizacji, aby uzyskać więcej pomocy.

## <a name="security-verification-versus-password-reset-authentication"></a>Weryfikacja zabezpieczeń a uwierzytelnianie resetowania hasła

Metody informacji zabezpieczających są używane zarówno do weryfikacji zabezpieczeń dwuskładnikowych, jak i resetowania hasła. Jednak nie wszystkie metody mogą służyć do obu celów.

| Metoda | Używana do |
| ------ | -------- |
| Aplikacja Authenticator | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Wiadomości SMS | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Rozmowy telefoniczne | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Klucz zabezpieczeń | Weryfikacja dwuskładnikowa i uwierzytelnianie za pomocą resetowania hasła. |
| Konto e-mail | Tylko uwierzytelnianie za pomocą resetowania hasła. Należy wybrać inną metodę na potrzeby weryfikacji dwuskładnikowej. |
| Pytania zabezpieczające | Tylko uwierzytelnianie za pomocą resetowania hasła. Należy wybrać inną metodę na potrzeby weryfikacji dwuskładnikowej. |

## <a name="what-is-a-security-key"></a>Co to jest klucz zabezpieczeń?

Obecnie obsługujemy kilka projektów i dostawców kluczy bezpieczeństwa za pomocą protokołów uwierzytelniania bez hasła [Fast Identity Online (FIDO2).](https://fidoalliance.org/fido2/) Te klucze umożliwiają zalogowanie się do konta służbowego w celu uzyskania dostępu do zasobów chmurowych organizacji na obsługiwanym urządzeniu i przeglądarce internetowej.

Administrator lub organizacja przekaże Ci klucz zabezpieczeń, jeśli będzie go wymagać do konta służbowego. Dostępne są różne typy kluczy zabezpieczeń, na przykład klucz USB podłączyny do urządzenia lub klawisz NFC, który można dotknąć czytnika NFC. Więcej informacji na temat klucza zabezpieczeń, w tym jego typu, można znaleźć w dokumentacji producenta.

> [!Note]
> Jeśli nie możesz użyć klucza zabezpieczeń FIDO2, istnieją inne metody weryfikacji bez hasła, których można użyć, takie jak aplikacja Microsoft Authenticator lub Windows Hello. Aby uzyskać więcej informacji na temat aplikacji Microsoft Authenticator, zobacz [Co to jest aplikacja Microsoft Authenticator?](user-help-auth-app-overview.md). Aby uzyskać więcej informacji na temat funkcji Windows Hello, zobacz [Omówienie funkcji Windows Hello](https://www.microsoft.com/windows/windows-hello).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby można było zarejestrować klucz zabezpieczeń, muszą być spełnione następujące zasady:

- Administrator włączył tę funkcję do użytku w organizacji.

- Korzystasz z urządzenia z aktualizacją systemu Windows 10 May 2019 i korzystasz z obsługiwanej przeglądarki.

- Masz fizyczny klucz zabezpieczeń zatwierdzony przez administratora lub organizację. Klucz zabezpieczeń musi być zgodny zarówno z FIDO2, jak i z firmą Microsoft. Jeśli masz jakieś pytania dotyczące klucza zabezpieczeń i zgodności z nim, skontaktuj się z działem pomocy technicznej organizacji.

## <a name="register-your-security-key"></a>Zarejestruj swój klucz zabezpieczeń

Aby zalogować się do konta służbowego za pomocą tego klucza, należy utworzyć klucz zabezpieczeń i nadać mu unikatowy kod PIN. Możesz mieć do 10 kluczy zarejestrowanych na swoim koncie. 

1. Przejdź do strony Mój https://myprofile.microsoft.com **profil** i zaloguj się, jeśli jeszcze tego nie zrobiłeś.

2. Wybierz **pozycję Informacje zabezpieczające**, wybierz pozycję **Dodaj metodę**, a następnie wybierz pozycję Klucz **zabezpieczeń** z listy **rozwijanej Dodaj metodę.**

    ![Pole Dodaj metodę z zaznaczonym klawiszem Zabezpieczenia](media/security-info/security-info-security-key-add-method.png)

3. Wybierz **pozycję Dodaj**, a następnie wybierz typ posiadanego klucza zabezpieczeń, urządzenie **USB** lub urządzenie **NFC**.

    ![Wybierz, czy masz klucz zabezpieczeń typu USB czy NFC](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > Jeśli nie masz pewności, jaki typ klucza zabezpieczeń posiadasz, zapoznaj się z dokumentacją producenta. Jeśli nie masz pewności co do producenta, skontaktuj się z działem pomocy technicznej swojej organizacji, aby uzyskać pomoc.

4. Fizycznie udostępnij klucz zabezpieczeń, a następnie w polu **Klucz zabezpieczeń** wybierz pozycję **Dalej**.

    ![Pole rejestracji uruchamiania klucza zabezpieczeń](media/security-info/security-info-security-key-start-setup.png)

    Pojawi się nowe pole ułatwiające konfigurowanie nowej metody logowania.

5. W polu **Konfigurowanie nowej metody logowania** wybierz pozycję **Dalej**, a następnie:

    - Jeśli klucz zabezpieczeń to urządzenie USB, włóż klucz zabezpieczeń do portu USB urządzenia.

    - Jeśli klucz zabezpieczeń to urządzenie NFC, naciśnij klawisz zabezpieczeń czytnika.

6. Wpisz unikatowy kod PIN klucza zabezpieczeń w polu **zabezpieczeń systemu Windows,** a następnie wybierz przycisk **OK**.

    Powrócisz do pola **Konfigurowanie nowej metody logowania.**

7. Wybierz **pozycję Dalej**.

8. Wróć do strony **Informacje o zabezpieczeniach,** wpisz nazwę, którą rozpoznasz później dla nowego klucza zabezpieczeń, a następnie wybierz pozycję **Dalej**.

    ![Strona informacji zabezpieczających, nazywanie klucza zabezpieczeń](media/security-info/security-info-security-key-name.png)

    Twój klucz zabezpieczeń jest zarejestrowany i gotowy do zalogowania się na konto służbowe.

9. Wybierz **pozycję Gotowe,** aby zamknąć okno **Klucz zabezpieczeń.**

    Strona **Informacje o zabezpieczeniach** jest aktualizowana o informacje klucza zabezpieczeń.

    ![Strona informacji zabezpieczających ze wszystkimi zarejestrowanymi metodami](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>Usuwanie klucza zabezpieczeń z informacji zabezpieczających

Jeśli zgubisz lub nie chcesz już używać klucza zabezpieczeń, możesz usunąć klucz z informacji zabezpieczających. Chociaż uniemożliwia to użycie klucza zabezpieczeń z kontem służbowym, klucz zabezpieczeń nadal przechowuje dane i informacje o poświadczeniach. Aby usunąć dane i informacje o poświadczeniach z samego klucza zabezpieczeń, należy postępować zgodnie z instrukcjami w sekcji [Resetowanie klucza zabezpieczeń zgodnego z](#reset-your-security-key) firmą Microsoft w tym artykule.

1. Wybierz **łącze Usuń** z klucza zabezpieczeń, aby je usunąć.

2. Z pola **Usuń klucz zabezpieczeń** wybierz **pozycję Ok.**

    Klucz zabezpieczeń zostanie usunięty i nie będzie już można go używać do logowania się na konto służbowe.

>[!Important]
>Jeśli usuniesz klucz zabezpieczeń przez pomyłkę, możesz zarejestrować go ponownie, korzystając z instrukcji w sekcji [Jak zarejestrować klucz zabezpieczeń w](#register-your-security-key) tym artykule.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Zarządzanie ustawieniami klucza zabezpieczeń w ustawieniach systemu Windows

Ustawienia klucza zabezpieczeń można zarządzać za pomocą aplikacji **Ustawienia systemu Windows,** w tym resetować klucz zabezpieczeń i tworzyć nowy numer PIN klucza zabezpieczeń.

### <a name="reset-your-security-key"></a>Resetowanie klucza zabezpieczeń

Jeśli chcesz usunąć wszystkie informacje o koncie przechowywane w fizycznym kluczu zabezpieczeń, musisz przywrócić klucz do ustawień fabrycznych. Zresetowanie klucza zabezpieczeń powoduje usunięcie wszystkiego z klucza, co pozwala rozpocząć od nowa.

>[!IMPORTANT]
>Zresetowanie klucza zabezpieczeń powoduje usunięcie wszystkiego z klucza, resetowanie go do ustawień fabrycznych.
>
> **Wszystkie dane i poświadczenia zostaną wyczyszczone.**

#### <a name="to-reset-your-security-key"></a>Aby zresetować klucz zabezpieczeń

1. Otwórz aplikację Ustawienia systemu Windows, wybierz pozycję **Konta**, wybierz pozycję **Opcje logowania**, wybierz pozycję **Klucz zabezpieczeń**, a następnie wybierz pozycję **Zarządzaj**.

2. Włóż klucz zabezpieczeń do portu USB lub naciśnij czytnik NFC, aby zweryfikować swoją tożsamość.

3. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie na podstawie konkretnego producenta klucza zabezpieczeń. Jeśli producenta kluczy nie ma na ekranie instrukcji, zapoznaj się z witryną producenta, aby uzyskać więcej informacji.

4. Wybierz **pozycję Zamknij,** aby zamknąć ekran **Zarządzanie.**

### <a name="create-a-new-security-key-pin"></a>Tworzenie nowego numeru PIN klucza zabezpieczeń

Możesz utworzyć nowy kod PIN klucza zabezpieczeń dla klucza zabezpieczeń.

#### <a name="to-create-a-new-security-key-pin"></a>Aby utworzyć nowy numer PIN klucza zabezpieczeń

1. Otwórz aplikację Ustawienia systemu Windows, wybierz pozycję **Konta**, wybierz pozycję **Opcje logowania**, wybierz pozycję **Klucz zabezpieczeń**, a następnie wybierz pozycję **Zarządzaj**.

2. Włóż klucz zabezpieczeń do portu USB lub naciśnij czytnik NFC, aby zweryfikować swoją tożsamość.
3. Wybierz **pozycję Dodaj** z obszaru Kod PIN **klucza zabezpieczeń,** wpisz i potwierdź nowy kod PIN klucza zabezpieczeń, a następnie wybierz przycisk **OK**.

     Klucz zabezpieczeń jest aktualizowany o nowy kod PIN klucza zabezpieczeń do użytku z kontem służbowym. Jeśli zdecydujesz się ponownie zmienić kod PIN, możesz wybrać przycisk **Zmień.**
4. Wybierz **pozycję Zamknij,** aby zamknąć ekran **Zarządzanie.**

## <a name="additional-security-info-methods"></a>Dodatkowe metody informacji o zabezpieczeniach

Aby zarejestrować klucz zabezpieczeń, musisz mieć zarejestrowaną co najmniej jedną dodatkową metodę weryfikacji zabezpieczeń. Zobacz [sekcję Omówienie,](security-info-add-update-methods-overview.md) aby uzyskać więcej informacji. 

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat metod weryfikacji bez hasła, przeczytaj usługę [Azure AD firmy Microsoft rozpoczyna publiczną wersję zapoznawczą kluczy zabezpieczeń FIDO2, włączając](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) blog logowania bez hasła lub przeczytaj artykuły [omówienie](https://www.microsoft.com/windows/windows-hello) [Co to jest microsoft Authenticator?](user-help-auth-app-overview.md)

- Aby uzyskać bardziej szczegółowe informacje na temat [kluczy zabezpieczeń zgodnych ze standardem Microsoft](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key).

- Zagubione lub zapomniane hasło możesz zresetować w [portalu resetowania hasła](https://passwordreset.microsoftonline.com/) lub wykonując kroki przedstawione w artykule [Resetowanie hasła służbowego](active-directory-passwords-update-your-own-password.md).

- Wskazówki i pomoc dotyczące rozwiązywania problemów z logowaniem znajdują się w artykule [Nie można zalogować się na konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
