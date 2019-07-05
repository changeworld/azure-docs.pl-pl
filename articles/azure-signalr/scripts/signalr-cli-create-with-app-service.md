---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie usługi SignalR Service za pomocą usługi App Service
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie usługi SignalR Service za pomocą usługi App Service
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: d0f0747aa393475265be4aeb9ca05000fbd5b97b
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565755"
---
# <a name="create-a-signalr-service-with-an-app-service"></a>Tworzenie usługi SignalR Service za pomocą usługi App Service

Ten przykładowy skrypt tworzy nowy zasób usługi Azure SignalR Service, który jest używany do wypychania w czasie rzeczywistym aktualizacji zawartości do klientów. Skrypt ten dodaje również nowy plan aplikacji internetowej i usługi App Service do hostowania aplikacji internetowej platformy ASP.NET Core korzystającej z usługi SignalR Service. Aplikację internetową skonfigurowano za pomocą ustawień aplikacji o nazwie *AzureSignalRConnectionString*, aby nawiązać połączenie z nowym zasobem usługi SignalR Service.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt używa rozszerzenia *signalr* dla interfejsu wiersza polecenia platformy Azure. Uruchom następujące polecenie, aby zainstalować rozszerzenie *signalr* dla interfejsu wiersza polecenia platformy Azure zanim użyjesz tego przykładowego skryptu:

```azurecli-interactive
az extension add -n signalr
```

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-with-app-service/create-signalr-with-app-service.sh "Create a new Azure SignalR Service and Web App")]

Zanotuj rzeczywistą nazwę wygenerowaną dla nowej grupy zasobów. Zostanie ona wyświetlona w danych wyjściowych. Tej nazwy grupy zasobów użyjesz, gdy zechcesz usunąć wszystkie zasoby w grupie.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Każde polecenie w tabeli stanowi link do dokumentacji polecenia. W tym skrypcie użyto następujących poleceń:

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az signalr create](/cli/azure/signalr#az-signalr-create) | Tworzy zasób usługi Azure SignalR Service. |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | Wyświetla listę kluczy, które będą używane przez Twoją aplikację przy wypychaniu w czasie rzeczywistym aktualizacji zawartości za pomocą usługi SignalR. |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | Tworzy plan usługi Azure App Service do hostowania aplikacji internetowych. |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | Tworzy aplikację internetową platformy Azure za pomocą planu hostingu usługi App Service. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Dodaje nowe ustawienie aplikacji dla aplikacji internetowej. To ustawienie aplikacji umożliwia przechowywanie parametrów połączenia usługi SignalR. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia usługi Azure SignalR Service można znaleźć w [dokumentacji usługi Azure SignalR Service](../signalr-reference-cli.md).
