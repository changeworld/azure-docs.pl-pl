---
title: Zmiany wprowadzone w programie ASP.NET projekt, kiedy połączenia do usługi Azure Key Vault | Dokumentacja firmy Microsoft
description: W tym artykule opisano, co się dzieje z projektu programu ASP.NET, po podłączeniu magazynu toKey za pomocą usługi Visual Studio połączony.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: a43f893c7ee87ffb02179c06ea5786715547e93a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-key-vault-connected-service"></a>Co się stało z projektu programu ASP.NET (Visual Studio Key Vault połączona usługa)?

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-key-vault-aspnet-get-started.md)
> - [Co się stało](vs-key-vault-aspnet-what-happened.md)

W tym artykule identyfikuje dokładne zmiany wprowadzone do projektu programu ASP.NET, podczas dodawania [Key Vault połączenia usługi przy użyciu programu Visual Studio](vs-key-vault-add-connected-service.md).

Aby uzyskać informacje na temat pracy z podłączonej usługi, zobacz [wprowadzenie](vs-key-vault-aspnet-get-started.md).

## <a name="added-references"></a>Dodano odwołań

Wpływa na *.NET odwołań pliku projektu i `packages.config` (NuGet odwołania).

| Typ | Informacje ogólne |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

## <a name="added-files"></a>Dodanymi plikami

- Dodano ConnectedService.json, która rejestruje niektóre informacje na temat połączenia usługodawcy, wersji i połącz się z dokumentacją.

## <a name="project-file-changes"></a>Zmiany w pliku projektu

- Dodaje plik połączony ItemGroup usług i ConnectedServices.json.
- Odwołania do zestawów platformy .NET, opisanego w [dodać odwołania do](#added-references) sekcji.

## <a name="webconfig-or-appconfig-changes"></a>zmiany w pliku Web.config lub app.config

- Dodano następujące pozycje konfiguracji:

    ```xml
    <appSettings>
       <add key="vaultName" value="<your Key Vault name>" />
       <add key="vaultUri" value="<the URI to your Key Vault in Azure>" />
    </appSettings>
    ```

## <a name="changes-on-azure"></a>Zmiany na platformie Azure

- Utworzona grupa zasobów (lub użyć istniejącego).
- Utworzony magazyn kluczy w określonej grupy zasobów.

