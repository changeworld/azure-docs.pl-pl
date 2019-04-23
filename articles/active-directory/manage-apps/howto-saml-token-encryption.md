---
title: Szyfrowanie tokenu języka SAML w usłudze Azure Active Directory
description: Dowiedz się, jak skonfigurować szyfrowanie tokenu języka SAML usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: celested
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7de6705ad38133b8321caabb7b0f4093284af503
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799358"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption-preview"></a>Instrukcje: Konfigurowanie szyfrowania tokenu języka SAML programu Azure AD (wersja zapoznawcza)

> [!NOTE]
> Szyfrowanie tokenu jest funkcją premium usługi Azure Active Directory (Azure AD). Aby dowiedzieć się więcej na temat usługi Azure AD wersjach, funkcje i ceny, zobacz [cennika usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

Szyfrowanie tokenu języka SAML umożliwia korzystanie z zaszyfrowanego twierdzenia SAML z aplikacją, która go obsługuje. Po skonfigurowaniu aplikacji usługi Azure AD spowoduje zaszyfrowanie twierdzenia SAML, które on emituje dla tej aplikacji przy użyciu klucza publicznego otrzymane od certyfikatu przechowywanego w usłudze Azure AD. Aplikacja musi używać zgodnego klucza prywatnego do odszyfrowania token, zanim będzie można jej używać jako dowód uwierzytelniania dla zalogowanego użytkownika.

Szyfrowanie twierdzenia SAML między usługą Azure AD i aplikacja udostępnia dodatkową pewność, że nie można przechwytywać zawartość tokenu i danych osobistych lub firmowych naruszenia zabezpieczeń.

Nawet bez szyfrowania tokenu tokeny Azure AD SAML nigdy nie są przekazywane w sieci niezaszyfrowane. Usługa Azure AD wymaga wymiany tokenu żądania/odpowiedzi pozwalają na zaszyfrowane kanały protokołu HTTPS/TLS tak, aby komunikacja między dostawcy tożsamości, przeglądarki i aplikacje odbywać się za pośrednictwem łącza zaszyfrowane. Należy wziąć pod uwagę wartości szyfrowania tokenu w danej sytuacji, w porównaniu z związanym z zarządzaniem dodatkowych certyfikatów.   

Do skonfigurowania szyfrowania tokenu, należy przekazać plik certyfikatu X.509, który zawiera klucz publiczny, który obiekt aplikacji usługi Azure AD, który reprezentuje aplikację. W celu uzyskania certyfikatu X.509 możesz pobrać go z aplikacji sam lub get przypadków z dostawcą aplikacji, w którym dostawca aplikacji udostępnia klucze szyfrowania lub w przypadkach, w którym aplikacja oczekuje, musisz podać klucz prywatny, może być utworzony za pomocą narzędzia kryptografii, część klucza prywatnego przekazany do magazynu kluczy aplikacji i zgodnego certyfikatu klucza publicznego przekazany do usługi Azure AD.

Usługa Azure AD używa algorytmu AES-256 do szyfrowania danych potwierdzenie SAML.

## <a name="configure-saml-token-encryption"></a>Skonfiguruj szyfrowanie tokenu języka SAML

Aby skonfigurować szyfrowanie tokenu języka SAML, wykonaj następujące kroki:

1. Uzyskaj certyfikat klucza publicznego, który pasuje do klucza prywatnego, który jest skonfigurowany w aplikacji.

    Utwórz asymetryczną parę kluczy do użycia podczas szyfrowania. Lub, jeśli aplikacja dostarcza klucza publicznego do użycia podczas szyfrowania, postępuj zgodnie z instrukcjami aplikacji, aby pobrać certyfikat X.509.

    Klucz publiczny powinny być przechowywane w pliku certyfikatu X.509 w formacie cer.

    Jeśli aplikacja używa klucza, które tworzysz dla swojego wystąpienia, postępuj zgodnie z instrukcjami wyświetlanymi przez aplikację do instalowania klucza prywatnego, który aplikacja będzie używany do odszyfrowywania tokenów z dzierżawą usługi Azure AD.

1. Dodaj certyfikat do konfiguracji aplikacji w usłudze Azure AD.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Aby skonfigurować szyfrowanie token w witrynie Azure portal

Można dodać certyfikatu publicznego do konfiguracji aplikacji w witrynie Azure portal.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

1. Przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw** bloku, a następnie wybierz aplikację, którą chcesz skonfigurować szyfrowania tokenu dla.

1. Na stronie aplikacji wybierz **tokenem szyfrowania**.

    ![Opcja szyfrowania tokenu w witrynie Azure portal](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > **Tokenem szyfrowania** opcja jest dostępna tylko dla aplikacji SAML, które zostały skonfigurowane z **aplikacje dla przedsiębiorstw** bloku w witrynie Azure portal, z poziomu galerii aplikacji lub Aplikacja spoza galerii. Dla innych aplikacji ta opcja jest wyłączona. Dla aplikacji jest rejestrowane za pomocą **rejestracje aplikacji** środowisko w witrynie Azure portal, którą można skonfigurować szyfrowania dla manifestu tokeny SAML za pomocą aplikacji, za pomocą programu Microsoft Graph lub programu PowerShell.

1. Na **tokenem szyfrowania** wybierz opcję **zaimportować certyfikat** można zaimportować plik cer zawierający publicznego certyfikatu X.509.

    ![Zaimportuj plik cer zawierający certyfikat X.509](./media/howto-saml-token-encryption/import-certificate-small.png)

1. Po zaimportowaniu certyfikatu i klucza prywatnego jest skonfigurowana do użycia na stronie aplikacji, Aktywuj szyfrowania, wybierając **...**  dalej, aby stan odcisku palca, a następnie wybierz **aktywować szyfrowania tokenu** opcję z menu rozwijanego.

1. Wybierz **tak** o potwierdzenie aktywacji certyfikat szyfrowania tokenu.

1. Upewnij się, że twierdzenia SAML emitowane dla aplikacji są szyfrowane.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Aby dezaktywować szyfrowania tokenu w witrynie Azure portal

1. W witrynie Azure portal przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw**, a następnie wybierz aplikację, która ma włączone szyfrowanie tokenu języka SAML.

1. Na stronie aplikacji wybierz **tokenem szyfrowania**, Znajdź certyfikat, a następnie wybierz **...**  opcję, aby wyświetlić menu rozwijanego.

1. Wybierz **dezaktywować szyfrowania tokenu**.

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>Konfigurowanie przy użyciu interfejsu API programu Graph, programu PowerShell lub manifest aplikacji szyfrowanie tokenu języka SAML

Certyfikaty szyfrowania są przechowywane w obiekcie aplikacji w usłudze Azure AD za pomocą `encrypt` tagu użycia. Można skonfigurować wiele certyfikatów szyfrowania i jedną, która jest aktywny przez szyfrowanie tokenów jest identyfikowany przez `tokenEncryptionKeyID` atrybutu.

Potrzebna będzie identyfikator obiektu aplikacji do skonfigurowania szyfrowania tokenu przy użyciu interfejsu API Microsoft Graph lub programu PowerShell. Możesz znaleźć tę wartość, programowo lub przechodząc do aplikacji **właściwości** strony w witrynie Azure portal i notujący **obiektu o identyfikatorze** wartość.

Po skonfigurowaniu keyCredential, przy użyciu programu Graph, programu PowerShell lub w manifeście aplikacji powinna Wygeneruj identyfikator GUID do użytku identyfikator klucza.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Aby skonfigurować szyfrowanie tokenu przy użyciu programu Microsoft Graph

1. Aktualizuj aplikację `keyCredentials` za pomocą certyfikatu X.509 do szyfrowania. Poniższy przykład pokazuje, jak to zrobić.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid>

    { 
       "keyCredentials":[ 
          { 
             "type":"AsymmetricX509Cert","usage":"Encrypt",
             "keyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35",    (Use a GUID generator to obtain a value for the keyId)
             "key": "MIICADCCAW2gAwIBAgIQ5j9/b+n2Q4pDvQUCcy3…"  (Base64Encoded .cer file)
          }
        ]
    }
    ```

1. Zidentyfikuj certyfikat szyfrowania, który jest aktywny w celu szyfrowania tokenów. Poniższy przykład pokazuje, jak to zrobić.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>Aby skonfigurować szyfrowanie tokenu przy użyciu programu PowerShell

Ta funkcja będzie dostępna wkrótce. 

<!--
1. Use the latest Azure AD PowerShell module to connect to your tenant.

1. Set the token encryption settings using the **[Set-AzureApplication](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview)** command.

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials “<KeyCredentialsObject>”  -TokenEncryptionKeyId <keyID>
    ```

1. Read the token encryption settings using the following commands.

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

-->

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Aby skonfigurować szyfrowanie tokenu przy użyciu manifest aplikacji

1. W witrynie Azure portal, przejdź do **usługi Azure Active Directory > Rejestracje aplikacji**.

1. Wybierz **wszystkie aplikacje** z listy rozwijanej, aby wyświetlić wszystkie aplikacje, a następnie wybierz aplikacji przedsiębiorstwa, który chcesz skonfigurować.

1. Na stronie aplikacji wybierz **manifestu** edytować [manifest aplikacji](../develop/reference-app-manifest.md).

1. Ustaw wartość `tokenEncryptionKeyId` atrybutu.

    W poniższym przykładzie przedstawiono manifest aplikacji skonfigurowany z dwiema certyfikaty szyfrowania, a za pomocą drugiego wybrany jako aktywna przy użyciu tokenEnryptionKeyId.

    ```json
    { 
      "id": "3cca40e2-367e-45a5-8440-ed94edd6cc35",
      "accessTokenAcceptedVersion": null,
      "allowPublicClient": false,
      "appId": "cb2df8fb-63c4-4c35-bba5-3d659dd81bf1",
      "appRoles": [],
      "oauth2AllowUrlPathMatching": false,
      "createdDateTime": "2017-12-15T02:10:56Z",
      "groupMembershipClaims": "SecurityGroup",
      "informationalUrls": { 
         "termsOfService": null, 
         "support": null, 
         "privacy": null, 
         "marketing": null 
      },
      "identifierUris": [ 
        "https://testapp"
      ],
      "keyCredentials": [ 
        { 
          "customKeyIdentifier": "Tog/O1Hv1LtdsbPU5nPphbMduD=", 
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "8be4cb65-59d9-404a-a6f5-3d3fb4030351", 
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest" 
        }, 
        {
          "customKeyIdentifier": "U5nPphbMduDmr3c9Q3p0msqp6eEI=",
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851",
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest2" 
        } 
      ], 
      "knownClientApplications": [], 
      "logoUrl": null, 
      "logoutUrl": null, 
      "name": "Test SAML Application", 
      "oauth2AllowIdTokenImplicitFlow": true, 
      "oauth2AllowImplicitFlow": false, 
      "oauth2Permissions": [], 
      "oauth2RequirePostResponse": false, 
      "orgRestrictions": [], 
      "parentalControlSettings": { 
         "countriesBlockedForMinors": [], 
         "legalAgeGroupRule": "Allow" 
        }, 
      "passwordCredentials": [], 
      "preAuthorizedApplications": [], 
      "publisherDomain": null, 
      "replyUrlsWithType": [], 
      "requiredResourceAccess": [], 
      "samlMetadataUrl": null, 
      "signInUrl": "https://127.0.0.1:444/applications/default.aspx?metadata=customappsso|ISV9.1|primary|z" 
      "signInAudience": "AzureADMyOrg",
      "tags": [], 
      "tokenEncryptionKeyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851" 
    }  
    ```

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, [jak usługa Azure AD korzysta z protokołu SAML](../develop/active-directory-saml-protocol-reference.md)
* Dowiedz się, format, zabezpieczenia właściwości i zawartość [tokeny SAML w usłudze Azure AD](../develop/reference-saml-tokens.md)