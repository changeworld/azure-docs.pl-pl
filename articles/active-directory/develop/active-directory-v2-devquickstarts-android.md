---
title: "Aplikacji systemu Android w wersji 2.0 usługi Azure Active Directory | Dokumentacja firmy Microsoft"
description: "Jak utworzyć aplikację systemu Android, logujący się użytkownicy posiadający zarówno osobistego konta Microsoft i pracy lub kont służbowych i wywołania interfejsu API programu Graph przy użyciu bibliotek innych firm."
services: active-directory
documentationcenter: 
author: danieldobalian
manager: mtillman
editor: 
ms.assetid: 16294c07-f27d-45c9-833f-7dbb12083794
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: b1c30362a7b14c8f7f0c44d911c46c491b3de3c0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
---
# <a name="add-sign-in-to-an-android-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Dodaj logowanie do aplikacji systemu Android przy użyciu biblioteki innych firm z interfejsu API programu Graph przy użyciu punktu końcowego v2.0
Platforma Microsoft Identity korzysta z otwartych standardów, takich jak OAuth2 i OpenID Connect. Deweloperzy mogą używać dowolnej bibliotece potrzebnymi do integracji z naszych usług. Aby pomóc deweloperom korzystać z innych bibliotek platformy, możemy napisanych kilka wskazówki podobny do pokazują, jak skonfigurować biblioteki innych firm, aby nawiązać połączenia z platformą tożsamości Microsoft. Większość bibliotek, które implementują [specyfikację RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) można nawiązać połączenia z platformą tożsamości Microsoft.

Aplikacja, która tworzy tego przewodnika użytkownicy mogą zalogować się do organizacji i następnie wyszukaj się w organizacji za pomocą interfejsu API programu Graph.

Jeśli jesteś nowym użytkownikiem OAuth2 lub OpenID Connect, znacznie to przykładowa konfiguracja może nie mieć sensu do Ciebie. Zalecamy przeczytanie [2.0 protokołów - przepływu kodu autoryzacji OAuth 2.0](active-directory-v2-protocols-oauth-code.md) w tle.

> [!NOTE]
> Niektóre funkcje platformy, które mają wyrażenia OAuth2 lub OpenID Connect standardy, takie jak dostęp warunkowy i zarządzanie zasadami usługi Intune, trzeba użyć naszych Otwórz źródło bibliotek tożsamości usługi Microsoft Azure.
> 
> 

Punktu końcowego v2.0 nie obsługuje wszystkich scenariuszy Azure Active Directory i funkcji.

> [!NOTE]
> Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj o [ograniczenia v2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="download-the-code-from-github"></a>Pobierz kod z usługi GitHub
Kod używany w tym samouczku jest przechowywany [ w serwisie GitHub](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2).  Aby z niego skorzystać, można [pobrać szkielet aplikacji w formie .zip](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2/archive/skeleton.zip) lub sklonować szkielet:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

Można także po prostu pobrać przykład i od razu rozpocząć:

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## <a name="register-an-app"></a>Rejestracja aplikacji
Utwórz nową aplikację na [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), lub wykonaj szczegółowy opis kroków w [jak zarejestrować aplikację z punktem końcowym v2.0](active-directory-v2-app-registration.md).  Upewnij się, że:

* Kopiuj **identyfikator aplikacji** przypisany do aplikacji, ponieważ będzie on potrzebny wkrótce.
* Dodaj **Mobile** platformy aplikacji.

> Uwaga: Portal rejestracji aplikacji udostępnia **identyfikator URI przekierowania** wartość. Jednak w tym przykładzie należy użyć wartość domyślną `https://login.microsoftonline.com/common/oauth2/nativeclient`.
> 
> 

## <a name="download-the-nxoauth2-third-party-library-and-create-a-workspace"></a>Pobierz NXOAuth2 biblioteki innych firm i tworzenie obszaru roboczego
W ramach tego przewodnika skorzystasz OIDCAndroidLib z serwisu GitHub, które jest oparte na kodzie Google OpenID Connect biblioteką OAuth2. Ona profil aplikacji natywnej implementuje i obsługuje punktu końcowego autoryzacji użytkownika. Są to wszystkie czynności, które należy zintegrować z platformą tożsamości Microsoft.

Klonowanie repozytorium OIDCAndroidLib do komputera.

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![androidStudio](../media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

## <a name="set-up-your-android-studio-environment"></a>Konfigurowanie środowiska Android Studio
1. Utwórz nowy projekt Android Studio i zaakceptuj ustawienia domyślne w kreatorze.
   
    ![Utwórz nowy projekt w programie Android Studio](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)
   
    ![Docelowych urządzeń z systemem Android](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)
   
    ![Dodawanie działania na telefon komórkowy](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)
2. Aby skonfigurować moduły projektu, należy przenieść sklonowanego repozytorium do lokalizacji projektu. Można także utworzyć projekt, a następnie sklonować bezpośrednio do lokalizacji projektu.
   
    ![Moduły projektu](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)
3. Otwórz ustawienia modułów projektu za pomocą menu kontekstowego lub przy użyciu skrótu Ctrl + Alt + główn + S.
   
    ![Ustawienia modułów projektu](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)
4. Usunąć domyślnego modułu aplikacji, ponieważ mają tylko ustawienia kontenera projektu.
   
    ![Domyślny moduł aplikacji](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)
5. Zaimportuj moduły z sklonowanego repozytorium dla bieżącego projektu.
   
    ![Importowanie projektu gradle](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG) ![Utwórz nową stronę modułu](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)
6. Powtórz te kroki dla `oidlib-sample` modułu.
7. Sprawdź zależności oidclib na `oidlib-sample` modułu.
   
    ![zależności oidclib w module oidlib próbki](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)
8. Kliknij przycisk **OK** i poczekaj, aż synchronizacja narzędzia gradle.
   
    Twoje settings.gradle powinna wyglądać:
   
    ![Zrzut ekranu przedstawiający settings.gradle](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)
9. Tworzenie przykładowej aplikacji, aby upewnić się, że Przykładowe prawidłowe działanie.
   
    Nie można użyć tego jeszcze z usługą Azure Active Directory. Potrzebujemy najpierw skonfigurować niektóre punkty końcowe. To upewnij się, że masz problemy z systemem Android Studio przed Rozpoczniemy Dostosowywanie przykładowej aplikacji.
10. Tworzenie i uruchamianie `oidlib-sample` jako cel w programie Android Studio.
    
    ![Postęp kompilacji oidlib próbki](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)
11. Usuń `app ` katalogu, w którym pozostawało po usunięciu modułu z projektu, ponieważ Android Studio nie powoduje usunięcia go dla bezpieczeństwa.
    
    ![Struktura pliku, który zawiera katalog aplikacji](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)
12. Otwórz **Edytuj konfiguracje** menu do usunięcia konfiguracji procesu, który pozostawało również usunięcie modułu z projektu.
    
    ![Edytuj konfiguracje menu](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG)
    ![Uruchom konfiguracji aplikacji](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## <a name="configure-the-endpoints-of-the-sample"></a>Skonfiguruj punkty końcowe próbki
Teraz, gdy masz `oidlib-sample` uruchomiony pomyślnie, umożliwia edytowanie niektórych punktów końcowych, aby działało z usługą Azure Active Directory.

### <a name="configure-your-client-by-editing-the-oidcclientconfxml-file"></a>Konfigurowanie klienta, edytując plik oidc_clientconf.xml
1. Ponieważ używasz przepływów OAuth2 tylko do pobrania tokenu i wywołania interfejsu API programu Graph, Ustaw klienta OAuth2 tylko. OIDC wejdzie w przykładzie nowsze.
   
    ```xml
        <bool name="oidc_oauth2only">true</bool>
    ```
2. Konfigurowanie Identyfikatora klienta, otrzymany z portalu rejestracji.
   
    ```xml
        <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
        <string name="oidc_clientSecret"></string>
    ```
3. Skonfiguruj identyfikator URI przekierowania, poniżej.
   
    ```xml
        <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
    ```
4. Skonfiguruj zakresach potrzebne w celu uzyskania dostępu do interfejsu API programu Graph.
   
    ```xml
        <string-array name="oidc_scopes">
            <item>openid</item>
            <item>https://graph.microsoft.com/User.Read</item>
            <item>offline_access</item>
        </string-array>
    ```

`User.Read` Wartość w `oidc_scopes` umożliwia odczytanie profilu podstawowego podpisanej w użytkownika.
Dowiedz się więcej o wszystkie dostępne zakresy na [zakresy uprawnień Microsoft Graph](https://graph.microsoft.io/docs/authorization/permission_scopes).

Jeśli chcesz wyjaśnień dotyczących `openid` lub `offline_access` jako zakresów w OpenID Connect, zobacz [2.0 protokołów - przepływu kodu autoryzacji OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

### <a name="configure-your-client-endpoints-by-editing-the-oidcendpointsxml-file"></a>Skonfiguruj punkty końcowe klienta, edytując plik oidc_endpoints.xml
* Otwórz `oidc_endpoints.xml` plik, a następnie dokonaj następujących zmian:
  
    ```xml
    <!-- Stores OpenID Connect provider endpoints. -->
    <resources>
        <string name="op_authorizationEnpoint">https://login.microsoftonline.com/common/oauth2/v2.0/authorize</string>
        <string name="op_tokenEndpoint">https://login.microsoftonline.com/common/oauth2/v2.0/token</string>
        <string name="op_userInfoEndpoint">https://www.example.com/oauth2/userinfo</string>
        <string name="op_revocationEndpoint">https://www.example.com/oauth2/revoketoken</string>
    </resources>
    ```

Tymi punktami końcowymi nigdy nie należy zmieniać, jeśli używasz protokołu OAuth2 jako protokołów.

> [!NOTE]
> Punkty końcowe dla `userInfoEndpoint` i `revocationEndpoint` nie są obecnie obsługiwane przez usługę Azure Active Directory. Pozostawienie tych z wartością domyślną example.com przypomnienie pojawi się czy nie są one dostępne w próbce :-)
> 
> 

## <a name="configure-a-graph-api-call"></a>Konfigurowanie wywołania interfejsu API programu Graph
* Otwórz `HomeActivity.java` plik, a następnie dokonaj następujących zmian:
  
    ```Java
       //TODO: set your protected resource url
        private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
    ```

W tym miejscu proste wywołania interfejsu API programu Graph zwraca naszych informacje.

Są to wszystkie zmiany, które należy wykonać. Uruchom `oidlib-sample` aplikacji, a następnie kliknij przycisk **Zaloguj**.

Po pomyślnie uwierzytelniono, wybierz **żądania zasobów chronionych** przycisk, aby przetestować wywołania do interfejsu API programu Graph.

## <a name="get-security-updates-for-our-product"></a>Pobierz aktualizacje zabezpieczeń naszych produktów
Firma Microsoft zachęca do otrzymywać powiadomień o przypadki naruszenia zabezpieczeń poprzez wizytę [Witryna TechCenter poświęcona zabezpieczeniom](https://technet.microsoft.com/security/dd252948) i subskrybowanie doradczych alertów zabezpieczeń.

