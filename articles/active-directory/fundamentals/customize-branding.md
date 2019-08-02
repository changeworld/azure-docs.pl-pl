---
title: Dodawać oznaczenia marki do Twojej organizacji strony logowania — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Instrukcje dotyczące sposobu dodawania organizacji znakowania na stronie logowania w usłudze Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: ajburnle
ms.reviewer: kexia
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ae329bd031a2a57ea4fc2eec9cde1e532439bb4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561712"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Dodawanie znakowania na stronie logowania w usłudze Azure Active Directory w organizacji
Zapewnienie spójnego wyglądu i działania na stronach logowania w usłudze Azure Active Directory (Azure AD), należy użyć logo organizacji i niestandardowych schematów kolorów. Strony logowania są wyświetlane, gdy użytkownicy logują się do Twojej organizacji opartej na sieci web aplikacji, takich jak Office 365, która używa usługi Azure AD jako dostawcy tożsamości.

>[!Note]
>Dodając niestandardowe znakowanie wymaga użycia usługi Azure Active Directory Premium 1, 2 — wersja Premium lub wersji podstawowa lub mieć licencję usługi Office 365. Aby uzyskać więcej informacji o licencjonowaniu i wersji, zobacz [Załóż konto usługi Azure AD Premium](active-directory-get-started-premium.md).<br><br>Klienci w Chinach mogą używać wersji Premium i Podstawowa usługi Azure AD za pośrednictwem wystąpienia usługi Azure Active Directory dostępnego na całym świecie. Wersje Premium i Podstawowa usługi Azure AD nie są obecnie obsługiwane w usłudze platformy Azure świadczonej przez firmę 21Vianet w Chinach. Aby uzyskać więcej informacji, porozmawiaj z nami, korzystając z [forum usługi Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customize-your-azure-ad-sign-in-page"></a>Dostosuj swoją stronę logowania w usłudze Azure AD
Można dostosować swoje usługi Azure AD logowania stron, które są wyświetlane, gdy użytkownicy logują się do aplikacji specyficznym dla dzierżawy organizacji, takich jak [ *https://outlook.com/contoso.com* ](https://outlook.com/contoso.com), lub przy przekazywaniu zmiennej domeny, takich jak [ *https://passwordreset.microsoftonline.com/?whr=contoso.com* ](https://passwordreset.microsoftonline.com/?whr=contoso.com).

Niestandardowa znakowanie nie zostanie natychmiast wyświetlone, gdy użytkownicy przejdą do witryn,\.takich jak Office.com www. Zamiast tego użytkownik będzie musiał zalogować przed wyświetleniem dostosowanego znakowania.

> [!NOTE]
> Wszystkie elementy znakowania są opcjonalne. Na przykład jeśli określisz Baner logo z Brak obrazu tła strony logowania Pokaż logo przy użyciu domyślnego obrazu tła, z lokacji docelowej (na przykład Office 365).<br><br>Ponadto znakowanie strony logowania nie jest przenoszone do osobistych kont Microsoft. Jeśli użytkowników lub Goście biznesowi logują się przy użyciu osobistego konta Microsoft, strony logowania nie będzie zawierać znakowania organizacji.

### <a name="to-customize-your-branding"></a>Aby dostosować znakowanie
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **usługi Azure Active Directory**, a następnie wybierz pozycję **marką firmy**, a następnie wybierz pozycję **Konfiguruj**.

    ![Contoso — strona znakowania firmy, wyróżnioną opcją Konfiguruj](media/customize-branding/company-branding-configure-button.png)

3. Na **znakowania firmowego Konfiguruj** podaj dowolne lub wszystkie z poniższych informacji.

    >[!Important]
    >Wszystkie obrazy niestandardowe dodane na tej stronie mają rozmiar obrazu (w pikselach) i potencjalnie plików rozmiar (KB), ograniczenia. Ze względu na ograniczenia te najbardziej prawdopodobną należy utworzyć obrazy o rozmiarze zerowym za pomocą edytora zdjęć.

    - **Ustawienia ogólne**

        ![Konfiguruj znakowanie strony, przy użyciu ustawień ogólnych, ukończono firmowe](media/customize-branding/configure-company-branding-general-settings.png)

        - **Język.** Język zostanie automatycznie ustawione jako domyślne i nie można jej zmienić.
        
        - **Obraz tła strony logowania.** Wybierz plik obrazu PNG lub jpg, aby pojawiało się jako tła stron logowania. 
        
            Obraz nie może być większa niż 1920 x 1080 pikseli, a musi mieć rozmiar pliku wynosi mniej niż 300 KB.

        - **Logo baneru.** Wybierz wersję .png lub .jpg logo była wyświetlana na stronie logowania, po użytkownik wprowadza nazwę użytkownika i na **Moje aplikacje** strony portalu.
            
            Rozmiar obrazu nie może być większy niż 60 pikseli lub większy niż 280 pikseli. Zalecamy używanie przezroczystego obrazu, ponieważ w tle mogą być niezgodne z tła logo. Zalecamy również rezygnację z dodawania wypełnienia wokół obrazu lub może być Szukaj małe logo.

        - **Wskazówka dotycząca nazwy użytkownika.** Wpisz tekst wskazówki, która jest wyświetlana użytkownikom, gdy zapomną swoją nazwę użytkownika. Ten tekst musi być Unicode, bez łącza lub kodu i nie może przekraczać 64 znaków. Goście zalogować się do aplikacji, zaleca się nie zostaną dodane niej tę wskazówkę.

        - **Tekst strony logowania.** Wpisz tekst, który pojawia się w dolnej części strony logowania. Ten tekst można użyć do komunikowania się dodatkowe informacje, takie jak numer telefonu do pomocy technicznej lub klauzulę prawną. Ten tekst musi być Unicode i nie może przekraczać 256 znaków. Zalecamy również, nie wliczając linków lub tagów HTML.

    - **Ustawienia zaawansowane**
            
        ![Konfiguruj znakowanie strony za pomocą ustawień zaawansowanych, ukończono firmowe](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **Kolor tła strony logowania.** Określanie koloru w formacie szesnastkowym (na przykład białe jest #FFFFFF) wyświetlanego zamiast obrazu tła w sytuacjach połączenia o niskiej przepustowości. Zalecamy używanie podstawowego koloru baneru logo lub koloru organizacji.

        - **Obraz kwadratowego logo.** Wybierz PNG (preferowany) lub jpg obraz logo organizacji, aby były widoczne dla użytkowników podczas procesu instalacji dla nowych urządzeń systemu Windows 10 Enterprise. Ten obraz jest używana tylko na potrzeby uwierzytelniania Windows i pojawia się tylko w przypadku dzierżaw korzystających z [rozwiązania Windows Autopilot]( https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) wdrażania lub podczas wprowadzania hasła napotyka stron w innym systemie Windows 10. W niektórych przypadkach może również pojawić się w oknie dialogowym zgody.
        
            Obraz nie może być większa niż 240 x 240 pikseli i musi mieć plik o rozmiarze poniżej 10 KB. Zalecamy używanie przezroczystego obrazu, ponieważ w tle mogą być niezgodne z tła logo. Zalecamy również rezygnację z dodawania wypełnienia wokół obrazu lub może być Szukaj małe logo.
    
        - **Obraz kwadratowego logo, motyw ciemny.** Taka sama jak powyższy obraz kwadratowego logo. Ten obraz logo odbywa się obraz kwadratowego logo, gdy jest używana na ciemnym tle, takie jak z ekranami przyłączone do systemu Windows 10 w usłudze Azure AD podczas początkowego out-of-box (OOBE).  Jeśli logo wygląda dobrze na biały, ciemny niebieski i czarnego tła, nie trzeba dodać ten obraz. 
        
        - **Wyświetl opcję umożliwiającą pozostanie zalogowanym.** Można wybrać umożliwić użytkownikom pozostania zalogowanym do usługi Azure AD, aż jawnie wylogowywania. Jeśli wybierzesz **nie**, ta opcja jest ukryta, a użytkownicy muszą logować się za każdym razem, zamknięcia i ponownego otworzenia przeglądarki.
        
            >[!Note]
            >Niektóre funkcje usługi SharePoint Online oraz pakietu Office 2010 zależą od tego, czy użytkownicy mogą wybrać opcję pozostania zalogowanym. Jeśli ta opcja zostanie ustawiona na **Nie**, użytkownicy mogą otrzymywać dodatkowe i nieoczekiwane monity o zalogowanie.
   

3. Po zakończeniu, dodając znak marki, wybierz **Zapisz**.

    Jeśli ten proces tworzy pierwszej niestandardowej konfiguracji znakowania, staje się domyślnie dla Twojej dzierżawy. Jeśli masz dodatkowe konfiguracje, będzie można wybrać konfigurację domyślną.
    
    >[!Important]
    >Aby dodać więcej firmowych, znakowania konfiguracje dla Twojej dzierżawy należy wybrać **nowy język** na **Contoso - znakowania firmowego** strony. Spowoduje to otwarcie **znakowania firmowego Konfiguruj** strony, a następnie wykonaj te same czynności opisanych powyżej.

## <a name="update-your-custom-branding"></a>Zaktualizuj niestandardowe znakowanie
Po utworzeniu niestandardowe znakowanie może Przejdź wstecz i wszystkich danych, które chcesz zmienić.

### <a name="to-edit-your-custom-branding"></a>Aby edytować niestandardowe znakowanie
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **usługi Azure Active Directory**, a następnie wybierz pozycję **marką firmy**, a następnie wybierz pozycję **Konfiguruj**.

    ![Contoso — strony znakowania firmy, za pomocą konfiguracji domyślnej przedstawiono](media/customize-branding/company-branding-default-config.png)

3. Na **znakowania firmowego Konfiguruj** strony, Dodaj, usuń lub zmień informacje, w oparciu o opisów znajdujących się w [Dostosuj swoją stronę logowania w usłudze Azure AD](#customize-your-azure-ad-sign-in-page) dalszej części tego artykułu.

4. Wybierz pozycję **Zapisz**.

   Uwzględnienie zmian znakowania na stronie logowania może potrwać do godziny.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Dodawanie specyficznego dla języka znakowania firmowego do katalogu
Nie można zmienić język oryginalną konfigurację z językiem domyślnym. Jednak jeśli potrzebujesz konfiguracji w innym języku, można utworzyć nowej konfiguracji.

### <a name="to-add-a-language-specific-branding-configuration"></a>Aby dodać konfigurację znakowania specyficzny dla języka

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **usługi Azure Active Directory**, a następnie wybierz pozycję **marką firmy**, a następnie wybierz pozycję **nowy język**.

    ![Contoso — strony znakowania firmy, z podświetloną opcją nowy język](media/customize-branding/company-branding-new-language.png)

3. Na **znakowania firmowego Konfiguruj** stronie, wybierz swój język (na przykład francuski), a następnie dodaj dane przetłumaczone na opisów znajdujących się w podstawie [Dostosuj swoją stronę logowania w usłudze Azure AD](#customize-your-azure-ad-sign-in-page) w dalszej części tego artykułu.

4. Wybierz pozycję **Zapisz**.

    **Contoso — logo firmy** aktualizacji, aby pokazać nowej konfiguracji francuska stron.

    ![Contoso — strony znakowania firmy, za pomocą konfiguracji domyślnej przedstawiono](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Dodaj niestandardowe znakowanie do stron
Dodaj niestandardowe znakowanie do stron, modyfikując na końcu adresu URL z tekstem, `?whr=yourdomainname`. Ta modyfikacja działa na wielu stronach, w tym strony konfiguracji usługi Multi-Factor Authentication (MFA), strony konfiguracji samoobsługowego resetowania haseł (SSPR) i znak na stronie.

**Przykłady:**

**Oryginalny adres URL:** https://aka.ms/MFASetup<br>
**Niestandardowy adres URL:** https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com

**Oryginalny adres URL:** https://aka.ms/SSPR<br>
**Niestandardowy adres URL:** https://passwordreset.microsoftonline.com/?whr=contoso.com

 
