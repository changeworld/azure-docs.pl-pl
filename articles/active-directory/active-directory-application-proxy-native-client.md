---
title: Publikowanie aplikacji Aplikacja native client - usługi Azure AD | Dokumentacja firmy Microsoft
description: Uwzględniono również sposób umożliwić aplikacjom natywnego klienta do komunikowania się z łącznika serwera Proxy aplikacji usługi AD platformy Azure do zapewniania bezpiecznego dostępu zdalnego do aplikacji lokalnych.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: f0cae145-e346-4126-948f-3f699747b96e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 823939e76bcf982d37b58595795dca84c1830e19
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2018
---
# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Włączanie aplikacji klienta natywnego wchodzić w interakcje z serwera proxy aplikacji

Oprócz aplikacji sieci web serwer Proxy usługi Azure Active Directory aplikacji mogą służyć do publikowania aplikacji klientami, które są skonfigurowane z Azure AD Authentication Library (ADAL). Aplikacja Native client aplikacji różnią się od aplikacji sieci web, ponieważ są zainstalowane na urządzeniu, podczas gdy aplikacje sieci web są dostępne za pośrednictwem przeglądarki. 

Serwer Proxy aplikacji obsługuje aplikacje klienta natywnego akceptują usługi Azure AD wystawionych tokenów wysyłany w nagłówku. Usługa Serwer Proxy aplikacji przeprowadza uwierzytelnianie w imieniu użytkowników. To rozwiązanie nie korzysta z tokenów aplikacji uwierzytelniania. 

![Relacja między użytkowników końcowych, Azure Active Directory i opublikowanych aplikacji](./media/active-directory-application-proxy-native-client/richclientflow.png)

Azure AD Authentication Library, która zajmuje się uwierzytelniania i obsługuje wiele środowiska klienta, należy używać do publikowania natywnych aplikacji. Serwer Proxy aplikacji jest dopasowywana do [aplikacji natywnej do interfejsu API sieci Web scenariusza](develop/active-directory-authentication-scenarios.md#native-application-to-web-api). 

W tym artykule przedstawiono cztery kroki publikowania aplikacji natywnej z serwera Proxy aplikacji i Azure AD Authentication Library. 

## <a name="step-1-publish-your-application"></a>Krok 1: Publikowanie aplikacji
Publikowanie serwera proxy aplikacji, jak w przypadku innych aplikacji i przypisać użytkownikom uzyskiwanie dostępu do aplikacji. Aby uzyskać więcej informacji, zobacz [publikowania aplikacji za pomocą serwera Proxy aplikacji](active-directory-application-proxy-publish.md).

## <a name="step-2-configure-your-application"></a>Krok 2: Konfigurowanie aplikacji
Skonfiguruj natywnych aplikacji w następujący sposób:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do **usługi Azure Active Directory** > **rejestracji aplikacji**.
3. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
4. Określ nazwę aplikacji, wybierz pozycję **natywnego** jako typ aplikacji i podaj identyfikator URI przekierowania dla aplikacji. 

   ![Tworzenie nowej rejestracji aplikacji](./media/active-directory-application-proxy-native-client/create.png)
5. Wybierz pozycję **Utwórz**.

Aby uzyskać szczegółowe informacje dotyczące tworzenia nowej rejestracji aplikacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](.//develop/active-directory-integrating-applications.md).


## <a name="step-3-grant-access-to-other-applications"></a>Krok 3: Udzielanie dostępu do innych aplikacji
Włącz aplikacji natywnej uwidocznienia do innych aplikacji w katalogu:

1. Nadal **rejestracji aplikacji**, wybierz nową aplikację native nowo utworzony.
2. Wybierz **wymagane uprawnienia**.
3. Wybierz pozycję **Dodaj**.
4. Pierwszym krokiem, otwórz **wybierz interfejs API**.
5. Użyj pasek wyszukiwania, aby znaleźć aplikację serwera Proxy aplikacji, która została opublikowana w pierwszej sekcji. Wybierz tej aplikacji, a następnie kliknij przycisk **wybierz**. 

   ![Wyszukaj aplikację, serwer proxy](./media/active-directory-application-proxy-native-client/select_api.png)
6. Otwórz drugi etap **wybierz uprawnienia**.
7. Użyj pola wyboru, aby udzielić dostępu aplikacji natywnej do serwera proxy aplikacji, a następnie kliknij przycisk **wybierz**.

   ![Udziel dostępu do serwera proxy aplikacji](./media/active-directory-application-proxy-native-client/select_perms.png)
8. Wybierz **gotowe**.


## <a name="step-4-edit-the-active-directory-authentication-library"></a>Krok 4: Edytuj biblioteki uwierzytelniania usługi Active Directory
Edytuj kod natywnych aplikacji w kontekście uwierzytelniania programu Active Directory Authentication Library (ADAL) do uwzględnienia następujący tekst:

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

* **Identyfikator dzierżawy** można znaleźć w portalu Azure. Przejdź do **usługi Azure Active Directory** > **właściwości** i skopiuj nazwę katalogu. 
* **Zewnętrzny adres URL** jest adres URL frontonu wprowadzony w aplikacji serwera Proxy. Aby znaleźć tę wartość, przejdź do **serwera proxy aplikacji** części aplikacji serwera proxy.
* **Identyfikator aplikacji** natywnych aplikacji można znaleźć w **właściwości** strony natywnej aplikacji.
* **Identyfikator URI przekierowania aplikacji natywnej** można znaleźć w **identyfikator URI przekierowania** strony natywnej aplikacji.

Po modyfikacji biblioteki ADAL z tych parametrów użytkowników należy do uwierzytelniania do aplikacji natywnej klienta, nawet wtedy, gdy są one spoza sieci firmowej. 

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o przepływie natywnych aplikacji, zobacz [aplikacji natywnej do interfejsu API sieci web](develop/active-directory-authentication-scenarios.md#native-application-to-web-api)

Dowiedz się więcej o konfigurowaniu [logowanie jednokrotne dla serwera Proxy aplikacji](application-proxy-sso-overview.md)
