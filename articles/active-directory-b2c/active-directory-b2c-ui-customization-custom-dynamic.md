---
title: 'Usługa Azure Active Directory B2C: Dostosowania interfejsu użytkownika (UI) usługi Azure AD B2C dynamicznie za pomocą zasad niestandardowych'
description: Obsługuje wiele środowisk znakowania z zawartością HTML5/CSS, która zmienia się dynamicznie w czasie wykonywania.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 09/20/2017
ms.author: davidmu
ms.openlocfilehash: 77f6ae1df8a364eecc5e7d6d6fe3a07dd215ac16
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Usługa Azure Active Directory B2C: Konfigurowanie interfejsu użytkownika z dynamiczną zawartością za pomocą zasad niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Za pomocą usługi Azure Active Directory B2C zasad niestandardowych (Azure AD B2C), możesz wysłać parametr w ciągu zapytania. Przez przekazanie parametru do punktu końcowego HTML, można dynamicznie zmieniać zawartość strony. Na przykład można zmienić obraz tła w witrynie usługi Azure AD B2C zapisywania się lub zaloguj się na podstawie parametru, który jest przekazywany z sieci web lub aplikacji mobilnej. 

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przedstawiono sposób dostosowania interfejsu użytkownika usługi Azure AD B2C z *zawartości dynamicznej* za pomocą niestandardowych zasad. Aby rozpocząć, zobacz [dostosowywania interfejsu użytkownika w zasadach niestandardowych](active-directory-b2c-ui-customization-custom.md). 

>[!NOTE]
>Artykuł usługi Azure AD B2C [skonfigurować dostosowywania interfejsu użytkownika w zasadach niestandardowych](active-directory-b2c-ui-customization-custom.md), w tym artykule omówiono następujące podstawy:
> * Funkcja dostosowania interfejsu użytkownika strony.
> * Podstawowe narzędzia do testowania dostosowywanie funkcji interfejsu użytkownika strony za pomocą nasze przykłady.
> * Elementy interfejsu użytkownika core każdego typu strony.
> * Najlepsze rozwiązania dotyczące stosowania tej funkcji.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>Dodaj łącze do szablonów HTML5/CSS do podróży użytkownika

W zasadach niestandardowych definicję zawartości definiuje stronę HTML5 identyfikator URI, który służy do określonego etapu interfejsu użytkownika (na przykład strony logowania lub tworzenia konta). Podstawowe zasady określają domyślnego wyglądu i działania poprzez wskazanie identyfikatora URI HTML5 plików (CSS). W zasadach rozszerzenia można zmodyfikować wygląd i działanie przez zastąpienie LoadUri pliku HTML5. Definicje zawartości zawiera adresy URL do zawartości zewnętrznej, który jest zdefiniowany przez obsługuje tworzenie plików HTML5/CSS, zależnie od potrzeb. 

`ContentDefinitions` Sekcja zawiera szereg `ContentDefinition` elementów XML. Atrybut ID `ContentDefinition` element określa typ strony, która odnosi się do definicji zawartości. Element określają kontekst szablon niestandardowy HTML5/CSS ma zastosowanie. W poniższej tabeli opisano zestaw identyfikatorów, które są rozpoznawane przez aparat IEF i typy stron, które odnoszą się do nich definicji zawartości.

| Identyfikator definicji zawartości | Domyślny szablon HTML5| Opis | 
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Strona błędu**. Ta strona jest wyświetlana po napotkaniu wyjątku lub wystąpił błąd. |
| *API.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Strona wyboru dostawcy tożsamości**. Ta strona zawiera listę dostawców tożsamości, które użytkownicy mogą wybierać podczas logowania. Opcje są zazwyczaj enterprise dostawców tożsamości, dostawców tożsamości społecznościowych, takich jak Facebook i Google + lub kont lokalnych. |
| *API.idpselections.Signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Wybór dostawcy tożsamości dla rejestracji**. Ta strona zawiera listę dostawców tożsamości, które użytkownicy mogą wybierać podczas tworzenia konta. Dostępne opcje to enterprise dostawców tożsamości, dostawców tożsamości społecznościowych, takich jak Facebook i Google + lub kont lokalnych. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Nie pamiętasz hasła strony**. Ta strona zawiera formularz, który użytkownicy muszą wykonać, aby zainicjować resetowania hasła.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Strona logowania konta lokalnego**. Ta strona zawiera formularz logowania przy użyciu konta lokalnego, która jest oparta na adres e-mail lub nazwę użytkownika. Formularz może zawierać pola do wprowadzania tekstu, a w polu wprowadzania hasła. |
| *API.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Stronę konta lokalnego konta**. Ta strona zawiera formularza do skorzystania z konta lokalnego, która jest oparta na adres e-mail lub nazwę użytkownika. Formularz może zawierać różne kontrolki wejściowe, takich jak: tekst wejściowy pola, pole wprowadzania hasła, przycisk radiowy, jednokrotnym zaznaczeniem pola listy rozwijanej i pola wyboru wielokrotnego wyboru. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Strona uwierzytelniania wieloskładnikowego**. Na tej stronie użytkownicy mogą sprawdzić swoje numery telefonów (przy użyciu tekstowych lub głosowych) podczas tworzenia konta lub logowania. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Strony rejestracji społecznościowych konta**. Ta strona zawiera formularz, który użytkownicy muszą wykonać podczas logowania przy użyciu istniejącego konta od dostawcy tożsamości społecznościowych. Ta strona jest podobny do poprzedniego konta społecznościowych stronę tworzenia konta, z wyjątkiem pól wprowadzania hasła. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Strona aktualizacji profilu**. Ta strona zawiera formularz, który użytkownicy mogą uzyskiwać dostęp do zaktualizowania swój profil. Ta strona jest podobna do strony rejestracji społecznościowych konto, z wyjątkiem pól wprowadzania hasła. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Ujednolicone stronę tworzenia konta lub logowania**. Ta strona obsługuje proces rejestracji i logowania użytkownika. Użytkownicy mogą używać enterprise dostawców tożsamości, dostawców tożsamości społecznościowych, takich jak Facebook lub Google + lub kont lokalnych.  |

## <a name="serving-dynamic-content"></a>Obsługę zawartości dynamicznej
W [skonfigurować dostosowywania interfejsu użytkownika w zasadach niestandardowych](active-directory-b2c-ui-customization-custom.md) artykułu, przekazywanie plików HTML5 do magazynu obiektów Blob Azure. Te pliki HTML5 są statyczne i renderowania zawartości dla każdego żądania do tego samego kodu HTML. 

W tym artykule możesz użyć aplikacji sieci web platformy ASP.NET, która może akceptować parametrów ciągu zapytania i odpowiednio odpowiadać. 

W tym przewodniku możesz:
* Tworzenie aplikacji sieci web platformy ASP.NET Core obsługujący szablony HTML5. 
* Dodaj szablon niestandardowy HTML5 _unified.cshtml_. 
* Opublikuj aplikację sieci web w usłudze Azure App Service. 
* Ustaw współużytkowanie zasobów między źródłami (CORS) do udostępniania dla aplikacji sieci web.
* Zastąpienie `LoadUri` elementy, aby wskazać plik HTML5.

## <a name="step-1-create-an-aspnet-web-app"></a>Krok 1: Tworzenie aplikacji sieci web platformy ASP.NET

1. W programie Visual Studio Utwórz projekt, wybierając **pliku** > **nowy** > **projektu**.

2. W **nowy projekt** wybierz **Visual C#** > **Web** > **aplikacji sieci Web platformy ASP.NET Core (.NET Core)**.

3. Nazwa aplikacji (na przykład *Contoso.AADB2C.UI*), a następnie wybierz **OK**.

    ![Utwórz nowy projekt programu Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. Wybierz **aplikacji sieci Web** szablonu.

5. Ustawienia uwierzytelniania **bez uwierzytelniania**.

    ![Wybierz szablon aplikacji sieci Web](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. Wybierz **OK** Aby utworzyć projekt.

## <a name="step-2-create-mvc-view"></a>Krok 2: Tworzenie widoku MVC
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>Krok 2.1: Pobieranie wbudowanych szablonów HTML5 B2C
Szablon HTML5 niestandardowy jest oparty na wbudowanych szablonów usługi Azure AD B2C w HTML5. Możesz pobrać [pliku unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) lub pobrać szablonu z [pakiet początkowy](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Ten plik HTML5 umożliwia tworzenie ujednoliconego strony rejestracji lub logowania.

### <a name="step-22-add-the-mvc-view"></a>Krok 2.2: Dodawanie widoku MVC
1. Kliknij prawym przyciskiem myszy folder domowych widoków, a następnie **Dodaj** > **nowy element**.

    ![Dodaj nowy element MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. W **Dodaj nowy element - Contoso.AADB2C.UI** wybierz **sieci Web > ASP.NET**.

3. Wybierz **MVC Wyświetl stronę**.

4. W **nazwa** Zmień nazwę pliku na **unified.cshtml**.

5. Wybierz pozycję **Dodaj**.

    ![Dodaj widok MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. Jeśli *unified.cshtml* plik nie jest już otwarty, kliknij dwukrotnie plik, aby go otworzyć, a następnie wyczyść zawartość pliku.

7. W ramach tego przewodnika możemy Usuń odwołanie do strony układu. Dodaj poniższy fragment kodu do _unified.cshtml_:

    ```csharp
    @{
        Layout = null;
    }
    ```

8. Otwórz _unified.cshtml_ plik został pobrany z usługi Azure AD B2C wbudowanych szablonów HTML5.

9. Zastąp jeden znaków (@) z podwójnym znakami (@@).

10. Skopiuj zawartość pliku i wklej go poniżej definicji układu. Kod powinien wyglądać podobnie jak:

    ![Unified.cshtml plik po dodaniu HTML5](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>Krok 2.3: Zmień obraz tła

Zlokalizuj `<img>` element, który zawiera `ID` wartość *background_background_image*, a następnie zastąp `src` wartości z **https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** lub innych obraz tła, który ma być używany.

![Zmiana tła strony](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>Krok 2.4: Dodaj widok do kontrolera MVC

1. Otwórz **Controllers\HomeController.cs**i dodaj następujące metody: 

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Ten kod określa, czy należy użyć metody *widoku* pliku szablonu do renderowania odpowiedzi do przeglądarki. Ponieważ nie jest jawnie określona nazwa *widoku* pliku szablonu MVC używa domyślnie _unified.cshtml_ pliku widoku w */widoków domowych* folderu.
    
    Po dodaniu _ujednoliconego_ metody kod powinien wyglądać następująco:
    
    ![Zmienianie kontrolera do renderowania widoku](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. Debugowanie aplikacji sieci web i upewnij się, że _ujednoliconego_ strona jest dostępna (na przykład `http://localhost:<Port number>/Home/unified`).

### <a name="step-25-publish-to-azure"></a>Krok 2.5: Publikowanie na platformie Azure
1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **Contoso.AADB2C.UI** projektu, a następnie wybierz **publikowania**.

    ![Publikowanie do usługi Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. Wybierz **Microsoft Azure App Service** Kafelek, a następnie wybierz **publikowania**.

    ![Utwórz nowy Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    **Tworzenie usługi App Service** zostanie otwarte okno. W nim można rozpocząć tworzenie wszystkich zasobów Azure niezbędne do uruchomienia aplikacji sieci web platformy ASP.NET na platformie Azure.

    > [!NOTE]
    > Aby uzyskać więcej informacji o publikowaniu, zobacz [tworzenie aplikacji sieci web platformy ASP.NET na platformie Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. W **Nazwa aplikacji sieci Web** wpisz unikatowej nazwy aplikacji (prawidłowe znaki to a-z, A-Z, 0-9 oraz łącznik (-). Adres URL aplikacji internetowej to `http://<app_name>.azurewebsites.NET`, gdzie `<app_name>` to nazwa aplikacji internetowej. Możesz zaakceptować automatycznie wygenerowaną nazwę, która jest unikatowa.

4. Wybierz pozycję **Utwórz**, aby rozpocząć tworzenie zasobów platformy Azure.

    ![Podaj właściwości usługi aplikacji](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    Po zakończeniu procesu tworzenia Kreator opublikuje aplikacji sieci web platformy ASP.NET na platformie Azure i następnie uruchamia aplikację w domyślnej przeglądarce.

5. Skopiuj adres URL _ujednoliconego_ strony (na przykład _https://<app_name>.azurewebsites.net/home/unified_).

## <a name="step-3-configure-cors-in-azure-app-service"></a>Krok 3: Konfigurowanie mechanizmu CORS w usłudze aplikacji Azure
1. W [portalu Azure](https://portal.azure.com/), wybierz pozycję **usługi aplikacji**, a następnie wybierz nazwę aplikacji interfejsu API.

    ![Wybieranie aplikacji interfejsu API w portalu Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. W **ustawienia** w obszarze **interfejsu API** zaznacz **CORS**.

    ![Wybierz ustawienia mechanizmu CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. W **CORS** okna w **dozwolone źródła** polu, wykonaj jedną z następujących czynności:

    * Wprowadź adres URL lub adresów URL, które mają być dozwolone wywołania języka JavaScript, które pochodzą z.
    * Wprowadź znak gwiazdki (*), aby określić, że wszystkie domeny pochodzenia są akceptowane.

4. Wybierz pozycję **Zapisz**.

    ![Okno CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    Po wybraniu **zapisać**, w aplikacji interfejsu API akceptuje wywołań języka JavaScript z określonych adresów URL. 

## <a name="step-4-html5-template-validation"></a>Krok 4: Weryfikacja szablonu HTML5
Szablon HTML5 jest gotowe do użycia. Jednak nie jest dostępna w `ContentDefinition` kodu. Aby można było dodać `ContentDefinition` do zasad niestandardowych, upewnij się, że:
* Zawartość jest HTML5 zgodne i jest dostępny.
* Serwer zawartości jest włączona dla CORS.

    >[!NOTE]
    >Aby zweryfikować, że lokacji, w którym przechowujesz zawartości ma włączoną obsługę mechanizmu CORS i przetestować żądań CORPS, przejdź do [cors.org testu](http://test-cors.org/) witryny sieci Web. 

* Obsługiwane zawartość jest bezpieczna za pośrednictwem **HTTPS**.
* Używasz *bezwzględnych adresów URL*, takich jak *https://yourdomain/content*, łącza, zawartość arkusza CSS i obrazów.

## <a name="step-5-configure-your-content-definition"></a>Krok 5: Konfigurowanie definicję zawartości
Aby skonfigurować `ContentDefinition`, wykonaj następujące czynności:
1. Otwórz plik podstawowy zasad (na przykład *TrustFrameworkBase.xml*).

2. Wyszukaj `<ContentDefinitions>` elementu, a następnie skopiuj cała zawartość `<ContentDefinitions>` węzła.

3. Otwórz plik rozszerzenia (na przykład *TrustFrameworkExtensions.xml*), a następnie wyszukaj `<BuildingBlocks>` elementu. Jeśli element nie istnieje, należy go dodać.

4. Wklej całą zawartość `<ContentDefinitions>` węzła, który został skopiowany jako element podrzędny `<BuildingBlocks>` elementu. 

5. Wyszukaj `<ContentDefinition>` węzła, który zawiera `Id="api.signuporsignin"` w pliku XML, który został skopiowany.

6. Zmień wartość `LoadUri` z _~/tenant/default/unified_ do _https://<app_name>.azurewebsites.net/home/unified_.  
    Niestandardowe zasady powinna wyglądać następująco:
    
    ![Definicja zawartości](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Krok 6: Przekaż zasady dla Twojej dzierżawy
1. W [portalu Azure](https://portal.azure.com), przełącz się do [kontekstu dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), a następnie wybierz **usługi Azure AD B2C**.

2. Wybierz **Framework obsługi tożsamości**.

3. Wybierz **wszystkich zasad**.

4. Wybierz **przekazywać zasady**.

5. Wybierz **zastąpić zasady, jeśli istnieje** pole wyboru.

6. Przekaż *TrustFrameworkExtensions.xml* pliku i upewnij się, że przekazaniem sprawdzania poprawności.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Krok 7: Testowanie zasad niestandardowych za pomocą Uruchom teraz
1. Wybierz **ustawienia usługi Azure AD B2C**, a następnie wybierz **Framework obsługi tożsamości**.

    >[!NOTE]
    >Uruchom teraz wymaga co najmniej jednej aplikacji można preregistered dla dzierżawcy. Aby dowiedzieć się, jak zarejestrować aplikacji, zapoznaj się z usługi Azure AD B2C [wprowadzenie](active-directory-b2c-get-started.md) artykułu lub [Rejestracja aplikacji](active-directory-b2c-app-registration.md) artykułu.

2. Otwórz **B2C_1A_signup_signin**, jednostki uzależnionej strony (RP) zasad niestandardowych, które można przekazać, a następnie wybierz **Uruchom teraz**.  
    Można wyświetlić z niestandardowych HTML5 utworzonego wcześniej w tle.

    ![Zasady rejestracji i logowania](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>Krok 8: Dodawanie zawartości dynamicznej
Zmień tło na podstawie parametru ciągu zapytania o nazwie _campaignId_. Aplikacja RP (sieć web i aplikacji mobilnych) wysyła parametr do usługi Azure AD B2C. Twoje zasady odczytuje parametr i wysyła wartość do szablonu HTML5. 

### <a name="step-81-add-a-content-definition-parameter"></a>Krok 8.1: Dodawanie parametru definicję zawartości

Dodaj `ContentDefinitionParameters` element, wykonując następujące czynności:
1. Otwórz *SignUpOrSignin* pliku zasad (na przykład *SignUpOrSignin.xml*).

2. Wyszukaj `<DefaultUserJourney>` węzła. 

3. W `<DefaultUserJourney>` węzła, Dodaj następujący fragment kodu XML:  

    ```XML
    <UserJourneyBehaviors>
        <ContentDefinitionParameters>
            <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>Krok 8.2: Zmień swój kod, aby zaakceptować parametr ciągu zapytania i zamienić obraz tła
Modyfikowanie HomeController `unified` metodę, aby zaakceptować parametru campaignId. Metoda sprawdza następnie parametr na wartość i ustawia `ViewData["background"]` zmiennej odpowiednio.

1. Otwórz *Controllers\HomeController.cs* pliku, a następnie zmień `unified` metody, dodając następujący fragment kodu:

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

2. Zlokalizuj `<img>` elementu z Identyfikatorem `background_background_image`i Zastąp `src` wartości z `@ViewData["background"]`.

    ![Zmiana tła strony](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8.3: Przekaż zmiany i opublikuj zasad
1. Publikowanie projektu programu Visual Studio w usłudze Azure App Service.

2. Przekaż *SignUpOrSignin.xml* zasady usługi Azure AD B2C.

3. Otwórz **B2C_1A_signup_signin**, niestandardowych zasad planu odzyskiwania, które można przekazać, a następnie wybierz **Uruchom teraz**.  
    Ten sam obraz tła, który wcześniej był wyświetlany powinna zostać wyświetlona.

4. Skopiuj adres URL z pola adresu przeglądarki.

5. Dodaj _campaignId_ parametr ciągu do identyfikatora URI zapytania. Na przykład dodać `&campaignId=hawaii`, jak pokazano na poniższej ilustracji:

    ![Zmiana tła strony](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. Wybierz **Enter** do wyświetlania obrazu tła Hawajach.

    ![Zmiana tła strony](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Zmień wartość na *Tokio*, a następnie wybierz **Enter**.  
    W przeglądarce pojawi się w tle Tokio.

    ![Zmiana tła strony](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Krok 9: Zmień rest podróży użytkownika
W przypadku wybrania **Zamów teraz** łącze na stronie logowania w przeglądarce Wyświetla domyślny obraz tła, definicja nie obrazu. To zachowanie pojawia się, ponieważ zmieniono tylko stronę tworzenia konta lub logowania. Aby zmienić reszty własnym Assert definicje zawartości:
1. Wróć do "Krok 2" i wykonaj następujące czynności:

    a. Pobierz *selfasserted* pliku.

    b. Skopiuj zawartość pliku.

    c. Utwórz nowy widok *selfasserted*.

    d. Dodaj *selfasserted* do **Home** kontrolera.

2. Przejdź do sekcji "Krok 4" i wykonaj następujące czynności: 

    a. W zasadach rozszerzenia, należy znaleźć `<ContentDefinition>` węzła, który zawiera `Id="api.selfasserted"`, `Id="api.localaccountsignup"`, i `Id="api.localaccountpasswordreset"`.

    b. Ustaw `LoadUri` atrybutu użytkownika *selfasserted* identyfikatora URI.

3. Wróć do "Krok 8.2" i zmień swój kod, aby zaakceptować parametrów ciągu zapytania, ale tym razem do *selfasserted* funkcji. 

4. Przekaż *TrustFrameworkExtensions.xml* zasad i upewnij się, że przekazaniem sprawdzania poprawności.

5. Uruchom test zasad, a następnie wybierz **Zamów teraz** aby zobaczyć wynik.

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcjonalnie) Pobierz pliki pełną zasad i kod
* Po ukończeniu [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) wskazówki, zaleca się tworzenia scenariusz przy użyciu plików zasady niestandardowe. Użytkownikowi, firma Microsoft umieściła [przykładowe pliki zasad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).
* Możesz pobrać pełną kod z [przykładowe rozwiązanie Visual Studio dla odwołania](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).




