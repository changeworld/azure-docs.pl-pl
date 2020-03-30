---
title: Korzystanie z usługi MSAL w usłudze Azure Active Directory B2CLearn | Azure
titleSuffix: Microsoft identity platform
description: Biblioteka uwierzytelniania firmy Microsoft (MSAL) umożliwia aplikacjom współdziałanie z usługą Azure AD B2C i uzyskiwanie tokenów do wywoływania zabezpieczonych interfejsów API sieci Web. Te internetowe interfejsy API mogą być microsoft graph, inne interfejsy API firmy Microsoft, interfejsy API sieci web od innych lub własnego interfejsu API sieci web.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: e25564e64410701754390024a5bcfd39321343e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696456"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>Współdziałanie z usługą Azure Active Directory B2C za pomocą biblioteki uwierzytelniania firmy Microsoft

Biblioteka uwierzytelniania firmy Microsoft (MSAL) umożliwia deweloperom aplikacji uwierzytelnianie użytkowników przy użyciu tożsamości społecznościowych i lokalnych przy użyciu [usługi Azure Active Directory B2C (Azure AD B2C).](https://docs.microsoft.com/azure/active-directory-b2c/) Usługa Azure AD B2C to usługa zarządzania tożsamościami. Korzystając z niego, można dostosować i kontrolować, jak klienci zarejestrować się, zalogować się i zarządzać swoimi profilami podczas korzystania z aplikacji.

Usługa Azure AD B2C umożliwia również znakowanie i dostosowywanie interfejsu użytkownika aplikacji, aby zapewnić bezproblemowe środowisko klientom.

W tym samouczku pokazano, jak używać usługi MSAL do współpracy z usługą Azure AD B2C.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie utworzono jeszcze własnej [dzierżawy usługi Azure AD B2C,](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)utwórz go teraz. Można również użyć istniejącej dzierżawy usługi Azure AD B2C.

## <a name="javascript"></a>JavaScript

Poniższe kroki pokazują, jak aplikacja jednostronicowa może używać usługi Azure AD B2C do rejestrowania się, logowania i wywoływania chronionego interfejsu API sieci Web.

### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji

Aby zaimplementować uwierzytelnianie, należy najpierw zarejestrować aplikację. Zobacz [Rejestrowanie aplikacji, aby](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) uzyskać szczegółowe kroki.

### <a name="step-2-download-the-sample-application"></a>Krok 2: Pobierz przykładową aplikację

Pobierz próbkę jako plik zip lub sklonuj go z GitHub:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Krok 3: Konfigurowanie uwierzytelniania

1. Otwórz plik **index.html** w próbce.

1. Skonfiguruj przykład przy przyuśle klienta i klucz, który został nagrany wcześniej podczas rejestrowania aplikacji. Zmień następujące wiersze kodu, zastępując wartości nazwami Twojego katalogu i interfejsów API:

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

Nazwa przepływu [użytkownika](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) w tym samouczku jest **B2C_1_signupsignin1**. Jeśli używasz innej nazwy przepływu użytkownika, ustaw wartość **urzędu** na tę nazwę.

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>Krok 4: Skonfiguruj aplikację do użycia`b2clogin.com`

Zamiast adresu `b2clogin.com` URL `login.microsoftonline.com` przekierowania można go używać. To zrobić w aplikacji usługi Azure AD B2C podczas konfigurowania dostawcy tożsamości do rejestracji i logowania.

Stosowanie `b2clogin.com` w kontekście `https://your-tenant-name.b2clogin.com/your-tenant-guid` ma następujące skutki:

- Usługi firmy Microsoft zajmują mniej miejsca w nagłówku plików cookie.
- Adresy URL nie zawierają już odwołania do firmy Microsoft. Na przykład aplikacja usługi Azure AD B2C prawdopodobnie odwołuje się do `login.microsoftonline.com`.

 Aby `b2clogin.com`użyć programu , należy zaktualizować konfigurację aplikacji.  

- Ustaw **właściwość validateAuthority** na , `false` `b2clogin.com` aby można było przekierować za pomocą.

W poniższym przykładzie pokazano, jak można ustawić właściwość:

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
> Aplikacja usługi Azure AD B2C `login.microsoftonline.com` prawdopodobnie odwołuje się do w kilku miejscach, takich jak odwołania przepływu użytkownika i punktów końcowych tokenu. Upewnij się, że punkt końcowy autoryzacji, token końcowy `your-tenant-name.b2clogin.com`i wystawca zostały zaktualizowane do użycia .

Postępuj zgodnie z [tym przykładem msal javascript,](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) jak korzystać z msal preview dla języka JavaScript (MSAL.js). Przykład pobiera token dostępu i wywołuje interfejs API zabezpieczony przez usługę Azure AD B2C.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usługach:

- [Zasady niestandardowe](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Dostosowywanie interfejsu użytkownika](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)