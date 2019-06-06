---
title: Zabezpieczanie usługi RESTful przy użyciu certyfikatów klienta w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Zabezpieczanie usługi niestandardowe wymianą oświadczenia interfejsu API REST usługi Azure AD B2C przy użyciu certyfikatów klienta
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b007aa4619effbd34e4e969e4ce7b58f3b0c4cf6
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510540"
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>Zabezpieczanie usługi RESTful przy użyciu certyfikatów klienta

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W powiązanym artykule możesz [tworzenia usługi RESTful](active-directory-b2c-custom-rest-api-netfw.md) która współdziała z usługi Azure Active Directory B2C (Azure AD B2C).

W tym artykule dowiesz się, jak ograniczyć dostęp do aplikacji sieci web platformy Azure (interfejs API RESTful) przy użyciu certyfikatu klienta. Mechanizm ten jest nazywany wzajemnego uwierzytelniania protokołu TLS lub *uwierzytelnianie certyfikatu klienta*. Tylko w przypadku usług, które mają odpowiednie certyfikaty, takich jak usługi Azure AD B2C, może uzyskać dostęp do usługi.

>[!NOTE]
>Można również Zabezpieczanie usługi RESTful przy użyciu [podstawowe uwierzytelnianie HTTP](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). Jednak podstawowe uwierzytelnianie HTTP jest uważany za mniej bezpieczna opcja za pośrednictwem certyfikatu klienta. Nasze zalecenie jest Zabezpieczanie usługi RESTful przy użyciu uwierzytelniania certyfikatu klienta, zgodnie z opisem w tym artykule.

Ten artykuł zawiera instrukcje:
* Skonfiguruj aplikację sieci web, aby użyć uwierzytelniania certyfikatu klienta.
* Przekaż certyfikat do kluczy zasad usługi Azure AD B2C.
* Konfigurowanie niestandardowych zasad do użycia certyfikatu klienta.

## <a name="prerequisites"></a>Wymagania wstępne
* Wykonaj kroki [integracja interfejsu API REST oświadczeń wymiany](active-directory-b2c-custom-rest-api-netfw.md) artykułu.
* Uzyskaj prawidłowy certyfikat (plik PFX przy użyciu klucza prywatnego).

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>Krok 1: Konfigurowanie aplikacji sieci web do uwierzytelniania certyfikatu klienta
Aby skonfigurować **usługi Azure App Service** aby wymagać certyfikaty klienta, należy ustawić w aplikacji sieci web `clientCertEnabled` lokacji ustawienie, aby *true*. Aby wprowadzić tę zmianę, w witrynie Azure portal, otwórz stronę aplikacji sieci web. W lewym obszarze nawigacji w obszarze **ustawienia** wybierz **ustawienia protokołu SSL**. W **certyfikaty klienta** sekcji, Włącz **przychodzący certyfikat klienta** opcji.

>[!NOTE]
>Upewnij się, że planu usługi Azure App Service jest standardowy lub nowszej. Aby uzyskać więcej informacji, zobacz [szczegółowe omówienie planów usługi Azure App Service](https://docs.microsoft.com/azure/app-service/overview-hosting-plans).

>[!NOTE]
>Aby uzyskać więcej informacji o ustawieniu **elementu clientCertEnabled** właściwości, zobacz [Konfigurowanie wzajemnego uwierzytelniania protokołu TLS dla aplikacji sieci web](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Krok 2: Przekaż certyfikat na platformę klucze zasad usługi Azure AD B2C
Po ustawieniu `clientCertEnabled` do *true*, komunikacji za pomocą interfejsu API RESTful wymaga certyfikatu klienta. Uzyskaj przekazywania oraz przechowywania certyfikatu klienta w Twojej dzierżawie usługi Azure AD B2C, wykonaj następujące czynności: 
1. W ramach dzierżawy usługi Azure AD B2C wybierz **ustawieniami B2C** > **struktura środowiska tożsamości**.

2. Aby wyświetlić klucze, które są dostępne w Twojej dzierżawie, wybierz **klucze zasad**.

3. Wybierz pozycję **Dodaj**.  
    **Utwórz klucz** zostanie otwarte okno.

4. W **opcje** wybierz opcję **przekazywanie**.

5. W **nazwa** wpisz **B2cRestClientCertificate**.  
    Prefiks *B2C_1A_* jest automatycznie dodawany.

6. W **przekazywanie pliku** wybierz plik PFX certyfikatu z kluczem prywatnym.

7. W **hasło** wpisz hasło do certyfikatu.

    ![Przekaż klucz zasad](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. Wybierz pozycję **Utwórz**.

8. Aby wyświetlić klucze, które są dostępne w Twojej dzierżawie i upewnij się, że utworzono `B2C_1A_B2cRestClientCertificate` klucza, wybierz opcję **klucze zasad**.

## <a name="step-3-change-the-technical-profile"></a>Krok 3: Zmiany profilu technicznego
Aby obsługiwać uwierzytelnianie certyfikatu klienta w zasadach niestandardowych, należy zmienić profil techniczny, wykonując następujące czynności:

1. W katalogu roboczym otwórz *TrustFrameworkExtensions.xml* rozszerzenie pliku zasad.

2. Wyszukaj `<TechnicalProfile>` węzeł, który zawiera `Id="REST-API-SignUp"`.

3. Znajdź `<Metadata>` elementu.

4. Zmiana *AuthenticationType* do *ClientCertificate*, wykonując następujące czynności:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. Natychmiast po zamykającym `<Metadata>` elementu, Dodaj następujący fragment kodu XML: 

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    Po dodaniu fragment kodu, profilu technicznego powinien wyglądać podobnie do poniższego kodu XML:

    ![Ustaw elementy XML uwierzytelniania ClientCertificate](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Krok 4: Przekazywanie zasad dla Twojej dzierżawy

1. W [witryny Azure portal](https://portal.azure.com), przełącz się do [kontekstu dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), a następnie wybierz pozycję **usługi Azure AD B2C**.

2. Wybierz **struktura środowiska tożsamości**.

3. Wybierz **wszystkie zasady**.

4. Wybierz **przekazywać zasady**.

5. Wybierz **Zastąp zasady Jeśli istnieje** pole wyboru.

6. Przekaż *TrustFrameworkExtensions.xml* pliku, a następnie upewnij się, że przekazuje sprawdzania poprawności.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Krok 5. Testowanie zasad niestandardowych za pomocą polecenia Uruchom teraz
1. Otwórz **ustawienia usługi Azure AD B2C**, a następnie wybierz pozycję **struktura środowiska tożsamości**.

    >[!NOTE]
    >Uruchom teraz wymaga co najmniej jedną aplikację, aby być jest wstępnie zarejestrowane w ramach dzierżawy. Aby dowiedzieć się, jak zarejestrować aplikacji, zobacz temat usługi Azure AD B2C [wprowadzenie](active-directory-b2c-get-started.md) artykułu lub [rejestracji aplikacji](active-directory-b2c-app-registration.md) artykułu.

2. Otwórz **B2C_1A_signup_signin**, jednostki uzależnionej strona (RP) zasad niestandardowych, które przekazane, a następnie wybierz **Uruchom teraz**.

3. Przetestuj proces, wpisując **testu** w **imię** pole.  
    Usługa Azure AD B2C wyświetla komunikat o błędzie w górnej części okna.    

    ![Testowanie swoją tożsamość interfejsu API](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. W **imię** wpisz nazwę (innego niż "Test").  
    Usługa Azure AD B2C loguje się użytkownik, a następnie wysyła numer lojalności do aplikacji. Zanotuj liczbę podaną w tym przykładzie token JWT:

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
   >Jeśli zostanie wyświetlony komunikat o błędzie *nazwa nie jest prawidłowa, podaj prawidłową nazwę*, oznacza to, że usługi Azure AD B2C pomyślnie wywołać usługi RESTful, gdy on przedstawiony certyfikat klienta. Następnym krokiem jest do weryfikacji certyfikatu.

## <a name="step-6-add-certificate-validation"></a>Krok 6: Dodaj sprawdzanie poprawności certyfikatu
Certyfikat klienta usługi Azure AD B2C wysyła do usługi RESTful zostały poddane weryfikacji przez platformę Azure App Service, z wyjątkiem sytuacji, aby sprawdzić, czy certyfikat istnieje. Sprawdzanie poprawności certyfikatu jest odpowiedzialność aplikacji sieci web. 

W tej sekcji dodasz przykładowy kod platformy ASP.NET, która weryfikuje właściwości certyfikatu na potrzeby uwierzytelniania.

> [!NOTE]
>Aby uzyskać więcej informacji na temat konfigurowania usługi Azure App Service do uwierzytelniania certyfikatu klienta, zobacz [Konfigurowanie wzajemnego uwierzytelniania protokołu TLS dla aplikacji sieci web](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6.1 Dodawanie ustawień aplikacji do pliku web.config projektu
W projekcie Visual Studio, który został utworzony wcześniej, należy dodać następujące ustawienia aplikacji, aby *web.config* pliku po `appSettings` elementu:

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Zastąp certyfikatu **nazwy podmiotu**, **Nazwa wystawcy**, i **odcisk palca certyfikatu** wartości wartościami własnego certyfikatu.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6.2 Dodaj funkcję IsValidClientCertificate
Otwórz *Controllers\IdentityController.cs* pliku, a następnie dodaj do `Identity` kontrolera klasy następującą funkcję: 

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

W poprzednim kodzie przykład możemy zaakceptować certyfikat jako prawidłowe tylko wtedy, gdy są spełnione wszystkie następujące warunki:
* Certyfikat nie wygasł i jest aktywna przez bieżący czas na serwerze.
* `Subject` Nazwa certyfikatu jest równa `ClientCertificate:Subject` wartość ustawienia aplikacji.
* `Issuer` Nazwa certyfikatu jest równa `ClientCertificate:Issuer` wartość ustawienia aplikacji.
* `thumbprint` Certyfikatu jest równa `ClientCertificate:Thumbprint` wartość ustawienia aplikacji.

>[!IMPORTANT]
>W zależności od stopnia usługi może być konieczne dodanie więcej operacji sprawdzania poprawności. Na przykład może być konieczne Sprawdź, czy powiązany ten certyfikat zaufanego głównego urzędu certyfikacji, sprawdzanie poprawności nazwy organizacji wystawcy i tak dalej.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6.3 Wywołaj funkcję IsValidClientCertificate
Otwórz *Controllers\IdentityController.cs* pliku a następnie na początku `SignUp()` działa, Dodaj poniższy fragment kodu: 

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Po dodaniu tego fragmentu kodu z `Identity` kontroler powinien wyglądać podobnie do poniższego kodu:

![Dodać kod sprawdzania poprawności certyfikatu](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>Krok 7: Publikowanie projektu na platformie Azure i przetestować go
1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **Contoso.AADB2C.API** projektu, a następnie wybierz **Publikuj**.

2. Powtórz "Krok 6" i ponownie przetestuj niestandardowych zasad weryfikacji certyfikatu. Spróbuj uruchomić zasady i upewnij się, że wszystko działa po dodaniu sprawdzania poprawności.

3. W swojej *web.config* plików, zmień wartość właściwości `ClientCertificate:Subject` do **nieprawidłowy**. Ponownie uruchom zasad i powinien zostać wyświetlony komunikat o błędzie.

4. Zmień wartość z powrotem na **prawidłowe**i upewnij się, że zasady można wywołać interfejsu API REST.

Jeśli trzeba rozwiązać tego kroku, zobacz [zbieranie dzienników za pomocą usługi Application Insights](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcjonalnie) Pobierz pliki zasad kompletny i kodu
* Po ukończeniu [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) wskazówki, firma Microsoft zaleca tworzenie scenariusza za pomocą plików zasad niestandardowych. Dla Twojej informacji udostępniliśmy [przykładowe pliki zasad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert).
* Możesz pobrać kompletny kod z [przykładowe rozwiązanie Visual Studio dla odwołania](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API). 
