---
title: Dowiedz się, jak aplikacje mogą współdziałać z Azure AD B2C przy użyciu biblioteki uwierzytelniania firmy Microsoft
description: Biblioteka Microsoft Authentication Library (MSAL) umożliwia aplikacjom współdziałanie z Azure AD B2C i pozyskiwanie tokenów do wywoływania zabezpieczonych interfejsów API sieci Web. Te interfejsy API sieci Web mogą być Microsoft Graph, inne interfejsy API firmy Microsoft, interfejsy API sieci Web od innych, lub własny internetowy interfejs API.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0eea0fd03b1df49e912a867b0c667ff0fd28c08a
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097617"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>Używanie biblioteki uwierzytelniania firmy Microsoft do współpracy z usługą Azure Active Directory B2C

Biblioteka Microsoft Authentication Library (MSAL) umożliwia deweloperom aplikacji uwierzytelnianie użytkowników za pomocą tożsamości społecznościowych i lokalnych przy użyciu [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Azure AD B2C to usługa zarządzania tożsamościami. Korzystając z tej funkcji, można dostosowywać i kontrolować sposób tworzenia kont i logowania klientów oraz zarządzania nimi przy użyciu aplikacji.

Azure AD B2C umożliwia również markę i dostosowanie interfejsu użytkownika aplikacji, aby zapewnić klientom bezproblemowe środowisko.

W tym samouczku pokazano, jak używać MSAL do współpracy z Azure AD B2C.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie utworzono jeszcze własnej dzierżawy [Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), utwórz ją teraz. Możesz również użyć istniejącej dzierżawy Azure AD B2C.

## <a name="javascript"></a>JavaScript

Poniższe kroki przedstawiają sposób, w jaki aplikacja jednostronicowa może używać Azure AD B2C do rejestrowania, logowania i wywoływania chronionego internetowego interfejsu API.

### <a name="step-1-register-your-application"></a>Krok 1: Zarejestruj swoją aplikację

Aby zaimplementować uwierzytelnianie, należy najpierw zarejestrować aplikację. Aby uzyskać szczegółowe instrukcje, zobacz artykuł [Rejestrowanie aplikacji](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) .

### <a name="step-2-download-the-sample-application"></a>Krok 2: Pobieranie przykładowej aplikacji

Pobierz przykład jako plik zip lub Sklonuj go z witryny GitHub:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Krok 3: Konfigurowanie uwierzytelniania

1. Otwórz plik **index. html** w przykładzie.

1. Skonfiguruj przykład przy użyciu identyfikatora klienta i klucza, który został zarejestrowany wcześniej podczas rejestrowania aplikacji. Zmień następujące wiersze kodu, zastępując wartości nazwami Twojego katalogu i interfejsów API:

   ```javascript
   // The current application coordinates were pre-registered in a B2C tenant.

    var appConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };

    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902" //This is your client/application ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    // create UserAgentApplication instance
    const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   ```

Nazwa [przepływu użytkownika](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) w tym samouczku to **B2C_1_signupsignin1**. Jeśli używasz innej nazwy przepływu użytkownika, ustaw wartość **urzędu** na tę nazwę.

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>Krok 4: Skonfiguruj aplikację do użycia`b2clogin.com`

Zamiast tego `b2clogin.com` `login.microsoftonline.com` można użyć jako adresu URL przekierowania. Należy to zrobić w aplikacji Azure AD B2C podczas konfigurowania dostawcy tożsamości na potrzeby rejestracji i logowania.

`b2clogin.com` Użycie w`https://your-tenant-name.b2clogin.com/your-tenant-guid` kontekście ma następujące skutki:

- Usługi firmy Microsoft zużywają mniej miejsca w nagłówku pliku cookie.
- Adresy URL nie zawierają już odwołania do firmy Microsoft. Na przykład aplikacja Azure AD B2C prawdopodobnie odwołuje się do `login.microsoftonline.com`programu.

 Aby użyć `b2clogin.com`programu, należy zaktualizować konfigurację aplikacji.  

- Ustaw właściwość **validateAuthority** na `false`, aby przekierować przy użyciu `b2clogin.com` może wystąpić.

Poniższy przykład pokazuje, jak można ustawić właściwość:

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
        validateAuthority: false;

};
// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

> [!NOTE]
> Aplikacja Azure AD B2C prawdopodobnie odwołuje się `login.microsoftonline.com` do programu w kilku miejscach, takich jak odwołania do przepływu użytkownika i punkty końcowe tokenu. Upewnij się, że punkt końcowy autoryzacji, punkt końcowy tokenu i wystawca zostały zaktualizowane do `your-tenant-name.b2clogin.com`użycia.

Postępuj zgodnie z [tym przykładem w języku JavaScript MSAL](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) na temat korzystania z wersji zapoznawczej MSAL języka JavaScript (MSAL. js). Przykład pobiera token dostępu i wywołuje interfejs API zabezpieczony przez Azure AD B2C.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usługach:

- [Zasady niestandardowe](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Dostosowanie interfejsu użytkownika](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)