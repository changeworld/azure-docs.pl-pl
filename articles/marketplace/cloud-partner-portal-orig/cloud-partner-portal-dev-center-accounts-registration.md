---
title: Tworzenie konta microsoft developer | Azure Marketplace
description: Wymagania i kroki dotyczące tworzenia konta microsoft developer.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 161abde1ef0dfd86842fb56afe699fa632e0d9ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280375"
---
<a name="create-a-microsoft-developer-account"></a>Tworzenie konta programu Microsoft Developer
====================================

W tym artykule opisano sposób stan publikowania zatwierdzonego programu Microsoft Developer dla portalu Azure Marketplace.

## <a name="create-a-microsoft-account"></a>Tworzenie konta Microsoft

Aby rozpocząć proces publikowania, musisz ukończyć rejestrację **Centrum deweloperów firmy Microsoft.** Użyjesz tego samego zarejestrowanego konta w **[portalu Cloud Partner Portal,](https://cloudpartner.azure.com/)** aby rozpocząć proces publikowania.

### <a name="general-account-guidelines"></a>Ogólne wytyczne dotyczące konta

Firma Microsoft zaleca, aby mieć tylko jedno konto Microsoft dla oferty portalu Azure Marketplace. To konto nie powinno być specyficzne dla usług ani ofert.

Adres, który tworzy nazwę użytkownika, powinien znajdować się w domenie i być kontrolowany przez zespół IT. Wszystkie działania związane z publikowaniem powinny być wykonywane za pośrednictwem tego konta.

>[!WARNING]
>Słowa takie jak "Azure" i "Microsoft" nie są obsługiwane podczas rejestracji konta Microsoft. Unikaj używania tych słów do ukończenia procesu tworzenia i rejestracji konta.

### <a name="company-account-guidelines"></a>Wytyczne dotyczące konta firmowego

Postępuj zgodnie z tymi wskazówkami, jeśli więcej niż jedna osoba będzie musiała uzyskać dostęp do konta, logując się za pomocą konta Microsoft, które otworzyło konto.

>[!IMPORTANT]
>Aby umożliwić wielu użytkownikom dostęp do konta Centrum deweloperów, zalecamy przypisywanie ról poszczególnym użytkownikom za pomocą usługi Azure Active Directory. Mogą uzyskać dostęp do konta, logując się przy użyciu swoich indywidualnych poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Zarządzanie użytkownikami kont](https://docs.microsoft.com/windows/uwp/publish/manage-account-users).

-   Utwórz konto Microsoft przy użyciu adresu e-mail należącego do domeny firmy,\'ale nie do jednej osoby. Na przykład windowsapps\@fabrikam.com.
-   Ogranicz dostęp do tego konta Microsoft do jak najmniejszej liczby deweloperów.
-   Skonfiguruj firmową listę dystrybucyjną poczty e-mail, która obejmuje wszystkich użytkowników, którzy muszą uzyskać dostęp do konta dewelopera, i dodaj ten adres e-mail do informacji zabezpieczających. Dzięki temu wszyscy pracownicy na liście mogą otrzymywać kody zabezpieczeń w razie potrzeby i zarządzać informacjami zabezpieczającymi konta Microsoft. Jeśli skonfigurowanie listy dystrybucyjnej nie jest możliwe, właściciel indywidualnego konta e-mail musi być dostępny, aby uzyskać dostęp do kodu zabezpieczającego i udostępnić go po wyświetleniu monitu (na przykład gdy nowe informacje zabezpieczające zostaną dodane do konta lub gdy musi być uzyskiwany z nowego urządzenia).
-   Dodaj firmowy numer telefonu, który nie wymaga rozszerzenia i jest dostępny dla kluczowych członków zespołu.
-   Ogólnie rzecz biorąc, deweloperzy używają zaufanych urządzeń do logowania się na konto dewelopera firmy. Wszyscy kluczowi członkowie zespołu powinni mieć dostęp do tych zaufanych urządzeń. Zmniejszy to potrzebę wysyłania kodów zabezpieczających podczas uzyskiwania dostępu do konta.
-   Jeśli chcesz zezwolić na dostęp do konta z niezaufanego komputera, ogranicz ten dostęp do maksymalnie pięciu deweloperów. W idealnym przypadku deweloperzy ci powinni uzyskać dostęp do konta z komputerów, które mają tę samą lokalizację geograficzną i sieciową.
-   Często sprawdzaj [informacje zabezpieczające firmy,](https://account.live.com/proofs/Manage) aby upewnić się, że są aktualne.

>[!IMPORTANT]
>Dostęp do konta dewelopera powinien być dostępny głównie z zaufanych komputerów. Ma to kluczowe znaczenie, ponieważ istnieje limit liczby kodów generowanych na konto w tygodniu. Umożliwia również najbardziej bezproblemowe środowisko logowania.
>
>Aby uzyskać więcej informacji, zobacz [dodatkowe wskazówki dotyczące kont deweloperów i zabezpieczeń](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="to-create-a-microsoft-account"></a>Aby utworzyć konto Microsoft

1.  Otwórz nową sesję przeglądania Chrome Incognito lub Internet Explorer InPrivate, aby upewnić się, że nie zalogowano się na istniejące konto.
2.  Zarejestruj wiadomość e-mail (przy użyciu poprzednich wytycznych) jako konto Microsoft, korzystając z tego [łącza](https://signup.live.com/signup.aspx). Wykonaj następujące instrukcje dotyczące rejestracji:

    - Rejestrując konto jako konto Microsoft, musisz podać prawidłowy numer telefonu, aby system wysłał Ci kod weryfikacyjny konta jako wiadomość tekstową lub połączenie automatyczne.
    - Rejestrując konto jako konto Microsoft, musisz podać prawidłowy identyfikator wiadomości e-mail do odbierania automatycznej wiadomości e-mail w celu weryfikacji konta.
    - Sprawdź adres e-mail wysłany do biblioteki dl.

    Teraz możesz korzystać z nowego konta Microsoft w Centrum deweloperów firmy Microsoft.

## <a name="register-your-account-in-microsoft-developer-center"></a>Zarejestruj swoje konto w Centrum deweloperów firmy Microsoft

Centrum deweloperów firmy Microsoft służy do jednorazowego rejestrowania informacji o firmie. Rejestrujący musi być ważnym przedstawicielem przedsiębiorstwa i musi podać swoje dane osobowe w celu potwierdzenia swojej tożsamości. Osoba rejestrująca się musi używać konta Microsoft udostępnionego dla firmy, **a to samo konto musi być używane w portalu cloud partnerów.** Przed podjęciem próby jego utworzenia należy sprawdzić, czy firma nie ma jeszcze konta Centrum deweloperów firmy Microsoft. Podczas procesu będziemy zbierać informacje o adresie firmy, informacje o koncie bankowym i informacje podatkowe. Zazwyczaj można je uzyskać od finansów lub kontaktów biznesowych.

>[!IMPORTANT]
>Należy wykonać następujące składniki profilu dewelopera, aby przejść przez różne fazy tworzenia i wdrażania oferty.

| Profil dewelopera     | Aby rozpocząć pracę roboczą    | Przygotowanie       | Publikowanie szablonu bezpłatnego i rozwiązania   | Publikowanie reklam   |
|---------------------- |----------------   |-----------    |-------------------------------------  |---------------------  |
| Rejestracja firmy  | Musi mieć         | Musi mieć     | Musi mieć                             | Musi mieć             |
| Identyfikator profilu podatkowego        | Optional (Opcjonalność)          | Optional (Opcjonalność)      | Optional (Opcjonalność)                              | Musi mieć             |
| Konto bankowe          | Optional (Opcjonalność)          | Optional (Opcjonalność)      | Optional (Opcjonalność)                              | Musi mieć             |

>[!NOTE]
>Bring Your Own License (BYOL) jest obsługiwany tylko dla maszyn wirtualnych i jest uważany za bezpłatną ofertę.

### <a name="register-your-company-account"></a>Zarejestruj swoje konto firmowe

1. Otwórz nową sesję przeglądania Internet Explorer InPrivate lub Chrome Incognito, aby upewnić się, że nie zalogowano się na konto osobiste.

2. Przejdź do [Centrum deweloperów systemu Windows,](https://dev.windows.com/registration?accountprogram=azure) aby zarejestrować się jako sprzedawca. Przed kontynuowaniem prosimy o zapoznanie się z poniższą ważną uwagą.

   ![Weryfikacja konta Microsoft](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-verify.jpg)

    >[!IMPORTANT]
    >Upewnij się, że identyfikator wiadomości e-mail lub lista dystrybucyjna (zalecana jest lista dystrybucyjna w celu usunięcia zależności od osób fizycznych), której będziesz używać do rejestracji w Centrum deweloperów, jest początkowo zarejestrowana jako konto Microsoft. Jeśli nie, zarejestruj się za pomocą tego linku. Ponadto nie można używać identyfikatora wiadomości e-mail w domenie firmy Microsoft do rejestracji w Centrum deweloperów."

   ![Zaloguj się centrum deweloperów](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-login.jpg)

3. Uruchom kreatora "Pomóż nam chronić Twoje konto", aby zweryfikować twoją tożsamość za pomocą numeru telefonu lub adresu e-mail.

4. W obszarze Informacje o koncie rejestracyjnym wybierz **kraj/region konta** z listy rozwijanej, a następnie wybierz pozycję **Dalej**.

   ![Wybierz kraj/region](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_04.png)

    >[!WARNING]
    >Kraje/regiony "sprzedaj z": Aby sprzedawać swoje usługi w portalu Azure Marketplace, zarejestrowana jednostka musi pochodzić z jednego z zatwierdzonych krajów/regionów sprzedaży wyświetlanych na liście rozwijanej. Ograniczenie to dotyczy wypłat i podatków. Aby uzyskać więcej informacji, zobacz zasady uczestnictwa w portalu Marketplace.

5. Wybierz **firmę** jako "Typ konta", a następnie wybierz **pozycję Dalej**.

    >[!IMPORTANT]
    >Aby lepiej zrozumieć typy kont i zdecydować, który typ jest dla Ciebie najlepszy, wyświetl typy kont, lokalizacje i opłaty na stronie wyświetlane w następnym przechwytywaniu ekranu.

    ![Typy kont dla sprzedających](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_05.png)

6. Wprowadź **nazwę wyświetlaną programu Publisher**. Zazwyczaj jest to nazwa twojej firmy.

    >[!NOTE]
    >Nazwa wyświetlana wydawcy wprowadzona w Centrum deweloperów nie jest wyświetlana w portalu Azure Marketplace po wyświetlenie oferty. Ale te informacje są potrzebne do zakończenia procesu rejestracji.

7. Wprowadź **informacje kontaktowe dotyczące** weryfikacji konta.

    >[!IMPORTANT]
    >Musisz podać dokładne dane kontaktowe, ponieważ będą wykorzystywane w naszym procesie weryfikacji, aby Twoja firma została zatwierdzona w Centrum deweloperów."

8. Wprowadź informacje kontaktowe **osoby zatwierdzającej firmę**. Osoba zatwierdzająca firmę to osoba, która może zweryfikować, czy użytkownik jest upoważniony do utworzenia konta w Centrum deweloperów w imieniu organizacji. Po podasz te informacje wybierz **pozycję Dalej,** aby przejść do **sekcji Płatność**.

    ![Identyfikowanie osoby zatwierdzającej firmę](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_08.png)

9. Wprowadź informacje o płatności dla swojego konta. Jeśli masz kod promocyjny, który pokrywa koszt rejestracji, możesz wprowadzić go tutaj. W przeciwnym razie podaj dane karty kredytowej (lub paypal na obsługiwanych rynkach). Wybierz **przycisk Dalej,** aby przejść do ostatecznego **przeglądu**.

   ![Rejestracja płatności](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_09.png)

10. Przejrzyj informacje o koncie i potwierdź, że wszystko jest w porządku. Przeczytaj i zaakceptuj warunki [umowy wydawcy portalu Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/?LinkID=699560). Zaznacz to pole, aby wskazać, że przeczytałeś i zaakceptowałeś te warunki.

11. Wybierz **Zakończ,** aby potwierdzić rejestrację. Na Twój adres e-mail zostanie wysłana wiadomość z potwierdzeniem.

12. Jeśli planujesz publikować tylko bezpłatne oferty, wybierz [pozycję Przejdź do portalu cloud partner](https://cloudpartner.azure.com/) i przejdź do "Zarejestruj swoje konto w portalu partnerów w chmurze" w tym artykule.

### <a name="commercial-offers"></a>Oferty handlowe

Jeśli planujesz publikować oferty handlowe, takie jak oferta maszyny wirtualnej przy użyciu modelu rozliczeń godzinowych, musisz podać informacje podatkowe i bankowe. W tym celu zaloguj się na konto Centrum deweloperów i wybierz pozycję **Zaktualizuj informacje o koncie**. Postępuj zgodnie z instrukcjami w następnej sekcji "Dodaj informacje bankowe i podatkowe".

>[!IMPORTANT]
>Nie będzie można wypchnąć oferty komercyjnej do produkcji bez podania informacji o koncie bankowym i podatku.

Jeśli wolisz zaktualizować informacje bankowe i podatkowe później, możesz przejść do "Zarejestruj swoje konto w portalu partnerów w chmurze" w tym artykule.

>[!NOTE]
>Zalecamy jak najszybsze podanie informacji o kontach bankowych i podatkach, ponieważ weryfikacja informacji podatkowych wymaga czasu.

### <a name="add-banking-and-tax-information"></a>Dodawanie informacji bankowych i podatkowych

Aby opublikować oferty handlowe do zakupu, należy dodać informacje o wypłatach i podatkach oraz przesłać je do weryfikacji w Centrum deweloperów.

**Aby dostarczyć informacji bankowych**

1.  Zaloguj się do [Centrum deweloperów firmy Microsoft](https://dev.windows.com/registration?accountprogram=azure) za pomocą konta Microsoft.
2.  Wybierz **konto wypłat** w menu po lewej stronie, w obszarze Wybierz metodę **płatności**wybierz pozycję Konto **bankowe** lub **PayPal**.

    >[!NOTE]
    >Jeśli masz oferty handlowe, które klienci kupują w Marketplace, jest to konto, na którym otrzymasz wypłatę za te zakupy.
3.  Wprowadź informacje o płatności, a następnie wybierz pozycję **Zapisz**.

    >[!IMPORTANT]
    >Jeśli chcesz zaktualizować lub zmienić konto wypłat, wykonaj poprzednie kroki, aby zastąpić bieżące informacje nowymi informacjami.
    >
    >Zmiana konta wypłat może opóźnić płatności nawet o jeden cykl płatności. To opóźnienie występuje, ponieważ musimy zweryfikować zmianę konta, tak jak to zrobiliśmy podczas pierwszego konfigurowania konta wypłat. Po zweryfikowaniu konta nadal otrzymasz zapłatę za pełną kwotę; wszelkie płatności należne za bieżący cykl płatności zostaną dodane do następnego.

4.  Wybierz **pozycję Dalej**.

**Aby uzyskać informacje podatkowe**

1.  Zaloguj się do [Centrum deweloperów firmy Microsoft](https://dev.windows.com/registration?accountprogram=azure) za pomocą konta Microsoft (w razie potrzeby).
2.  W menu po lewej stronie wybierz **pozycję Profil podatkowy**.
3.  Na stronie **Konfigurowanie formularza podatku:**
    - Wybierz kraj lub region, w którym masz stałe miejsce zamieszkania.
    - Wybierz kraj lub region, w którym masz obywatelstwo podstawowe.
    - Wybierz **pozycję Dalej**.
4.  Wprowadź szczegóły podatku, a następnie wybierz przycisk **Dalej**.

>[!WARNING]
>Nie będzie można wypchnąć do ofert komercyjnych do produkcji bez podania informacji o koncie bankowym i podatkach na koncie Microsoft Developer Center.

### <a name="developer-center-registration-issues"></a>Problemy z rejestracją Centrum deweloperów

Jeśli masz problemy z rejestracją Centrum deweloperów, użyj następujących kroków, aby otworzyć bilet pomocy technicznej.

1.  Przejdź do [linku pomocy technicznej](https://developer.microsoft.com/windows/support).
2.  W obszarze **Kontakt z nami**wybierz pozycję **Prześlij zdarzenie**.
    ![Otwieranie biletu](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_02.png)
3.  W przypadku **typu problem**wybierz opcję "Pomoc z Centrum deweloperów", a w **kategorii**wybierz opcję "Publikuj aplikacje i zarządzaj nimi". Wybierz **pozycję Rozpocznij wiadomość e-mail**.

    ![identyfikowanie typu problemu](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_03.png)

4.  Otrzymasz stronę logowania. Zaloguj się za pomocą dowolnego konta Microsoft. Jeśli nie masz konta Microsoft, [utwórz je](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).\

5.  Podaj szczegółowe informacje o problemie i wybierz **opcję Prześlij,** aby wysłać bilet.

    ![prześlij bilet](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_05.png)

## <a name="register-your-account-in-the-cloud-partner-portal"></a>Zarejestruj swoje konto w portalu partnerów w chmurze

Za pomocą [portalu Cloud Partner Portal](https://cloudpartner.azure.com/) można publikować oferty i zarządzać nimi.

1.  Otwórz nową sesję przeglądania Chrome Incognito lub Internet Explorer InPrivate, aby upewnić się, że nie zalogowano się na konto osobiste.
2.  Przejdź do [portalu cloud partner .](https://cloudpartner.azure.com/)
3.  Jeśli jesteś nowym użytkownikiem i logujesz się do [portalu Cloud Partner Portal](https://cloudpartner.azure.com/) po raz pierwszy, musisz zalogować się przy użyciu tego samego identyfikatora e-mail, który jest zarejestrowany na twoim koncie Centrum deweloperów. Dzięki temu twoje konto Centrum deweloperów i konto portalu partnera w chmurze są ze sobą połączone.

Później można dodać innych członków firmy, którzy pracują nad aplikacją. Możesz je jako współautorów lub właścicieli w portalu partnerów w chmurze, wykonując kroki w następnej sekcji.

Jeśli zostaniesz dodany jako współautor/właściciel w portalu portalu partnerów w chmurze, możesz zalogować się przy za pomocą własnego konta.

>[!TIP]
>Zasady uczestnictwa są opisane w witrynie sieci Web platformy Azure.

## <a name="manage-users-as-owners-or-contributors-in-the-cloud-partner-portal"></a>Zarządzanie użytkownikami jako właścicielami lub współautorami w portalu partnerów w chmurze

[Kroki zarządzania użytkownikami w portalu partnerów w chmurze](./cloud-partner-portal-manage-users.md)


## <a name="next-steps"></a>Następne kroki

Teraz, gdy twoje konto jest tworzone i zarejestrowane, możesz rozpocząć proces publikowania w portalu Azure Marketplace.
