---
title: Szybki start:Tworzenie profilu aplikacji — narzędzie platformy Azure CLI — Usługa Azure Traffic Manager
description: W tym artykule przewodnika Szybki start opisano sposób tworzenia profilu usługi Traffic Manager w celu utworzenia aplikacji sieci web o wysokiej dostępności.
services: traffic-manager
author: rohinkoul
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: rohink
ms.openlocfilehash: e19850243498fc24c9a726f4603590df15f3a046
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79531519"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>Szybki start: tworzenie profilu usługi Traffic Manager dla aplikacji sieci Web o wysokiej dostępności przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku Szybki start opisano tworzenie profilu usługi Traffic Manager, który zapewni wysoką dostępność aplikacji internetowej.

W tym przewodniku Szybki start utworzysz dwa wystąpienia aplikacji sieci web. Każde z nich jest uruchamiane w innym regionie świadczenia usługi Azure. Utworzysz profil usługi Traffic Manager na podstawie [priorytetu punktu końcowego](traffic-manager-routing-methods.md#priority-traffic-routing-method). Profil kieruje ruch użytkowników do lokacji głównej, w której działa aplikacja internetowa. Usługa Traffic Manager stale monitoruje aplikację internetową. Jeśli lokacja główna jest niedostępna, usługa zapewnia automatyczne przejście w tryb failover w lokacji zapasowej.

Jeśli nie masz subskrypcji platformy Azure, utwórz teraz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz grupę zasobów za pomocą polecenia [az group create](https://docs.microsoft.com/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus:*

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

Utwórz profil usługi Traffic Manager przy użyciu [profilu menedżera ruchu sieciowego az,](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-create) który kieruje ruch użytkowników na podstawie priorytetu punktu końcowego.

W poniższym przykładzie zastąp **<profile_name>** unikatową nazwą profilu usługi Traffic Manager.

```azurecli-interactive

az network traffic-manager profile create \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --routing-method Priority \
    --path "/" \
    --protocol HTTP \
    --unique-dns-name <profile_name> \
    --ttl 30 \
    --port 80

```

## <a name="create-web-apps"></a>Tworzenie aplikacji sieci Web

W tym przewodniku Szybki start będą potrzebne dwa wystąpienia aplikacji internetowej wdrożone w dwóch różnych regionach świadczenia usługi Azure (*Wschodnie stany USA* i *Europa Zachodnia*). Każda będzie służyć jako podstawowy punkt końcowy i punkt końcowy trybu failover dla usługi Traffic Manager.

### <a name="create-web-app-service-plans"></a>Tworzenie planów usług aplikacji sieci Web
Tworzenie planów usługi aplikacji sieci web przy użyciu [planu usługi az appservice utworzyć](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) dla dwóch wystąpień aplikacji sieci web, które można wdrożyć w dwóch różnych regionach platformy Azure.

W poniższym przykładzie zastąp **<>appspname_eastus** i<>**appspname_westeurope** unikatową nazwą planu usługi app service

```azurecli-interactive

az appservice plan create \
    --name <appspname_eastus> \
    --resource-group myResourceGroup \
    --location eastus \
    --sku S1

az appservice plan create \
    --name <appspname_westeurope> \
    --resource-group myResourceGroup \
    --location westeurope \
    --sku S1

```

### <a name="create-a-web-app-in-the-app-service-plan"></a>Tworzenie aplikacji sieci Web w planie usługi aplikacji
Utwórz dwa wystąpienia aplikacji sieci web przy użyciu [aplikacji sieci web az utworzyć](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) w planach usługi app service we *wschodnich stanach USA* i Europie *Zachodniej* regionów platformy Azure.

W poniższym przykładzie zastąp **<>app1name_eastus** i<app2name_westeurope **>** unikatową nazwą aplikacji i zastąp<**appspname_eastus>** i<**appspname_westeurope>** nazwą używaną do tworzenia planów usługi aplikacji w poprzedniej sekcji.

```azurecli-interactive

az webapp create \
    --name <app1name_eastus> \
    --plan <appspname_eastus> \
    --resource-group myResourceGroup

az webapp create \
    --name <app2name_westeurope> \
    --plan <appspname_westeurope> \
    --resource-group myResourceGroup

```

## <a name="add-traffic-manager-endpoints"></a>Dodawanie punktów końcowych usługi Traffic Manager
Dodaj dwie aplikacje sieci Web jako punkty końcowe usługi Traffic Manager przy użyciu [punktu końcowego menedżera ruchu sieciowego az](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create) do profilu Usługi Traffic Manager w następujący sposób:

- Określ identyfikator aplikacji sieci Web i dodaj aplikację sieci Web znajdującą się w regionie East *US* Azure jako podstawowy punkt końcowy do kierowania całego ruchu użytkownika. 
- Określ identyfikator aplikacji sieci Web i dodaj aplikację sieci Web znajdującą się w regionie Platformy Azure *w Europie Zachodniej* jako punkt końcowy trybu failover. 

Gdy podstawowy punkt końcowy będzie niedostępny, ruch będzie automatycznie kierowany do punktu końcowego trybu failover.

W poniższym przykładzie zastąp **<app1name_eastus>** i<>**app2name_westeurope** nazwami aplikacji utworzonymi dla każdego regionu w poprzedniej sekcji, zastąp<**appspname_eastus>** i<appspname_westeurope **>** nazwą używaną do tworzenia planów usługi aplikacji w poprzedniej sekcji i zastąp<**profile_name>** nazwą profilu użytą w poprzedniej sekcji. 

**Punkt końcowy wschodnich stanów USA**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```

Zanotuj identyfikator wyświetlany w danych wyjściowych i użyj w następującym poleceniu, aby dodać punkt końcowy:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 1 \
    --endpoint-status Enabled
```

**Punkt końcowy w Europie Zachodniej**

```azurecli-interactive

az webapp show \
    --name <app2name_westeurope> \
    --resource-group myResourceGroup \
    --query id

```

Zanotuj identyfikator wyświetlany w danych wyjściowych i użyj w następującym poleceniu, aby dodać punkt końcowy:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_westeurope> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 2 \
    --endpoint-status Enabled

```

## <a name="test-your-traffic-manager-profile"></a>Testowanie profilu Menedżera ruchu

W tej sekcji sprawdzisz nazwę domeny profilu usługi Traffic Manager. Skonfigurujesz również podstawowy punkt końcowy tak, aby był niedostępny. Na końcu zobaczysz, że aplikacja internetowa jest nadal dostępna. Dzieje się tak, ponieważ usługa Traffic Manager wysyła ruch do punktu końcowego trybu failover.

W poniższym przykładzie zastąp **<app1name_eastus>** i<>**app2name_westeurope** nazwami aplikacji utworzonymi dla każdego regionu w poprzedniej sekcji, zastąp<**appspname_eastus>** i<appspname_westeurope **>** nazwą używaną do tworzenia planów usługi aplikacji w poprzedniej sekcji i zastąp<**profile_name>** nazwą profilu użytą w poprzedniej sekcji.

### <a name="determine-the-dns-name"></a>Ustalanie nazwy DNS

Określ nazwę DNS profilu Usługi Traffic Manager za pomocą [programu AZ Network Traffic Manager .](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-show)

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

Skopiuj wartość **RelativeDnsName.** Nazwa DNS profilu usługi Traffic Manager to *http://<* relativednsname *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Wyświetlanie informacji o działaniu usługi Traffic Manager
1. W przeglądarce internetowej wprowadź nazwę DNS profilu usługi Traffic Manager (*http://<* relativednsname *>.trafficmanager.net*), aby wyświetlić domyślną witrynę sieci Web aplikacji.

    > [!NOTE]
    > W tym scenariuszu Szybki start wszystkie żądania są kierowane do podstawowego punktu końcowego. Jest on ustawiony na wartość **Priorytet 1**.
2. Aby wyświetlić tryb failover usługi Traffic Manager w akcji, wyłącz witrynę główną przy użyciu [aktualizacji punktu końcowego menedżera ruchu sieciowego az](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update).

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Skopiuj nazwę DNS profilu usługi Traffic Manager (*http://<* relativednsname *>.trafficmanager.net*), aby wyświetlić witrynę sieci Web w nowej sesji przeglądarki internetowej.
4. Sprawdź, czy aplikacja internetowa jest nadal dostępna.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu usuń grupy zasobów, aplikacje sieci Web i wszystkie powiązane zasoby za pomocą [programu AZ Group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono profil usługi Traffic Manager, który zapewnia wysoką dostępność dla aplikacji sieci web. Aby dowiedzieć się więcej o kierowaniu ruchu, przejdź do samouczków usługi Traffic Manager.

> [!div class="nextstepaction"]
> [Samouczki usługi Traffic Manager](tutorial-traffic-manager-improve-website-response.md)
