---
title: Zmiany wprowadzone w programie ASP.NET projekt, gdy połączenia usługi Azure Key Vault | Dokumentacja firmy Microsoft
description: W tym artykule opisano, co się dzieje z projektem platformy ASP.NET, po nawiązaniu połączenia toKey magazynu przy użyciu usług połączonych programu Visual Studio.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: a15f9c41c5af8803bfb230b19cbbf2f1bdbc2686
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44050693"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-key-vault-connected-service"></a>Co się stało z moim projektem platformy ASP.NET (usługa połączona programu Visual Studio Key Vault)?

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-key-vault-aspnet-get-started.md)
> - [Co się stało](vs-key-vault-aspnet-what-happened.md)

W tym artykule identyfikuje dokładne zmiany wprowadzone do projektu programu ASP.NET, podczas dodawania [usługi Key Vault połączone usługi przy użyciu programu Visual Studio](vs-key-vault-add-connected-service.md).

Aby uzyskać informacje na temat pracy z podłączoną usługę, zobacz [wprowadzenie](vs-key-vault-aspnet-get-started.md).

## <a name="added-references"></a>Dodano odwołania

Wpływa na odwołania *.NET pliku projektu i `packages.config` (odwołań NuGet).

| Typ | Informacje ogólne |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

## <a name="added-files"></a>Dodanymi plikami

- Plik ConnectedService.json jest dodawany, który rejestruje pewne informacje o dostawcy usługi połączonej, wersji i łączy się z dokumentacją.

## <a name="project-file-changes"></a>Zmiany w plikach projektu

- Dodano plik ItemGroup usługi połączone i ConnectedServices.json.
- Odwołania do zestawów .NET opisanego w [dodaje odwołania do](#added-references) sekcji.

## <a name="webconfig-or-appconfig-changes"></a>plik Web.config lub app.config zmiany

- Dodano następujące pozycje konfiguracji:

    ```xml
    <configSections>
      <section
           name="configBuilders"
           type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" 
           restartOnExternalChanges="false"
           requirePermission="false" />
    </configSections>
    <configBuilders>
      <builders>
        <add 
             name="AzureKeyVault"
             vaultName="vaultname"
             type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral" 
             vaultUri="https://vaultname.vault.azure.net" />
      </builders>
    </configBuilders>
    ```

## <a name="changes-on-azure"></a>Zmiany na platformie Azure

- Utworzono grupę zasobów (lub użyć istniejącego).
- Utworzony magazyn kluczy w określonej grupie zasobów.

