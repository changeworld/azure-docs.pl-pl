---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 1eaae979f030d3044b44c46bedee2bd9e3346784
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502657"
---
## <a name="register-your-application"></a>Rejestrowanie aplikacji

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/), aby zarejestrować aplikację.
1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory**, a następnie pozycję **Rejestracje aplikacji (wersja zapoznawcza) > Nowa rejestracja**.
1. Po wyświetleniu strony **Rejestrowanie aplikacji** wprowadź nazwę aplikacji.
1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
1. W obszarze **identyfikator URI przekierowania** zaznacz **Web** platformy i ustaw wartość adres URL aplikacji oparta na serwerze sieci web. Zobacz sekcje poniżej, aby uzyskać instrukcje na temat ustawiania i uzyskać adres URL przekierowania w programie Visual Studio i języka Node.
1. Po zakończeniu wybierz pozycję **Rejestruj**.
1. Na stronie **Przegląd** aplikacji zanotuj wartość **Identyfikator aplikacji (klienta)**.
1. Ten przewodnik Szybki start wymaga włączenia [przepływu niejawnego udzielenia](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md). W okienku nawigacji po lewej stronie w zarejestrowanej aplikacji wybierz pozycję **Uwierzytelnianie**.
1. Na stronie **Ustawienia zaawansowane** w obszarze **Niejawne udzielenie** zaznacz obydwa pola wyboru: **Tokeny identyfikatorów** i **Tokeny dostępu**. Tokeny identyfikatorów i tokeny dostępu są wymagane, ponieważ ta aplikacja musi zalogować użytkowników i wywołać interfejs API.
1. Wybierz pozycję **Zapisz**.

> #### <a name="setting-the-redirect-url-for-node"></a>Ustawienie adresu URL przekierowania dla węzła
> Dla środowiska Node.js, można ustawić internetowego port serwera w *server.js* pliku. W tym samouczku korzysta z portu 30662 odwołania, ale można użyć dostępny port. Postępuj zgodnie z poniższymi instrukcjami, aby skonfigurować adres URL przekierowania w informacje o rejestracji aplikacji:<br/>
> - Przejdź z powrotem do *Rejestracja aplikacji* i ustaw `http://localhost:30662/` jako `Redirect URL`, lub użyj `http://localhost:[port]/` Jeśli używasz niestandardowego portu TCP (gdzie *[port]* jest niestandardowy numer portu TCP).

<p>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Visual Studio dotyczącymi uzyskiwania adresu URL przekierowania
> Wykonaj następujące kroki, aby uzyskać adres URL przekierowania:
> 1. W **Eksploratora rozwiązań**, wybierz projekt i przyjrzyj się **właściwości** okna. Jeśli nie widzisz **właściwości** naciśnij klawisze **F4**.
> 2. Skopiuj wartości z **adresu URL** do Schowka:<br/> ![Właściwości projektu](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. Przejdź z powrotem do *Rejestracja aplikacji* i ustaw wartość jako **adresu URL przekierowania**.

#### <a name="configure-your-javascript-spa"></a>Konfigurowanie aplikacji JEDNOSTRONICOWEJ JavaScript

1. W `index.html` plik utworzony podczas konfiguracji projektu, Dodaj informacje o rejestracji aplikacji. Dodaj następujący kod u góry strony, w ramach `<script></script>` tagów w treści swoje `index.html` pliku:

    ```javascript
    var applicationConfig = {
        clientID: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here",
        graphScopes: ["user.read"],
        graphEndpoint: "https://graph.microsoft.com/v1.0/me"
    };
    ```

    Gdzie:
    - `Enter_the_Application_Id_here` -jest **identyfikator aplikacji (klienta)** dla aplikacji został zarejestrowany.
    - `Enter_the_Tenant_Info_Here` -jest ustawiony na jedną z następujących opcji:
       - Jeśli aplikacja obsługuje tryb **Konta w tym katalogu organizacyjnym**, zastąp tę wartość za pomocą wartości **Identyfikator dzierżawy** lub **Nazwa dzierżawy** (na przykład contoso.microsoft.com)
       - Jeśli aplikacja obsługuje **kont w dowolnym katalogu organizacji**, Zastąp tę wartość za pomocą `organizations`
       - Jeśli aplikacja obsługuje **kont w dowolnym katalogu organizacji i osobistych kont Microsoft**, Zastąp tę wartość za pomocą `common`
