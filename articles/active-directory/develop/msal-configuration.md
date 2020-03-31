---
title: Plik konfiguracyjny Android MSAL | Azure
titleSuffix: Microsoft identity platform
description: Omówienie pliku konfiguracji biblioteki uwierzytelniania firmy Android Microsoft (MSAL), który reprezentuje konfigurację aplikacji w usłudze Azure Active Directory.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/12/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 9e35ba5a3f3705a52e80262da9bbfbfda489bf83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050377"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>Plik konfiguracji Biblioteki uwierzytelniania Microsoft systemu Android

Biblioteka uwierzytelniania Microsoft systemu Android (MSAL) jest dostarczana z [domyślnym plikiem JSON konfiguracji,](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json) który można dostosować do definiowania zachowania publicznej aplikacji klienckiej dla rzeczy, takich jak domyślny urząd, które będą używane i tak dalej.

Ten artykuł pomoże Ci zrozumieć różne ustawienia w pliku konfiguracyjnym i jak określić plik konfiguracyjny do użycia w aplikacji opartej na msal.

## <a name="configuration-settings"></a>Ustawienia konfiguracji

### <a name="general-settings"></a>Ustawienia ogólne

| Właściwość | Typ danych | Wymagany | Uwagi |
|-----------|------------|-------------|-------|
| `client_id` | Ciąg | Tak | Identyfikator klienta aplikacji ze [strony Rejestracja aplikacji](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `redirect_uri`   | Ciąg | Tak | Identyfikator URI przekierowania aplikacji ze [strony Rejestracja aplikacji](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `authorities` | >\<urzędu listy | Nie | Lista urzędów, które aplikacja potrzebuje |
| `authorization_user_agent` | Agent Autoryzacji (wyliczenia) | Nie | Możliwe `DEFAULT`wartości: `BROWSER`, ,`WEBVIEW` |
| `http` | Konfiguracja http | Nie | Konfigurowanie `HttpUrlConnection` `connect_timeout` i`read_timeout` |
| `logging` | Konfiguracja rejestrowania | Nie | Określa poziom szczegółowości rejestrowania. Opcjonalne konfiguracje `pii_enabled`obejmują: , który ma `log_level`wartość `ERROR`logiczną, i , który trwa , `WARNING`, `INFO`, lub `VERBOSE`. |

### <a name="client_id"></a>client_id

Identyfikator klienta lub identyfikator aplikacji, który został utworzony podczas rejestracji aplikacji.

### <a name="redirect_uri"></a>redirect_uri

Identyfikator URI przekierowania zarejestrowany podczas rejestracji aplikacji. Jeśli identyfikator URI przekierowania jest do aplikacji brokera, zapoznaj się z [przekierowanie identyfikatora URI dla publicznych aplikacji klienckich,](msal-client-application-configuration.md#redirect-uri-for-public-client-apps) aby upewnić się, że używasz poprawnego formatu URI przekierowania dla aplikacji brokera.

### <a name="authorities"></a>Władze

Lista organów, które są znane i zaufane przez Ciebie. Oprócz wymienionych tutaj władz MSAL wysyła również zapytania firmy Microsoft, aby uzyskać listę chmur i urzędów znanych firmie Microsoft. Na tej liście urzędów określ typ urzędu i wszelkie `"audience"`dodatkowe parametry opcjonalne, takie jak , które powinny być zgodne z odbiorcami aplikacji na podstawie rejestracji aplikacji. Poniżej znajduje się przykładowa lista organów:

```javascript
// Example AzureAD and Personal Microsoft Account
{
    "type": "AAD",
    "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
    },
    "default": true // Indicates that this is the default to use if not provided as part of the acquireToken or acquireTokenSilent call
},
// Example AzureAD My Organization
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMyOrg",
        "tenantId": "contoso.com" // Provide your specific tenant ID here
    }
},
// Example AzureAD Multiple Organizations
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMultipleOrgs"
    }
},
//Example PersonalMicrosoftAccount
{
    "type": "AAD",
    "audience": {
        "type": "PersonalMicrosoftAccount"
    }
}
```

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>Mapuj uprawnienia AAD & odbiorców do punktów końcowych platformy tożsamości firmy Microsoft

| Typ | Grupy odbiorców | Identyfikator dzierżawy | Authority_Url | Wynikowy punkt końcowy | Uwagi |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADandPersonalMicrosoftaccount | | | `https://login.microsoftonline.com/common` | `common`jest aliasem dzierżawy dla tego, gdzie znajduje się konto. Takie jak określona dzierżawa usługi Azure Active Directory lub system kont Microsoft. |
| AAD | AzureADMyOrg | contoso.com | | `https://login.microsoftonline.com/contoso.com` | Tylko konta obecne w contoso.com mogą uzyskać token. Każda zweryfikowana domena lub identyfikator GUID dzierżawy może służyć jako identyfikator dzierżawy. |
| AAD | AzureADMultipleOrgs | | | `https://login.microsoftonline.com/organizations` | Tylko konta usługi Azure Active Directory mogą być używane z tym punktem końcowym. Konta Microsoft mogą być członkami organizacji. Aby uzyskać token przy użyciu konta Microsoft dla zasobu w organizacji, należy określić dzierżawę organizacyjną, z której chcesz token. |
| AAD | Konto PersonalMicrosoftAccount | | | `https://login.microsoftonline.com/consumers` | Tylko konta Microsoft mogą używać tego punktu końcowego. |
| B2C | | | Zobacz Wynikowy punkt końcowy | `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/` | Tylko konta obecne w contoso.onmicrosoft.com dzierżawcy mogą uzyskać token. W tym przykładzie zasady B2C jest częścią ścieżki adresu URL urzędu. |

> [!NOTE]
> Nie można włączyć i wyłączyć sprawdzania poprawności urzędu w urzędzie.
> Urzędy są znane jako deweloper, jak określono w konfiguracji lub znane firmie Microsoft za pośrednictwem metadanych.
> Jeśli MSAL odbiera żądanie tokenu do nieznanego urzędu, wyniki `MsalClientException` typu. `UnknownAuthority`

#### <a name="authority-properties"></a>Właściwości urzędu

| Właściwość | Typ danych  | Wymagany | Uwagi |
|-----------|-------------|-----------|--------|
| `type` | Ciąg | Tak | Odzwierciedla grupę odbiorców lub konto, aby wpisać cele aplikacji. Możliwe wartości: `AAD`,`B2C` |
| `audience` | Obiekt | Nie | Dotyczy tylko wpisywać/ .`AAD` Określa tożsamość obiektów docelowych aplikacji. Użyj wartości z rejestracji aplikacji |
| `authority_url` | Ciąg | Tak | Wymagane tylko przy`B2C`type= . Określa adres URL urzędu lub zasady, których aplikacja powinna używać  |
| `default` | wartość logiczna | Tak | Jeden `"default":true` jest wymagany, gdy określony jest jeden lub więcej organów. |

#### <a name="audience-properties"></a>Właściwości odbiorców

| Właściwość | Typ danych  | Wymagany | Uwagi |
|-----------|-------------|------------|-------|
| `type` | Ciąg | Tak | Określa odbiorców, na które aplikacja chce kierować reklamy. Możliwe `AzureADandPersonalMicrosoftAccount`wartości: `PersonalMicrosoftAccount` `AzureADMultipleOrgs`, , ,`AzureADMyOrg` |
| `tenant_id` | Ciąg | Tak | Wymagane tylko `"type":"AzureADMyOrg"`wtedy, gdy . Opcjonalnie `type` dla innych wartości. Może to być domena `contoso.com`dzierżawy, taka jak `72f988bf-86f1-41af-91ab-2d7cd011db46`identyfikator dzierżawy, taki jak ) |

### <a name="authorization_user_agent"></a>authorization_user_agent

Wskazuje, czy podczas logowania konta lub autoryzowania dostępu do zasobu ma być używany osadzony widok internetowy, czy domyślna przeglądarka na urządzeniu.

Możliwe wartości:
- `DEFAULT`: Preferuje przeglądarkę system. Używa osadzonego widoku sieci Web, jeśli przeglądarka nie jest dostępna na urządzeniu.
- `WEBVIEW`: Użyj osadzonego widoku sieci Web.
- `BROWSER`: Używa domyślnej przeglądarki na urządzeniu.

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

W przypadku klientów obsługujących `true`wiele chmur krajowych należy określić opcję . Platforma tożsamości firmy Microsoft automatycznie przekieruje do właściwej chmury krajowej podczas autoryzacji i realizacji tokenów. Chmurę krajową zalogowanego konta można określić, badając organ `AuthenticationResult`skojarzony z programem . Należy zauważyć, że `AuthenticationResult` nie zapewnia adres końcowy specyficzne dla chmury krajowej zasobu, dla którego żądasz tokenu.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Wartość logiczna wskazująca, czy używasz brokera microsoft identity zgodne w brokera przekierowania identyfikatora URI. Ustaw, `false` jeśli nie chcesz korzystać z brokera w aplikacji.

Jeśli korzystasz z urzędu AAD z `"MicrosoftPersonalAccount"`ustawioną na Grupę odbiorców, broker nie będzie używany.

### <a name="http"></a>http

Konfigurowanie ustawień globalnych dla limitów czasu HTTP, takich jak:

| Właściwość | Typ danych | Wymagany | Uwagi |
| ---------|-----------|------------|--------|
| `connect_timeout` | int | Nie | Czas w milisekundach |
| `read_timeout` | int | Nie | Czas w milisekundach |

### <a name="logging"></a>rejestrowanie

Następujące ustawienia globalne są do rejestrowania:

| Właściwość | Typ danych  | Wymagany | Uwagi |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | wartość logiczna | Nie | Czy emitować dane osobowe |
| `log_level`   | wartość logiczna | Nie | Które komunikaty dziennika do wyjścia |
| `logcat_enabled` | wartość logiczna | Nie | Czy do wyjścia do logowania cat oprócz interfejsu rejestrowania |

### <a name="account_mode"></a>account_mode

Określa, ile kont może być używanych w aplikacji naraz. Możliwe wartości są następujące:

- `MULTIPLE`(Domyślnie)
- `SINGLE`

Skonstruowanie `PublicClientApplication` przy użyciu trybu konta, który nie pasuje do tego ustawienia spowoduje wyjątek.

Aby uzyskać więcej informacji na temat różnic między pojedynczymi i wieloma kontami, zobacz [Aplikacje jedno i wiele kont](single-multi-account.md).

### <a name="browser_safelist"></a>browser_safelist

Lista przeglądarek zgodnych z msal. Te przeglądarki poprawnie obsługują przekierowania do intencji niestandardowych. Możesz dodać do tej listy. Wartość domyślna jest dostępna w konfiguracji domyślnej pokazanej poniżej.
``
## <a name="the-default-msal-configuration-file"></a>Domyślny plik konfiguracyjny MSAL

Domyślna konfiguracja MSAL, która jest dostarczana z MSAL jest pokazana poniżej. Możesz zobaczyć najnowszą wersję na [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json).

Ta konfiguracja jest uzupełniona o podane wartości. Podane wartości zastępują wartości domyślne.

```javascript
{
  "authorities": [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      },
      "default": true
    }
  ],
  "authorization_user_agent": "DEFAULT",
  "multiple_clouds_supported": false,
  "broker_redirect_uri_registered": false,
  "http": {
    "connect_timeout": 10000,
    "read_timeout": 30000
  },
  "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": false
  },
  "shared_device_mode_supported": false,
  "account_mode": "MULTIPLE",
  "browser_safelist": [
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "45"
    },
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "57"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "4.0"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.cloudmosa.puffinFree",
      "browser_signature_hashes": [
        "1WqG8SoK2WvE4NTYgr2550TRhjhxT-7DWxu6C_o6GrOLK6xzG67Hq7GCGDjkAFRCOChlo2XUUglLRAYu3Mn8Ag=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.duckduckgo.mobile.android",
      "browser_signature_hashes": [
        "S5Av4cfEycCvIvKPpKGjyCuAE5gZ8y60-knFfGkAEIZWPr9lU5kA7iOAlSZxaJei08s0ruDvuEzFYlmH-jAi4Q=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.explore.web.browser",
      "browser_signature_hashes": [
        "BzDzBVSAwah8f_A0MYJCPOkt0eb7WcIEw6Udn7VLcizjoU3wxAzVisCm6bW7uTs4WpMfBEJYf0nDgzTYvYHCag=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.ksmobile.cb",
      "browser_signature_hashes": [
        "lFDYx1Rwc7_XUn4KlfQk2klXLufRyuGHLa3a7rNjqQMkMaxZueQfxukVTvA7yKKp3Md3XUeeDSWGIZcRy7nouw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.microsoft.emmx",
      "browser_signature_hashes": [
        "Ivy-Rk6ztai_IudfbyUrSHugzRqAtHWslFvHT0PTvLMsEKLUIgv7ZZbVxygWy_M5mOPpfjZrd3vOx3t-cA6fVQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.browser",
      "browser_signature_hashes": [
        "FIJ3IIeqB7V0qHpRNEpYNkhEGA_eJaf7ntca-Oa_6Feev3UkgnpguTNV31JdAmpEFPGNPo0RHqdlU0k-3jWJWw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.mini.native",
      "browser_signature_hashes": [
        "TOTyHs086iGIEdxrX_24aAewTZxV7Wbi6niS2ZrpPhLkjuZPAh1c3NQ_U4Lx1KdgyhQE4BiS36MIfP6LbmmUYQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "mobi.mgeek.TunnyBrowser",
      "browser_signature_hashes": [
        "RMVoXuK1sfJZuGZ8onG1yhMc-sKiAV2NiB_GZfdNlN8XJ78XEE2wPM6LnQiyltF25GkHiPN2iKQiGwaO2bkyyQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "org.mozilla.focus",
      "browser_signature_hashes": [
        "L72dT-stFqomSY7sYySrgBJ3VYKbipMZapmUXfTZNqOzN_dekT5wdBACJkpz0C6P0yx5EmZ5IciI93Q0hq0oYA=="
      ],
      "browser_use_customTab" : false
    }
  ]
}
```
## <a name="example-basic-configuration"></a>Przykładowa konfiguracja podstawowa

Poniższy przykład ilustruje podstawową konfigurację, która określa identyfikator klienta, przekierowanie identyfikatora URI, czy przekierowanie brokera jest zarejestrowany i listę urzędów.

```javascript
{
  "client_id" : "4b0db8c2-9f26-4417-8bde-3f0e3656f8e0",
  "redirect_uri" : "msauth://com.microsoft.identity.client.sample.local/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      }
      "default": true
    }
  ]
}
```

## <a name="how-to-use-a-configuration-file"></a>Jak korzystać z pliku konfiguracyjnego

1. Utwórz plik konfiguracyjny. Zaleca się utworzenie niestandardowego pliku `res/raw/auth_config.json`konfiguracyjnego w programie . Ale można umieścić go w dowolnym miejscu, że chcesz.
2. Powiedz msal, gdzie szukać konfiguracji podczas `PublicClientApplication`konstruowania . Przykład:

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
