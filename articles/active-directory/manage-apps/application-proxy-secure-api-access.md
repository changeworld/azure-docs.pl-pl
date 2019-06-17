---
title: Dostęp do interfejsów API za pomocą serwera Proxy aplikacji usługi Azure AD lokalnych
description: Serwer Proxy aplikacji usługi Azure Active Directory umożliwia aplikacji natywnych, bezpieczny dostęp do interfejsów API i logiki biznesowej, które są hostowane w środowisku lokalnym lub na maszynach wirtualnych w chmurze.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: c2b99525e3d0a61c02dc502fcd0927ea65993e5b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108637"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Bezpieczny dostęp do interfejsów API w środowisku lokalnym, za pomocą serwera Proxy aplikacji usługi Azure AD

Może mieć logikę biznesową interfejsów API działających lokalnie lub hostowanych na maszynach wirtualnych w chmurze. Natywne aplikacje systemu Android, iOS, Mac lub Windows muszą wchodzić w interakcje z punktów końcowych interfejsu API, korzystanie z danych lub interakcji z użytkownikiem. Serwer Proxy aplikacji usługi Azure AD i [interfejsów Azure Active Directory uwierzytelniania biblioteki (ADAL)](/azure/active-directory/develop/active-directory-authentication-libraries) pozwala aplikacji natywnych bezpieczny dostęp do interfejsów API w środowisku lokalnym. Usługa Azure Active Directory serwera Proxy aplikacji jest szybsze i bardziej bezpiecznego rozwiązania niż otwarcie portów zapory i sterowanie uwierzytelniania i autoryzacji w warstwie aplikacji. 

W tym artykule przedstawiono konfigurowanie rozwiązania serwera Proxy aplikacji usługi Azure AD dla usługi interfejsu API sieci web, który natywne aplikacje mogą uzyskiwać dostęp do hosta. 

## <a name="overview"></a>Omówienie

Na poniższym diagramie przedstawiono sposób tradycyjny publikowanie interfejsów API w środowisku lokalnym. Takie podejście wymaga otwierania portów przychodzących 80 i 443.

![Tradycyjny dostęp do interfejsu API](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

Na poniższym diagramie przedstawiono, jak serwer Proxy aplikacji usługi Azure AD umożliwia bezpieczne publikowanie interfejsów API bez konieczności otwierania żadnych portów przychodzących:

![Dostęp interfejsu API serwera Proxy aplikacji usługi AD Azure](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Serwer Proxy aplikacji usługi AD systemu Azure stanowi szkielet rozwiązania, działa jako publiczny punkt końcowy, aby uzyskać dostęp do interfejsu API i zapewnienia uwierzytelniania i autoryzacji. Dostępne interfejsy API z szeroką gamę platform za pomocą [ADAL](/azure/active-directory/develop/active-directory-authentication-libraries) bibliotek. 

Ponieważ serwer Proxy aplikacji usługi Azure AD, uwierzytelnianie i autoryzacja są zbudowane na podstawie usługi Azure AD, można użyć dostępu warunkowego usługi Azure AD, aby upewnić się, że tylko zaufane urządzenia mają dostęp do interfejsów API opublikowane za pośrednictwem serwera Proxy aplikacji. Użyj usługi Azure AD Join lub dołączono do usługi Azure AD hybrydowego dla komputerów stacjonarnych i zarządzane przez usługę Intune dla urządzeń. Możesz również skorzystać z korzystać z funkcji usługi Azure Active Directory — wersja Premium, takich jak uwierzytelnianie wieloskładnikowe systemu Azure i maszyn opartych na learning bezpieczeństwa [Azure Identity Protection](/azure/active-directory/active-directory-identityprotection).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skorzystać z tego przewodnika, potrzebne są:

- Dostęp administracyjny do katalogu platformy Azure przy użyciu konta, które można tworzyć i rejestrowanie aplikacji
- Przykładowy internetowy interfejs API i aplikacje klienta natywnego z [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>Publikowanie interfejsu API za pośrednictwem serwera Proxy aplikacji

Aby opublikować interfejs API spoza sieci intranet za pośrednictwem serwera Proxy aplikacji, należy wykonać tego samego wzorca, jak w przypadku publikowania aplikacji sieci web. Aby uzyskać więcej informacji, zobacz [Samouczek: Dodawanie aplikacji w środowisku lokalnym dostępu zdalnego za pośrednictwem serwera Proxy aplikacji w usłudze Azure Active Directory](application-proxy-add-on-premises-application.md).

Aby opublikować SecretAPI interfejsu API sieci web za pośrednictwem serwera Proxy aplikacji:

1. Twórz i Publikuj przykładowy projekt SecretAPI jako aplikację sieci web ASP.NET na komputerze lokalnym lub w sieci intranet. Upewnij się, że możesz uzyskiwać dostęp do aplikacji sieci web w środowisku lokalnym. 
   
1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **usługi Azure Active Directory** w nawigacji po lewej stronie. Następnie na **Przegląd** wybierz opcję **aplikacje dla przedsiębiorstw**.
   
1. W górnej części **aplikacje w przedsiębiorstwie — wszystkie aplikacje** wybierz opcję **nową aplikację**.
   
1. Na **dodać aplikację** w obszarze **Dodaj własną aplikację**, wybierz opcję **aplikacje lokalne**. 
   
1. Jeśli nie masz zainstalowanego łącznika serwera Proxy aplikacji, zostanie wyświetlony monit go zainstalować. Wybierz **pobieranie łącznika serwera Proxy aplikacji** do pobrania i zainstalowania łącznika. 
   
1. Po zainstalowaniu łącznika serwera Proxy aplikacji, na **Dodaj własną aplikację w środowisku lokalnym** strony:
   
   1. Wprowadź *SecretAPI* obok **nazwa**.
      
   1. Wprowadź adres URL umożliwia dostęp do interfejsu API w sieci intranet, obok **wewnętrzny adres Url**. 
      
   1. Upewnij się, że **wstępnego uwierzytelniania** ustawiono **usługi Azure Active Directory**. 
      
   1. Wybierz **Dodaj** w górnej części strony i zaczekaj, aż aplikacja ma zostać utworzony.
   
   ![Dodawanie aplikacji interfejsu API](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. Na **aplikacje w przedsiębiorstwie — wszystkie aplikacje** wybierz opcję **SecretAPI** aplikacji. 
   
1. Na **SecretAPI — omówienie** wybierz opcję **właściwości** w nawigacji po lewej stronie.
   
1. Nie ma interfejsów API mają być dostępne dla użytkowników końcowych w **MyApps** panelu, więc ustaw **widoczne dla użytkowników** do **nie** w dolnej części **właściwości**strony, a następnie wybierz pozycję **Zapisz**.
   
   ![Nie są widoczne dla użytkowników](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
Udostępniono interfejsu API sieci web za pośrednictwem serwera Proxy aplikacji usługi Azure AD. Teraz Dodaj użytkowników, którzy mogą uzyskiwać dostęp do aplikacji. 

1. Na **SecretAPI — omówienie** wybierz opcję **użytkowników i grup** w nawigacji po lewej stronie.
   
1. Na **użytkowników i grup** wybierz opcję **Dodaj użytkownika**.  
   
1. Na **Dodaj przypisanie** wybierz opcję **użytkowników i grup**. 
   
1. Na **użytkowników i grup** strony, wyszukaj i wybierz użytkowników, którzy mogą uzyskiwać dostęp do aplikacji, co najmniej włącznie ze sobą. Po wybraniu wszystkich użytkowników, wybierz **wybierz**. 
   
   ![Wybierz, a następnie przypisz użytkownika](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. Po powrocie **Dodaj przydziału** wybierz opcję **przypisać**. 

> [!NOTE]
> Interfejsy API, które korzystania ze zintegrowanego uwierzytelniania Windows może wymagać [dodatkowe kroki](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd).

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Rejestrowanie aplikacji natywnych i udzielanie dostępu do interfejsu API

Natywne aplikacje są programy zaprojektowane w celu użycia na konkretnej platformie lub urządzeniu. Zanim aplikacji natywnej mogą nawiązywać połączenia i dostęp do interfejsu API, należy zarejestrować go w usłudze Azure AD. Poniższe kroki pokazują, jak zarejestrować aplikację natywną i zapewnia dostęp do internetowego interfejsu API opublikowane za pośrednictwem serwera Proxy aplikacji.

Aby zarejestrować aplikację natywną AppProxyNativeAppSample:

1. W usłudze Azure Active Directory **Przegląd** wybierz opcję **rejestracje aplikacji**i w górnej części **rejestracje aplikacji** okienku wybierz **nowej rejestracji** .
   
1. Na **rejestrowania aplikacji** strony:
   
   1. W obszarze **nazwa**, wprowadź *AppProxyNativeAppSample*. 
      
   1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**. 
      
   1. W obszarze **adresu URL przekierowania**, listy rozwijanej i wybierz **klientem publicznym (mobilnych i klasycznych)** , a następnie wprowadź *https:\//appproxynativeapp*. 
      
   1. Wybierz **zarejestrować**i poczekaj na jej do zarejestrowania pomyślnie. 
      
      ![Rejestrowanie nowej aplikacji](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
Teraz zarejestrowanego w aplikacji AppProxyNativeAppSample w usłudze Azure Active Directory. Aby zapewnić aplikacji natywnej dostęp do SecretAPI internetowego interfejsu API:

1. W usłudze Azure Active Directory **Przegląd** > **rejestracje aplikacji** wybierz opcję **AppProxyNativeAppSample** aplikacji. 
   
1. Na **AppProxyNativeAppSample** wybierz opcję **uprawnienia do interfejsu API** w nawigacji po lewej stronie. 
   
1. Na **uprawnienia do interfejsu API** wybierz opcję **Dodaj uprawnienia**.
   
1. W pierwszym **uprawnienia do żądania interfejsu API** wybierz opcję **Moja organizacja korzysta z interfejsów API** kartę, a następnie wyszukaj i wybierz **SecretAPI**. 
   
1. Na następnej **uprawnienia do żądania interfejsu API** strony, zaznacz pole wyboru obok pozycji **user_impersonation**, a następnie wybierz pozycję **Dodaj uprawnienia**. 
   
    ![Wybieranie interfejsu API](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. Po powrocie **uprawnienia do interfejsu API** strony, można wybrać **udzielić zgody administratora firmy Contoso** aby uniemożliwić innym użytkownikom konieczności indywidualnie wyrażenia zgody na aplikację. 

## <a name="configure-the-native-app-code"></a>Konfigurowanie kodu aplikacji natywnej

Ostatnim krokiem jest skonfigurowanie aplikacji natywnej. Poniższy fragment kodu z *Form1.cs* biblioteki ADAL do uzyskania tokenu do żądania wywołania interfejsu API i dołączyć go jako elementu nośnego w nagłówku aplikacji powoduje, że plik NativeClient przykładową aplikację. 
   
   ```csharp
       AuthenticationResult result = null;
       HttpClient httpClient = new HttpClient();
       authContext = new AuthenticationContext(authority);
       result = await authContext.AcquireTokenAsync(todoListResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
       
       // Append the token as bearer in the request header.
       httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
       
       // Call the API.
       HttpResponseMessage response = await httpClient.GetAsync(todoListBaseAddress + "/api/values/4");
   
       // MessageBox.Show(response.RequestMessage.ToString());
       string s = await response.Content.ReadAsStringAsync();
       MessageBox.Show(s);
   ```
   
Aby skonfigurować aplikację natywną, aby nawiązać połączenie z usługą Azure Active Directory i wywołanie serwera Proxy aplikacji interfejsu API, zaktualizuj wartości symboli zastępczych w *App.config* pliku NativeClient przykładową aplikację przy użyciu wartości z usługi Azure AD: 

- Wklej **identyfikator katalogu (dzierżawcy)** w `<add key="ida:Tenant" value="" />` pola. Możesz znaleźć i skopiuj tę wartość (GUID) z **Przegląd** strony jednej z aplikacji. 
  
- Wklej AppProxyNativeAppSample **identyfikator aplikacji (klienta)** w `<add key="ida:ClientId" value="" />` pola. Możesz znaleźć i skopiuj tę wartość (GUID) z AppProxyNativeAppSample **Przegląd** strony.
  
- Wklej AppProxyNativeAppSample **identyfikator URI przekierowania** w `<add key="ida:RedirectUri" value="" />` pola. Możesz znaleźć i skopiuj tę wartość (URI) z AppProxyNativeAppSample **uwierzytelniania** strony. 
  
- Wklej SecretAPI **identyfikator URI Identyfikatora aplikacji** w `<add key="todo:TodoListResourceId" value="" />` pola. Możesz znaleźć i skopiuj tę wartość (URI) z SecretAPI **uwidaczniania interfejsu API** strony.
  
- Wklej SecretAPI **adres URL strony głównej** w `<add key="todo:TodoListBaseAddress" value="" />` pola. Możesz znaleźć i skopiuj tę wartość (URL) z SecretAPI **znakowania** strony.

Po skonfigurowaniu parametrów, tworzenie i uruchamianie aplikacji natywnej. Po wybraniu **Sign In** przycisku aplikacji pozwala zalogować, a następnie wyświetla ekran powodzenia, aby upewnić się, że pomyślnie połączona SecretAPI.

![Powodzenie](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Kolejne kroki

- [Samouczek: Dodawanie aplikacji w środowisku lokalnym dostępu zdalnego za pośrednictwem serwera Proxy aplikacji w usłudze Azure Active Directory](application-proxy-add-on-premises-application.md)
- [Szybki start: Konfigurowanie aplikacji klienckiej dostęp do interfejsów API sieci web](../develop/quickstart-configure-app-access-web-apis.md)
- [Jak włączyć natywne aplikacje klienckie do interakcji z serwera proxy aplikacji](application-proxy-configure-native-client-application.md)
