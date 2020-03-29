---
title: Szyfrowanie tokenów SAML w usłudze Azure Active Directory
description: Dowiedz się, jak skonfigurować szyfrowanie tokenów SAML usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: mimart
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0082d841faf22745e609d38444f4a97553b3c867
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365870"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption"></a>Jak: Konfigurowanie szyfrowania tokenów SAML usługi Azure AD

> [!NOTE]
> Szyfrowanie tokenów jest funkcją premium usługi Azure Active Directory (Azure AD). Aby dowiedzieć się więcej o wersjach, funkcjach i cenach usługi Azure AD, zobacz [Cennik usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

Szyfrowanie tokenów SAML umożliwia korzystanie z zaszyfrowanych potwierdzeń SAML z aplikacją, która go obsługuje. Po skonfigurowaniu dla aplikacji usługa Azure AD będzie szyfrować potwierdzenia SAML, które emituje dla tej aplikacji przy użyciu klucza publicznego uzyskanego z certyfikatu przechowywanego w usłudze Azure AD. Aplikacja musi użyć pasującego klucza prywatnego, aby odszyfrować token, zanim będzie można go użyć jako dowód uwierzytelniania dla zalogowanego użytkownika.

Szyfrowanie potwierdzeń SAML między usługą Azure AD a aplikacją zapewnia dodatkową pewność, że nie można przechwycić zawartości tokenu i naruszyć bezpieczeństwo danych osobowych lub firmowych.

Nawet bez szyfrowania tokenów tokenów SAML usługi Azure AD nigdy nie są przekazywane w sieci w sposób czysty. Usługa Azure AD wymaga wymiany żądań/odpowiedzi tokenu odbywa się za pośrednictwem zaszyfrowanych kanałów HTTPS/TLS, dzięki czemu komunikacja między IDP, przeglądarką i aplikacją odbywa się za pośrednictwem zaszyfrowanych łączy. Należy wziąć pod uwagę wartość szyfrowania tokenu dla sytuacji w porównaniu z obciążeniem związane z zarządzaniem dodatkowych certyfikatów.   

Aby skonfigurować szyfrowanie tokenu, należy przekazać plik certyfikatu X.509, który zawiera klucz publiczny do obiektu aplikacji usługi Azure AD, który reprezentuje aplikację. Aby uzyskać certyfikat X.509, można go pobrać z samej aplikacji lub pobrać go od dostawcy aplikacji w przypadkach, gdy dostawca aplikacji udostępnia klucze szyfrowania lub w przypadkach, gdy aplikacja oczekuje, że udostępnisz klucz prywatny, może być utworzone przy użyciu narzędzi kryptograficznych, części klucza prywatnego przekazanej do magazynu kluczy aplikacji oraz pasującego certyfikatu klucza publicznego przekazanego do usługi Azure AD.

Usługa Azure AD używa AES-256 do szyfrowania danych potwierdzenia SAML.

## <a name="configure-saml-token-encryption"></a>Konfigurowanie szyfrowania tokenów SAML

Aby skonfigurować szyfrowanie tokenów SAML, wykonaj następujące czynności:

1. Uzyskaj certyfikat klucza publicznego, który pasuje do klucza prywatnego skonfigurowanego w aplikacji.

    Utwórz asymetryczną parę kluczy do użycia do szyfrowania. Lub jeśli aplikacja dostarcza klucz publiczny do użycia do szyfrowania, postępuj zgodnie z instrukcjami aplikacji, aby pobrać certyfikat X.509.

    Klucz publiczny powinien być przechowywany w pliku certyfikatu X.509 w formacie cer.

    Jeśli aplikacja używa klucza, który można utworzyć dla wystąpienia, postępuj zgodnie z instrukcjami dostarczonymi przez aplikację do instalowania klucza prywatnego, który aplikacja będzie używać do odszyfrowywania tokenów z dzierżawy usługi Azure AD.

1. Dodaj certyfikat do konfiguracji aplikacji w usłudze Azure AD.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Aby skonfigurować szyfrowanie tokenów w witrynie Azure portal

Certyfikat publiczny można dodać do konfiguracji aplikacji w witrynie Azure portal.

1. Przejdź do [witryny Azure portal](https://portal.azure.com).

1. Przejdź do bloku **aplikacji usługi Azure Active Directory > Enterprise,** a następnie wybierz aplikację, dla której chcesz skonfigurować szyfrowanie tokenów.

1. Na stronie aplikacji wybierz pozycję **Szyfrowanie tokenów**.

    ![Opcja szyfrowania tokenów w witrynie Azure portal](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > Opcja **szyfrowania tokenu** jest dostępna tylko dla aplikacji SAML, które zostały skonfigurowane z bloku **aplikacji Enterprise** w portalu Azure portal, z Galerii aplikacji lub aplikacji spoza galerii. W przypadku innych aplikacji ta opcja menu jest wyłączona. W przypadku aplikacji zarejestrowanych za pośrednictwem **środowiska rejestracji aplikacji** w witrynie Azure portal można skonfigurować szyfrowanie tokenów SAML przy użyciu manifestu aplikacji za pośrednictwem programu Microsoft Graph lub za pośrednictwem programu PowerShell.

1. Na stronie **Szyfrowanie tokenu** wybierz pozycję **Importuj certyfikat,** aby zaimportować plik cer zawierający publiczny certyfikat X.509.

    ![Importowanie pliku cer zawierającego certyfikat X.509](./media/howto-saml-token-encryption/import-certificate-small.png)

1. Po zaimportowaniu certyfikatu i skonfigurowaniu klucza prywatnego do użycia po stronie aplikacji aktywuj szyfrowanie, wybierając **...** obok stanu odcisku palca, a następnie wybierz **pozycję Aktywuj szyfrowanie tokenu** z opcji w menu rozwijanym.

1. Wybierz **opcję Tak,** aby potwierdzić aktywację certyfikatu szyfrowania tokenu.

1. Upewnij się, że potwierdzenia SAML emitowane dla aplikacji są szyfrowane.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Aby dezaktywować szyfrowanie tokenów w witrynie Azure portal

1. W witrynie Azure portal przejdź do **usługi Azure Active Directory > aplikacje enterprise**, a następnie wybierz aplikację z włączonym szyfrowaniem tokenów SAML.

1. Na stronie aplikacji wybierz pozycję **Szyfrowanie tokenów**, znajdź certyfikat, a następnie wybierz opcję **...,** aby wyświetlić menu rozwijane.

1. Wybierz **pozycję Dezaktywuj szyfrowanie tokenu**.

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>Konfigurowanie szyfrowania tokenów SAML przy użyciu interfejsu API programu Graph, programu PowerShell lub manifestu aplikacji

Certyfikaty szyfrowania są przechowywane w obiekcie aplikacji w usłudze Azure AD z tagiem `encrypt` użycia. Można skonfigurować wiele certyfikatów szyfrowania, a ten, który jest aktywny `tokenEncryptionKeyID` dla szyfrowania tokenów jest identyfikowany przez atrybut.

Identyfikator obiektu aplikacji będzie potrzebny do skonfigurowania szyfrowania tokenów przy użyciu interfejsu API programu Microsoft Graph lub programu PowerShell. Tę wartość można znaleźć programowo lub przechodząc do strony **Właściwości** aplikacji w witrynie Azure portal i odnośną wartość **identyfikatora obiektu.**

Podczas konfigurowania keyCredential przy użyciu programu Graph, PowerShell lub w manifeście aplikacji, należy wygenerować identyfikator GUID do użycia dla keyId.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Aby skonfigurować szyfrowanie tokenów przy użyciu programu Microsoft Graph

1. Zaktualizuj `keyCredentials` aplikację za pomocą certyfikatu X.509 do szyfrowania. W przykładzie poniżej pokazano, jak to zrobić.

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

1. Zidentyfikuj certyfikat szyfrowania, który jest aktywny do szyfrowania tokenów. W przykładzie poniżej pokazano, jak to zrobić.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>Aby skonfigurować szyfrowanie tokenów przy użyciu programu PowerShell

1. Użyj najnowszego modułu programu Azure AD PowerShell, aby połączyć się z dzierżawą.

1. Ustaw ustawienia szyfrowania tokenu za pomocą polecenia **[Set-AzureApplication.](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview)**

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. Odczyt ustawień szyfrowania tokenu przy użyciu następujących poleceń.

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Aby skonfigurować szyfrowanie tokenów przy użyciu manifestu aplikacji

1. W witrynie Azure portal przejdź do **usługi Azure Active Directory > rejestracji aplikacji**.

1. Wybierz **pozycję Wszystkie aplikacje** z listy rozwijanej, aby wyświetlić wszystkie aplikacje, a następnie wybierz aplikację dla przedsiębiorstw, którą chcesz skonfigurować.

1. Na stronie aplikacji wybierz **manifest,** aby edytować [manifest aplikacji](../develop/reference-app-manifest.md).

1. Ustaw wartość atrybutu. `tokenEncryptionKeyId`

    W poniższym przykładzie przedstawiono manifest aplikacji skonfigurowany z dwoma certyfikatami szyfrowania, a drugi wybrany jako aktywny przy użyciu identyfikatora tokenEnryptionKeyId.

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

## <a name="next-steps"></a>Następne kroki

* Dowiedz [się, jak usługa Azure AD korzysta z protokołu SAML](../develop/active-directory-saml-protocol-reference.md)
* Poznaj format, charakterystykę zabezpieczeń i zawartość [tokenów SAML w usłudze Azure AD](../develop/reference-saml-tokens.md)