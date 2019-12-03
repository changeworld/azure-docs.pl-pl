---
title: 'Interfejs wiersza polecenia: przekazywanie i powiązywanie certyfikatu SSL z aplikacją'
description: Dowiedz się, jak zautomatyzować wdrażanie aplikacji App Service i zarządzanie nią za pomocą interfejsu wiersza polecenia platformy Azure. Ten przykład pokazuje, jak powiązać niestandardowy certyfikat SSL z aplikacją.
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.custom: seodec18
ms.openlocfilehash: 1ef07cb55af4b9fe9f54d58bbd496789928c1cec
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74686683"
---
# <a name="bind-a-custom-ssl-certificate-to-an-app-service-app-using-cli"></a>Tworzenie powiązania niestandardowego certyfikatu SSL z aplikacją usługi App Service przy użyciu interfejsu wiersza polecenia

Ten przykładowy skrypt tworzy aplikację w usłudze App Service z powiązanymi zasobami, a następnie tworzy powiązanie certyfikatu SSL nazwy domeny niestandardowej z tą aplikacją. Do pracy z tym przykładem potrzebne są:

* Dostęp do strony konfiguracji serwera DNS rejestratora domen.
* Prawidłowy plik PFX i jego hasło na potrzeby certyfikatu SSL, który ma zostać przekazany i powiązany.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, musisz mieć interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Tworzy plan usługi App Service. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Tworzy aplikację usługi App Service. |
| [`az webapp config hostname add`](/cli/azure/webapp/config/hostname?view=azure-cli-latest#az-webapp-config-hostname-add) | Mapuje domenę niestandardową na aplikację usługi App Service. |
| [`az webapp config ssl upload`](/cli/azure/webapp/config/ssl?view=azure-cli-latest#az-webapp-config-ssl-upload) | Przekazuje certyfikat SSL do aplikacji usługi App Service. |
| [`az webapp config ssl bind`](/cli/azure/webapp/config/ssl?view=azure-cli-latest#az-webapp-config-ssl-bind) | Tworzy powiązanie przekazanego certyfikatu SSL z aplikacją usługi App Service. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi App Service można znaleźć w [dokumentacji usługi Azure App Service](../samples-cli.md).
