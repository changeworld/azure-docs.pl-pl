---
title: Zmiany wprowadzone do platformy ASP.NET Core projektu, gdy połączenia do usługi Azure Key Vault | Dokumentacja firmy Microsoft
description: W tym artykule opisano, co się dzieje z projektu platformy ASP.NET Core po podłączeniu magazynu toKey za pomocą usługi Visual Studio połączony.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: 8b6c590344db2997c1a987da14cabba76cdca83b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="what-happened-to-my-aspnet-core-project-visual-studio-key-vault-connected-service"></a>Co się stało z projektu platformy ASP.NET Core (usługi Visual Studio Key Vault połączony)?

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-key-vault-aspnet-core-get-started.md)
> - [Co się stało](vs-key-vault-aspnet-core-what-happened.md)

W tym artykule identyfikuje dokładne zmiany wprowadzone do projektu programu ASP.NET, podczas dodawania [Key Vault połączenia usługi przy użyciu programu Visual Studio](vs-key-vault-add-connected-service.md).

Aby uzyskać informacje na temat pracy z podłączonej usługi, zobacz [wprowadzenie](vs-key-vault-aspnet-core-get-started.md).

## <a name="added-references"></a>Dodano odwołań

Wpływa na odwołania *.NET pliku projektu i odwołania do pakietu NuGet.

| Typ | Informacje ogólne |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

## <a name="added-files"></a>Dodanymi plikami

- Dodany ConnectedService.json, który rejestruje niektóre informacje na temat połączenia usługodawcy, wersji i łącze dokumentacji.

## <a name="project-file-changes"></a>Zmiany w pliku projektu

- Dodaje plik połączony ItemGroup usług i ConnectedServices.json.

## <a name="launchsettingsjson-changes"></a>launchsettings.JSON zmiany

- Dodano następujące wpisy zmiennej środowiskowej do profilu serwera IIS Express i profil, który odpowiada nazwę projektu sieci web:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

## <a name="changes-on-azure"></a>Zmiany na platformie Azure

- Utworzona grupa zasobów (lub użyć istniejącego).
- Utworzony magazyn kluczy w określonej grupy zasobów.

