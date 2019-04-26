---
title: Publikowanie aplikacji klienta natywnego — usługa Azure AD | Dokumentacja firmy Microsoft
description: Opisano sposób włączania aplikacje klienta natywnego nawiązać połączenia z usługą platformy Azure łącznika serwera Proxy aplikacji usługi AD, aby zapewnić bezpieczny dostęp zdalny do aplikacji w środowisku lokalnym.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21e101dee878a48cce1005d51ad5e59166b0cfa1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60293323"
---
# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Jak włączyć aplikacje klienta natywnego do interakcji z serwera proxy aplikacji

Oprócz aplikacji sieci web serwer Proxy usługi Azure Active Directory aplikacji można również publikować aplikacje klienta natywnego, które są skonfigurowane przy użyciu usługi Azure AD Authentication Library (ADAL). Aplikacje klienta natywnego różnią się od aplikacji sieci web, ponieważ są zainstalowane na urządzeniu, natomiast aplikacje sieci web są dostępne za pośrednictwem przeglądarki. 

Serwer Proxy aplikacji obsługuje aplikacje klienta natywnego przyjmuje usługi Azure AD wystawionych tokenów wysyłany w nagłówku. Usługa serwera Proxy aplikacji jest przeprowadzane uwierzytelnianie w imieniu użytkowników. To rozwiązanie nie używa tokenów aplikacji do uwierzytelniania. 

![Relacja między użytkownikami końcowymi, usługi Azure Active Directory a opublikowane aplikacje](./media/application-proxy-configure-native-client-application/richclientflow.png)

Azure AD Authentication Library, która zajmuje się uwierzytelnieniem i obsługuje wiele środowisk klienta, należy używać do publikowania aplikacji natywnych. Serwer Proxy aplikacji jest dopasowywana do [natywną aplikację interfejsu API sieci Web scenariusza](../develop/native-app.md). 

W tym artykule przedstawiono cztery kroki publikowania aplikacji natywnej za pomocą serwera Proxy aplikacji i Azure AD Authentication Library. 

## <a name="step-1-publish-your-application"></a>Krok 1: Publikowanie aplikacji
Publikuj swoje aplikacje serwera proxy, jak w przypadku innych aplikacji i Przypisz użytkownikom uzyskiwanie dostępu do aplikacji. Aby uzyskać więcej informacji, zobacz [publikowania aplikacji za pomocą serwera Proxy aplikacji](application-proxy-add-on-premises-application.md).

## <a name="step-2-configure-your-application"></a>Krok 2: Konfigurowanie aplikacji
Skonfiguruj aplikację natywną w następujący sposób:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do **usługi Azure Active Directory** > **rejestracje aplikacji**.
3. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
4. Określ nazwę aplikacji, wybierz pozycję **natywnych** jako typ aplikacji i podaj identyfikator URI przekierowania dla aplikacji. 

   ![Tworzenie nowej rejestracji aplikacji](./media/application-proxy-configure-native-client-application/create.png)
5. Wybierz pozycję **Utwórz**.

Aby uzyskać szczegółowe informacje dotyczące tworzenia nowej rejestracji aplikacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).


## <a name="step-3-grant-access-to-other-applications"></a>Krok 3: Udzielanie dostępu do innych aplikacji
Włączanie aplikacji natywnej być udostępniane innym aplikacjom w katalogu:

1. Nadal w **rejestracje aplikacji**, wybierz nową aplikację native, która właśnie utworzony.
2. Wybierz **uprawnienia do interfejsu API**.
3. Wybierz **Dodaj uprawnienia**.
4. Pierwszym krokiem Otwórz **wybierz interfejs API**.
5. Użyj paska wyszukiwania, aby znaleźć aplikację serwera Proxy aplikacji, która została opublikowana w pierwszej sekcji. Wybierz aplikację, a następnie kliknij przycisk **wybierz**. 

   ![Wyszukaj aplikację serwera proxy](./media/application-proxy-configure-native-client-application/select_api.png)
6. Otwórz drugi etap **wybierz uprawnienia**.
7. Użyj pola wyboru, aby przyznać aplikacji natywnej dostęp do serwera proxy aplikacji, a następnie kliknij przycisk **wybierz**.

   ![Udzielanie dostępu do serwera proxy aplikacji](./media/application-proxy-configure-native-client-application/select_perms.png)
8. Wybierz pozycję **Done** (Gotowe).


## <a name="step-4-edit-the-active-directory-authentication-library"></a>Krok 4: Edytuj biblioteki uwierzytelniania usługi Active Directory
Edytuj kod aplikacji macierzystej w kontekście uwierzytelniania programu Active Directory Authentication Library (ADAL) do uwzględnienia następujący tekst:

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

Zmienne w przykładowym kodzie powinna zostać zastąpiona w następujący sposób:

* **Identyfikator dzierżawy** można znaleźć w witrynie Azure portal. Przejdź do **usługi Azure Active Directory** > **właściwości** i skopiuj nazwę katalogu. 
* **Zewnętrzny adres URL** frontonu adres URL wprowadzony w aplikacji serwera Proxy. Aby znaleźć tę wartość, przejdź do **serwera proxy aplikacji** części aplikacji serwera proxy.
* **Identyfikator aplikacji** natywnych aplikacji można znaleźć na **właściwości** strony natywnej aplikacji.
* **Identyfikator URI przekierowania aplikacji natywnej** znajduje się na **identyfikatory URI przekierowań** strony natywnej aplikacji.

Po biblioteki ADAL jest edytowany przy użyciu tych parametrów, użytkownicy powinno być możliwe uwierzytelnienie aplikacje klienta natywnego, nawet wtedy, gdy są one poza siecią firmową. 

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o przepływie aplikacja natywna, zobacz [aplikacji natywnej do interfejsu API sieci web](../develop/native-app.md)

Dowiedz się więcej o konfigurowaniu [logowanie jednokrotne dla serwera Proxy aplikacji](what-is-single-sign-on.md#single- sign-on-methods)
