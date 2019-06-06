---
title: Dynamiczne Dostosowywanie interfejsu użytkownika usługi Azure Active Directory B2C (UI), za pomocą zasad niestandardowych | Dokumentacja firmy Microsoft
description: Obsługa wielu środowisk znakowania z zawartością HTML5/CSS, która zmienia się dynamicznie w czasie wykonywania.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e1abdfa8bc47f42f7373760370588c0bc41fc1dc
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507778"
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Azure Active Directory B2C: Konfigurowanie interfejsu użytkownika z zawartością dynamiczną za pomocą zasad niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Za pomocą usługi Azure Active Directory B2C (Azure AD B2C), niestandardowych zasad możesz wysłać parametr ciągu zapytania. Przekazanie parametru do punktu końcowego HTML pozwala na dynamiczną zmianę zawartość strony. Na podstawie parametru przekazywanego z aplikacji internetowej lub aplikacji mobilnej można na przykład zmienić obraz tła na stronie rejestracji lub logowania usługi Azure AD B2C. 

## <a name="prerequisites"></a>Wymagania wstępne
Ten artykuł koncentruje się na temat sposobu dostosowywania interfejsu użytkownika usługi Azure AD B2C z *zawartości dynamicznej* za pomocą zasad niestandardowych. Aby rozpocząć pracę, zobacz [dostosowywania interfejsu użytkownika w przypadku zasad niestandardowych](active-directory-b2c-ui-customization-custom.md). 

>[!NOTE]
>Artykuł usługi Azure AD B2C [Konfigurowanie dostosowywania interfejsu użytkownika w przypadku zasad niestandardowych](active-directory-b2c-ui-customization-custom.md), w tym artykule omówiono następujące podstawy:
> * Funkcja dostosowywania interfejsu użytkownika strony.
> * Podstawowe narzędzia do testowania funkcji dostosowywania interfejsu użytkownika strony za pomocą naszej przykładowej zawartości.
> * Wpisz podstawowych elementów interfejsu użytkownika dla każdej strony.
> * Najlepsze rozwiązania dotyczące stosowania tej funkcji.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>Dodaj link do szablonów HTML5/CSS swoją podróż po użytkownik

W przypadku zasad niestandardowych definicji zawartości definiuje stronę HTML5 identyfikator URI, który jest używany do określonego etapu interfejsu użytkownika (na przykład strony logowania lub tworzenia konta). Podstawowa zasada definiuje domyślny wygląd i działanie poprzez wskazanie identyfikatora URI HTML5 plików (CSS). W zasadach rozszerzenia można zmodyfikować wygląd i działanie przez zastąpienie parametr LoadUri dla pliku HTML5. Definicji zawartości zawiera adresy URL do zawartości zewnętrznej, która jest zdefiniowana przy tworzeniu plików HTML5/CSS, zgodnie z potrzebami. 

`ContentDefinitions` Sekcja zawiera szereg `ContentDefinition` elementów XML. Atrybut ID `ContentDefinition` element określa typ strony, które odnoszą się do definicji zawartości. Oznacza to, że element definiuje kontekst, który zamierza stosowanie niestandardowy szablon HTML5/CSS. W poniższej tabeli opisano zestaw definicji zawartości identyfikatorów, które są rozpoznawane przez aparat IEF i typy stron, które odnoszą się do nich.

| Identyfikator definicji zawartości | Domyślny szablon HTML5| Opis | 
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Strona błędu**. Ta strona jest wyświetlana, gdy występuje wyjątek lub komunikat o błędzie. |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Strona wyboru dostawcy tożsamości**. Ta strona zawiera listę dostawców tożsamości, które użytkownicy mogą wybierać podczas logowania. Opcje są zazwyczaj enterprise dostawców tożsamości, dostawców tożsamości społecznościowych, takich jak Facebook i Google + lub kont lokalnych. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Wybór dostawcy tożsamości dla rejestracji**. Ta strona zawiera listę dostawców tożsamości, które użytkownicy mogą wybierać podczas rejestracji. Opcje są enterprise dostawców tożsamości, dostawców tożsamości społecznościowych, takich jak Facebook i Google + lub kont lokalnych. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Strona obsługi zapomnianego hasła**. Ta strona zawiera formularz, który użytkownicy muszą wykonać, aby zainicjować resetowania hasła.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Strona logowania dla kont lokalnych**. Ta strona zawiera formularz logujesz się przy użyciu konta lokalnego, który jest oparty na adres e-mail lub nazwę użytkownika. Formularz może zawierać pola wprowadzania tekstu, a pole wprowadzania hasła. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Strona tworzenia nowego konta lokalnego**. Ta strona zawiera formularz za utworzenie konta lokalnego, który jest oparty na adres e-mail lub nazwę użytkownika. Formularz mogą zawierać różne kontrolki wejściowe, takie jak: tekst wejściowy pola, pole wprowadzania hasła, przycisk radiowy, pola listy rozwijanej wybierz jedną, a następnie zaznacz pola wyboru. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Strona uwierzytelniania wieloskładnikowego**. Na tej stronie użytkownicy mogą sprawdzić swoje numery telefonów (przy użyciu tekstowych lub głosowych) podczas tworzenia konta lub logowania. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Strona rejestracji dla kont społecznościowych**. Ta strona zawiera formularz, który użytkownicy muszą wykonać po utworzeniu konta przy użyciu istniejącego konta z dostawcy tożsamości społecznościowych. Ta strona jest podobny do poprzedniego konta społecznościowego zapisywania strony, z wyjątkiem pól wprowadzania hasła. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Strona aktualizacji profilu**. Ta strona zawiera formularz, który użytkownicy mogą uzyskać dostęp, aby zaktualizować swój profil. Ta strona jest podobna do strony rejestracji konta w sieci społecznościowej, z wyjątkiem pól wprowadzania hasła. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Ujednolicona strona rejestracji lub logowania**. Ta strona obsługuje proces rejestracji i logowania użytkownika. Użytkownicy mogą korzystać przedsiębiorstwa dostawców tożsamości, dostawców tożsamości społecznościowych, takich jak Facebook lub Google + lub kont lokalnych.  |

## <a name="serving-dynamic-content"></a>Obsługujących zawartość dynamiczną
W [Konfigurowanie dostosowywania interfejsu użytkownika w przypadku zasad niestandardowych](active-directory-b2c-ui-customization-custom.md) artykułu, możesz przekazać pliki HTML5 do usługi Azure Blob storage. Te pliki HTML5 są statyczne i renderowania zawartości dla każdego żądania do tego samego kodu HTML. 

W tym artykule używasz aplikację sieci web ASP.NET, który może akceptować parametry ciągu zapytania i odpowiednio reagować. 

W tym przewodniku możesz:
* Utwórz aplikację sieci web platformy ASP.NET Core, która obsługuje szablony HTML5. 
* Dodaj szablon niestandardowy HTML5 _unified.cshtml_. 
* Publikowanie aplikacji sieci web w usłudze Azure App Service. 
* Ustaw cross-origin resource sharing (CORS) dla aplikacji sieci web.
* Zastąp `LoadUri` elementów, aby wskazać plik HTML5.

## <a name="step-1-create-an-aspnet-web-app"></a>Krok 1: Tworzenie aplikacji internetowej platformy ASP.NET

1. W programie Visual Studio Utwórz projekt, wybierając **pliku** > **New** > **projektu**.

2. W **nowy projekt** wybierz **Visual C#**  > **Web** > **aplikacja sieci Web programu ASP.NET Core (.NET Core)** .

3. Nazwij aplikację (na przykład *Contoso.AADB2C.UI*), a następnie wybierz pozycję **OK**.

    ![Utwórz nowy projekt programu Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. Wybierz **aplikacji sieci Web** szablonu.

5. Ustaw uwierzytelnianie **bez uwierzytelniania**.

    ![Wybierz szablon aplikacji sieci Web](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. Wybierz **OK** do tworzenia projektu.

## <a name="step-2-create-mvc-view"></a>Krok 2: Tworzenie widoku MVC
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>Krok 2.1. Pobierz szablon HTML5 z wbudowanych B2C
Szablon niestandardowy, HTML5 opiera się na wbudowany szablon języka HTML5 usługi Azure AD B2C. Możesz pobrać [pliku unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) lub pobrać szablon z [pakiet startowy](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Ten plik HTML5 umożliwia utworzenie ujednoliconego strony rejestracji lub logowania.

### <a name="step-22-add-the-mvc-view"></a>Krok 2.2. Dodawanie widoku MVC
1. Kliknij prawym przyciskiem myszy folder domowych widoki, a następnie **Dodaj** > **nowy element**.

    ![Dodaj nowy element MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. W **Dodaj nowy element - Contoso.AADB2C.UI** wybierz **Web > ASP.NET**.

3. Wybierz **MVC wyświetlić stronę**.

4. W **nazwa** pola, Zmień nazwę na **unified.cshtml**.

5. Wybierz pozycję **Dodaj**.

    ![Dodawanie widoku MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. Jeśli *unified.cshtml* plik nie jest już otwarty, kliknij dwukrotnie plik, aby go otworzyć, a następnie wyczyść zawartość pliku.

7. W ramach tego przewodnika, możemy usunąć odwołania do strony układu. Dodaj poniższy fragment kodu do _unified.cshtml_:

    ```csharp
    @{
        Layout = null;
    }
    ```

8. Otwórz _unified.cshtml_ pliku pobranego z wbudowanych szablonów języka HTML5 usługi Azure AD B2C.

9. Zastąp pojedynczej precyzji na znaki (@) z podwójnym znakami (@@).

10. Skopiuj zawartość pliku i wklej go poniżej definicji układu. Kod powinien wyglądać podobnie jak:

    ![Plik Unified.cshtml po dodaniu HTML5](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>Krok 2.3 Zmień obraz tła

Znajdź `<img>` element, który zawiera `ID` wartość *background_background_image*, a następnie zastąp `src` wartością **https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** lub innych obraz tła, których chcesz użyć.

![Zmień tło strony](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>Krok 2.4 Dodaj widok do kontrolera MVC

1. Otwórz **Controllers\HomeController.cs**i dodaj następujące metody: 

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Ten kod określa, że metoda powinny używać *widoku* plik szablonu do renderowania odpowiedzi do przeglądarki. Ponieważ firma Microsoft nie jawnie określić nazwę *widoku* plik szablonu MVC używa domyślnie _unified.cshtml_ plik widoku w */widoków domowych* folderu.
    
    Po dodaniu _ujednoliconego_ metody, kod powinien wyglądać następująco:
    
    ![Zmień kontroler do renderowania widoku](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. Debugowanie aplikacji sieci web i upewnij się, że _ujednoliconego_ strona jest niedostępna (na przykład `http://localhost:<Port number>/Home/unified`).

### <a name="step-25-publish-to-azure"></a>Krok 2.5 Publikowanie na platformie Azure
1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **Contoso.AADB2C.UI** projektu, a następnie wybierz **Publikuj**.

    ![Publikowanie w usłudze Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. Wybierz **Microsoft Azure App Service** kafelka, a następnie wybierz pozycję **Publikuj**.

    ![Utwórz nowy Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    **Tworzenie usługi App Service** zostanie otwarte okno. W nim można rozpocząć tworzenie wszystkich zasobów platformy Azure niezbędnych do uruchomienia aplikacji internetowej ASP.NET na platformie Azure.

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat publikowania zobacz [tworzenie aplikacji internetowej ASP.NET na platformie Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).

3. W **Nazwa aplikacji sieci Web** wpisz unikatową nazwę aplikacji (prawidłowe znaki to a-z, A-Z, 0 – 9 oraz łącznik (-). Adres URL aplikacji internetowej to `http://<app_name>.azurewebsites.NET`, gdzie `<app_name>` to nazwa aplikacji internetowej. Możesz zaakceptować automatycznie wygenerowaną nazwę, która jest unikatowa.

4. Wybierz pozycję **Utwórz**, aby rozpocząć tworzenie zasobów platformy Azure.

    ![Podaj właściwości usługi App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    Po zakończeniu procesu tworzenia Kreator publikuje aplikację internetową ASP.NET na platformie Azure, a następnie uruchamia aplikację w domyślnej przeglądarce.

5. Skopiuj adres URL _ujednoliconego_ strony (na przykład _https://<app_name>.azurewebsites.net/home/unified_).

## <a name="step-3-configure-cors-in-azure-app-service"></a>Krok 3: Konfigurowanie mechanizmu CORS w usłudze Azure App Service
1. W [witryny Azure portal](https://portal.azure.com/), wybierz opcję **App Services**, a następnie wybierz nazwę aplikacji interfejsu API.

    ![Wybieranie aplikacji interfejsu API w witrynie Azure portal](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. W **ustawienia** sekcji w obszarze **API** zaznacz **CORS**.

    ![Wybierz ustawienia mechanizmu CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. W **CORS** okna w **dozwolone źródła** wykonaj jedną z następujących czynności:

    * Wprowadź adres URL lub adresy URL, które mają być dozwolone wywołania języka JavaScript pochodzą. Należy używać małych liter w adresach URL, które należy wprowadzić.
    * Wprowadź znak gwiazdki (*), aby określić, że wszystkie domeny pochodzenia są akceptowane.

4. Wybierz pozycję **Zapisz**.

    ![W oknie mechanizmu CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    Po wybraniu **Zapisz**, aplikacji interfejsu API akceptuje wywołania języka JavaScript pochodzące z określonych adresów URL. 

## <a name="step-4-html5-template-validation"></a>Krok 4: Weryfikacja szablonu HTML5
Szablon języka HTML5 jest gotowy do użycia. Jednak nie jest dostępna w `ContentDefinition` kodu. Aby można było dodać `ContentDefinition` do zdefiniowania zasad niestandardowych, upewnij się, że:
* Twoja zawartość jest HTML5, zgodne i jest dostępny.
* Serwer zawartości jest włączona dla mechanizmu CORS.

    >[!NOTE]
    >Aby sprawdzić, czy witryny, w którym przechowujesz zawartości włączył mechanizmu CORS i przetestować żądań CORPS, przejdź do [cors.org testu](https://test-cors.org/) witryny sieci Web. 

* Obsługiwane zawartość jest bezpieczna za pośrednictwem **HTTPS**.
* Używasz *bezwzględne adresy URL*, takich jak `https://yourdomain/content`, wszystkie łącza, zawartość arkusza CSS i obrazów.

## <a name="step-5-configure-your-content-definition"></a>Krok 5. Skonfiguruj swoją definicję zawartości
Aby skonfigurować `ContentDefinition`, wykonaj następujące czynności:
1. Otwieranie pliku podstawowego zasad (na przykład *TrustFrameworkBase.xml*).

2. Wyszukaj `<ContentDefinitions>` elementu, a następnie skopiować całą zawartość `<ContentDefinitions>` węzła.

3. Otwórz plik rozszerzenia (na przykład *TrustFrameworkExtensions.xml*) i wyszukaj `<BuildingBlocks>` elementu. Jeśli element nie istnieje, należy go dodać.

4. Wklej całą zawartość `<ContentDefinitions>` węzeł, który został skopiowany jako element podrzędny elementu `<BuildingBlocks>` elementu. 

5. Wyszukaj `<ContentDefinition>` węzeł, który zawiera `Id="api.signuporsignin"` w formacie XML, który został skopiowany.

6. Zmień wartość właściwości `LoadUri` z _~/tenant/default/unified_ do _https://<app_name>.azurewebsites.net/home/unified_.  
    Niestandardowe zasady powinny wyglądać następująco:
    
    ![Definicji zawartości](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Krok 6: Przekazywanie zasad dla Twojej dzierżawy
1. W [witryny Azure portal](https://portal.azure.com), przełącz się do [kontekstu dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), a następnie wybierz pozycję **usługi Azure AD B2C**.

2. Wybierz **struktura środowiska tożsamości**.

3. Wybierz **wszystkie zasady**.

4. Wybierz **przekazywać zasady**.

5. Wybierz **Zastąp zasady Jeśli istnieje** pole wyboru.

6. Przekaż *TrustFrameworkExtensions.xml* pliku i upewnij się, że przekazuje sprawdzania poprawności.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Krok 7: Testowanie zasad niestandardowych za pomocą polecenia Uruchom teraz
1. Wybierz **ustawienia usługi Azure AD B2C**, a następnie wybierz pozycję **struktura środowiska tożsamości**.

    >[!NOTE]
    >Uruchom teraz wymaga co najmniej jedną aplikację, aby być jest wstępnie zarejestrowane w ramach dzierżawy. Aby dowiedzieć się, jak zarejestrować aplikacji, zobacz temat usługi Azure AD B2C [wprowadzenie](active-directory-b2c-get-started.md) artykułu lub [rejestracji aplikacji](active-directory-b2c-app-registration.md) artykułu.

2. Otwórz **B2C_1A_signup_signin**, jednostki uzależnionej strona (RP) zasad niestandardowych, które przekazane, a następnie wybierz **Uruchom teraz**.  
    Można wyświetlić swoje niestandardowe HTML5 utworzonego wcześniej w tle.

    ![Zasady rejestracji lub logowania](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>Krok 8: Dodaj zawartość dynamiczną
Zmiana tła, w oparciu o nazwę parametru ciągu zapytania _campaignId_. Aplikacja jednostki Uzależnionej (aplikacji internetowych i mobilnych) wysyła parametr do usługi Azure AD B2C. Twoje zasady odczytuje parametru i wysyła wartość do szablonu HTML5. 

### <a name="step-81-add-a-content-definition-parameter"></a>Krok 8.1: Dodaj parametr definicję zawartości

Dodaj `ContentDefinitionParameters` elementu, wykonując następujące czynności:
1. Otwórz *SignUpOrSignin* pliku zasad (na przykład *SignUpOrSignin.xml*).

2. W obszarze `<DefaultUserJourney>` węzła, Dodaj `UserJourneyBehaviors` węzła:  

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ContentDefinitionParameters>
          <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>Krok 8.2: Zmień swój kod, aby zaakceptować jako parametr ciągu zapytania i zamienić obraz tła
Modyfikowanie HomeController `unified` metodę, aby zaakceptować parametru campaignId. Metoda następnie sprawdza parametru przez wartość i ustawia `ViewData["background"]` zmiennej odpowiednio.

1. Otwórz *Controllers\HomeController.cs* pliku, a następnie zmień `unified` metody, dodając poniższy fragment kodu:

    ```csharp
    public IActionResult unified(string campaignId)
    {
        // If campaign ID is Hawaii, show Hawaii background
        if (campaignId != null && campaignId.ToLower() == "hawaii")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1";
        }
        // If campaign ID is Tokyo, show Tokyo background
        else if (campaignId != null && campaignId.ToLower() == "tokyo")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19666_en_1";
        }
        // Default background
        else
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/18983_en_1";
        }

        return View();
    }

    ```

2. Znajdź `<img>` elementu z Identyfikatorem `background_background_image`i Zastąp `src` wartością `@ViewData["background"]`.

    ![Zmień tło strony](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8.3: Przekaż zmiany i publikowanie zasad
1. Publikowanie projektu programu Visual Studio w usłudze Azure App Service.

2. Przekaż *SignUpOrSignin.xml* zasady usługi Azure AD B2C.

3. Otwórz **B2C_1A_signup_signin**, przekazać, a następnie wybierz pozycję zasady niestandardowe jednostki Uzależnionej **Uruchom teraz**.  
    Powinien być widoczny ten sam obraz tła, który wcześniej był wyświetlany.

4. Skopiuj adres URL z paska adresu w przeglądarce.

5. Dodaj _campaignId_ parametr ciągu do identyfikatora URI zapytania. Na przykład dodać `&campaignId=hawaii`, jak pokazano na poniższej ilustracji:

    ![Zmień tło strony](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. Wybierz **Enter** do wyświetlania obrazu tła Hawajach.

    ![Zmień tło strony](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Zmień wartość na *Tokio*, a następnie wybierz pozycję **Enter**.  
    W przeglądarce pojawi się w tle Tokio.

    ![Zmień tło strony](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Krok 9: Zmień pozostałą część podróży użytkownika
Jeśli wybierzesz **Zarejestruj się teraz** łącze na stronie logowania w przeglądarce Wyświetla domyślny obraz tła, definicja nie obrazu. To zachowanie pojawia się, ponieważ zmieniono tylko na stronie tworzenia konta lub logowania. Aby zmienić pozostałe własnym Assert definicji zawartości:
1. Wróć do "Krok 2" i wykonaj następujące czynności:

    a. Pobierz *selfasserted* pliku.

    b. Skopiuj zawartość pliku.

    c. Utwórz nowy widok *selfasserted*.

    d. Dodaj *selfasserted* do **Home** kontrolera.

2. Przejdź do sekcji "Krok 4" i wykonaj następujące czynności: 

    a. W zasadach rozszerzenie, należy znaleźć `<ContentDefinition>` węzeł, który zawiera `Id="api.selfasserted"`, `Id="api.localaccountsignup"`, i `Id="api.localaccountpasswordreset"`.

    b. Ustaw `LoadUri` atrybutu do Twojej *selfasserted* identyfikatora URI.

3. Wróć do "Krok 8.2" i zmień swój kod, aby akceptował parametry ciągu zapytania, ale w tym momencie *selfasserted* funkcji. 

4. Przekaż *TrustFrameworkExtensions.xml* zasad i upewnij się, że przekazuje sprawdzania poprawności.

5. Uruchom test zasad, a następnie wybierz **Zarejestruj się teraz** można przejrzeć wyniki.

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcjonalnie) Pobierz pliki zasad kompletny i kodu
* Po ukończeniu [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) wskazówki, firma Microsoft zaleca tworzenie scenariusza za pomocą plików zasad niestandardowych. Dla Twojej informacji udostępniliśmy [przykładowe pliki zasad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).
* Możesz pobrać kompletny kod z [przykładowe rozwiązanie Visual Studio dla odwołania](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).




