---
title: Włączanie samoobsługowego resetowania hasła usługi Azure Active Directory
description: W tym samouczku dowiesz się, jak włączyć samoobsługowe resetowanie hasła usługi Azure Active Directory dla grupy użytkowników i przetestować proces resetowania hasła.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71b9052f364dfbae205dd324ba69de9578ccc225
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77027678"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Samouczek: Umożliwianie użytkownikom odblokowywania konta lub resetowania haseł przy użyciu samoobsługowego resetowania hasła usługi Azure Active Directory

Samoobsługowe resetowanie haseł usługi Azure Active Directory (Azure AD) umożliwia użytkownikom zmianę lub zresetowanie hasła bez udziału administratora lub pomocy technicznej. Jeśli konto użytkownika jest zablokowane lub zapomni hasła, może wykonać monity, aby odblokować się i wrócić do pracy. Ta funkcja zmniejsza liczbę połączeń pomocy technicznej i utratę produktywności, gdy użytkownik nie może zalogować się do urządzenia lub aplikacji.

> [!IMPORTANT]
> Ten przewodnik Szybki start pokazuje administratorowi, jak włączyć samoobsługowe resetowanie hasła. Jeśli jesteś użytkownikiem końcowym już zarejestrowanym do samodzielnego resetowania hasła i https://aka.ms/ssprmusisz wrócić na swoje konto, przejdź do .
>
> Jeśli zespół IT nie włączył możliwości resetowania własnego hasła, skontaktuj się z działem pomocy technicznej, aby uzyskać dodatkową pomoc.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Włączanie samoobsługowego resetowania hasła dla grupy użytkowników usługi Azure AD
> * Konfigurowanie metod uwierzytelniania i opcji rejestracji
> * Testowanie procesu samowzdnienia jako użytkownika

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebne są następujące zasoby i uprawnienia:

* Działająca dzierżawa usługi Azure AD z włączoną co najmniej próbną wersją licencji.
    * W razie potrzeby [utwórz go za darmo](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Konto z uprawnieniami *administratora globalnego.*
* Użytkownik niebędący administratorem z hasłem, który znasz, na przykład *testuser*. Testowanie środowiska samoużytego użytkownika końcowego przy użyciu tego konta w tym samouczku.
    * Jeśli chcesz utworzyć użytkownika, zobacz [Szybki start: Dodawanie nowych użytkowników do usługi Azure Active Directory](../add-users-azure-active-directory.md).
* Grupa, do których należy użytkownik niebędący administratorem, na przykład *SSPR-Test-Group*. W tym samouczku można włączyć wiele numerów SSPR dla tej grupy.
    * Jeśli chcesz utworzyć grupę, zobacz, jak [utworzyć grupę i dodać członków w usłudze Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Włączanie samoobsługowego resetowania hasła

Usługa Azure AD umożliwia włączenie *None*wiele *Selected*łat. *All* Ta szczegółowa możliwość pozwala wybrać podzbiór użytkowników, aby przetestować proces rejestracji i przepływ pracy SSPR. Gdy użytkownik jest zadowolony z tego procesu i może komunikować się z wymaganiami z szerszym zestawem użytkowników, możesz wybrać dodatkowe grupy użytkowników, aby włączyć dla sspr. Można też włączyć wiele łatW dla wszystkich osób w dzierżawie usługi Azure AD.

W tym samouczku skonfiguruj wiele samoustaw dla zestawu użytkowników w grupie testowej. W poniższym przykładzie używana jest grupa *SSPR-Test-Group.* W razie potrzeby podaj własną grupę usługi Azure AD:

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu konta z uprawnieniami *administratora globalnego.*
1. Wyszukaj i wybierz **pozycję Azure Active Directory**, a następnie wybierz **polecenie Resetowanie hasła** z menu po lewej stronie.
1. Na stronie **Właściwości** w obszarze opcja *Resetowanie hasła samoobsługowego wybierz*pozycję **Wybierz grupę**
1. Wyszukaj i wybierz grupę usługi Azure AD, na przykład *SSPR-Test-Group,* a następnie wybierz pozycję *Wybierz*.

    [![](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png "Select a group in the Azure portal to enable for self-service password reset")](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

    W ramach szerszego wdrożenia samowzdronienia SSPR obsługiwane są grupy zagnieżdżone. Upewnij się, że użytkownicy w ybranych grupach mają przypisane odpowiednie licencje. Obecnie nie ma procesu sprawdzania poprawności tych wymagań licencyjnych.

1. Aby włączyć wiele łatW dla wybranych użytkowników, wybierz pozycję **Zapisz**.

## <a name="select-authentication-methods-and-registration-options"></a>Wybieranie metod uwierzytelniania i opcji rejestracji

Gdy użytkownicy muszą odblokować swoje konto lub zresetować hasło, zostanie wyświetlony monit o podanie dodatkowej metody potwierdzenia. Ten dodatkowy czynnik uwierzytelniania zapewnia, że tylko zatwierdzone zdarzenia SSPR są zakończone. Można wybrać metody uwierzytelniania, które mają być zezwalane, na podstawie informacji rejestracyjnych, które użytkownik udostępnia.

1. Na stronie **Metody uwierzytelniania** z menu po lewej stronie ustaw **liczbę metod wymaganych do zresetowania** do *1*.

    Aby zwiększyć bezpieczeństwo, można zwiększyć liczbę metod uwierzytelniania wymaganych dla sspr.

1. Wybierz **metody dostępne dla użytkowników,** na które organizacja chce zezwolić. W tym samouczku zaznacz pola, aby włączyć następujące metody:

    * *Powiadomienie aplikacji mobilnej*
    * *Kod aplikacji mobilnej*
    * *Adres e-mail*
    * *Telefon komórkowy*
    * *Telefon biurowy*

1. Aby zastosować metody uwierzytelniania, wybierz pozycję **Zapisz**.

Aby użytkownicy mogli odblokować swoje konto lub zresetować hasło, muszą zarejestrować swoje dane kontaktowe. Te informacje kontaktowe są używane dla różnych metod uwierzytelniania skonfigurowanych w poprzednich krokach.

Administrator może ręcznie podać te informacje kontaktowe lub użytkownicy mogą przejść do portalu rejestracji, aby sami podać informacje. W tym samouczku skonfiguruj użytkowników, którzy mają być monitowani o rejestrację podczas następnego logowania.

1. Na stronie **Rejestracja** z menu po lewej stronie wybierz pozycję *Tak* dla **Wymagaj od użytkowników rejestracji podczas logowania**.
1. Ważne jest, aby informacje kontaktowe były aktualizowane. Jeśli informacje kontaktowe są nieaktualne po uruchomieniu zdarzenia SSPR, użytkownik może nie być w stanie odblokować swojego konta lub zresetować hasła.

    Dla opcji **Liczba dni, zanim użytkownicy zostaną poproszeni o ponowne potwierdzenie swoich informacji uwierzytelniania** ustaw wartość *180*.
1. Aby zastosować ustawienia rejestracji, wybierz pozycję **Zapisz**.

## <a name="configure-notifications-and-customizations"></a>Konfigurowanie powiadomień i dostosowań

Aby informować użytkowników o aktywności konta, można skonfigurować powiadomienia e-mail, które mają być wysyłane po zdarzeniu samoosiąż. Powiadomienia te mogą obejmować zarówno zwykłe konta użytkowników, jak i konta administratorów. W przypadku kont administratorów to powiadomienie zapewnia dodatkową warstwę świadomości, gdy hasło uprzywilejowanego konta administratora jest resetowane przy użyciu funkcji SSPR.

1. Na stronie **Powiadomienia** z menu po lewej stronie skonfiguruj następujące opcje:

   * Dla opcji **Czy powiadamiać użytkowników o resetowaniu hasła?** ustaw wartość *Tak*.
   * Dla opcji **Czy powiadamiać wszystkich administratorów, gdy inni administratorzy zresetują swoje hasło?** ustaw wartość *Tak*.

1. Aby zastosować preferencje powiadomień, wybierz pozycję **Zapisz**.

Jeśli użytkownicy potrzebują dodatkowej pomocy w procesie SSPR, możesz dostosować łącze do "Skontaktuj się z administratorem". Ten link jest używany w procesie rejestracji SSPR i gdy użytkownik odblokowuje swoje konto lub resetuje swoje hasło. Aby upewnić się, że użytkownicy otrzymają potrzebną pomoc techniczną, zdecydowanie zaleca się podanie niestandardowej poczty e-mail lub adresu URL działu pomocy technicznej.

1. Na stronie **Dostosowywanie** z menu po lewej stronie ustaw *łącze Dostosuj punkt pomocy* technicznej na **Tak**.
1. W polu **Niestandardowy adres e-mail lub adres URL pomocy** technicznej podaj adres e-mail lub adres URL strony sieci Web, w którym użytkownicy mogą uzyskać dodatkową pomoc od organizacji, na przykład*https://support.contoso.com/*
1. Aby zastosować łącze niestandardowe, wybierz pozycję **Zapisz**.

## <a name="test-self-service-password-reset"></a>Testowanie funkcji samoobsługowego resetowania haseł

Po włączeniu i skonfigurowaniu funkcji SSPR przetestuj proces samookreślenia z użytkownikiem, który jest częścią grupy wybranej w poprzedniej sekcji, takiej jak *Test-SSPR-Group*. W poniższym przykładzie używane jest konto *testser.* Podaj własne konto użytkownika, które jest częścią grupy włączonej dla samowzmuniaka SSPR w pierwszej sekcji tego samouczka.

> [!NOTE]
> Podczas testowania samoobsługowego resetowania hasła należy użyć konta niebędącego administratorem. Administratorzy są zawsze włączeni do samodzielnego resetowania hasła i muszą użyć dwóch metod uwierzytelniania, aby zresetować swoje hasło.

1. Aby wyświetlić proces rejestracji ręcznej, otwórz nowe okno przeglądarki w trybie InPrivate lub incognito i przejdź do [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)pliku . Użytkownicy powinni być kierowani do tego portalu rejestracji podczas następnego logowania.
1. Zaloguj się za pomocą użytkownika testowego niebędącego administratorem, takiego jak *testuser,* i zarejestruj informacje kontaktowe metod uwierzytelniania.
1. Po zakończeniu wybierz przycisk oznaczony jako **Wygląda dobrze** i zamknij okno przeglądarki.
1. Otwórz nowe okno przeglądarki w trybie InPrivate lub incognito i przejdź do [https://aka.ms/sspr](https://aka.ms/sspr).
1. Wprowadź informacje o koncie użytkowników testowych niebędących *administratorami,* takie jak testser , znaki z captcha, a następnie wybierz **przycisk Dalej**.

    ![Wprowadź informacje o koncie użytkownika, aby zresetować hasło](media/tutorial-enable-sspr/password-reset-page.png)

1. Postępuj zgodnie z instrukcjami weryfikacji, aby zresetować hasło. Po zakończeniu powinieneś otrzymać powiadomienie e-mail o zresetowaniu hasła.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W poniższym samouczku z tej serii można skonfigurować storcą storbanie hasła. Ta funkcja zapisuje zmiany hasła z samowzd zadumy usługi Azure AD z powrotem do lokalnego środowiska usługi AD. Jeśli chcesz kontynuować tę serię samouczków, aby skonfigurować zapisywanie haseł, nie wyłączaj teraz samookreślenia SSPR.

Jeśli nie chcesz już używać funkcji równoustrujów SSPR skonfigurowanych w ramach tego samouczka, ustaw stan równoustrujnika SSPR na **Brak,** wykonując następujące czynności:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wyszukaj i wybierz **pozycję Azure Active Directory**, a następnie wybierz **polecenie Resetowanie hasła** z menu po lewej stronie.
1. Na stronie **Właściwości** w obszarze opcja *Resetowanie hasła samoobsługowego wybierz*pozycję **Brak**.
1. Aby zastosować zmianę sspr, wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono samoobsługowe resetowanie hasła usługi Azure AD dla wybranej grupy użytkowników. W tym samouczku omówiono:

> [!div class="checklist"]
> * Włączanie samoobsługowego resetowania hasła dla grupy użytkowników usługi Azure AD
> * Konfigurowanie metod uwierzytelniania i opcji rejestracji
> * Testowanie procesu samowzdnienia jako użytkownika

> [!div class="nextstepaction"]
> [Włączanie usługi Azure Multi-Factor Authentication](tutorial-mfa-applications.md)
