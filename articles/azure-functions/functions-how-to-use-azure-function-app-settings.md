---
title: Konfigurowanie ustawień aplikacji funkcji na platformie Azure
description: Dowiedz się, jak skonfigurować ustawienia aplikacji funkcji platformy Azure.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 08/14/2019
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 662a04dbcc39f3fa95b0098eb8fe556b18b3495b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357554"
---
# <a name="manage-your-function-app"></a>Zarządzanie aplikacją funkcji 

W Azure Functions aplikacja funkcji udostępnia kontekst wykonywania dla poszczególnych funkcji. Zachowania aplikacji funkcji dotyczą wszystkich funkcji hostowanych przez daną aplikację funkcji. Wszystkie funkcje w aplikacji funkcji muszą być w tym samym [języku](supported-languages.md). 

Poszczególne funkcje w aplikacji funkcji są wdrażane razem i są skalowane jednocześnie. Wszystkie funkcje w tym samym zakresie zasobów funkcji, na wystąpienie, jako aplikacja funkcji skaluje się. 

Parametry połączenia, zmienne środowiskowe i inne ustawienia aplikacji są definiowane osobno dla każdej aplikacji funkcji. Wszelkie dane, które muszą być współużytkowane przez aplikacje funkcji, powinny być przechowywane zewnętrznie w utrwalonym magazynie.

W tym artykule opisano sposób konfigurowania aplikacji funkcji i zarządzania nimi. 

> [!TIP]  
> Wiele opcji konfiguracji można także zarządzać za pomocą [Interfejs wiersza polecenia platformy Azure]. 

## <a name="get-started-in-the-azure-portal"></a>Wprowadzenie do Azure Portal

Aby rozpocząć, przejdź do [Azure Portal] i zaloguj się na koncie platformy Azure. Na pasku wyszukiwania w górnej części portalu wpisz nazwę aplikacji funkcji i wybierz ją z listy. Po wybraniu aplikacji funkcji zostanie wyświetlona następująca strona:

![Przegląd aplikacji funkcji w Azure Portal](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

Możesz przejść do wszystkiego, czego potrzebujesz do zarządzania aplikacją funkcji, na stronie przeglądu, w szczególności z **[ustawieniami aplikacji](#settings)** i **[funkcjami platformy](#platform-features)** .

## <a name="settings"></a>Ustawienia aplikacji

Karta **Ustawienia aplikacji** obsługuje ustawienia, które są używane przez aplikację funkcji. Te ustawienia są przechowywane w postaci zaszyfrowanej i należy wybrać opcję **Pokaż wartości** , aby wyświetlić wartości w portalu. Dostęp do ustawień aplikacji można również uzyskać przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="portal"></a>Portal

Aby dodać ustawienie w portalu, wybierz pozycję **nowe ustawienie aplikacji** i Dodaj nową parę klucz-wartość.

![Ustawienia aplikacji funkcji w Azure Portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Polecenie [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) zwraca istniejące ustawienia aplikacji, jak w poniższym przykładzie:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

Polecenie [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) dodaje lub aktualizuje ustawienie aplikacji. Poniższy przykład tworzy ustawienie z kluczem o nazwie `CUSTOM_FUNCTION_APP_SETTING` i wartością `12345`:


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>Użyj ustawień aplikacji

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Podczas lokalnego tworzenia aplikacji funkcji należy zachować lokalne kopie tych wartości w pliku projektu Local. Settings. JSON. Aby dowiedzieć się więcej, zobacz [plik ustawień lokalnych](functions-run-local.md#local-settings-file).

## <a name="platform-features"></a>Funkcje platformy

![Karta funkcje platformy aplikacji funkcji.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Aplikacje funkcji działają w programie i są obsługiwane przez platformę Azure App Service. W związku z tym aplikacje funkcji mają dostęp do większości funkcji platformy hostingu w sieci Web platformy Azure. Karta **funkcje platformy** to miejsce, w którym można uzyskać dostęp do wielu funkcji platformy App Service, których można używać w aplikacjach funkcji. 

> [!NOTE]
> Nie wszystkie funkcje App Service są dostępne, gdy aplikacja funkcji jest uruchamiana w ramach planu hostingu zużycia.

Pozostała część tego artykułu koncentruje się na następujących App Service funkcjach w Azure Portal, które są przydatne w przypadku funkcji:

+ [Edytor App Service](#editor)
+ [Konsola](#console)
+ [Narzędzia zaawansowane (kudu)](#kudu)
+ [Opcje wdrażania](#deployment)
+ [CORS](#cors)
+ [Uwierzytelnianie](#auth)

Aby uzyskać więcej informacji na temat sposobu pracy z ustawieniami App Service, zobacz [konfigurowanie Azure App Service ustawień](../app-service/configure-common.md).

### <a name="editor"></a>Edytor usługi App Service

![Edytor App Service](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

Edytor App Service to zaawansowany edytor w portalu, którego można użyć do modyfikacji plików konfiguracji JSON i plików kodu. Wybranie tej opcji powoduje uruchomienie oddzielnej karty przeglądarki z podstawowym edytorem. Dzięki temu można zintegrować z repozytorium git, uruchamiać i debugować kod oraz modyfikować ustawienia aplikacji funkcji. Ten Edytor zapewnia ulepszone środowisko programistyczne dla funkcji w porównaniu z wbudowanym edytorem funkcji.  

Zalecamy rozważenie opracowywania funkcji na komputerze lokalnym. Podczas tworzenia lokalnie i publikowania na platformie Azure pliki projektu są tylko do odczytu w portalu. Aby dowiedzieć się więcej, zobacz temat [kod i test Azure Functions lokalnie](functions-develop-local.md).

### <a name="console"></a>Konsoli

![Konsola aplikacji funkcji](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

Konsola w portalu jest idealnym narzędziem deweloperskim, gdy wolisz korzystać z aplikacji funkcji z poziomu wiersza polecenia. Typowe polecenia obejmują Tworzenie katalogów i plików oraz nawigację, a także wykonywanie plików wsadowych i skryptów. 

Podczas programowania lokalnego zalecamy używanie [Azure Functions Core Tools](functions-run-local.md) i [Interfejs wiersza polecenia platformy Azure].

### <a name="kudu"></a>Narzędzia zaawansowane (kudu)

![Konfigurowanie kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

Zaawansowane narzędzia dla App Service (znane również jako kudu) zapewniają dostęp do zaawansowanych funkcji administracyjnych aplikacji funkcji. Z usługi kudu można zarządzać informacjami o systemie, ustawieniami aplikacji, zmiennymi środowiskowymi, rozszerzeniami witryn, nagłówkami HTTP i zmiennymi serwera. Możesz również uruchomić **kudu** , przechodząc do punktu końcowego SCM dla swojej aplikacji funkcji, np. `https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment"></a>Centrum wdrażania

W przypadku korzystania z rozwiązania do kontroli źródła w celu opracowywania i konserwowania kodu funkcji centrum wdrażania umożliwia tworzenie i wdrażanie z kontroli źródła. Projekt został skompilowany i wdrożony na platformie Azure podczas wprowadzania aktualizacji. Aby uzyskać więcej informacji, zobacz [technologie wdrażania w Azure Functions](functions-deployment-technologies.md).

### <a name="cors"></a>Współużytkowanie zasobów między źródłami

Aby zapobiec wykonywaniu złośliwego kodu na kliencie, nowoczesne przeglądarki blokują żądania z aplikacji sieci Web do zasobów uruchomionych w oddzielnej domenie. [Współużytkowanie zasobów między źródłami (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) umożliwia `Access-Control-Allow-Origin` nagłówku zadeklarować, które pochodzenia mogą wywoływać punkty końcowe w aplikacji funkcji.

#### <a name="portal"></a>Portal

Po skonfigurowaniu listy **dozwolonych źródeł** dla aplikacji funkcji nagłówek `Access-Control-Allow-Origin` jest automatycznie dodawany do wszystkich odpowiedzi z punktów końcowych HTTP w aplikacji funkcji. 

![Skonfiguruj listę CORS aplikacji funkcji](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

Gdy jest używany symbol wieloznaczny (`*`), wszystkie pozostałe domeny zostaną zignorowane. 

Użyj [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) polecenie, aby dodać domenę do listy dozwolonych źródeł. Poniższy przykład dodaje domenę contoso.com:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Użyj polecenia [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) , aby wyświetlić listę bieżących dozwolonych źródeł.

### <a name="auth"></a>Ponowne

![Konfigurowanie uwierzytelniania dla aplikacji funkcji](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Gdy funkcje używają wyzwalacza HTTP, można wymagać, aby wywołania były najpierw uwierzytelniane. App Service obsługuje uwierzytelnianie Azure Active Directory i logowanie się przy użyciu dostawców społecznościowych, takich jak Facebook, Microsoft i Twitter. Aby uzyskać szczegółowe informacje na temat konfigurowania konkretnych dostawców uwierzytelniania, zobacz [Omówienie uwierzytelniania Azure App Service](../app-service/overview-authentication-authorization.md). 


## <a name="next-steps"></a>Następne kroki

+ [Skonfiguruj ustawienia Azure App Service](../app-service/configure-common.md)
+ [Ciągłe wdrażanie dla usługi Azure Functions](functions-continuous-deployment.md)

[Interfejs wiersza polecenia platformy Azure]: /cli/azure/
[Azure Portal]: https://portal.azure.com
