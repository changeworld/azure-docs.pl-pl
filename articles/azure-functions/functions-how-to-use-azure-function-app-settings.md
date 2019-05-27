---
title: Konfigurowanie ustawień aplikacji funkcji platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować ustawienia aplikacji funkcji platformy Azure.
services: ''
documentationcenter: .net
author: ggailey777
manager: jeconnoc
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 92ca09040836dfc55a9d709b12a0ee01192d6bac
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957391"
---
# <a name="how-to-manage-a-function-app-in-the-azure-portal"></a>Jak zarządzać aplikacji funkcji w witrynie Azure portal 

W usłudze Azure Functions aplikacja funkcji zapewnia kontekst wykonania dla poszczególnych funkcji. Zachowania aplikacji funkcji mają zastosowanie do wszystkich funkcji obsługiwanych przez aplikację daną funkcję. W tym temacie opisano sposób konfigurowania i zarządzania aplikacji funkcji w witrynie Azure portal.

Aby rozpocząć, przejdź do [witryny Azure portal](https://portal.azure.com) i zaloguj się do konta platformy Azure. Na pasku wyszukiwania w górnej części portalu wpisz nazwę aplikacji funkcji i wybierz ją z listy. Po wybraniu aplikacji funkcji, zostanie wyświetlona następująca strona:

![Omówienie aplikacji funkcji w witrynie Azure portal](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

Możesz przejść do wszystkiego, czego potrzebujesz, aby zarządzać swoją aplikację funkcji na stronie Przegląd w szczególności **[ustawienia aplikacji](#settings)** i **[funkcje platformy](#platform-features)**.

## <a name="settings"></a>Ustawienia aplikacji

**Ustawienia aplikacji** kartę przechowuje ustawienia, które są używane przez aplikację funkcji.

![Ustawienia aplikacji funkcji w witrynie Azure portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

Te ustawienia są przechowywane w postaci zaszyfrowanej i należy wybrać **Pokaż wartości** aby zobaczyć wartości, w portalu.

Aby dodać ustawienie, wybierz pozycję **nowe ustawienie aplikacji** i Dodaj nową parę klucz wartość.

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Podczas tworzenia aplikacji funkcji, który jest lokalnie, te wartości są obsługiwane w pliku local.settings.json w projekcie.

## <a name="platform-features"></a>Funkcje platformy

![Karta funkcje platformy aplikacji funkcji.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Aplikacje funkcji działają w i są obsługiwane przez platformę Azure App Service. W efekcie Twoje aplikacje funkcji mają dostęp do większości funkcji platformy Azure core hostingu platformy. **Funkcje platformy** karta jest, gdzie dostęp do wielu funkcji platformę usługi App Service, na której można używać w aplikacji funkcji. 

> [!NOTE]
> Nie wszystkie funkcje usługi App Service są dostępne, gdy aplikacja funkcji jest uruchamiany na użycie planu hostingu.

Pozostała część tego tematu koncentruje się na następujące funkcje usługi App Service w witrynie Azure portal, które są przydatne w przypadku funkcji:

+ [Edytor usługi App Service](#editor)
+ [Console](#console)
+ [Narzędzia zaawansowane (Kudu)](#kudu)
+ [Opcje wdrażania](#deployment)
+ [CORS](#cors)
+ [Uwierzytelnianie](#auth)
+ [Definicja interfejsu API](#swagger)

Aby uzyskać więcej informacji na temat sposobu pracy z ustawieniami usługi App Service, zobacz [Konfigurowanie ustawień usługi aplikacji Azure](../app-service/configure-common.md).

### <a name="editor"></a>App Service Editor

| | |
|-|-|
| ![Aplikacja funkcji edytora usługi App Service.](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | Edytor usługi App Service jest Zaawansowany edytor w portalu, używanej do modyfikowania pliki konfiguracji JSON i podobne plików kodu. Wybranie tej opcji spowoduje uruchomienie na osobnej karcie przeglądarki za pomocą edytora podstawowych. Umożliwia integrację z repozytorium Git, uruchamiania i debugowania kodu i modyfikowania ustawień aplikacji funkcji. Ten edytor zapewnia środowisko deweloperskich dla funkcji w porównaniu z bloku domyślnym aplikacji funkcji.    |

![Edytor usługi App Service](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="console"></a>Console

| | |
|-|-|
| ![Za pomocą konsoli aplikacji funkcji w witrynie Azure portal](./media/functions-how-to-use-azure-function-app-settings/function-app-console.png) | Konsoli w portalu jest narzędziem projektanta idealnym rozwiązaniem, gdy użytkownik nie chce korzystać z aplikacji funkcji z poziomu wiersza polecenia. Typowe polecenia obejmują katalogu i tworzenia pliku i nawigacji, a także wykonywanie plików wsadowych i skryptów. |

![Funkcja aplikacji konsoli](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

### <a name="kudu"></a>Narzędzia zaawansowane (Kudu)

| | |
|-|-|
| ![Aplikacja funkcji Kudu w witrynie Azure portal](./media/functions-how-to-use-azure-function-app-settings/function-app-advanced-tools.png) | Zaawansowane narzędzia dla usługi App Service (znany także jako Kudu) zapewniają dostęp do zaawansowanych funkcji administracyjnych w aplikacji funkcji. Aparat Kudu zarządzania informacje o systemie, ustawienia aplikacji, zmienne środowiskowe, rozszerzeń witryny, nagłówków HTTP i zmiennych serwera. Można również uruchomić **Kudu** , przechodząc do SCM punktu końcowego dla aplikacji funkcji, takie jak `https://<myfunctionapp>.scm.azurewebsites.net/` |

![Konfigurowanie programu Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)


### <a name="a-namedeploymentdeployment-options"></a><a name="deployment">Opcje wdrażania

| | |
|-|-|
| ![Opcje wdrażania aplikacji funkcji w witrynie Azure portal](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | Functions pozwala tworzyć kod funkcji na komputerze lokalnym. Następnie możesz przekazać swój projekt aplikacji funkcji lokalnej na platformę Azure. Oprócz tradycyjnych przekazywania FTP Functions umożliwia wdrażanie aplikacji funkcji przy użyciu rozwiązań popularnych ciągłej integracji, takich jak GitHub, DevOps platformy Azure, Dropbox, Bitbucket i inne. Aby uzyskać więcej informacji, zobacz [ciągłe wdrażanie dla usługi Azure Functions](functions-continuous-deployment.md). Aby przekazać ręcznie przy użyciu protokołu FTP i lokalnego narzędzia Git, również należy [skonfiguruj poświadczenia wdrożenia](functions-continuous-deployment.md#credentials). |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![Aplikacja funkcji CORS w witrynie Azure portal](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | Aby zapobiec wykonaniu złośliwego kodu w usługach, usługi App Service blokuje wywołania do aplikacji funkcji ze źródeł zewnętrznych. Usługa Functions obsługuje cross-origin resource sharing (CORS), co pozwala zdefiniować "dozwolonych" dozwolonych źródeł, z których funkcji może akceptować żądań zdalnych.  |

![Konfigurowanie aplikacji funkcji CORS](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>Uwierzytelnianie

| | |
|-|-|
| ![Uwierzytelnianie aplikacji funkcji w witrynie Azure portal](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | Korzystając z funkcji wyzwalacza HTTP, możesz wymagać wywołania najpierw uwierzytelnieni. Usługa App Service obsługuje uwierzytelnianie usługi Azure Active Directory i zaloguj się przy użyciu dostawców sieci społecznościowych, takich jak Facebook, Microsoft i Twitter. Aby uzyskać więcej informacji na temat konfigurowania uwierzytelniania określonych dostawców, zobacz [omówienie uwierzytelniania w usłudze Azure App Service](../app-service/overview-authentication-authorization.md). |

![Konfigurowanie uwierzytelniania dla aplikacji funkcji](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>Definicja interfejsu API

| | |
|-|-|
| ![Funkcja aplikacji API struktury swagger definicji w witrynie Azure portal](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | Usługa Functions obsługuje strukturę Swagger, aby zezwolić klientom na używanie funkcji wyzwalanej przez HTTP. Aby uzyskać więcej informacji na temat tworzenia definicji interfejsu API w strukturze Swagger, odwiedź stronę [hostowanie interfejsu API RESTful z mechanizmem CORS w usłudze Azure App Service](../app-service/app-service-web-tutorial-rest-api.md). Serwery proxy usługi Functions umożliwia również zdefiniować jedną powierzchnię interfejsu API dla wielu funkcji. Aby uzyskać więcej informacji, zobacz [Praca z usługi Azure Functions Proxies](functions-proxies.md). |

![Konfigurowanie aplikacji funkcji interfejsu API](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>Kolejne kroki

+ [Konfigurowanie ustawień usługi Azure App Service](../app-service/configure-common.md)
+ [Ciągłe wdrażanie dla usługi Azure Functions](functions-continuous-deployment.md)



