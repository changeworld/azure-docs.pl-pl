---
title: Konfigurowanie ustawień aplikacji funkcji na platformie Azure
description: Dowiedz się, jak skonfigurować ustawienia aplikacji funkcji platformy Azure.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 08/14/2019
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 662a04dbcc39f3fa95b0098eb8fe556b18b3495b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276948"
---
# <a name="manage-your-function-app"></a>Zarządzanie aplikacją funkcji 

W usłudze Azure Functions aplikacja funkcji zapewnia kontekst wykonywania dla poszczególnych funkcji. Zachowania aplikacji funkcji dotyczą wszystkich funkcji obsługiwanych przez daną aplikację funkcji. Wszystkie funkcje w aplikacji funkcyjnej muszą być w tym samym [języku.](supported-languages.md) 

Poszczególne funkcje w aplikacji funkcji są wdrażane razem i są skalowane razem. Wszystkie funkcje w tej samej aplikacji funkcji współużytkują zasoby na wystąpienie, ponieważ aplikacja funkcji jest skalowana. 

Parametry połączenia, zmienne środowiskowe i inne ustawienia aplikacji są definiowane oddzielnie dla każdej aplikacji funkcji. Wszelkie dane, które muszą być współużytkowane między aplikacjami funkcji powinny być przechowywane zewnętrznie w magazynie utrwalonym.

W tym artykule opisano sposób konfigurowania aplikacji funkcji i zarządzania nimi. 

> [!TIP]  
> Wiele opcji konfiguracji można również zarządzać za pomocą [interfejsu wiersza polecenia platformy Azure]. 

## <a name="get-started-in-the-azure-portal"></a>Rozpoczęcie pracy w witrynie Azure portal

Aby rozpocząć, przejdź do [witryny Azure portal] i zaloguj się do konta platformy Azure. Na pasku wyszukiwania w górnej części portalu wpisz nazwę aplikacji funkcji i wybierz ją z listy. Po wybraniu aplikacji funkcji zostanie wyświetlona następująca strona:

![Omówienie aplikacji funkcji w witrynie Azure portal](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

Możesz przejść do wszystkiego, czego potrzebujesz, aby zarządzać aplikacją funkcji ze strony przeglądu, w szczególności **[ustawienia aplikacji](#settings)** i **[funkcje platformy](#platform-features)**.

## <a name="application-settings"></a><a name="settings"></a>Ustawienia aplikacji

Karta **Ustawienia aplikacji** zachowuje ustawienia używane przez aplikację funkcyjną. Te ustawienia są przechowywane zaszyfrowane i należy wybrać **opcję Pokaż wartości,** aby wyświetlić wartości w portalu. Można również uzyskać dostęp do ustawień aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="portal"></a>Portal

Aby dodać ustawienie w portalu, wybierz **pozycję Nowe ustawienie aplikacji** i dodaj nową parę klucz-wartość.

![Ustawienia aplikacji funkcji w witrynie Azure portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Polecenie [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) zwraca istniejące ustawienia aplikacji, jak w poniższym przykładzie:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

Polecenie [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) dodaje lub aktualizuje ustawienie aplikacji. Poniższy przykład tworzy ustawienie z `CUSTOM_FUNCTION_APP_SETTING` kluczem `12345`o nazwie i wartością:


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>Korzystanie z ustawień aplikacji

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Podczas tworzenia aplikacji funkcji lokalnie, należy zachować lokalne kopie tych wartości w pliku projektu local.settings.json. Aby dowiedzieć się więcej, zobacz [Plik ustawień lokalnych](functions-run-local.md#local-settings-file).

## <a name="platform-features"></a>Funkcje platformy

![Karta Funkcje platformy aplikacji funkcji.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Aplikacje funkcji są uruchamiane i obsługiwane przez platformę usługi Azure App Service. W związku z tym aplikacje funkcji mają dostęp do większości funkcji podstawowej platformy hostingowej platformy Azure. **Karta Funkcje platformy** umożliwia dostęp do wielu funkcji platformy usługi app service, których można używać w aplikacjach funkcji. 

> [!NOTE]
> Nie wszystkie funkcje usługi App Service są dostępne, gdy aplikacja funkcji działa w planie hostingu zużycie.

W dalszej części tego artykułu koncentruje się na następujących funkcji usługi App Service w witrynie Azure portal, które są przydatne dla funkcji:

+ [Edytor usługi aplikacji](#editor)
+ [Console](#console)
+ [Zaawansowane narzędzia (Kudu)](#kudu)
+ [Opcje wdrażania](#deployment)
+ [CORS](#cors)
+ [Uwierzytelnianie](#auth)

Aby uzyskać więcej informacji na temat pracy z ustawieniami usługi App Service, zobacz [Konfigurowanie ustawień usługi aplikacji Platformy Azure](../app-service/configure-common.md).

### <a name="app-service-editor"></a><a name="editor"></a>Edytor usług aplikacji

![Edytor usługi aplikacji](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

Edytor usługi App Service to zaawansowany edytor w portalu, którego można używać do modyfikowania plików konfiguracyjnych JSON i plików kodu. Wybranie tej opcji powoduje uruchomienie osobnej karty przeglądarki z podstawowym edytorem. Dzięki temu można zintegrować się z repozytorium Git, uruchomić i debugować kod i zmodyfikować ustawienia aplikacji funkcji. Ten edytor zapewnia ulepszone środowisko programistyczne dla funkcji w porównaniu z wbudowanym edytorem funkcji.  

Zaleca się rozważenie tworzenia funkcji na komputerze lokalnym. Podczas tworzenia lokalnie i publikowania na platformie Azure pliki projektu są tylko do odczytu w portalu. Aby dowiedzieć się więcej, zobacz [Kod i testowanie usług Azure Functions lokalnie.](functions-develop-local.md)

### <a name="console"></a><a name="console"></a>Console

![Konsola aplikacji funkcji](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

Konsola w portalu jest idealnym narzędziem deweloperskim, gdy wolisz wchodzić w interakcję z aplikacją funkcyjną z wiersza polecenia. Typowe polecenia obejmują tworzenie i nawigację w katalogu i plikach, a także wykonywanie plików wsadowych i skryptów. 

Podczas tworzenia lokalnie zaleca się korzystanie z [narzędzi podstawowe usług Azure Functions Core Tools](functions-run-local.md) i interfejsu [wiersza polecenia platformy Azure.]

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>Zaawansowane narzędzia (Kudu)

![Konfigurowanie Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

Zaawansowane narzędzia usługi App Service (znane również jako Kudu) zapewniają dostęp do zaawansowanych funkcji administracyjnych aplikacji funkcji. Z Kudu zarządzasz informacjami systemowymi, ustawieniami aplikacji, zmiennymi środowiskowymi, rozszerzeniami witryn, nagłówkami HTTP i zmiennymi serwera. **Kudu** można również uruchomić, przeglądając punkt końcowy SCM dla aplikacji funkcyjnej, np.`https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment-center"></a><a name="deployment"></a>Centrum wdrażania

Korzystając z rozwiązania kontroli źródła do tworzenia i obsługi kodu funkcji, Centrum wdrażania umożliwia tworzenie i wdrażanie z kontroli źródła. Twój projekt jest zbudowany i wdrożony na platformie Azure podczas wprowadzania aktualizacji. Aby uzyskać więcej informacji, zobacz [Technologie wdrażania w usłudze Azure Functions](functions-deployment-technologies.md).

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>Współużytkowanie zasobów między źródłami

Aby zapobiec wykonywaniu złośliwego kodu na kliencie, nowoczesne przeglądarki blokują żądania z aplikacji sieci web do zasobów uruchomionych w osobnej domenie. [Udostępnianie zasobów między źródłami (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) umożliwia nagłówkowi `Access-Control-Allow-Origin` zadeklarowanie, które źródła mogą wywoływać punkty końcowe w aplikacji funkcji.

#### <a name="portal"></a>Portal

Po skonfigurowaniu listy **Dozwolonych źródeł** dla aplikacji `Access-Control-Allow-Origin` funkcji, nagłówek jest automatycznie dodawany do wszystkich odpowiedzi z punktów końcowych HTTP w aplikacji funkcji. 

![Konfigurowanie listy CORS aplikacji funkcji](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

Gdy używany jest`*`symbol wieloznaczny ( ) wszystkie inne domeny są ignorowane. 

Użyj [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) polecenia, aby dodać domenę do listy dozwolonych źródeł. W poniższym przykładzie dodano domenę contoso.com:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Użyj [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) polecenia, aby wyświetlić listę bieżących dozwolonych początków.

### <a name="authentication"></a><a name="auth"></a>Uwierzytelnianie

![Konfigurowanie uwierzytelniania dla aplikacji funkcyjnej](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Gdy funkcje używają wyzwalacza HTTP, można wymagać, aby wywołania były najpierw uwierzytelnione. Usługa App Service obsługuje uwierzytelnianie usługi Azure Active Directory i logowanie się za pomocą dostawców usług społecznościowych, takich jak Facebook, Microsoft i Twitter. Aby uzyskać szczegółowe informacje na temat konfigurowania określonych dostawców uwierzytelniania, zobacz [omówienie uwierzytelniania usługi Azure App Service](../app-service/overview-authentication-authorization.md). 


## <a name="next-steps"></a>Następne kroki

+ [Konfigurowanie ustawień usługi aplikacji platformy Azure](../app-service/configure-common.md)
+ [Ciągłe wdrażanie dla usług Azure Functions](functions-continuous-deployment.md)

[Interfejs wiersza polecenia platformy Azure]: /cli/azure/
[Portal Azure]: https://portal.azure.com
