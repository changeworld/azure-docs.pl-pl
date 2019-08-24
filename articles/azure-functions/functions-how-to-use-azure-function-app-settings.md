---
title: Konfigurowanie ustawień usługi Azure aplikacja funkcji | Microsoft Docs
description: Dowiedz się, jak skonfigurować ustawienia aplikacji funkcji platformy Azure.
author: ggailey777
manager: gwallace
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 27e56c3bcaa08fee25c362cdfed64c61b92873cb
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982655"
---
# <a name="how-to-manage-a-function-app"></a>Jak zarządzać aplikacją funkcji

W Azure Functions aplikacja funkcji udostępnia kontekst wykonywania dla poszczególnych funkcji. Zachowania aplikacji funkcji dotyczą wszystkich funkcji hostowanych przez daną aplikację funkcji. W tym temacie opisano sposób konfigurowania aplikacji funkcji i zarządzania nimi w Azure Portal.

Aby rozpocząć, przejdź do [Azure Portal](https://portal.azure.com) i zaloguj się na koncie platformy Azure. Na pasku wyszukiwania w górnej części portalu wpisz nazwę aplikacji funkcji i wybierz ją z listy. Po wybraniu aplikacji funkcji zostanie wyświetlona następująca strona:

![Przegląd aplikacji funkcji w Azure Portal](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

Możesz przejść do wszystkiego, czego potrzebujesz do zarządzania aplikacją funkcji, na stronie przeglądu, w szczególności z **[ustawieniami aplikacji](#settings)** i **[funkcjami platformy](#platform-features)** .

## <a name="settings"></a>Ustawienia aplikacji

Karta **Ustawienia aplikacji** obsługuje ustawienia, które są używane przez aplikację funkcji.

![Ustawienia aplikacji funkcji w Azure Portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

Te ustawienia są przechowywane w postaci zaszyfrowanej i należy wybrać opcję **Pokaż wartości** , aby wyświetlić wartości w portalu.

Aby dodać ustawienie, wybierz pozycję **nowe ustawienie aplikacji** i Dodaj nową parę klucz-wartość.

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

W przypadku lokalnego tworzenia aplikacji funkcji te wartości są przechowywane w pliku projektu Local. Settings. JSON.

## <a name="platform-features"></a>Funkcje platformy

![Karta funkcje platformy aplikacji funkcji.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Aplikacje funkcji działają w programie i są obsługiwane przez platformę Azure App Service. W związku z tym aplikacje funkcji mają dostęp do większości funkcji platformy hostingu w sieci Web platformy Azure. Karta **funkcje platformy** to miejsce, w którym można uzyskać dostęp do wielu funkcji platformy App Service, których można używać w aplikacjach funkcji. 

> [!NOTE]
> Nie wszystkie funkcje App Service są dostępne, gdy aplikacja funkcji jest uruchamiana w ramach planu hostingu zużycia.

Pozostała część tego tematu koncentruje się na następujących App Service funkcjach w Azure Portal, które są przydatne w przypadku funkcji:

+ [Edytor App Service](#editor)
+ [Console](#console)
+ [Narzędzia zaawansowane (kudu)](#kudu)
+ [Opcje wdrażania](#deployment)
+ [CORS](#cors)
+ [Uwierzytelnianie](#auth)
+ [Definicja interfejsu API](#swagger)

Aby uzyskać więcej informacji na temat sposobu pracy z ustawieniami App Service, zobacz [konfigurowanie Azure App Service ustawień](../app-service/configure-common.md).

### <a name="editor"></a>Edytor usługi App Service

| | |
|-|-|
| ![Edytor App Service aplikacji funkcji.](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | Edytor App Service to zaawansowany edytor w portalu, którego można użyć do modyfikacji plików konfiguracji JSON i plików kodu. Wybranie tej opcji powoduje uruchomienie oddzielnej karty przeglądarki z podstawowym edytorem. Dzięki temu można zintegrować z repozytorium git, uruchamiać i debugować kod oraz modyfikować ustawienia aplikacji funkcji. Ten Edytor zapewnia ulepszone środowisko programistyczne dla funkcji w porównaniu z blokiem domyślnej aplikacji funkcji.    |

![Edytor App Service](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="console"></a>Konsoli

| | |
|-|-|
| ![Konsola aplikacji funkcji w Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-console.png) | Konsola w portalu jest idealnym narzędziem deweloperskim, gdy wolisz korzystać z aplikacji funkcji z poziomu wiersza polecenia. Typowe polecenia obejmują Tworzenie katalogów i plików oraz nawigację, a także wykonywanie plików wsadowych i skryptów. |

![Konsola aplikacji funkcji](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

### <a name="kudu"></a>Narzędzia zaawansowane (kudu)

| | |
|-|-|
| ![Aplikacja funkcji kudu w Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-advanced-tools.png) | Zaawansowane narzędzia dla App Service (znane również jako kudu) zapewniają dostęp do zaawansowanych funkcji administracyjnych aplikacji funkcji. Z usługi kudu można zarządzać informacjami o systemie, ustawieniami aplikacji, zmiennymi środowiskowymi, rozszerzeniami witryn, nagłówkami HTTP i zmiennymi serwera. Możesz również uruchomić **kudu** , przechodząc do punktu końcowego SCM dla aplikacji funkcji, np.`https://<myfunctionapp>.scm.azurewebsites.net/` |

![Konfigurowanie kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)


### <a name="a-namedeploymentdeployment-options"></a><a name="deployment">Opcje wdrażania

| | |
|-|-|
| ![Opcje wdrażania aplikacji funkcji w Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | Funkcje umożliwiają tworzenie kodu funkcji na komputerze lokalnym. Następnie możesz przekazać projekt aplikacji funkcji lokalnych na platformę Azure. Oprócz tradycyjnego przekazywania FTP funkcje umożliwiają wdrażanie aplikacji funkcji przy użyciu popularnych rozwiązań ciągłej integracji, takich jak GitHub, Azure DevOps, Dropbox, Bitbucket i inne. Aby uzyskać więcej informacji, zobacz [wdrażanie ciągłe dla Azure Functions](functions-continuous-deployment.md). Aby przekazać ręcznie przy użyciu protokołu FTP lub lokalnego narzędzia Git, należy również [skonfigurować poświadczenia wdrożenia](functions-continuous-deployment.md#credentials). |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![Funkcja CORS aplikacji funkcji w Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | Aby zapobiec wykonywaniu złośliwego kodu w usługach, App Service blokuje wywołania do aplikacji funkcji ze źródeł zewnętrznych. Funkcje obsługują współużytkowanie zasobów między źródłami (CORS), aby umożliwić zdefiniowanie "dozwolonych" dozwolonych źródeł, z których funkcje mogą akceptować żądania zdalne.  |

![Konfigurowanie mechanizmu CORS aplikacja funkcji](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>Ponowne

| | |
|-|-|
| ![Uwierzytelnianie aplikacji funkcji w Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | Gdy funkcje używają wyzwalacza HTTP, można wymagać, aby wywołania były najpierw uwierzytelniane. App Service obsługuje uwierzytelnianie Azure Active Directory i logują się przy użyciu dostawców społecznościowych, takich jak Facebook, Microsoft i Twitter. Aby uzyskać szczegółowe informacje na temat konfigurowania konkretnych dostawców uwierzytelniania, zobacz [Omówienie uwierzytelniania Azure App Service](../app-service/overview-authentication-authorization.md). |

![Konfigurowanie uwierzytelniania dla aplikacji funkcji](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>Definicja interfejsu API

| | |
|-|-|
| ![Definicja struktury Swagger interfejsu API aplikacji funkcji w Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | Funkcja obsługuje strukturę Swagger, aby umożliwić klientom łatwiejsze korzystanie z funkcji wyzwalanych przez protokół HTTP. Aby uzyskać więcej informacji na temat tworzenia definicji interfejsu API za pomocą struktury Swagger, odwiedź [hosta RESTful API with CORS w Azure App Service](../app-service/app-service-web-tutorial-rest-api.md). Można również użyć serwerów proxy usługi Functions do zdefiniowania pojedynczej powierzchni interfejsu API dla wielu funkcji. Aby uzyskać więcej informacji, zobacz [Praca z serwery proxy usługi Azure Functions](functions-proxies.md). |

![Konfigurowanie interfejsu API aplikacja funkcji](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>Następne kroki

+ [Skonfiguruj ustawienia Azure App Service](../app-service/configure-common.md)
+ [Ciągłe wdrażanie dla usługi Azure Functions](functions-continuous-deployment.md)



