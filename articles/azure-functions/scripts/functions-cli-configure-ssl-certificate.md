---
title: Przykład skryptu interfejsu wiersza polecenia platformy Azure — tworzenie powiązania niestandardowego certyfikatu SSL z aplikacją funkcji | Microsoft Docs
description: Przykład skryptu interfejsu wiersza polecenia platformy Azure — tworzenie powiązania niestandardowego certyfikatu SSL z aplikacją funkcji na platformie Azure
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/03/2013
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 84cc4e92419e683f8d2c29883b26148fdef23356
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60325735"
---
# <a name="bind-a-custom-ssl-certificate-to-a-function-app"></a>Tworzenie powiązania niestandardowego certyfikatu SSL z aplikacją funkcji

Ten przykładowy skrypt służy do tworzenia aplikacji funkcji z powiązanymi zasobami, a następnie tworzenia powiązania certyfikatu SSL nazwy domeny niestandardowej z tą aplikacją. Do pracy z tym przykładem potrzebne są:

* Dostęp do strony konfiguracji serwera DNS rejestratora domen.
* Prawidłowy plik PFX i jego hasło na potrzeby certyfikatu SSL, który ma zostać przekazany i powiązany.
* Rekord A skonfigurowany w domenie niestandardowej, który wskazuje domyślną nazwę domeny aplikacji internetowej. Aby uzyskać więcej informacji, zobacz [Instrukcje mapowania domeny niestandardowej dla usługi Azure App Service](https://aka.ms/appservicecustomdns).

Aby utworzyć powiązanie certyfikatu SSL, aplikacja funkcji musi zostać utworzona w ramach planu usługi App Service, a nie planu Zużycie.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Tworzy konto magazynu wymagane przez aplikację funkcji. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az-appservice-plan-create) | Tworzy plan usługi App Service wymagany do utworzenia powiązania certyfikatów SSL. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Tworzy aplikację funkcji w planie usługi App Service. |
| [az functionapp config hostname add](https://docs.microsoft.com/cli/azure/functionapp/config/hostname#az-functionapp-config-hostname-add) | Mapuje domenę niestandardową na aplikację funkcji. |
| [az functionapp config ssl upload](https://docs.microsoft.com/cli/azure/functionapp/config/ssl#az-functionapp-config-ssl-upload) | Przekazuje certyfikat SSL do aplikacji funkcji. |
| [az functionapp config ssl bind](https://docs.microsoft.com/cli/azure/functionapp/config/ssl#az-functionapp-config-ssl-bind) | Tworzy powiązanie przekazanego certyfikatu SSL z aplikacją funkcji. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi App Service można znaleźć w [dokumentacji usługi Azure App Service](../functions-cli-samples.md).
