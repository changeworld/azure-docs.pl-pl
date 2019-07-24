---
title: Dynamicznie dostosowuj interfejs użytkownika Azure Active Directory B2C przy użyciu zasad niestandardowych | Microsoft Docs
description: Obsługa wielu oznaczeń znakowania przy użyciu zawartości HTML5/CSS, która zmienia się dynamicznie w czasie wykonywania.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 43c0da3ca8fa4b2f74d48b0e202cc56bc8b9406c
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227217"
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Azure Active Directory B2C: Konfigurowanie interfejsu użytkownika z zawartością dynamiczną przy użyciu zasad niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Za pomocą zasad niestandardowych Azure Active Directory B2C (Azure AD B2C) można wysłać parametr w ciągu zapytania. Przekazanie parametru do punktu końcowego HTML pozwala na dynamiczną zmianę zawartość strony. Na podstawie parametru przekazywanego z aplikacji internetowej lub aplikacji mobilnej można na przykład zmienić obraz tła na stronie rejestracji lub logowania usługi Azure AD B2C.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule omówiono sposób dostosowywania interfejsu użytkownika Azure AD B2C z *zawartością dynamiczną* przy użyciu zasad niestandardowych. Aby rozpocząć, zobacz [Dostosowywanie interfejsu użytkownika w zasadach niestandardowych](active-directory-b2c-ui-customization-custom.md).

>[!NOTE]
>W Azure AD B2C artykule [Skonfiguruj dostosowanie interfejsu użytkownika w zasadach niestandardowych](active-directory-b2c-ui-customization-custom.md), omówiono następujące zagadnienia:
> * Funkcja dostosowywania interfejsu użytkownika (UI) strony.
> * Podstawowe narzędzia do testowania funkcji dostosowywania interfejsu użytkownika strony przy użyciu naszej przykładowej zawartości.
> * Podstawowe elementy interfejsu użytkownika poszczególnych typów stron.
> * Najlepsze rozwiązania dotyczące stosowania tej funkcji.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>Dodawanie linku do szablonów HTML5/CSS do podróży użytkownika

W przypadku zasad niestandardowych definicja zawartości definiuje identyfikator URI strony HTML5, który jest używany do określonego kroku interfejsu użytkownika (na przykład strony logowania lub rejestracji). Zasady podstawowe definiują domyślny wygląd i działanie, wskazując identyfikator URI plików HTML5 (w CSS). W zasadach rozszerzenia można zmodyfikować wygląd i działanie, zastępując LoadUri pliku HTML5. Definicje zawartości zawierają adresy URL do zawartości zewnętrznej zdefiniowanej przy użyciu plików HTML5/CSS, zgodnie z potrzebami.

Sekcja zawiera serię elementów XML `ContentDefinition` `ContentDefinitions` . Atrybut `ContentDefinition` ID elementu określa typ strony, która odnosi się do definicji zawartości. Oznacza to, że element definiuje kontekst, który ma zostać zastosowany niestandardowy szablon HTML5/CSS. W poniższej tabeli opisano zestaw identyfikatorów definicji zawartości rozpoznawanych przez aparat IEF oraz typy stron, które odnoszą się do nich.

| Identyfikator definicji zawartości | Domyślny szablon HTML5| Opis |
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Strona błędu**. Ta strona jest wyświetlana po napotkaniu wyjątku lub błędu. |
| *API. idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Strona wyboru dostawcy tożsamości**. Ta strona zawiera listę dostawców tożsamości, z których użytkownicy mogą wybierać podczas logowania. Te opcje są zwykle dostawcami tożsamości przedsiębiorstwa, dostawcami tożsamości społecznościowych, takimi jak Facebook, Google + lub kontami lokalnymi. |
| *API. idpselections. signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Wybór dostawcy tożsamości na potrzeby rejestracji**. Ta strona zawiera listę dostawców tożsamości, których użytkownicy mogą wybrać podczas tworzenia konta. Dostępne opcje to dostawcy tożsamości przedsiębiorstwa, dostawcy tożsamości społecznościowej, takich jak Facebook, Google + lub konta lokalne. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Zapomniane hasło strony**. Ta strona zawiera formularz, który użytkownicy muszą wykonać w celu zainicjowania resetowania hasła.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Strona logowania do konta lokalnego**. Ta strona zawiera formularz służący do logowania się przy użyciu konta lokalnego na podstawie adresu e-mail lub nazwy użytkownika. Formularz może zawierać pole wprowadzania tekstu i pole wprowadzania hasła. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Strona rejestracji konta lokalnego**. Ta strona zawiera formularz służący do tworzenia konta lokalnego na podstawie adresu e-mail lub nazwy użytkownika. Formularz może zawierać różne kontrolki danych wejściowych, na przykład: pole wprowadzania tekstu, pole wprowadzania hasła, przycisk radiowy, pola rozwijane z pojedynczym wybieraniem i pola wyboru z wieloma zaznaczeniami. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Strona uwierzytelniania**wieloskładnikowego. Na tej stronie użytkownicy mogą weryfikować numery telefonów (za pomocą tekstu lub głosu) podczas rejestracji lub logowania. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Strona rejestracji konta społecznościowego**. Ta strona zawiera formularz, który użytkownicy muszą zakończyć podczas rejestrowania się przy użyciu istniejącego konta od dostawcy tożsamości społecznościowej. Ta strona jest podobna do poprzedniej strony rejestracji konta społecznościowego, z wyjątkiem pól wprowadzania hasła. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Strona aktualizacji profilu**. Ta strona zawiera formularz, do którego użytkownicy mogą uzyskać dostęp w celu zaktualizowania swojego profilu. Ta strona jest podobna do strony rejestracji konta społecznościowego, z wyjątkiem pól wprowadzania hasła. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Ujednolicona Strona rejestracji lub logowania**. Ta strona obsługuje proces tworzenia konta i logowania użytkownika. Użytkownicy mogą korzystać z dostawców tożsamości przedsiębiorstwa, dostawców tożsamości społecznościowych, takich jak Facebook, Google + lub konta lokalnego.  |

## <a name="serving-dynamic-content"></a>Obsługa zawartości dynamicznej
W artykule [Konfigurowanie dostosowywania interfejsu użytkownika w ramach zasad niestandardowych](active-directory-b2c-ui-customization-custom.md) można przekazać pliki HTML5 do usługi Azure Blob Storage. Te pliki HTML5 są statyczne i renderują tę samą zawartość HTML dla każdego żądania.

W tym artykule opisano użycie aplikacji sieci Web ASP.NET, która może akceptować parametry ciągu zapytania i odpowiadać odpowiednio.

W tym instruktażu zawarto następujące instrukcje:
* Utwórz ASP.NET Core aplikację sieci Web, która hostuje Szablony HTML5.
* Dodaj niestandardowy szablon HTML5, _Unified. cshtml_.
* Opublikuj swoją aplikację sieci Web, aby Azure App Service.
* Ustaw współużytkowanie zasobów między źródłami (CORS) dla aplikacji sieci Web.
* Zastąp `LoadUri` elementy, aby wskazywały na plik HTML5.

## <a name="step-1-create-an-aspnet-web-app"></a>Krok 1: Tworzenie aplikacji internetowej platformy ASP.NET

1. W programie Visual Studio Utwórz projekt, wybierając pozycję **plik** > **Nowy** > **projekt**.

2. W oknie **Nowy projekt**  > wybierz pozycję **Visual C#**  **Web** > **ASP.NET Core Web Application (.NET Core)** .

3. Nadaj nazwę aplikacji (na przykład *contoso. AADB2C. UI*), a następnie wybierz **przycisk OK**.

    ![Utwórz nowy projekt programu Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. Wybierz szablon **aplikacji sieci Web** .

5. Ustaw uwierzytelnianie na **Brak uwierzytelniania**.

    ![Wybieranie szablonu aplikacji sieci Web](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. Wybierz **OK** do tworzenia projektu.

## <a name="step-2-create-mvc-view"></a>Krok 2: Utwórz widok MVC
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>Krok 2.1. Pobierz wbudowany szablon HTML5 B2C
Niestandardowy szablon HTML5 jest oparty na Azure AD B2C wbudowanym szablonie HTML5. Możesz pobrać ujednolicony [plik HTML](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) lub pobrać szablon z [pakietu Starter Pack](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Ten plik HTML5 służy do tworzenia ujednoliconej strony rejestracji lub logowania.

### <a name="step-22-add-the-mvc-view"></a>Krok 2.2. Dodawanie widoku MVC
1. Kliknij prawym przyciskiem myszy widok/folder macierzysty, a następnie **Dodaj** > **nowy element**.

    ![Element menu Dodaj nowy element w programie Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. W oknie **Dodawanie nowego elementu — contoso. AADB2C. UI** wybierz pozycję **Web > ASP.NET**.

3. Wybierz **stronę widoku MVC**.

4. W polu **Nazwa** Zmień nazwę na **ujednolicone. cshtml**.

5. Wybierz pozycję **Dodaj**.

    ![Okno dialogowe Dodawanie nowego elementu w programie Visual Studio z wyróżnioną stroną widoku MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. Jeśli plik *Unified. cshtml* nie jest już otwarty, kliknij dwukrotnie plik, aby go otworzyć, a następnie wyczyść zawartość pliku.

7. W tym instruktażu usuniemy odwołanie do strony układu. Dodaj następujący fragment kodu do _ujednoliconego. cshtml_:

    ```csharp
    @{
        Layout = null;
    }
    ```

8. Otwórz plik _Unified. cshtml_ pobrany z Azure AD B2C wbudowanego szablonu HTML5.

9. Zamień znaki pojedynczego znaku (@) na znaki podwójnej precyzji (@ @).

10. Skopiuj zawartość pliku i wklej go poniżej definicji układu. Kod powinien wyglądać następująco:

    ![ujednolicony plik. cshtml po dodaniu HTML5](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>Krok 2,3: Zmień obraz tła

`ID` `src` Znajdź element, który zawiera wartość *background_background_image* **https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** , a następnie Zastąp wartość lub innym obrazem tła, który ma być używany. `<img>`

![img, element z niestandardową wartością src background_background_image](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>Krok 2,4: Dodawanie widoku do kontrolera MVC

1. Otwórz **Controllers\HomeController.cs**i Dodaj następującą metodę:

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Ten kod określa, że metoda powinna używać pliku szablonu *widoku* , aby renderować odpowiedź do przeglądarki. Ponieważ nie określamy jawnie nazwy pliku szablonu *widoku* , domyślnie jest używany plik widoku _Unified. cshtml_ w folderze */views/Home* .

    Po dodaniu _ujednoliconej_ metody kod powinien wyglądać następująco:

    ![Zmień kontroler, aby renderować widok](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. Debuguj aplikację sieci Web i upewnij się, że jest  dostępna jednolita strona (na przykład `http://localhost:<Port number>/Home/unified`).

### <a name="step-25-publish-to-azure"></a>Krok 2,5: Publikowanie na platformie Azure
1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt **contoso. AADB2C. UI** , a następnie wybierz pozycję **Publikuj**.

    ![Publikuj w Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. Wybierz kafelek **App Service Microsoft Azure** , a następnie wybierz pozycję **Publikuj**.

    ![Utwórz nowy App Service Microsoft Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    Zostanie otwarte okno **tworzenia App Service** . W tym celu możesz rozpocząć tworzenie wszystkich zasobów platformy Azure niezbędnych do uruchomienia aplikacji sieci Web ASP.NET na platformie Azure.

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat publikowania, zobacz [Tworzenie aplikacji sieci web ASP.NET na platformie Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).

3. W polu **Nazwa aplikacji sieci Web** wpisz unikatową nazwę aplikacji (prawidłowe znaki to a-z, a-z, 0-9 i łącznik (-). Adres URL aplikacji internetowej to `http://<app_name>.azurewebsites.NET`, gdzie `<app_name>` to nazwa aplikacji internetowej. Możesz zaakceptować automatycznie wygenerowaną nazwę, która jest unikatowa.

4. Wybierz pozycję **Utwórz**, aby rozpocząć tworzenie zasobów platformy Azure.

    ![Podaj właściwości App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    Po zakończeniu procesu tworzenia Kreator opublikuje aplikację sieci Web ASP.NET na platformie Azure, a następnie uruchomi aplikację w domyślnej przeglądarce.

5. Skopiuj adres URL ujednoliconej  strony (na przykład _https://< APP_NAME >. azurewebsites. net/Home/Unified_).

## <a name="step-3-configure-cors-in-azure-app-service"></a>Krok 3: Konfigurowanie mechanizmu CORS w Azure App Service
1. W [Azure Portal](https://portal.azure.com/)wybierz pozycję **App Services**, a następnie wybierz nazwę aplikacji interfejsu API.

    ![Wybierz aplikację interfejsu API w Azure Portal](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. W sekcji **Ustawienia** w obszarze **interfejs API** wybierz pozycję **CORS**.

    ![Element menu CORS wyróżniony w menu App Service w Azure Portal](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. W oknie **CORS** w polu **dozwolone źródła** wykonaj jedną z następujących czynności:

    * Wprowadź adres URL lub adresy URL, z których chcesz zezwolić na wywołania JavaScript. Musisz użyć wszystkich małych liter w wprowadzonych adresach URL.
    * Wprowadź gwiazdkę (*), aby określić, że wszystkie domeny pochodzenia są akceptowane.

4. Wybierz pozycję **Zapisz**.

    ![Strona ustawień CORS z wyróżnioną gwiazdką w dozwolonych źródłach](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    Po wybraniu opcji **Zapisz**aplikacja interfejsu API akceptuje wywołania języka JavaScript z określonych adresów URL.

## <a name="step-4-html5-template-validation"></a>Krok 4: Walidacja szablonu HTML5
Twój szablon HTML5 jest gotowy do użycia. Nie jest jednak dostępna w `ContentDefinition` kodzie. Przed dodaniem `ContentDefinition` do zasad niestandardowych upewnij się, że:
* Zawartość jest zgodna z językiem HTML5 i jest dostępna.
* Na serwerze zawartości włączono funkcję CORS.

    >[!NOTE]
    >Aby sprawdzić, czy witryna, w której jest obsługiwana zawartość, włączyła funkcję CORS i może testować żądania CORS, przejdź do witryny sieci Web [test-CORS.org](https://test-cors.org/) .

* Obsługiwana zawartość jest zabezpieczona za pośrednictwem **protokołu HTTPS**.
* Używasz bezwzględnych *adresów URL*, takich `https://yourdomain/content`jak, dla wszystkich linków, zawartości CSS i obrazów.

## <a name="step-5-configure-your-content-definition"></a>Krok 5. Konfigurowanie definicji zawartości
Aby skonfigurować `ContentDefinition`program, wykonaj następujące czynności:
1. Otwórz podstawowy plik zasad (na przykład *TrustFrameworkBase. XML*).

2. Wyszukaj element, a następnie skopiuj całą zawartość `<ContentDefinitions>` węzła. `<ContentDefinitions>`

3. Otwórz plik rozszerzenia (na przykład *TrustFrameworkExtensions. XML*), a następnie wyszukaj `<BuildingBlocks>` element. Jeśli element nie istnieje, Dodaj go.

4. Wklej całą zawartość `<ContentDefinitions>` węzła skopiowaną jako element podrzędny `<BuildingBlocks>` elementu.

5. `<ContentDefinition>` Wyszukaj węzeł `Id="api.signuporsignin"` zawierający kod XML, który został skopiowany.

6. Zmień wartość `LoadUri` z z _~/tenant/default/Unified_ na _https://< APP_NAME >. azurewebsites. net/Home/Unified_.
    Zasady niestandardowe powinny wyglądać następująco:

    ![Przykład fragmentu kodu XML z wyróżnionym elementem LoadUri](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Krok 6: Przekazywanie zasad do dzierżawy
1. W [Azure Portal](https://portal.azure.com)przejdź do [kontekstu Azure AD B2C dzierżawy](active-directory-b2c-navigate-to-b2c-context.md), a następnie wybierz pozycję **Azure AD B2C**.

2. Wybierz pozycję **platforma obsługi tożsamości**.

3. Wybierz pozycję **wszystkie zasady**.

4. Wybierz pozycję **Przekaż zasady**.

5. Zaznacz pole wyboru **Zastąp zasady, jeśli istnieje** .

6. Przekaż plik *TrustFrameworkExtensions. XML* i upewnij się, że przeszedł sprawdzanie poprawności.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Krok 7: Testowanie zasad niestandardowych za pomocą polecenia Uruchom teraz
1. Wybierz pozycję **Azure AD B2C ustawienia**, a następnie wybierz pozycję **platforma obsługi tożsamości**.

    >[!NOTE]
    >Uruchomienie teraz wymaga, aby co najmniej jedna aplikacja była przedrejestrowana w dzierżawie. Aby dowiedzieć się, jak zarejestrować aplikacje, Azure AD B2C Zobacz artykuł [Rozpoczynanie pracy](active-directory-b2c-get-started.md) i artykuł dotyczący [rejestracji aplikacji](active-directory-b2c-app-registration.md) .

2. Otwórz **B2C_1A_signup_signin**, załadowane zasady niestandardowe jednostki uzależnionej (RP), a następnie wybierz pozycję **Uruchom teraz**.
    Powinien być w stanie zobaczyć swoją niestandardową HTML5 z utworzonym wcześniej tłem.

    ![Zasady rejestracji lub logowania](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>Krok 8: Dodaj zawartość dynamiczną
Zmień tło na podstawie parametru ciągu zapytania o nazwie _campaignId_. Aplikacja RP (aplikacje internetowe i mobilne) wysyła parametr do Azure AD B2C. Zasady odczytuje parametr i wysyła jego wartość do szablonu HTML5.

### <a name="step-81-add-a-content-definition-parameter"></a>Krok 8,1: Dodaj parametr definicji zawartości

`ContentDefinitionParameters` Dodaj element, wykonując następujące czynności:
1. Otwórz plik *SignUpOrSignin* zasad (na przykład *SignUpOrSignin. XML*).

2. `<DefaultUserJourney>` W węźle`UserJourneyBehaviors` Dodaj węzeł:

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

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>Krok 8,2: Zmień kod, aby akceptować parametr ciągu zapytania i zastąpić obraz tła
Zmodyfikuj metodę HomeController `unified` , aby akceptowała parametr campaignId. Metoda sprawdza wartość parametru i odpowiednio ustawia `ViewData["background"]` zmienną.

1. Otwórz plik *Controllers\HomeController.cs* , a następnie zmień `unified` metodę, dodając następujący fragment kodu:

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

2. Znajdź element z identyfikatorem `background_background_image` iZastąp`src` wartość wartością. `@ViewData["background"]` `<img>`

    ![element img z wyróżnioną wartością src ](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8,3: Przekazywanie zmian i publikowanie zasad
1. Opublikuj projekt programu Visual Studio w celu Azure App Service.

2. Przekaż zasady *SignUpOrSignin. XML* do Azure AD B2C.

3. Otwórz **B2C_1A_signup_signin**, przekazane zasady niestandardowe RP, a następnie wybierz pozycję **Uruchom teraz**.
    Powinien pojawić się ten sam obraz tła, który był wcześniej wyświetlany.

4. Skopiuj adres URL z paska adresu przeglądarki.

5. Dodaj parametr ciągu zapytania _campaignId_ do identyfikatora URI. Na przykład Dodaj `&campaignId=hawaii`, jak pokazano na poniższej ilustracji:

    ![Wyróżniono identyfikator URI przy użyciu parametru ciągu zapytania campaignId](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. Wybierz **klawisz ENTER** , aby wyświetlić obraz tła Hawaje.

    ![Strona logowania przy użyciu konta z niestandardowym tłem obrazu Hawaje](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Zmień wartość na *Tokio*, a następnie wybierz **klawisz ENTER**.
    Przeglądarka wyświetla tło Tokio.

    ![Strona logowania do rejestracji z niestandardowym tłem obrazu w postaci Tokio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Krok 9: Zmiana reszty podróży użytkownika
W przypadku wybrania linku **Utwórz konto teraz** na stronie logowania w przeglądarce zostanie wyświetlony domyślny obraz tła, a nie zdefiniowany obraz. Takie zachowanie występuje, ponieważ zmieniono tylko stronę rejestracji lub logowania. Aby zmienić resztę definicji zawartości z dowolnego potwierdzenia:
1. Wróć do "krok 2" i wykonaj następujące czynności:

    a. Pobierz plik *selfasserted* .

    b. Skopiuj zawartość pliku.

    c. Utwórz nowy widok, *selfasserted*.

    d. Dodaj *selfasserted* do kontrolera **macierzystego** .

2. Wróć do "krok 4" i wykonaj następujące czynności:

    a. W `<ContentDefinition>` zasadach rozszerzenia Znajdź węzeł zawierający `Id="api.selfasserted"`, `Id="api.localaccountsignup"`, i `Id="api.localaccountpasswordreset"`.

    b. Ustaw atrybut na identyfikator URI *selfasserted.* `LoadUri`

3. Wróć do "krok 8,2," i Zmień kod, aby akceptował parametry ciągu zapytania, ale tym razem z funkcją *selfasserted* .

4. Przekaż zasady *TrustFrameworkExtensions. XML* i upewnij się, że przeszedł weryfikację.

5. Uruchom test zasad, a następnie wybierz pozycję **zarejestruj się teraz** , aby zobaczyć wynik.

## <a name="optional-download-the-complete-policy-files-and-code"></a>Obowiązkowe Pobieranie kompletnych plików zasad i kodu
* Po ukończeniu przewodnika [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) zalecamy utworzenie scenariusza przy użyciu własnych niestandardowych plików zasad. W odniesieniu do Twojej dokumentacji udostępniono [przykładowe pliki zasad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).
* Możesz pobrać kompletny kod z przykładowego [rozwiązania programu Visual Studio, aby uzyskać odwołanie](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).




