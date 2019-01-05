---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 135ee9f6b833165cd393b9c5ca582e0ee9499e0f
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54057404"
---
## <a name="register-your-application"></a>Rejestrowanie aplikacji

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) zarejestrować aplikację.
1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory**, a następnie pozycję **Rejestracje aplikacji (wersja zapoznawcza) > Nowa rejestracja**.
1. Gdy **rejestrowania aplikacji** zostanie wyświetlona strona, wprowadź nazwę aplikacji.
1. W obszarze **obsługiwane typy kont**, wybierz opcję **kont w dowolnym katalogu organizacji i osobistych kont Microsoft**.
1. W obszarze **identyfikator URI przekierowania** zaznacz **Web** platformy i ustaw wartość adres URL aplikacji oparta na serwerze sieci web. Zobacz sekcje poniżej, aby uzyskać instrukcje na temat ustawiania i uzyskać adres URL przekierowania w programie Visual Studio i języka Node.
1. Po zakończeniu wybierz pozycję **Rejestruj**.
1. W aplikacji **Przegląd** strony, zanotuj **identyfikator aplikacji (klienta)** wartość.
1. Ten przewodnik Szybki Start wymaga [niejawne udzielić przepływ](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md) włączenia. W okienku nawigacji po lewej stronie w zarejestrowanej aplikacji wybierz **uwierzytelniania**.
1. W **Zaawansowane ustawienia**w obszarze **przyznawanie niejawne**, włączyć zarówno **tokeny Identyfikatora** i **tokeny dostępu** pola wyboru. Identyfikator tokenów i tokenów dostępu są wymagane, ponieważ ta aplikacja wymaga logowania użytkowników i wywoływać interfejs API.
1. Wybierz pozycję **Zapisz**.

> #### <a name="setting-the-redirect-url-for-node"></a>Ustawienie adresu URL przekierowania dla węzła
> Dla środowiska Node.js, można ustawić internetowego port serwera w *server.js* pliku. W tym samouczku korzysta z portu 30662 odwołania, ale można użyć dostępny port. Postępuj zgodnie z poniższymi instrukcjami, aby skonfigurować adres URL przekierowania w informacje o rejestracji aplikacji:<br/>
> - Przejdź z powrotem do *Rejestracja aplikacji* i ustaw `http://localhost:30662/` jako `Redirect URL`, lub użyj `http://localhost:[port]/` Jeśli używasz niestandardowego portu TCP (gdzie *[port]* jest niestandardowy numer portu TCP).

<p/>

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
        authority: "https://login.microsoftonline.com/common",
        graphScopes: ["user.read"],
        graphEndpoint: "https://graph.microsoft.com/v1.0/me"
    };
    ```

<ol start="2">
<li>
Zastąp <code>Enter the application Id here</code> identyfikatorem aplikacji, które właśnie zostało zarejestrowane.
</li>
</ol>
