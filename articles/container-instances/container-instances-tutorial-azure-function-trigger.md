---
title: Samouczek — grupa kontenerów wyzwalacza według funkcji platformy Azure
description: Tworzenie wyzwalanej przez protokół HTTP, bezserwerowej funkcji programu PowerShell w celu automatyzacji tworzenia wystąpień kontenerów platformy Azure
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: ''
ms.openlocfilehash: 9dbb22a2449e4c41bff802ab827da4489fc7ffeb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78331029"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Samouczek: Tworzenie grupy kontenerów za pomocą funkcji platformy Azure wyzwalanej przez protokół HTTP

[Usługa Azure Functions](../azure-functions/functions-overview.md) to usługa obliczeniowa bezserwerowa, która może uruchamiać skrypty lub kod w odpowiedzi na różne zdarzenia, takie jak żądanie HTTP, czasomierz lub komunikat w kolejce usługi Azure Storage.

W tym samouczku utworzysz funkcję platformy Azure, która przyjmuje żądanie HTTP i wyzwala wdrożenie [grupy kontenerów](container-instances-container-groups.md). W tym przykładzie przedstawiono podstawy korzystania z usługi Azure Functions do automatycznego tworzenia zasobów w wystąpieniach kontenera platformy Azure. Zmodyfikuj lub rozszerz przykład dla bardziej złożonych scenariuszy lub innych wyzwalaczy zdarzeń. 

Omawiane kwestie:

> [!div class="checklist"]
> * Użyj programu Visual Studio Code z [rozszerzeniem usługi Azure Functions,](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) aby utworzyć podstawową funkcję programu PowerShell wyzwalaną przez protokół HTTP.
> * Włącz tożsamość w aplikacji funkcji i nadaj jej uprawnienia do tworzenia zasobów platformy Azure.
> * Modyfikowanie i ponowne publikowanie funkcji programu PowerShell w celu zautomatyzowania wdrażania grupy kontenerów z jednym kontenerem.
> * Sprawdź wdrożenie kontenera wyzwalane przez protokół HTTP.

> [!IMPORTANT]
> Program PowerShell for Azure Functions jest obecnie w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="prerequisites"></a>Wymagania wstępne

Zobacz Tworzenie pierwszej funkcji na [platformie Azure,](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-powershell#configure-your-environment) aby uzyskać wymagania wstępne do zainstalowania i używania kodu programu Visual Studio za pomocą funkcji platformy Azure w swoim operacyjn. systemu operacyjnym.

Niektóre kroki w tym artykule należy użyć interfejsu wiersza polecenia platformy Azure. Aby wykonać te kroki, można użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-a-basic-powershell-function"></a>Tworzenie podstawowej funkcji programu PowerShell

Wykonaj kroki w [Tworzenie pierwszej funkcji programu PowerShell w platformie Azure,](../azure-functions/functions-create-first-function-powershell.md) aby utworzyć funkcję programu PowerShell przy użyciu szablonu wyzwalacza HTTP. Użyj domyślnej nazwy funkcji platformy Azure **HttpTrigger**. Jak pokazano w przewodniku Szybki start, przetestować funkcję lokalnie i opublikować projekt do aplikacji funkcji na platformie Azure. W tym przykładzie jest podstawowa funkcja wyzwalana przez protokół HTTP, która zwraca ciąg tekstowy. W dalszej części tego artykułu można zmodyfikować funkcję, aby utworzyć grupę kontenerów.

W tym artykule przyjęto założenie, że projekt zostanie opublikowany przy użyciu nazwy *myfunctionapp*, w grupie zasobów platformy Azure automatycznie nazwanej zgodnie z nazwą aplikacji funkcji (również *myfunctionapp).* Zastąp unikatową nazwę aplikacji funkcji i nazwę grupy zasobów w późniejszych krokach.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Włączanie tożsamości zarządzanej przez platformę Azure w aplikacji funkcji

Teraz włącz przypisaną do systemu [tożsamość zarządzaną](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity) w aplikacji funkcji. Host programu PowerShell z uruchomioną aplikacją może automatycznie uwierzytelniać się przy użyciu tej tożsamości, umożliwiając funkcjom podejmowanie akcji w usługach platformy Azure, do których tożsamość została przyznana. W tym samouczku można udzielić uprawnień tożsamości zarządzanej do tworzenia zasobów w grupie zasobów aplikacji funkcji. 

Najpierw użyj polecenia [az group show,][az-group-show] aby uzyskać identyfikator grupy zasobów aplikacji funkcyjnej i zapisać ją w zmiennej środowiskowej. W tym przykładzie przyjęto założenie, że polecenie jest uruchamiane w powłoce Bash.

```azurecli
rgID=$(az group show --name myfunctionapp --query id --output tsv)
```

Uruchom [az functionapp identity app assign][az-functionapp-identity-app-assign] to assign a local identity to the function app and assign a contributor role to the resource group. Ta rola umożliwia tożsamości do tworzenia dodatkowych zasobów, takich jak grupy kontenerów w grupie zasobów.

```azurecli
az functionapp identity assign \
  --name myfunctionapp \
  --resource-group myfunctionapp \
  --role contributor --scope $rgID
```

## <a name="modify-httptrigger-function"></a>Modyfikowanie funkcji HttpTrigger

Zmodyfikuj kod programu PowerShell dla funkcji **HttpTrigger,** aby utworzyć grupę kontenerów. W `run.ps1` pliku dla funkcji, znajdź następujący blok kodu. Ten kod wyświetla wartość nazwy, jeśli jest przekazywana jako ciąg zapytania w adresie URL funkcji:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
[...]
```

Zastąp ten kod następującym przykładowym blokiem. W tym miejscu, jeśli wartość nazwy jest przekazywana w ciągu zapytania, jest on używany do nazwy i tworzenia grupy kontenerów przy użyciu polecenia cmdlet [New-AzContainerGroup.][new-azcontainergroup] Pamiętaj, aby zastąpić nazwę grupy zasobów *myfunctionapp* nazwą grupy zasobów dla aplikacji funkcyjnej:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    $body = "Started container group $name"
}
[...]
```

W tym przykładzie tworzy grupę kontenerów `alpine` składającą się z pojedynczego wystąpienia kontenera z uruchomionym obrazem. Kontener uruchamia pojedyncze `echo` polecenie, a następnie kończy działanie. W przykładzie rzeczywistych można wyzwolić utworzenie co najmniej jednej grupy kontenerów do uruchamiania zadania wsadowego.
 
## <a name="test-function-app-locally"></a>Aplikacja funkcji testowania lokalnie

Upewnij się, że funkcja działa poprawnie lokalnie przed ponownym opublikowaniem projektu aplikacji funkcji na platformie Azure. Jak pokazano w [programie Szybki start programu PowerShell,](../azure-functions/functions-create-first-function-powershell.md)wstaw lokalny `Wait-Debugger` punkt przerwania w skrypcie programu PowerShell i wywołanie nad nim. Aby uzyskać wskazówki dotyczące debugowania, zobacz [Debugowanie programu PowerShell Azure Functions lokalnie.](../azure-functions/functions-debug-powershell-local.md)


## <a name="republish-azure-function-app"></a>Ponowne publikowanie aplikacji funkcji platformy Azure

Po sprawdzeniu, że funkcja działa poprawnie na komputerze lokalnym, nadszedł czas, aby ponownie opublikować projekt do istniejącej aplikacji funkcji na platformie Azure.

> [!NOTE]
> Pamiętaj, aby usunąć `Wait-Debugger` wszelkie wywołania przed opublikowaniem funkcji na platformie Azure.

1. W programie Visual Studio Code otwórz paletę poleceń. Wyszukaj `Azure Functions: Deploy to function app...`i wybierz opcję .
1. Wybierz bieżący folder roboczy do spakowania i wdrożenia.
1. Wybierz subskrypcję, a następnie nazwę istniejącej aplikacji funkcji *(myfunctionapp*). Upewnij się, że chcesz zastąpić poprzednie wdrożenie.

Po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrożeniowego zostanie wyświetlone powiadomienie. Wybierz **opcję Wyświetl dane wyjściowe** w tym powiadomieniu, aby wyświetlić wyniki tworzenia i wdrażania, w tym zaktualizowane zasoby platformy Azure.

## <a name="run-the-function-in-azure"></a>Uruchamianie funkcji na platformie Azure

Po pomyślnym zakończeniu wdrażania pobierz adres URL funkcji. Na przykład użyj **obszaru Azure: Functions** w kodzie programu Visual Studio, aby skopiować adres URL funkcji **HttpTrigger** lub uzyskać adres URL funkcji w [witrynie Azure portal](../azure-functions/functions-create-first-azure-function.md#test-the-function).

Adres URL funkcji zawiera unikatowy kod i jest w formularzu:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==
```

### <a name="run-function-without-passing-a-name"></a>Uruchamianie funkcji bez przekazywania nazwy

Jako pierwszy test uruchom `curl` polecenie i przekaż adres URL `name` funkcji bez dołączania ciągu zapytania. Upewnij się, że zawiera unikatowy kod funkcji.

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M=="
```

Funkcja zwraca kod stanu 400 `Please pass a name on the query string or in the request body`i tekst:

```
[...]
> GET /api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M== HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 400 
< content-length: 62
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:08:15 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Please pass a name on the query string or in the request body.
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Uruchom funkcję i przekaż nazwę grupy kontenerów

Teraz uruchom `curl` polecenie, dołączając nazwę grupy kontenerów (*mycontainergroup* `&name=mycontainergroup`) jako ciąg zapytania:

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup"
```

Funkcja zwraca kod stanu 200 i wyzwala utworzenie grupy kontenerów:

```
[...]
> GET /api/HttpTrigger?ode=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 200 
< content-length: 28
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:15:30 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Started container group mycontainergroup
```

Sprawdź, czy kontener został uruchomiony za pomocą polecenia [dzienniki kontenera az:][az-container-logs]

```azurecli
az container logs --resource-group myfunctionapp --name mycontainergroup
```

Przykładowe dane wyjściowe:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już żadnych zasobów utworzonych w tym samouczku, możesz wykonać polecenie [delete grupy az,][az-group-delete] aby usunąć grupę zasobów i wszystkie zasoby, które zawiera. To polecenie usuwa utworzony rejestr kontenerów, a także uruchomiony kontener i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono funkcję platformy Azure, która przyjmuje żądanie HTTP i wyzwala wdrożenie grupy kontenerów. W tym samouczku omówiono:

> [!div class="checklist"]
> * Użyj programu Visual Studio Code z rozszerzeniem usługi Azure Functions, aby utworzyć podstawową funkcję programu PowerShell wyzwalaną przez protokół HTTP.
> * Włącz tożsamość w aplikacji funkcji i nadaj jej uprawnienia do tworzenia zasobów platformy Azure.
> * Zmodyfikuj kod funkcji programu PowerShell, aby zautomatyzować wdrażanie grupy kontenerów z jednym kontenerem.
> * Sprawdź wdrożenie kontenera wyzwalane przez protokół HTTP.

Aby uzyskać szczegółowy przykład uruchamiania i monitorowania zadania konteneryzowanym w blogu, zobacz wpis w blogu [Kontenery bezserwerowe oparte na zdarzeniach z programem PowerShell Azure Functions i wystąpieniami kontenerów platformy Azure](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) oraz towarzyszącym [przykładem kodu.](https://github.com/anthonychu/functions-powershell-run-aci)

Szczegółowe [wskazówki dotyczące](/azure/azure-functions/) tworzenia funkcji platformy Azure i publikowania projektów funkcji można znaleźć w dokumentacji usługi Azure Functions. 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-functionapp-identity-app-assign]: /cli/azure/functionapp/identity#az-functionapp-identity-assign
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[az-container-logs]: /cli/azure/container#az-container-logs
