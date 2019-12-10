---
title: Zabezpieczanie usługi RESTful przy użyciu certyfikatów klienta
titleSuffix: Azure AD B2C
description: Zabezpiecz swoją niestandardową wymianę oświadczeń interfejsu API REST w Azure AD B2C przy użyciu certyfikatów klienta
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1d396de57a12285fb6cc682510fa6f95585465d0
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949861"
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>Zabezpieczanie usługi RESTful przy użyciu certyfikatów klienta

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W powiązanym artykule [utworzysz usługę RESTful](active-directory-b2c-custom-rest-api-netfw.md) , która współdziała z Azure Active Directory B2C (Azure AD B2C).

W tym artykule dowiesz się, jak ograniczyć dostęp do aplikacji sieci Web platformy Azure (API RESTful) przy użyciu certyfikatu klienta. Mechanizm ten jest nazywany uwierzytelnianiem obustronnym TLS lub *uwierzytelnianiem certyfikatu klienta*. Tylko usługi, które mają odpowiednie certyfikaty, takie jak Azure AD B2C, mogą uzyskiwać dostęp do usługi.

>[!NOTE]
>Usługę RESTful można także zabezpieczyć za pomocą [uwierzytelniania podstawowego protokołu HTTP](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). Jednak uwierzytelnianie podstawowe protokołu HTTP jest uznawane za mniej bezpieczne przez certyfikat klienta. Nasze zalecenie polega na zabezpieczeniu usługi RESTful przy użyciu uwierzytelniania za pomocą certyfikatu klienta zgodnie z opisem w tym artykule.

W tym artykule szczegółowo opisano, jak:
* Skonfiguruj aplikację sieci Web do używania uwierzytelniania certyfikatu klienta.
* Przekaż certyfikat do Azure AD B2C kluczy zasad.
* Skonfiguruj zasady niestandardowe do korzystania z certyfikatu klienta.

## <a name="prerequisites"></a>Wymagania wstępne
* Wykonaj kroki opisane w artykule [integracja z usługą integracji interfejsów API REST](active-directory-b2c-custom-rest-api-netfw.md) .
* Uzyskaj prawidłowy certyfikat (plik PFX z kluczem prywatnym).

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>Krok 1. Konfigurowanie aplikacji sieci Web na potrzeby uwierzytelniania certyfikatu klienta
Aby skonfigurować **Azure App Service** tak, aby wymagały certyfikatów klienta, należy ustawić *wartość true*dla aplikacji internetowej `clientCertEnabled` ustawienie witryny. Aby wprowadzić tę zmianę, w Azure Portal Otwórz stronę aplikacji sieci Web. W lewym okienku nawigacji w obszarze **Ustawienia** wybierz pozycję **Ustawienia protokołu SSL**. W sekcji **Certyfikaty klienta** Włącz opcję **przychodzącego certyfikatu klienta** .

>[!NOTE]
>Upewnij się, że plan Azure App Service jest standardowym lub wyższym. Aby uzyskać więcej informacji, zobacz [szczegółowe Omówienie planów Azure App Service](https://docs.microsoft.com/azure/app-service/overview-hosting-plans).

>[!NOTE]
>Aby uzyskać więcej informacji na temat ustawiania właściwości **clientCertEnabled** , zobacz [Konfigurowanie wzajemnego uwierzytelniania TLS dla aplikacji sieci Web](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Krok 2. Przekaż certyfikat do Azure AD B2C kluczy zasad
Po ustawieniu `clientCertEnabled` na *wartość true*komunikacja z interfejsem API RESTful wymaga certyfikatu klienta. Aby uzyskać, przekazać i zapisać certyfikat klienta w dzierżawie Azure AD B2C, wykonaj następujące czynności:
1. W dzierżawie Azure AD B2C wybierz pozycję **Ustawienia B2C** > **Framework Experience platform**.

2. Aby wyświetlić klucze, które są dostępne w dzierżawie, wybierz pozycję **klucze zasad**.

3. Wybierz pozycję **Dodaj**.
    Zostanie otwarte okno **Utwórz klucz** .

4. W polu **Opcje** wybierz pozycję **Przekaż**.

5. W polu **Nazwa** wpisz **B2cRestClientCertificate**.
    Prefiks *B2C_1A_* jest dodawany automatycznie.

6. W polu **przekazywanie pliku** wybierz plik PFX certyfikatu z kluczem prywatnym.

7. W polu **hasło** wpisz hasło certyfikatu.

    ![Przekaż klucz zasad na stronie Tworzenie klucza w Azure Portal](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. Wybierz pozycję **Utwórz**.

8. Aby wyświetlić klucze, które są dostępne w dzierżawie i potwierdzić, że utworzono klucz `B2C_1A_B2cRestClientCertificate`, wybierz pozycję **klucze zasad**.

## <a name="step-3-change-the-technical-profile"></a>Krok 3. zmiana profilu technicznego
Aby zapewnić obsługę uwierzytelniania certyfikatu klienta w zasadach niestandardowych, należy zmienić profil techniczny, wykonując następujące czynności:

1. W katalogu roboczym Otwórz plik zasad rozszerzenia *TrustFrameworkExtensions. XML* .

2. Wyszukaj węzeł `<TechnicalProfile>`, który zawiera `Id="REST-API-SignUp"`.

3. Znajdź element `<Metadata>`.

4. Zmień wartość *AuthenticationType* na *ClientCertificate*w następujący sposób:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. Bezpośrednio po elemencie `<Metadata>` zamykającym Dodaj następujący fragment kodu XML:

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    Po dodaniu fragmentu Twój profil techniczny powinien wyglądać podobnie do następującego kodu XML:

    ![Ustaw elementy XML uwierzytelniania ClientCertificate](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Krok 4. przekazywanie zasad do dzierżawy

1. W [Azure Portal](https://portal.azure.com)przejdź do [kontekstu Azure AD B2C dzierżawy](active-directory-b2c-navigate-to-b2c-context.md), a następnie wybierz pozycję **Azure AD B2C**.

2. Wybierz pozycję **platforma obsługi tożsamości**.

3. Wybierz pozycję **wszystkie zasady**.

4. Wybierz pozycję **Przekaż zasady**.

5. Zaznacz pole wyboru **Zastąp zasady, jeśli istnieje** .

6. Przekaż plik *TrustFrameworkExtensions. XML* , a następnie upewnij się, że przeszedł sprawdzanie poprawności.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Krok 5. Testowanie zasad niestandardowych za pomocą polecenia Uruchom teraz
1. Otwórz **Azure AD B2C ustawienia**, a następnie wybierz pozycję **platforma obsługi tożsamości**.

    >[!NOTE]
    >Uruchomienie teraz wymaga, aby co najmniej jedna aplikacja była przedrejestrowana w dzierżawie. Aby dowiedzieć się, jak zarejestrować aplikacje, Azure AD B2C Zobacz artykuł [Rozpoczynanie pracy](active-directory-b2c-get-started.md) i artykuł dotyczący [rejestracji aplikacji](active-directory-b2c-app-registration.md) .

2. Otwórz **B2C_1A_signup_signin**, załadowane zasady niestandardowe jednostki uzależnionej (RP), a następnie wybierz pozycję **Uruchom teraz**.

3. Przetestuj proces, wpisując **test** w **podanym polu Nazwa** .
    Azure AD B2C wyświetla komunikat o błędzie w górnej części okna.

    ![Podano wyróżnione pole tekstowe nazwy i pokazany błąd sprawdzania poprawności danych wejściowych](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. W **podanym polu Nazwa** wpisz nazwę (inną niż "test").
    Azure AD B2C zarejestrowaniu użytkownika, a następnie wysłania do aplikacji numeru lojalnościowego. Zanotuj liczbę z tego przykładu JWT:

   ```
   {
     "typ": "JWT",
     "alg": "RS256",
     "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
   }.{
     "exp": 1507125903,
     "nbf": 1507122303,
     "ver": "1.0",
     "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
     "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
     "acr": "b2c_1a_signup_signin",
     "nonce": "defaultNonce",
     "iat": 1507122303,
     "auth_time": 1507122303,
     "loyaltyNumber": "290",
     "given_name": "Emily",
     "emails": ["B2cdemo@outlook.com"]
   }
   ```

   >[!NOTE]
   >Jeśli zostanie wyświetlony komunikat o błędzie, *nazwa jest nieprawidłowa, podaj prawidłową nazwę*, co oznacza, że Azure AD B2C pomyślnie wezwała usługę RESTful podczas prezentowania certyfikatu klienta. Następnym krokiem jest zweryfikowanie certyfikatu.

## <a name="step-6-add-certificate-validation"></a>Krok 6. Dodawanie weryfikacji certyfikatu
Certyfikat klienta, który Azure AD B2C wysyłany do usługi RESTful, nie podlega weryfikacji przez platformę Azure App Service, z wyjątkiem tego, czy certyfikat istnieje. Sprawdzanie poprawności certyfikatu jest odpowiedzialne za aplikację sieci Web.

W tej sekcji dodasz przykładowy kod ASP.NET, który sprawdza poprawność właściwości certyfikatu na potrzeby uwierzytelniania.

> [!NOTE]
>Aby uzyskać więcej informacji o konfigurowaniu Azure App Service na potrzeby uwierzytelniania certyfikatów klientów, zobacz [Konfigurowanie wzajemnego uwierzytelniania TLS dla aplikacji sieci Web](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6,1. Dodaj ustawienia aplikacji do pliku Web. config projektu
W projekcie programu Visual Studio, który został utworzony wcześniej, Dodaj następujące ustawienia aplikacji do pliku *Web. config* po elemencie `appSettings`:

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Zastąp wartości w polu **nazwa podmiotu**certyfikatu, **nazwę wystawcy**i **odcisk palca certyfikatu** wartościami certyfikatu.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6,2 dodanie funkcji IsValidClientCertificate
Otwórz plik *Controllers\IdentityController.cs* , a następnie Dodaj do klasy kontrolera `Identity` następującej funkcji:

```csharp
private bool IsValidClientCertificate()
{
    string ClientCertificateSubject = ConfigurationManager.AppSettings["ClientCertificate:Subject"];
    string ClientCertificateIssuer = ConfigurationManager.AppSettings["ClientCertificate:Issuer"];
    string ClientCertificateThumbprint = ConfigurationManager.AppSettings["ClientCertificate:Thumbprint"];

    X509Certificate2 clientCertInRequest = RequestContext.ClientCertificate;
    if (clientCertInRequest == null)
    {
        Trace.WriteLine("Certificate is NULL");
        return false;
    }

    // Basic verification
    if (clientCertInRequest.Verify() == false)
    {
        Trace.TraceError("Basic verification failed");
        return false;
    }

    // 1. Check the time validity of the certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check the subject name of the certificate
    bool foundSubject = false;
    string[] certSubjectData = clientCertInRequest.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certSubjectData)
    {
        if (String.Compare(s.Trim(), ClientCertificateSubject) == 0)
        {
            foundSubject = true;
            break;
        }
    }

    if (!foundSubject)
    {
        Trace.TraceError($"Subject name '{clientCertInRequest.Subject}' is not valid");
        return false;
    }

    // 3. Check the issuer name of the certificate
    bool foundIssuerCN = false;
    string[] certIssuerData = clientCertInRequest.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certIssuerData)
    {
        if (String.Compare(s.Trim(), ClientCertificateIssuer) == 0)
        {
            foundIssuerCN = true;
            break;
        }
    }

    if (!foundIssuerCN)
    {
        Trace.TraceError($"Issuer '{clientCertInRequest.Issuer}' is not valid");
        return false;
    }

    // 4. Check the thumbprint of the certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test whether the certificate chains to a trusted root authority, you can uncomment the following code:
    //
    //X509Chain certChain = new X509Chain();
    //certChain.Build(certificate);
    //bool isValidCertChain = true;
    //foreach (X509ChainElement chElement in certChain.ChainElements)
    //{
    //    if (!chElement.Certificate.Verify())
    //    {
    //        isValidCertChain = false;
    //        break;
    //    }
    //}
    //if (!isValidCertChain) return false;
    return true;
}
```

W poprzednim przykładowym kodzie akceptujemy certyfikat jako prawidłowy tylko wtedy, gdy spełnione są wszystkie następujące warunki:
* Certyfikat nie wygasł i jest aktywny dla bieżącego czasu na serwerze.
* Nazwa `Subject` certyfikatu jest równa wartości ustawienia aplikacji `ClientCertificate:Subject`.
* Nazwa `Issuer` certyfikatu jest równa wartości ustawienia aplikacji `ClientCertificate:Issuer`.
* `thumbprint` certyfikatu jest równa wartości ustawienia aplikacji `ClientCertificate:Thumbprint`.

>[!IMPORTANT]
>W zależności od czułości usługi może być konieczne dodanie większej liczby walidacji. Na przykład może być konieczne przetestowanie, czy certyfikat tworzy łańcuch z zaufanym urzędem głównym, weryfikacją nazwy organizacji wystawcy i tak dalej.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6,3 wywołaj funkcję IsValidClientCertificate
Otwórz plik *Controllers\IdentityController.cs* , a następnie na początku funkcji `SignUp()` Dodaj następujący fragment kodu:

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Po dodaniu fragmentu kodu kontroler `Identity` powinien wyglądać następująco:

![Dodawanie kodu sprawdzania poprawności certyfikatu](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>Krok 7. opublikowanie projektu na platformie Azure i przetestowanie go
1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt **contoso. AADB2C. API** , a następnie wybierz pozycję **Publikuj**.

2. Powtórz "krok 6" i ponownie przetestuj zasady niestandardowe przy użyciu walidacji certyfikatu. Spróbuj uruchomić zasady i upewnij się, że wszystko działa po dodaniu walidacji.

3. W pliku *Web. config* zmień wartość `ClientCertificate:Subject` na **nieprawidłowe**. Uruchom zasady ponownie i powinien zostać wyświetlony komunikat o błędzie.

4. Zmień wartość z powrotem na **prawidłowe**i upewnij się, że zasady mogą wywołać interfejs API REST.

Jeśli musisz rozwiązać ten krok, zobacz [zbieranie dzienników przy użyciu Application Insights](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>Obowiązkowe Pobieranie kompletnych plików zasad i kodu
* Po ukończeniu przewodnika [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) zalecamy utworzenie scenariusza przy użyciu własnych niestandardowych plików zasad. W odniesieniu do Twojej dokumentacji udostępniono [przykładowe pliki zasad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert).
* Możesz pobrać kompletny kod z [przykładowego rozwiązania programu Visual Studio, aby uzyskać odwołanie](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API).
