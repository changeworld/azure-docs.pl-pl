---
title: Zmiany wprowadzone do platformy ASP.NET Core projektu, gdy połączenia usługi Azure Key Vault | Dokumentacja firmy Microsoft
description: W tym artykule opisano, co się dzieje z projektem platformy ASP.NET Core, po nawiązaniu połączenia toKey magazynu przy użyciu usług połączonych programu Visual Studio.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: b7cbe55fa3a524965e0ebc16c5ff350a60d6e440
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2018
ms.locfileid: "42061028"
---
# <a name="what-happened-to-my-aspnet-core-project-visual-studio-key-vault-connected-service"></a>Co się stało z moim projektem platformy ASP.NET Core (usługa połączona programu Visual Studio Key Vault)?

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-key-vault-aspnet-core-get-started.md)
> - [Co się stało](vs-key-vault-aspnet-core-what-happened.md)

W tym artykule identyfikuje dokładne zmiany wprowadzone do projektu programu ASP.NET, podczas dodawania [usługi Key Vault połączone usługi przy użyciu programu Visual Studio](vs-key-vault-add-connected-service.md).

Aby uzyskać informacje na temat pracy z podłączoną usługę, zobacz [wprowadzenie](vs-key-vault-aspnet-core-get-started.md).

## <a name="added-references"></a>Dodano odwołania

Wpływa na odwołania *.NET pliku projektu i odwołania do pakietu NuGet.

| Typ | Informacje ogólne |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

## <a name="added-files"></a>Dodanymi plikami

- Plik ConnectedService.json jest dodawany, który rejestruje pewne informacje o dostawcy usługi połączonej, wersji i łączy dokumentacji.

## <a name="project-file-changes"></a>Zmiany w plikach projektu

- Dodano plik ItemGroup usługi połączone i ConnectedServices.json.

## <a name="launchsettingsjson-changes"></a>zmiany launchsettings.JSON

- Dodano następujące wpisy zmiennych środowiskowych do profilu usługi IIS Express i profilu, który odpowiada nazwa projektu sieci web:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

## <a name="changes-on-azure"></a>Zmiany na platformie Azure

- Utworzono grupę zasobów (lub użyć istniejącego).
- Utworzony magazyn kluczy w określonej grupie zasobów.

