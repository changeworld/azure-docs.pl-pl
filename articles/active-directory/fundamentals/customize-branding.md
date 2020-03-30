---
title: Dodawanie znakowania do strony logowania organizacji — Usługa Azure AD
description: Instrukcje dotyczące dodawania znakowania organizacji do strony logowania usługi Azure Active Directory.
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
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 441fdd14cc2c734b6ce532f3ad1d30663b2f56c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049793"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Dodawanie znakowania do strony logowania usługi Azure Active Directory w organizacji
Użyj logo organizacji i niestandardowych schematów kolorów, aby zapewnić spójny wygląd na stronach logowania usługi Azure Active Directory (Azure AD). Strony logowania są wyświetlane, gdy użytkownicy logują się do aplikacji internetowych organizacji, takich jak Office 365, która używa usługi Azure AD jako dostawcy tożsamości.

>[!Note]
>Dodanie niestandardowego znakowania wymaga użycia wersji Azure Active Directory Premium 1, Premium 2 lub Basic lub licencji usługi Office 365. Aby uzyskać więcej informacji na temat licencjonowania i wersji, zobacz [Rejestrowanie się w usłudze Azure AD Premium](active-directory-get-started-premium.md).<br><br>Klienci w Chinach mogą używać wersji Premium i Podstawowa usługi Azure AD za pośrednictwem wystąpienia usługi Azure Active Directory dostępnego na całym świecie. Wersje Premium i Podstawowa usługi Azure AD nie są obecnie obsługiwane w usłudze platformy Azure świadczonej przez firmę 21Vianet w Chinach. Aby uzyskać więcej informacji, porozmawiaj z nami, korzystając z [forum usługi Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customize-your-azure-ad-sign-in-page"></a>Dostosowywanie strony logowania usługi Azure AD
Można dostosować strony logowania usługi Azure AD, które pojawiają się, gdy użytkownicy logują `https://outlook.com/contoso.com`się do aplikacji specyficznych dla `https://passwordreset.microsoftonline.com/?whr=contoso.com`dzierżawy organizacji, takich jak , lub podczas przekazywania zmiennej domeny, takiej jak .

Niestandardowa marka nie pojawi się natychmiast, gdy użytkownicy\.przejdą do witryn, takich jak www office.com. Zamiast tego użytkownik musi się zalogować, zanim pojawi się niestandardowa znakowanie. Po zalogowaniu się użytkownika znakowanie może potrwać 15 minut lub dłużej. 

> [!NOTE]
> Wszystkie elementy marki są opcjonalne. Jeśli na przykład określisz logo baneru bez obrazu tła, na stronie logowania zostanie wyświetlona domyślna strona z obrazem tła z witryny docelowej (na przykład usługa Office 365).<br><br>Ponadto znakowanie strony logowania nie jest przewożona na osobiste konta Microsoft. Jeśli użytkownicy lub goście biznesowi zalogują się przy użyciu osobistego konta Microsoft, strona logowania nie będzie odzwierciedlać marki w organizacji.

### <a name="to-customize-your-branding"></a>Aby dostosować markę
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **pozycję Azure Active Directory**, a następnie wybierz pozycję **Znakowanie firmy**, a następnie wybierz pozycję **Konfiguruj**.

    ![Contoso — strona znakowanie firmy, wyróżniona opcja Konfiguruj](media/customize-branding/company-branding-configure-button.png)

3. Na stronie **Konfigurowanie znakowania firmy** podaj dowolną lub wszystkie z następujących informacji.

    >[!Important]
    >Wszystkie obrazy niestandardowe dodawanye na tej stronie mają ograniczenia rozmiaru obrazu (pikseli) i potencjalnie rozmiaru pliku (KB). Z powodu tych ograniczeń najprawdopodobniej będziesz musiał użyć edytora zdjęć do tworzenia obrazów o odpowiednim rozmiarze.

    - **Ustawienia ogólne**

        ![Konfigurowanie strony znakowania firmy z ustawieniami ogólnymi zakończonymi](media/customize-branding/configure-company-branding-general-settings.png)

        - **Język.** Język jest automatycznie ustawiany jako domyślny i nie można go zmienić.
        
        - **Obraz tła strony logowania.** Wybierz plik obrazu png lub jpg, który ma być wyświetlany jako tło dla stron logowania. 
        
            Obraz nie może być większy niż rozmiar 1920x1080 pikseli i musi mieć rozmiar pliku mniejszy niż 300 KB.

        - **Logo banera.** Wybierz wersję .png lub jpg logo, która ma być wyświetlana na stronie logowania po wprowadzeniu przez użytkownika nazwy użytkownika i na stronie portalu **Moje aplikacje.**
            
            Obraz nie może być wyższy niż 60 pikseli lub szerszy niż 280 pikseli. Zalecamy użycie przezroczystego obrazu, ponieważ tło może nie pasować do tła logo. Nie zalecamy również dodawania dopełnienia wokół obrazu lub może to sprawić, że twoje logo będzie wyglądać na małe.

        - **Podpowiedź o nazwie użytkownika.** Wpisz tekst podpowiedzi, który pojawia się dla użytkowników, jeśli zapomną swoją nazwę użytkownika. Ten tekst musi być Unicode, bez łączy lub kodu i nie może przekraczać 64 znaków. Jeśli goście zalogują się do aplikacji, zalecamy niedawkowanie tej wskazówki.

        - **Tekst strony logowania.** Wpisz tekst wyświetlany u dołu strony logowania. Ten tekst służy do przekazywania dodatkowych informacji, takich jak numer telefonu do działu pomocy technicznej lub oświadczenia prawnego. Ten tekst musi być Unicode i nie przekracza 256 znaków. Sugerujemy również, aby nie uwzględniać linków ani tagów HTML.

    - **Ustawienia zaawansowane**
            
        ![Konfigurowanie strony znakowania firmy z zaawansowanymi ustawieniami zakończonymi](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **Kolor tła strony logowania.** Określ kolor szesnastkowy (na przykład biały jest #FFFFFF), który pojawi się zamiast obrazu tła w sytuacjach połączenia o niskiej przepustowości. Zalecamy użycie podstawowego koloru logo baneru lub koloru organizacji.

        - **Kwadratowy obraz logo.** Wybierz obraz png (preferowany) lub jpg logo organizacji, który będzie wyświetlany użytkownikom podczas procesu instalacji nowych urządzeń z systemem Windows 10 Enterprise. Ten obraz jest używany tylko do uwierzytelniania systemu Windows i pojawia się tylko w dzierżawach korzystających z [programu Windows Autopilot]( https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) do wdrażania lub do stron wprowadzania haseł w innych środowiskach systemu Windows 10. W niektórych przypadkach może również pojawić się w oknie dialogowym zgody.
        
            Obraz nie może mieć rozmiaru większego niż 240x240 pikseli i musi mieć rozmiar pliku mniejszy niż 10 KB. Zalecamy użycie przezroczystego obrazu, ponieważ tło może nie pasować do tła logo. Nie zalecamy również dodawania dopełnienia wokół obrazu lub może to sprawić, że twoje logo będzie wyglądać na małe.
    
        - **Kwadratowy obraz logo, ciemny motyw.** Tak samo jak na obrazku z kwadratowym logo powyżej. Ten obraz logo zajmuje miejsce obrazu kwadratowego logo, gdy jest używany z ciemnym tłem, na przykład z ekranami połączonymi z usługą Azure AD systemu Windows 10 podczas out-of-box experience (OOBE).  Jeśli twoje logo wygląda dobrze na białym, ciemnoniebieskim i czarnym tle, nie musisz dodawać tego obrazu. 
        
        - **Pokaż opcję pozostawania zalogowanym.** Można wybrać, aby użytkownicy pozostawali zalogowane do usługi Azure AD, dopóki jawnie się nie wylogujesz. Jeśli wybierzesz **opcję Nie,** ta opcja jest ukryta, a użytkownicy muszą zalogować się za każdym razem, gdy przeglądarka jest zamknięta i ponownie otwarta.
        
            >[!Note]
            >Niektóre funkcje usługi SharePoint Online oraz pakietu Office 2010 zależą od tego, czy użytkownicy mogą wybrać opcję pozostania zalogowanym. Jeśli ta opcja zostanie ustawiona na **Nie**, użytkownicy mogą otrzymywać dodatkowe i nieoczekiwane monity o zalogowanie.
   

3. Po dodaniu marki wybierz pozycję **Zapisz**.

    Jeśli ten proces tworzy pierwszą niestandardową konfigurację znakowania, staje się domyślną dla dzierżawy. Jeśli masz dodatkowe konfiguracje, możesz wybrać konfigurację domyślną.
    
    >[!Important]
    >Aby dodać więcej konfiguracji znakowania firmowego do dzierżawy, należy wybrać **nowy język** na stronie **Znakowanie contoso - firma.** Spowoduje to otwarcie strony **Konfigurowanie znakowania firmy,** na której można wykonać te same czynności, co powyżej.

## <a name="update-your-custom-branding"></a>Aktualizowanie niestandardowego znakowania
Po utworzeniu niestandardowego brandingu możesz wrócić i zmienić wszystko, co chcesz.

### <a name="to-edit-your-custom-branding"></a>Aby edytować niestandardową markę
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **pozycję Azure Active Directory**, a następnie wybierz pozycję **Znakowanie firmy**, a następnie wybierz pozycję **Konfiguruj**.

    ![Contoso — strona znakowanie firmy z domyślną konfiguracją](media/customize-branding/company-branding-default-config.png)

3. Na stronie **Konfigurowanie znakowania firmy** dodaj, usuń lub zmień dowolne informacje na podstawie opisów w sekcji Dostosowywanie strony logowania usługi Azure AD w tym [artykule.](#customize-your-azure-ad-sign-in-page)

4. Wybierz **pozycję Zapisz**.

   Uwzględnienie zmian znakowania na stronie logowania może potrwać do godziny.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Dodawanie specyficznego dla języka znakowania firmowego do katalogu
Nie można zmienić języka oryginalnej konfiguracji z domyślnego języka. Jeśli jednak potrzebna jest konfiguracja w innym języku, można utworzyć nową konfigurację.

### <a name="to-add-a-language-specific-branding-configuration"></a>Aby dodać konfigurację znakowania specyficznej dla języka

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **pozycję Azure Active Directory**, a następnie wybierz pozycję **Znakowanie firmy**, a następnie wybierz pozycję Nowy **język**.

    ![Contoso — strona znakowanie firmy z wyróżnioną opcją Nowy język](media/customize-branding/company-branding-new-language.png)

3. Na stronie **Konfigurowanie znakowania firmy** wybierz swój język (na przykład francuski), a następnie dodaj przetłumaczone informacje na podstawie opisów w sekcji Dostosowywanie strony logowania usługi Azure AD w tym [artykule.](#customize-your-azure-ad-sign-in-page)

4. Wybierz **pozycję Zapisz**.

    Aktualizacje strony **znakowania Contoso — firmy,** aby wyświetlić nową konfigurację francuską.

    ![Contoso — strona znakowanie firmy z domyślną konfiguracją](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Dodawanie niestandardowej marki do stron
Dodaj niestandardową markę do stron, modyfikując koniec `?whr=yourdomainname`adresu URL za pomocą tekstu. Ta modyfikacja działa na kilku stronach, w tym na stronie konfiguracji uwierzytelniania wieloskładnikowego (MFA), na stronie konfiguracji samoobsługowego resetowania haseł (SSPR) i na stronie logowania.

**Przykłady:**

**Oryginalny adres URL:**https://aka.ms/MFASetup<br>
**Niestandardowy adres URL:**`https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com`

**Oryginalny adres URL:**https://aka.ms/SSPR<br>
**Niestandardowy adres URL:**`https://passwordreset.microsoftonline.com/?whr=contoso.com`

 
