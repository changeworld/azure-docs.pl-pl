---
title: Szybki Start — Samoobsługowe resetowanie hasła w usłudze Azure AD
description: W tym przewodniku szybki start dowiesz się, jak skonfigurować Samoobsługowe resetowanie haseł w usłudze Azure AD, aby umożliwić użytkownikom Resetowanie własnych haseł i zmniejszenie nakładu pracy działu IT.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 12/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a168f9bf58c4942fc0b76b9ffefc2b32b5bfbe5a
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75549367"
---
# <a name="quickstart-configure-azure-active-directory-self-service-password-reset"></a>Szybki Start: Konfigurowanie Azure Active Directory samoobsługowego resetowania hasła

W tym przewodniku szybki start skonfigurujesz funkcję samoobsługowego resetowania haseł (SSPR) Azure Active Directory (AD), aby umożliwić użytkownikom Resetowanie swoich haseł lub odblokowywanie ich kont. W programie SSPR użytkownicy mogą resetować własne poświadczenia bez pomocy technicznej lub administratora. Dzięki temu użytkownicy mogą odzyskać dostęp do swoich kont bez czekania na dodatkową pomoc techniczną.

> [!IMPORTANT]
> Ten przewodnik Szybki Start zawiera informacje o tym, jak włączyć funkcję samoobsługowego resetowania hasła. Jeśli jesteś użytkownikiem końcowym już zarejestrowanym do samoobsługowego resetowania hasła i chcesz wrócić do swojego konta, przejdź do https://aka.ms/sspr.
>
> Jeśli Twój zespół IT nie włączył możliwości resetowania własnego hasła, skontaktuj się z pomocą techniczną, aby uzyskać dodatkową pomoc.

## <a name="prerequisites"></a>Wymagania wstępne

* Działająca dzierżawa usługi Azure AD z włączoną co najmniej próbną wersją licencji.
    * W razie potrzeby [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Konto z uprawnieniami administratora globalnego.
* Użytkownik testowy niebędący administratorami z hasłem znanym, takim jak *Użytkownik testowy*.
    * Jeśli musisz utworzyć użytkownika, zobacz [Szybki Start: Dodawanie nowych użytkowników do Azure Active Directory](../add-users-azure-active-directory.md).
* Grupa pilotażowa do przetestowania z tym, że użytkownik testowy niebędący administratorem jest członkiem, na przykład *SSPR-test-Group*.
    * Jeśli musisz utworzyć grupę, zobacz jak [utworzyć grupę i dodać członków w Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Włączanie samoobsługowego resetowania hasła

[Wyświetl ten proces jako film wideo w usłudze YouTube](https://youtu.be/Pa0eyqjEjvQ)

1. W menu Azure Portal lub na stronie **głównej** wybierz pozycję **Azure Active Directory** , a następnie wybierz pozycję **Resetowanie hasła**.

1. Na stronie **Właściwości** pod opcją samoobsługowego **resetowania hasła**wybierz pozycję **wybrane**.
1. Wybierz **pozycję Wybierz grupę**, a następnie wybierz grupę pilotażową utworzoną w ramach sekcji wymagania wstępne tego artykułu, na przykład *SSPR-test-Group*. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.
1. Na stronie **metody uwierzytelniania** wprowadź następujące opcje, a następnie wybierz pozycję **Zapisz**:
    * Liczba metod wymaganych do zresetowania: **1**
    * Metody dostępne dla użytkowników:
        * **Kod aplikacji mobilnej**
        * **Wiadomość e-mail**

    > [!div class="mx-imgBorder"]
    > ![Wybieranie metod uwierzytelniania dla SSPR][Authentication]

4. Na stronie **rejestracja** wprowadź następujące opcje, a następnie wybierz pozycję **Zapisz**:
   * Wymaganie od użytkowników rejestrowania się podczas logowania: **Tak**
   * Ustawienie liczby dni, zanim użytkownicy zostaną poproszeni o ponowne potwierdzenie swoich informacji uwierzytelniania: **365**

## <a name="test-self-service-password-reset"></a>Testowanie funkcji samoobsługowego resetowania haseł

Teraz umożliwia testowanie konfiguracji SSPR za pomocą użytkownika testowego, który jest częścią grupy wybranej w poprzedniej sekcji, takiej jak *Użytkownik testowy*. Ponieważ firma Microsoft narzuca silne wymagania w zakresie uwierzytelniania dla kont administratorów platformy Azure, testowanie z użyciem konta administratora może zmienić wyniki. Aby uzyskać więcej informacji na temat zasad haseł administratorów, zobacz nasz [artykuł dotyczący zasad haseł](concept-sspr-policy.md).

1. Otwórz nowe okno przeglądarki w trybie InPrivate lub incognito i przejdź do adresu [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
2. Zaloguj się przy użyciu użytkownika testowego, takiego jak *Użytkownik testowy*, i zarejestruj numer telefonu uwierzytelniania.
3. Po zakończeniu wybierz przycisk oznaczony jako **dobry** i Zamknij okno przeglądarki.
4. Otwórz nowe okno przeglądarki w trybie InPrivate lub incognito i przejdź do adresu [https://aka.ms/sspr](https://aka.ms/sspr).
5. Wprowadź identyfikator użytkownika niebędącego administratorem, taki jak *Użytkownik testowy*, znaki z CAPTCHA, a następnie wybierz przycisk **dalej**.
6. Wykonaj kroki weryfikacji, aby zresetować hasło.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby wyłączyć funkcję samoobsługowego resetowania hasła, Wyszukaj i wybierz **Azure Active Directory** w Azure Portal. Wybierz pozycję **właściwości** > **Resetowanie hasła**, a następnie wybierz pozycję **Brak** w obszarze włączona funkcja samoobsługowego **resetowania hasła**. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób konfigurowania samoobsługowego resetowania haseł dla użytkowników korzystających tylko z chmury. Aby dowiedzieć się, jak wykonać bardziej szczegółowe wdrożenie, przejdź do naszego przewodnika dotyczącego wdrożenia.

> [!div class="nextstepaction"]
> [Wdrażanie funkcji samoobsługowego resetowania haseł](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Dostępne metody uwierzytelniania w usłudze Azure AD i wymagane ilości"

<!-- INTERNAL LINKS -->
[register-sspr]: ../user-help/active-directory-passwords-reset-register.md
[reset-password]: ../user-help/active-directory-passwords-update-your-own-password.md
