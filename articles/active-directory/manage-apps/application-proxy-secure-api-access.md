---
title: Dostęp do lokalnych interfejsów API za pomocą serwera proxy aplikacji usługi Azure AD
description: Serwer proxy aplikacji usługi Azure Active Directory umożliwia aplikacjom macierzystym bezpieczny dostęp do interfejsów API i logiki biznesowej hostanych lokalnie lub na maszynach wirtualnych w chmurze.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: ecd5d8bae22d67f8d9f5b99d5c94eecf54a4a1f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77166008"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Bezpieczny dostęp do lokalnych interfejsów API za pomocą serwera proxy aplikacji usługi Azure AD

Może być interfejsów API logiki biznesowej uruchomionych lokalnie lub hostowanych na maszynach wirtualnych w chmurze. Twoje natywne aplikacje z systemem Android, iOS, Mac lub Windows muszą wchodzić w interakcje z punktami końcowymi interfejsu API, aby korzystać z danych lub zapewniać interakcję z użytkownikiem. Usługi Azure AD Application Proxy i [biblioteki uwierzytelniania usługi Azure Active Directory (ADAL)](/azure/active-directory/develop/active-directory-authentication-libraries) umożliwiają aplikacjom macierzystym bezpieczny dostęp do lokalnych interfejsów API. Usługa Azure Active Directory Application Proxy to szybsze i bezpieczniejsze rozwiązanie niż otwieranie portów zapory i kontrolowanie uwierzytelniania i autoryzacji w warstwie aplikacji. 

W tym artykule oszukuje cię konfigurowanie rozwiązania serwera proxy aplikacji usługi Azure AD do obsługi usługi interfejsu API sieci Web, do której mogą uzyskiwać dostęp aplikacje natywne. 

## <a name="overview"></a>Omówienie

Na poniższym diagramie przedstawiono tradycyjny sposób publikowania lokalnych interfejsów API. Takie podejście wymaga otwarcia portów przychodzących 80 i 443.

![Tradycyjny dostęp do API](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

Na poniższym diagramie pokazano, jak można bezpiecznie publikować interfejsy API za pomocą serwera proxy aplikacji usługi Azure AD bez otwierania żadnych portów przychodzących:

![Dostęp do interfejsu API serwera proxy aplikacji usługi Azure AD](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Serwer proxy aplikacji usługi Azure AD stanowi szkielet rozwiązania, działa jako publiczny punkt końcowy dla dostępu do interfejsu API i zapewnia uwierzytelnianie i autoryzację. Dostęp do interfejsów API można uzyskać z szerokiej gamy platform przy użyciu bibliotek [ADAL.](/azure/active-directory/develop/active-directory-authentication-libraries) 

Ponieważ uwierzytelnianie i autoryzacja serwera proxy aplikacji usługi Azure AD są oparte na usłudze Azure AD, można użyć dostępu warunkowego usługi Azure AD, aby upewnić się, że tylko zaufane urządzenia mogą uzyskiwać dostęp do interfejsów API opublikowanych za pośrednictwem serwera proxy aplikacji. Użyj usługi Azure AD Join lub Azure AD Hybrid Joined dla komputerów stacjonarnych i usługi Intune managed dla urządzeń. Można również skorzystać z funkcji usługi Azure Active Directory Premium, takich jak uwierzytelnianie wieloskładnikowe platformy Azure, oraz zabezpieczeń usługi [Azure Identity Protection](/azure/active-directory/active-directory-identityprotection)opartych na uczeniu maszynowym.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten przewodnik, potrzebujesz:

- Dostęp administratora do katalogu platformy Azure z kontem, które może tworzyć i rejestrować aplikacje
- Przykładowy internetowy interfejs API i natywne aplikacje klienckie[https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>Publikowanie interfejsu API za pośrednictwem serwera proxy aplikacji

Aby opublikować interfejs API poza intranetem za pośrednictwem serwera proxy aplikacji, należy wykonać ten sam wzorzec, jak w przypadku publikowania aplikacji sieci web. Aby uzyskać więcej informacji, zobacz [Samouczek: Dodawanie aplikacji lokalnej do zdalnego dostępu za pośrednictwem serwera proxy aplikacji w usłudze Azure Active Directory](application-proxy-add-on-premises-application.md).

Aby opublikować internetowy interfejs API SecretAPI za pośrednictwem serwera proxy aplikacji:

1. Skompiluj i opublikuj przykładowy projekt SecretAPI jako ASP.NET aplikacji sieci web na komputerze lokalnym lub intranecie. Upewnij się, że możesz uzyskać dostęp do aplikacji internetowej lokalnie. 
   
1. W [witrynie Azure portal](https://portal.azure.com)wybierz pozycję **Azure Active Directory**. Następnie wybierz **aplikacje enterprise**.
   
1. U góry strony **Aplikacje enterprise — wszystkie aplikacje** wybierz pozycję **Nowa aplikacja**.
   
1. Na stronie **Dodawanie aplikacji** wybierz pozycję **Aplikacje lokalne**. Zostanie wyświetlona strona **Dodaj własną aplikację lokalną.**
   
1. Jeśli nie masz zainstalowanego łącznika serwera proxy aplikacji, zostanie wyświetlony monit o jego zainstalowanie. Wybierz **opcję Pobierz łącznik serwera proxy aplikacji,** aby pobrać i zainstalować łącznik. 
   
1. Po zainstalowaniu łącznika serwera proxy aplikacji na stronie **Dodaj własną aplikację lokalną:**
   
   1. Obok **pozycji Nazwa**wprowadź *wpis SecretAPI*.
      
   1. Obok **pozycji Wewnętrzny adres URL**wprowadź adres URL używany do uzyskiwania dostępu do interfejsu API z poziomu intranetu.
      
   1. Upewnij się, że **uwierzytelnianie wstępne** jest ustawione na **usługę Azure Active Directory**. 
      
   1. Wybierz **pozycję Dodaj** u góry strony i poczekaj na utworzenie aplikacji.
   
   ![Dodawanie aplikacji interfejsu API](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. Na stronie **Aplikacje enterprise — wszystkie aplikacje** wybierz aplikację **SecretAPI.** 
   
1. Na stronie **SecretAPI — przegląd** wybierz pozycję **Właściwości** w lewej nawigacji.
   
1. Nie chcesz, aby interfejsy API były dostępne dla użytkowników końcowych w panelu **MyApps,** więc ustaw **opcję Widoczne** dla użytkowników na **Nie** u dołu strony **Właściwości,** a następnie wybierz pozycję **Zapisz**.
   
   ![Niewidoczne dla użytkowników](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
Opublikowano interfejs API sieci web za pośrednictwem serwera proxy aplikacji usługi Azure AD. Teraz dodaj użytkowników, którzy mogą uzyskać dostęp do aplikacji. 

1. Na stronie **SecretAPI — Przegląd** wybierz **pozycję Użytkownicy i grupy** w lewej nawigacji.
   
1. Na stronie **Użytkownicy i grupy** wybierz pozycję **Dodaj użytkownika**.  
   
1. Na stronie **Dodawanie przydziału** wybierz pozycję **Użytkownicy i grupy**. 
   
1. Na stronie **Użytkownicy i grupy** wyszukaj i wybierz użytkowników, którzy mają dostęp do aplikacji, w tym przynajmniej siebie. Po wybraniu wszystkich użytkowników wybierz pozycję **Wybierz**. 
   
   ![Zaznaczanie i przypisywanie użytkownika](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. Na stronie **Dodaj przydział** wybierz pozycję **Przypisz**. 

> [!NOTE]
> Interfejsy API korzystające ze zintegrowanego uwierzytelniania systemu Windows mogą wymagać [dodatkowych kroków](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd).

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Zarejestruj aplikację natywną i udziel dostępu do interfejsu API

Aplikacje natywne to programy opracowane do użycia na określonej platformie lub urządzeniu. Aby aplikacja natywna mogła łączyć się z interfejsem API i uzyskiwać do niego dostęp, należy go zarejestrować w usłudze Azure AD. Poniższe kroki pokazują, jak zarejestrować aplikację natywną i dać jej dostęp do internetowego interfejsu API opublikowanego za pośrednictwem serwera proxy aplikacji.

Aby zarejestrować aplikację natywną AppProxyNativeAppSample:

1. Na stronie **Przegląd** usługi Azure Active Directory wybierz pozycję **Rejestracje aplikacji,** a u góry okienka **Rejestracje aplikacji** wybierz pozycję **Nowa rejestracja**.
   
1. Na stronie **Zarejestruj zgłoszenie:**
   
   1. Pod **nazwą**wprowadź *AppProxyNativeAppSample*. 
      
   1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**. 
      
   1. W obszarze **Adres URL przekierowania**rozwijane i **wybieranie opcji Klient publiczny (mobilny & pulpit)**, a następnie wprowadź *https:\//appproxynativeapp*. 
      
   1. Wybierz **pozycję Zarejestruj**i poczekaj, aż aplikacja zostanie pomyślnie zarejestrowana. 
      
      ![Rejestrowanie nowej aplikacji](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
Aplikacja AppProxyNativeAppSample została zarejestrowana w usłudze Azure Active Directory. Aby nadać aplikacji natywnej dostęp do internetowego interfejsu API SecretAPI:

1. Na stronie**Rejestracje aplikacji** **przegląd usługi** > Azure Active Directory wybierz aplikację **AppProxyNativeAppSample.** 
   
1. Na **stronie AppProxyNativeAppSample** wybierz **pozycję Uprawnienia interfejsu API** w lewej nawigacji. 
   
1. Na stronie **Uprawnienia interfejsu API** wybierz pozycję Dodaj **uprawnienie**.
   
1. Na pierwszej stronie **uprawnień interfejsu API żądania** wybierz kartę Interfejsy API używane **przez moją organizację,** a następnie wyszukaj i wybierz pozycję **SecretAPI**. 
   
1. Na następnej stronie **Żądania uprawnień interfejsu API** zaznacz pole wyboru obok **user_impersonation**, a następnie wybierz pozycję **Dodaj uprawnienia**. 
   
    ![Wybieranie interfejsu API](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. Na stronie **uprawnień interfejsu API** można wybrać opcję **Udzielaj zgody administratora dla firmy Contoso,** aby uniemożliwić innym użytkownikom indywidualną zgodę na aplikację. 

## <a name="configure-the-native-app-code"></a>Konfigurowanie natywnego kodu aplikacji

Ostatnim krokiem jest skonfigurowanie aplikacji macierzystej. Poniższy fragment kodu z pliku *Form1.cs* w nativeclient przykładowej aplikacji powoduje biblioteki ADAL uzyskać token do żądania wywołania interfejsu API i dołączyć go jako nośnika do nagłówka aplikacji. 
   
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
   
Aby skonfigurować aplikację natywną do łączenia się z usługą Azure Active Directory i wywołania serwera proxy aplikacji interfejsu API, zaktualizuj wartości zastępcze w pliku *App.config* przykładowej aplikacji NativeClient z wartościami z usługi Azure AD: 

- Wklej **identyfikator katalogu (dzierżawy)** `<add key="ida:Tenant" value="" />` w tym polu. Tę wartość (identyfikator GUID) można znaleźć i skopiować na stronie **Przegląd** jednej z aplikacji. 
  
- Wklej identyfikator aplikacji AppProxyNativeAppSample **w** `<add key="ida:ClientId" value="" />` polu. Tę wartość (identyfikator GUID) można znaleźć i skopiować na stronie **Przegląd** aplikacji AppProxyNativeAppSample.
  
- Wklej appproxyNativeAppSample **Redirect URI** w `<add key="ida:RedirectUri" value="" />` polu. Tę wartość (URI) można znaleźć i skopiować na stronie **Uwierzytelnianie** AppProxyNativeAppSample. 
  
- Wklej identyfikator **URI identyfikatora aplikacji** `<add key="todo:TodoListResourceId" value="" />` SecretAPI w tym polu. Tę wartość (URI) można znaleźć i **skopiować** na stronie Udostępnianie interfejsu API przez secretapi.
  
- Wklej **adres URL strony głównej** `<add key="todo:TodoListBaseAddress" value="" />` SecretAPI w tym polu. Tę wartość (adres URL) można znaleźć i skopiować na stronie **Znakowanie** SecretAPI.

Po skonfigurowaniu parametrów skompiluj i uruchom aplikację macierzystą. Po wybraniu przycisku **Zaloguj** się aplikacja umożliwia zalogowanie się, a następnie wyświetli ekran sukcesu, aby potwierdzić, że pomyślnie połączony z funkcją SecretAPI.

![Powodzenie](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Dodawanie aplikacji lokalnej do zdalnego dostępu za pośrednictwem serwera proxy aplikacji w usłudze Azure Active Directory](application-proxy-add-on-premises-application.md)
- [Szybki start: konfigurowanie aplikacji klienckiej w celu uzyskiwania dostępu do internetowych interfejsów API](../develop/quickstart-configure-app-access-web-apis.md)
- [Jak włączyć natywne aplikacje klienckie do interakcji z aplikacjami proxy](application-proxy-configure-native-client-application.md)
