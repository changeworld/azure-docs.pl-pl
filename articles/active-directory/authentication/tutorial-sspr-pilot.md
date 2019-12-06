---
title: Włączanie pilotażu usługi Azure AD SSPR
description: W tym samouczku włączysz samoobsługowe resetowanie hasła usługi Azure AD dla pilotażowej grupy użytkowników
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 08/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecdde4ef12c6991fad53f2286ee462fec31606ae
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846287"
---
# <a name="tutorial-complete-an-azure-ad-self-service-password-reset-pilot-roll-out"></a>Samouczek: przeprowadzanie pilotażowego wdrożenia samoobsługowego resetowania hasła usługi Azure AD

W tym samouczku przeprowadzisz pilotażowe wdrożenie samoobsługowego resetowania hasła (SSPR) usługi Azure AD w swojej organizacji i przetestujesz je przy użyciu konta bez uprawnień administratora.

Ważne jest, aby wszystkie testy funkcji samoobsługowego resetowania hasła były wykonywane z kontami niebędącymi administratorami. Zasadami resetowania hasła dla kont administratorów zarządza firma Microsoft, która wymaga użycia silniejszych metod uwierzytelniania. Te zasady nie zezwalają na zastosowanie pytań zabezpieczających i odpowiedzi oraz wymagają użycia dwóch metod resetowania.

> [!div class="checklist"]
> * Włączanie samoobsługowego resetowania hasła
> * Testowanie funkcji SSPR jako użytkownik

## <a name="prerequisites"></a>Wymagania wstępne

* Konto administratora globalnego

## <a name="enable-self-service-password-reset"></a>Włączanie samoobsługowego resetowania hasła

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego.
1. Przejdź do usługi **Azure Active Directory** i wybierz pozycję **Resetowanie hasła**.
1. Zacznij od grupy pilotażowej, włączając samoobsługowe hasła dla podzbioru użytkowników w Twojej organizacji.
   * Na stronie **Właściwości** w obszarze opcja samoobsługowe **Resetowanie hasła**wybierz pozycję **wybrane**i wybierz grupę pilotażową.
      * Z funkcji samoobsługowego resetowania hasła mogą korzystać tylko członkowie określonej, wybranej przez Ciebie grupy usługi Azure AD. Zaleca się zdefiniowanie grupy użytkowników i korzystanie z tego ustawienia podczas wdrażania tej funkcji w celu weryfikacji koncepcji. W tym miejscu jest obsługiwane zagnieżdżanie grup zabezpieczeń.
      * Upewnij się, że użytkownicy w wybranej grupie posiadają prawidłowe licencje.
   * Kliknij pozycję **Zapisz**
1. Na stronie **Metody uwierzytelniania**
   * Ustaw **liczbę metod wymaganych do zresetowania** do **1**
   * W obszarze **Metody dostępne dla użytkowników** wybierz metody, które będą dozwolone przez organizację. Na potrzeby tego samouczka zaznacz pola wyboru, aby włączyć obsługę **poczty e-mail**, **telefonu komórkowego**, **telefonu biurowego**, **powiadomienia aplikacji mobilnej**i **kodu aplikacji mobilnej**.
   * Kliknij pozycję **Zapisz**
1. Na stronie **Rejestracja**
   * Wybierz wartość **Tak** dla opcji **Czy wymagać od użytkowników rejestrowania się podczas logowania?** .
   * Dla opcji **Liczba dni, zanim użytkownicy zostaną poproszeni o ponowne potwierdzenie swoich informacji uwierzytelniania** ustaw wartość **180**.
   * Kliknij pozycję **Zapisz**
1. Na stronie **Powiadomienia**
   * Dla opcji **Czy powiadamiać użytkowników o resetowaniu hasła?** ustaw wartość **Tak**.
   * Dla opcji **Czy powiadamiać wszystkich administratorów, gdy inni administratorzy zresetują swoje hasło?** ustaw wartość **Tak**.
1. Na stronie **Dostosowywanie**
   * Firma Microsoft zaleca, aby dla opcji **Dostosuj link do pomocy technicznej** ustawić wartość **Tak** i w polu **Niestandardowy adres e-mail lub adres URL pomocy technicznej** podać adres e-mail lub adres URL strony internetowej, gdzie użytkownicy będą mogli uzyskać dodatkową pomoc od organizacji.
   * W tym samouczku pozostawisz przycisk **Dostosuj pomoc techniczną** dla ustawienia **nie**.

Samoobsługowe resetowanie hasła dla użytkowników chmury w grupie pilotażowej zostało skonfigurowane.

## <a name="test-sspr-as-a-user"></a>Testowanie funkcji SSPR jako użytkownik

Przetestuj samoobsługowe resetowanie hasła za pomocą konta użytkownika testowego bez uprawnień administratora, będącego członkiem grupy pilotażowej. **W przypadku korzystania z konta, które ma przypisane role administratora, metody i numery uwierzytelniania mogą być inne niż wybrane przez firmę Microsoft do zarządzania zasadami administratora.**

1. Otwórz nowe okno przeglądarki w trybie InPrivate lub incognito.
1. Korzystając z testowego konta użytkownika, zarejestruj się na potrzeby samoobsługowego resetowania hasła, używając portalu rejestracji pod adresem [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
1. Przy użyciu tego samego testowego konta użytkownika przejdź do portalu samoobsługowego resetowania hasła [https://aka.ms/sspr](https://aka.ms/sspr) i spróbuj zresetować swoje hasło, korzystając z informacji podanych w poprzednim kroku.
1. Próba zresetowania hasła powinna zakończyć się pomyślnie.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zdecydujesz, że nie chcesz już korzystać z funkcji skonfigurowanych w ramach tego samouczka, wprowadź następujące zmiany.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
1. Przejdź do usługi **Azure Active Directory** i wybierz pozycję **Resetowanie hasła**.
1. Na stronie **Właściwości** w ramach opcji **Funkcja samoobsługowego resetowania hasła jest włączona** wybierz pozycję **Brak**.
1. Kliknij pozycję **Zapisz**

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono samoobsługowe resetowanie hasła usługi Azure AD. Przejdź do następnego samouczka, aby zobaczyć, jak można zintegrować lokalną infrastrukturę usług Active Directory Domain Services ze środowiskiem samoobsługowego resetowania hasła.

> [!div class="nextstepaction"]
> [Włączanie integracji zapisu zwrotnego samoobsługowego resetowania hasła ze środowiskiem lokalnym](tutorial-enable-writeback.md)
