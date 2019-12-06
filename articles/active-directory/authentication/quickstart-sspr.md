---
title: 'Szybki start: samoobsługowe resetowanie haseł w usłudze Azure AD'
description: W tym przewodniku Szybki start szybko skonfigurujesz funkcję samoobsługowego resetowania haseł w usłudze Azure AD, aby umożliwić użytkownikom resetowanie swoich haseł
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 582a6a32aa4c34b2e6fef37f3de5b5414de16cf3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846661"
---
# <a name="quickstart-self-service-password-reset"></a>Szybki start: samoobsługowe resetowanie haseł

W tym przewodniku Szybki start opisano konfigurowanie funkcji samoobsługowego resetowania haseł (SSPR), dzięki której administratorzy IT mogą w prosty sposób umożliwić użytkownikom zresetowanie swoich haseł lub odblokowanie kont.

## <a name="prerequisites"></a>Wymagania wstępne

* Działająca dzierżawa usługi Azure AD z włączoną co najmniej próbną wersją licencji.
* Konto z uprawnieniami administratora globalnego.
* Testowe konto użytkownika niebędącego administratorem i hasło do tego konta. Jeśli chcesz utworzyć użytkownika, zobacz artykuł [Szybki start: dodawanie nowych użytkowników do usługi Azure Active Directory](../add-users-azure-active-directory.md).
* Grupa pilotażowa na potrzeby testów z testowym użytkownikiem niebędącym administratorem jako jej członkiem. Jeśli chcesz utworzyć grupę, zobacz artykuł [Tworzenie grupy i dodawanie do niej członków w usłudze Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Włączanie samoobsługowego resetowania hasła

[Wyświetl ten proces jako film wideo w usłudze YouTube](https://youtu.be/Pa0eyqjEjvQ)

1. Z istniejącej dzierżawy usługi Azure AD w menu Azure Portal lub na stronie **głównej** wybierz pozycję **Azure Active Directory**. Następnie wybierz pozycję **Resetowanie hasła**.

2. Na stronie **Właściwości** w ramach opcji **Funkcja samoobsługowego resetowania hasła jest włączona** wybierz pozycję **Wybrane**.
    * W obszarze **Wybieranie grupy** wybierz grupę pilotażową utworzoną w sekcji dotyczącej wymagań wstępnych w tym artykule.
    * Kliknij przycisk **Save** (Zapisz).

3. Na stronie **Metody uwierzytelniania** wybierz następujące opcje:
   * Liczba metod wymaganych do zresetowania: **1**
   * Metody dostępne dla użytkowników:
      * **Wiadomość e-mail**
      * **Kod aplikacji mobilnej (wersja zapoznawcza)**
   * Kliknij przycisk **Save** (Zapisz).

     ![Wybieranie metod uwierzytelniania dla SSPR][Authentication]

4. Na stronie **Rejestracja** wybierz następujące opcje:
   * Wymaganie od użytkowników rejestrowania się podczas logowania: **Tak**
   * Ustawienie liczby dni, zanim użytkownicy zostaną poproszeni o ponowne potwierdzenie swoich informacji uwierzytelniania: **365**

## <a name="test-self-service-password-reset"></a>Testowanie funkcji samoobsługowego resetowania haseł

Teraz możesz przetestować konfigurację funkcji samoobsługowego resetowania haseł za pomocą użytkownika testowego. Ponieważ firma Microsoft narzuca silne wymagania w zakresie uwierzytelniania dla kont administratorów platformy Azure, testowanie z użyciem konta administratora może zmienić wyniki. Aby uzyskać więcej informacji na temat zasad haseł administratorów, zobacz nasz [artykuł dotyczący zasad haseł](concept-sspr-policy.md).

1. Otwórz nowe okno przeglądarki w trybie InPrivate lub incognito i przejdź do adresu [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
2. Zaloguj się jako użytkownik testowy bez uprawnień administratora i zarejestruj swój numer telefonu uwierzytelniania.
3. Po zakończeniu kliknij przycisk oznaczony jako **Wygląda dobrze** i zamknij okno przeglądarki.
4. Otwórz nowe okno przeglądarki w trybie InPrivate lub incognito i przejdź do adresu [https://aka.ms/sspr](https://aka.ms/sspr).
5. Wprowadź identyfikator użytkownika testowego niemającego uprawnień administratora i znaki z tekstu CAPTCHA, a następnie kliknij przycisk **Dalej**.
6. Wykonaj kroki weryfikacji, aby zresetować swoje hasło.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Samoobsługowe resetowanie haseł można w łatwy sposób wyłączyć. Otwórz dzierżawę usługi Azure AD, przejdź do obszaru **Właściwości** > **Resetowanie hasła**, a następnie wybierz pozycję **Brak** w obszarze **Funkcja samoobsługowego resetowania hasła** jest włączona.

## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z tym przewodnikiem szybkiego startu wiesz już, jak szybko skonfigurować samoobsługowe resetowanie haseł dla swoich użytkowników korzystających tylko z chmury. Aby dowiedzieć się, jak wykonać bardziej szczegółowe wdrożenie, przejdź do naszego przewodnika dotyczącego wdrożenia.

> [!div class="nextstepaction"]
> [Wdrażanie funkcji samoobsługowego resetowania haseł](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Dostępne metody uwierzytelniania w usłudze Azure AD i wymagane ilości"
