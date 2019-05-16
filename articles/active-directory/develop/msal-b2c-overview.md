---
title: Dowiedz się, jak zintegrować z usługą Azure AD B2C przy użyciu Microsoft Authentication Library (MSAL)
description: Microsoft Authentication Library (MSAL) umożliwia deweloperom aplikacji Integracja z usługą Azure AD B2C i uzyskać tokeny w celu wywołania interfejsów API sieci Web zabezpieczonych. Te interfejsy API sieci Web może być programu Microsoft Graph, inne APIS firmy Microsoft, interfejsów API sieci Web innych firm lub własnego interfejsu API sieci Web.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6af78a593fb8fadb836d2dc4c02115d95a7f2712
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546030"
---
# <a name="integrate-microsoft-authentication-library-msal-with-azure-active-directory-b2c"></a>Integracja Microsoft Authentication Library (MSAL) z usługą Azure Active Directory B2C

Microsoft Authentication Library (MSAL) umożliwia deweloperom aplikacji do uwierzytelniania użytkowników za pomocą tożsamości społecznościowych i lokalnych za pomocą [Azure Active Directory (Azure AD) B2C](https://docs.microsoft.com/azure/active-directory-b2c/). Usługa Azure Active Directory (Azure AD) B2C jest usługą zarządzania tożsamościami, która pozwala na dostosowywanie i kontrolowanie sposobu, w jaki klienci tworzą konto, logują się i zarządzają swoimi profilami podczas korzystania z Twoich aplikacji.

Usługa Azure Active Directory (Azure AD) B2C umożliwia również oznaczyć i dostosowywanie interfejsu użytkownika (UI) aplikacji w taki sposób, aby zapewnić bezproblemowe środowisko do klienta.

W tym samouczku pokazano, jak użyć Microsoft Authentication Library (MSAL) do integracji z usługą Azure Active Directory (Azure AD) B2C.


## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie zostało jeszcze utworzone własne [dzierżawy usługi Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), utwórz ją teraz. Można użyć istniejącej dzierżawy usługi Azure AD B2C. 

## <a name="javascript"></a>Javascript

Poniższe czynności, pokazuje, jak aplikacja jednostronicowa może używać usługi Azure AD B2C do rejestracji i logowania użytkowników i wywoływać chroniony internetowy interfejs API.

### <a name="step-1-register-your-application"></a>Krok 1: Zarejestruj swoją aplikację

Aby zaimplementować uwierzytelniania, należy najpierw zarejestrować aplikację. Aby zarejestrować aplikację, wykonaj [Zarejestruj swoją aplikację](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) szczegółowy opis kroków.

### <a name="steps-2-download-applications"></a>Kroki 2: Pobierz aplikacje

Pobierz plik zip lub sklonuj przykład z witryny GitHub.
>klona usługi git https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git

### <a name="steps-3-authentication"></a>Kroki 3: Authentication

1. Otwórz plik index.html w próbce.

2. Konfigurowanie przykładowych aplikacji, identyfikator i klucz zapisaną wcześniej podczas rejestrowania aplikacji. Zmień następujące wiersze kodu, zastępując wartości nazwami Twojego katalogu i interfejsów API:

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>",
        b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/hello/demo.read"],
        webApi: 'http://localhost:5000/hello',
  };

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

Nazwa [przepływ użytkownika](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) używanych w tym samouczku jest B2C_1_signupsignin1. Jeśli używasz innej usługi flow nazwy użytkownika użyj nazwy przepływu użytkownika w wartości urzędu.


### <a name="configure-application-to-use-b2clogincom"></a>Konfigurowanie aplikacji do użycia `b2clogin.com`

Możesz użyć `b2clogin.com` zamiast `login.microsoftonline.com` jako Przekierowanie adresu url, podczas konfigurowania dostawcy tożsamości w celu rejestracji i logowania w aplikacji Azure Active Directory (Azure AD) B2C.

**`b2clogin.com`** tj 
`https://your-tenant-name.b2clogin.com/your-tenant-guid` Służy do następujących:

- Zmniejszono ilość miejsca na używana w nagłówku plików cookie przez usługi Microsoft Azure.
- Adresami URL nie będzie zawierać odniesienie do firmy Microsoft. Na przykład aplikacja usługi Azure AD B2C prawdopodobnie odwołuje się do login.microsoftonline.com


 Aby użyć "z usługi b2clogin.com", musisz zaktualizować konfiguracji aplikacji.  

1. ValidateAuthority aktualizacji: Ustaw **validateAuthority** właściwość `false`. Gdy **validateAuthority** jest ustawiona na wartość FAŁSZ, przekierowania mogą z usługi b2clogin.com.

Poniższy przykład pokazuje, jak można ustawić właściwości:
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
> Aplikacja usługi Azure AD B2C prawdopodobnie odnosi się do login.microsoftonline.com w kilku miejscach, takie jak odwołania do przepływu użytkownika i token punktów końcowych. Upewnij się, że usługi punktu końcowego autoryzacji punktu końcowego tokenu i wystawcy zostały zaktualizowanie, aby użyć name.b2clogin.com Twojej dzierżawy.

Postępuj zgodnie z tym [przykładowe MSAL JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) dotyczące sposobu używania Microsoft Authentication Library w wersji zapoznawczej dla języka JavaScript (msal.js), aby uzyskać token dostępu i wywołać interfejs API zabezpieczony przez usługę Azure AD B2C.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o usługach:

- [Zasady niestandardowe](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Dostosowywanie interfejsu użytkownika](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)