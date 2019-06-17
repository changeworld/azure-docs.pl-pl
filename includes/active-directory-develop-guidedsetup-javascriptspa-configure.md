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
ms.openlocfilehash: 050bae64a62e90bdb74c93948109e255b69d7518
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133269"
---
## <a name="register-your-application"></a>Rejestrowanie aplikacji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu do dzierżawy usługi Azure AD, do którego chcesz używać.
1. Wybierz pozycję platforma tożsamości firmy Microsoft dla deweloperów [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) strony.
1. Po wyświetleniu strony **Rejestrowanie aplikacji** wprowadź nazwę aplikacji.
1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
1. W obszarze **identyfikator URI przekierowania** sekcji z listy rozwijanej wybierz **Web** platformy, a następnie ustaw wartość adres URL aplikacji, która opiera się na serwerze sieci web. 

   Aby uzyskać informacji na temat ustawiania i uzyskiwanie adres URL przekierowania w programie Visual Studio i środowiska Node.js Zobacz dwóch następnych sekcjach.

1. Wybierz pozycję **Zarejestruj**.
1. W aplikacji **Przegląd** stronie **identyfikator aplikacji (klienta)** wartości do późniejszego użycia.
1. Ten przewodnik Szybki start wymaga włączenia [przepływu niejawnego udzielenia](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md). W okienku po lewej stronie zarejestrowanej aplikacji wybierz **uwierzytelniania**.
1. W **Zaawansowane ustawienia**w obszarze **przyznawanie niejawne**, wybierz opcję **tokeny Identyfikatora** i **tokeny dostępu** pola wyboru. Identyfikator tokenów i tokenów dostępu są wymagane, ponieważ ta aplikacja wymaga logowania użytkowników i wywoływać interfejs API.
1. Wybierz pozycję **Zapisz**.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Ustaw adres URL przekierowania dla środowiska Node.js
> Dla środowiska Node.js, można ustawić internetowego port serwera w *server.js* pliku. W tym samouczku korzysta z portu 30662 odwołania, ale można użyć dostępny port. 
>
> Aby skonfigurować adres URL przekierowania w aplikacji informacje rejestracyjne, przejdź z powrotem do **Rejestracja aplikacji** okienko, i wykonaj jedną z następujących czynności:
>
> - Ustaw *`http://localhost:30662/`* jako **adres URL przekierowania**.
> - Jeśli używasz niestandardowego portu TCP, użyj *`http://localhost:<port>/`* (gdzie  *\<port >* jest niestandardowy numer portu TCP).
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Ustaw adres URL przekierowania dla programu Visual Studio
> Aby uzyskać adres URL przekierowania dla programu Visual Studio, wykonaj następujące czynności:
> 1. W **Eksploratora rozwiązań**, wybierz projekt.
>
>    **Właściwości** zostanie otwarte okno. Jeśli nie jest otwierany, naciśnij klawisz **F4**.
>
>    ![Okno właściwości projektu JavaScriptSPA](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. Kopiuj **adresu URL** wartość.
 
> 1. Przejdź z powrotem do **Rejestracja aplikacji** okienku i Wklej skopiowany wartość jako **adresu URL przekierowania**.

#### <a name="configure-your-javascript-spa"></a>Konfigurowanie aplikacji JEDNOSTRONICOWEJ JavaScript

1. W *index.html* pliku, który został utworzony podczas konfiguracji projektu, Dodaj informacje o rejestracji aplikacji. W górnej części pliku w ramach `<script></script>` tagi, Dodaj następujący kod:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Gdzie:
    - *\<Enter_the_Application_Id_here >* jest **identyfikator aplikacji (klienta)** dla aplikacji został zarejestrowany.
    - *\<Enter_the_Tenant_info_here >* jest ustawiony na jedną z następujących opcji:
       - Jeśli aplikacja obsługuje *kont w tym katalogu organizacji*, Zastąp tę wartość za pomocą **identyfikator dzierżawy** lub **nazwa dzierżawy** (na przykład  *contoso.microsoft.com*).
       - Jeśli aplikacja obsługuje *kont w dowolnym katalogu organizacji*, Zastąp tę wartość za pomocą **organizacje**.
       - Jeśli aplikacja obsługuje *kont w dowolnym katalogu organizacji i osobistych kont Microsoft*, Zastąp tę wartość za pomocą **wspólnej**. Aby ograniczyć obsługę do *tylko konta osobiste firmy Microsoft*, Zastąp tę wartość za pomocą **konsumentów**.
