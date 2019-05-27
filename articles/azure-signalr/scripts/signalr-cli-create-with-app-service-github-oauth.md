---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie aplikacji internetowej korzystającej z uwierzytelniania usług SignalR Service i GitHub
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie aplikacji internetowej korzystającej z uwierzytelniania usług SignalR Service i GitHub
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/22/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: 84020448019867744d08806acbbd47adbc1a83e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66128366"
---
# <a name="create-a-web-app-that-uses-signalr-service-and-github-authentication"></a>Tworzenie aplikacji internetowej korzystającej z uwierzytelniania usług SignalR Service i GitHub

Ten przykładowy skrypt tworzy nowy zasób usługi Azure SignalR Service, który jest używany do wypychania w czasie rzeczywistym aktualizacji zawartości do klientów. Skrypt ten dodaje również nowy plan aplikacji internetowej i usługi App Service do hostowania aplikacji internetowej platformy ASP.NET Core korzystającej z usługi SignalR Service. Aplikacja internetowa jest skonfigurowana z ustawieniami aplikacji do nawiązywania połączenia z nowym zasobem usługi SignalR Service i uwierzytelniania za pomocą [uwierzytelniania GitHub](https://developer.github.com/v3/guides/basics-of-authentication/). Aplikacja internetowa jest również skonfigurowana do używania lokalnego źródła wdrożenia repozytorium git.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt używa rozszerzenia *signalr* dla interfejsu wiersza polecenia platformy Azure. Uruchom następujące polecenie, aby zainstalować rozszerzenie *signalr* dla interfejsu wiersza polecenia platformy Azure zanim użyjesz tego przykładowego skryptu:

```azurecli-interactive
az extension add -n signalr
```

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-with-app-service-github-oauth/create-signalr-with-app-service-github-oauth.sh "Create a new SignalR Service and Web App configured to use SignalR, GitHub OAuth, and local Git repository deployment source.")]

Zanotuj rzeczywistą nazwę wygenerowaną dla nowej grupy zasobów. Zostanie ona wyświetlona w danych wyjściowych. Tej nazwy grupy zasobów użyjesz, gdy zechcesz usunąć wszystkie zasoby w grupie.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Każde polecenie w tabeli stanowi link do dokumentacji polecenia. W tym skrypcie użyto następujących poleceń:

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az signalr create](/cli/azure/ext/signalr/signalr#ext-signalr-az-signalr-create) | Tworzy zasób usługi Azure SignalR Service. |
| [az signalr key list](/cli/azure/ext/signalr/signalr/key#ext-signalr-az-signalr-key-list) | Wyświetla listę kluczy, które będą używane przez Twoją aplikację przy wypychaniu w czasie rzeczywistym aktualizacji zawartości za pomocą usługi SignalR. |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | Tworzy plan usługi Azure App Service do hostowania aplikacji internetowych. |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | Tworzy aplikację internetową platformy Azure za pomocą planu hostingu usługi App Service. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Dodaje nowe ustawienia aplikacji dla aplikacji internetowej. Te ustawienia aplikacji są używane do przechowywania parametrów połączenia usługi SignalR i wpisów tajnych aplikacji OAuth usługi GitHub. |
| [az webapp deployment user set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) | Aktualizacja poświadczeń wdrożenia. |
| [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-local-git) | Pobierz adres URL dla punktu końcowego repozytorium git do klonowania i wypychania dla wdrożenia aplikacji internetowej. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia usługi Azure SignalR Service można znaleźć w [dokumentacji usługi Azure SignalR Service](../signalr-reference-cli.md).
