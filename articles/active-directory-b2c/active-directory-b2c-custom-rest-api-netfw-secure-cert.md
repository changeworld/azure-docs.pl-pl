---
title: Zabezpieczanie usługi RESTful przy użyciu certyfikatów klientów w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Zabezpieczenia sieci niestandardowe wymiany oświadczenia interfejsu API REST w sieci Azure AD B2C przy użyciu certyfikatów klientów
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 09/25/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 004577ead56befce02771b82ace088706e8f0c3c
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "34709210"
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>Zabezpieczanie usługi RESTful przy użyciu certyfikatów klientów

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W artykule powiązane należy [tworzenia usługi RESTful](active-directory-b2c-custom-rest-api-netfw.md) który współdziała z usługi Azure Active Directory B2C (Azure AD B2C).

W tym artykule możesz dowiedzieć się, jak ograniczyć dostęp do aplikacji sieci web platformy Azure (interfejs API RESTful) przy użyciu certyfikatu klienta. Mechanizm ten nosi nazwę wzajemnego uwierzytelniania TLS, lub *uwierzytelnianie certyfikatu klienta*. Tylko usługi, które mają odpowiednie certyfikaty, takich jak usługi Azure AD B2C można uzyskać dostęp do usługi.

>[!NOTE]
>Można również zabezpieczyć przy użyciu usługi RESTful [podstawowe uwierzytelnianie HTTP](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). Jednak uwierzytelnianie podstawowe HTTP jest uważany za mniej bezpieczne za pośrednictwem certyfikatu klienta. Nasze zalecenie jest zapewnienie usługi RESTful przy użyciu wstępnego uwierzytelniania certyfikatu klienta, zgodnie z opisem w tym artykule.

Szczegóły tego artykułu jak:
* Konfigurowanie aplikacji sieci web, aby użyć uwierzytelniania certyfikatu klienta.
* Przekaż certyfikat do kluczy zasad usługi Azure AD B2C.
* Konfigurowanie zasad niestandardowych do używania certyfikatu klienta.

## <a name="prerequisites"></a>Wymagania wstępne
* Wykonaj kroki [integracji interfejsu API REST oświadczeń wymiany](active-directory-b2c-custom-rest-api-netfw.md) artykułu.
* Pobierz certyfikat (plik pfx z kluczem prywatnym).

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>Krok 1: Konfigurowanie aplikacji sieci web uwierzytelniania certyfikatu klienta
Aby skonfigurować **usłudze Azure App Service** aby wymagać certyfikaty klienta, należy ustawić aplikacji sieci web `clientCertEnabled` lokacji ustawienie, aby *true*. Aby to zrobić, należy użyć interfejsu API REST. Ustawienie jest dostępne za pośrednictwem możliwości zarządzania w portalu Azure. Aby zlokalizować ustawienie, w aplikacji RESTful **ustawienia** menu, w obszarze **narzędzi programistycznych**, wybierz pozycję **Eksploratora zasobów**.

>[!NOTE]
>Upewnij się, że planu usługi aplikacji Azure jest Standard lub większą. Aby uzyskać więcej informacji, zobacz [szczegółowe omówienie planów usługi aplikacji Azure](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).


Użyj [Eksploratora zasobów Azure (wersja zapoznawcza)](https://resources.azure.com) można ustawić **clientCertEnabled** właściwości *true*, jak pokazano na poniższej ilustracji:

![Ustawienie clientCertEnabled za pomocą Eksploratora zasobów Azure](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-resource-explorer.png)

>[!NOTE]
>Aby uzyskać więcej informacji o ustawieniu **clientCertEnabled** właściwości, zobacz [Konfigurowanie TLS wzajemnego uwierzytelniania dla aplikacji sieci web](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

>[!TIP]
>Aby ułatwić spreparować wywołania interfejsu API REST, możesz też użyć [ARMClient](https://github.com/projectkudu/ARMClient) narzędzia.

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Krok 2: Przekaż certyfikat na klucze zasad usługi Azure AD B2C
Po ustawieniu `clientCertEnabled` do *true*, komunikacji z interfejsem API RESTful, wymaga certyfikatu klienta. Uzyskaj, przekazywanie i z certyfikatem klienta w dzierżawie usługi Azure AD B2C, wykonaj następujące czynności: 
1. W dzierżawie usługi Azure AD B2C, wybierz **ustawieniami B2C** > **Framework obsługi tożsamości**.

2. Zaznacz, aby wyświetlić klucze, które są dostępne w Twojej dzierżawie **klucze zasad**.

3. Wybierz pozycję **Dodaj**.  
    **Utwórz klucz** zostanie otwarte okno.

4. W **opcje** wybierz opcję **przekazać**.

5. W **nazwa** wpisz **B2cRestClientCertificate**.  
    Prefiks *B2C_1A_* jest automatycznie dodawany.

6. W **przekazywania pliku** wybierz plik .pfx certyfikatu z kluczem prywatnym.

7. W **hasło** wpisz hasło do certyfikatu.

    ![Przekazywanie klucza zasad](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. Wybierz pozycję **Utwórz**.

8. Aby wyświetlić klucze, które są dostępne w Twojej dzierżawie i upewnij się, że utworzono `B2C_1A_B2cRestClientCertificate` klucza, wybierz pozycję **klucze zasad**.

## <a name="step-3-change-the-technical-profile"></a>Krok 3: Zmiany techniczne profilu
Aby obsługiwać uwierzytelnianie certyfikatu klienta w zasadach niestandardowych, należy zmienić techniczne profil w następujący sposób:

1. W katalogu roboczym, otwórz *TrustFrameworkExtensions.xml* rozszerzenia pliku zasad.

2. Wyszukaj `<TechnicalProfile>` węzła, który zawiera `Id="REST-API-SignUp"`.

3. Zlokalizuj `<Metadata>` elementu.

4. Zmień *AuthenticationType* do *ClientCertificate*w następujący sposób:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. Natychmiast po upływie `<Metadata>` elementu, Dodaj następujący fragment kodu XML: 

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    Po dodaniu fragmentu profilu techniczne powinna wyglądać następujący kod XML:

    ![Ustaw elementy XML uwierzytelniania ClientCertificate](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Krok 4: Przekaż zasady dla Twojej dzierżawy

1. W [portalu Azure](https://portal.azure.com), przełącz się do [kontekstu dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), a następnie wybierz **usługi Azure AD B2C**.

2. Wybierz **Framework obsługi tożsamości**.

3. Wybierz **wszystkich zasad**.

4. Wybierz **przekazywać zasady**.

5. Wybierz **zastąpić zasady, jeśli istnieje** pole wyboru.

6. Przekaż *TrustFrameworkExtensions.xml* pliku, a następnie upewnij się, że przekazaniem sprawdzania poprawności.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Krok 5: Testowanie zasad niestandardowych za pomocą Uruchom teraz
1. Otwórz **ustawienia usługi Azure AD B2C**, a następnie wybierz **Framework obsługi tożsamości**.

    >[!NOTE]
    >Uruchom teraz wymaga co najmniej jednej aplikacji można preregistered dla dzierżawcy. Aby dowiedzieć się, jak zarejestrować aplikacji, zapoznaj się z usługi Azure AD B2C [wprowadzenie](active-directory-b2c-get-started.md) artykułu lub [Rejestracja aplikacji](active-directory-b2c-app-registration.md) artykułu.

2. Otwórz **B2C_1A_signup_signin**, jednostki uzależnionej strony (RP) zasad niestandardowych, które można przekazać, a następnie wybierz **Uruchom teraz**.

3. Testowanie procesu, wpisując **testu** w **imię** pole.  
    Usługa Azure AD B2C wyświetla komunikat o błędzie w górnej części okna.    

    ![Testowanie tożsamości interfejsu API](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. W **imię** wpisz nazwę (innego niż "Test").  
    Usługa Azure AD B2C zarejestrowaniu użytkownika, a następnie wysyła numer lojalność do aplikacji. Zanotuj liczbę podaną w tym przykładzie JWT:

   ```
   {
     "typ": "JWT",
     "alg": "RS256",
     "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
   }.{
     "exp": 1507125903,
     "nbf": 1507122303,
     "ver": "1.0",
     "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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
   >Jeśli zostanie wyświetlony komunikat o błędzie *nazwa jest nieprawidłowa, podaj prawidłową nazwę*, oznacza to, że usługi Azure AD B2C pomyślnie wywoływana z usługą RESTful, gdy on przedstawiony certyfikat klienta. Następnym krokiem jest do weryfikacji certyfikatu.

## <a name="step-6-add-certificate-validation"></a>Krok 6: Dodawanie certyfikatu weryfikacji
Certyfikat klienta usługi Azure AD B2C wysyła do usługi RESTful nie podlegają weryfikacji przez platformę Azure Web Apps, z wyjątkiem przypadków, aby sprawdzić, czy certyfikat istnieje. Sprawdzanie poprawności certyfikatu jest odpowiedzialny za aplikacji sieci web. 

W tej sekcji możesz dodać przykładowy kod platformy ASP.NET, która weryfikuje właściwości certyfikatu na potrzeby uwierzytelniania.

> [!NOTE]
>Aby uzyskać więcej informacji dotyczących konfigurowania uwierzytelniania certyfikatu klienta usługi Azure App Service, zobacz [Konfigurowanie TLS wzajemnego uwierzytelniania dla aplikacji sieci web](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6.1 Dodaj ustawienia aplikacji do pliku web.config projektu
W projekt programu Visual Studio, który został utworzony wcześniej, należy dodać następujące ustawienia aplikacji, aby *web.config* plików po `appSettings` elementu:

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Zastąp certyfikatu **nazwa podmiotu**, **Nazwa wystawcy**, i **odcisk palca certyfikatu** wartości własnymi wartościami certyfikatu.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6.2 funkcja IsValidClientCertificate dodać
Otwórz *Controllers\IdentityController.cs* pliku, a następnie dodaj do `Identity` kontrolera klasy następujących funkcji: 

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

W poprzednim przykładowym kodzie możemy zaakceptować certyfikat jako prawidłowe tylko wtedy, gdy są spełnione wszystkie następujące warunki:
* Certyfikat nie utracił ważności i jest aktywny bieżący czas na serwerze.
* `Subject` Nazwa certyfikatu jest równa `ClientCertificate:Subject` wartość ustawienia aplikacji.
* `Issuer` Nazwa certyfikatu jest równa `ClientCertificate:Issuer` wartość ustawienia aplikacji.
* `thumbprint` Certyfikatu jest równa `ClientCertificate:Thumbprint` wartość ustawienia aplikacji.

>[!IMPORTANT]
>W zależności od czułość usługi może być konieczne Dodaj więcej operacji sprawdzania poprawności. Na przykład należy sprawdzić, czy powiązany ten certyfikat zaufanego głównego urzędu certyfikacji, sprawdzanie poprawności nazwy organizacji wystawcy i tak dalej.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6.3 wywołanie funkcji IsValidClientCertificate
Otwórz *Controllers\IdentityController.cs* pliku, a następnie na początku `SignUp()` funkcji, Dodaj poniższy fragment kodu: 

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Po dodaniu fragmentu, Twoje `Identity` kontroler powinien wyglądać podobnie do następującego kodu:

![Dodawanie certyfikatu weryfikacji kodu](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>Krok 7: Publikowanie projektu na platformie Azure i przetestować go
1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **Contoso.AADB2C.API** projektu, a następnie wybierz **publikowania**.

2. Powtórz "Krok 6", a następnie ponownie testów weryfikacji certyfikatu zasad niestandardowych. Spróbuj uruchomić zasady i upewnij się, że wszystko działa po dodaniu sprawdzania poprawności.

3. W Twojej *web.config* pliku, zmień wartość `ClientCertificate:Subject` do **nieprawidłowy**. Ponownie uruchom Zasady i powinien zostać wyświetlony komunikat o błędzie.

4. Zmień wartość z powrotem do **prawidłową**i upewnij się, że zasady można wywołać interfejsu API REST.

Jeśli potrzebujesz rozwiązać ten krok, zobacz [zbieranie dzienników przy użyciu usługi Application Insights](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcjonalnie) Pobierz pliki pełną zasad i kod
* Po ukończeniu [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) wskazówki, zaleca się tworzenia scenariusz przy użyciu plików zasady niestandardowe. Użytkownikowi, firma Microsoft umieściła [przykładowe pliki zasad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert).
* Możesz pobrać pełną kod z [przykładowe rozwiązanie Visual Studio dla odwołania](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API). 
