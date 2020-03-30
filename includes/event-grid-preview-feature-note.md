---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 11/06/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: d32beb2d799a60cb9c5be061c39e4ec834da8dcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66814755"
---
Ta funkcja jest dostępna w wersji zapoznawczej. Aby go użyć, należy zainstalować rozszerzenie lub moduł podglądu.

### <a name="install-extension-for-azure-cli"></a>Rozszerzenie instalacji dla interfejsu wiersza polecenia platformy Azure

W przypadku interfejsu wiersza polecenia platformy Azure potrzebne jest [rozszerzenie siatki zdarzeń](/cli/azure/azure-cli-extensions-list).

W [CloudShell](/azure/cloud-shell/quickstart):

* Jeśli rozszerzenie było wcześniej zainstalowane, zaktualizuj go`az extension update -n eventgrid`
* Jeśli rozszerzenie nie było wcześniej zainstalowane, zainstaluj je`az extension add -n eventgrid`

W przypadku instalacji lokalnej:

1. [Zainstaluj narzędzie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Upewnij się, że masz najnowszą wersję, sprawdzając za pomocą programu `az --version`.
1. Odinstalowywanie poprzednich wersji rozszerzenia`az extension remove -n eventgrid`
1. Zainstaluj `eventgrid` rozszerzenie za pomocą`az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>Moduł instalacyjny programu PowerShell

W przypadku programu PowerShell potrzebny jest [moduł AzureRM.EventGrid](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

W [CloudShell](/azure/cloud-shell/quickstart-powershell):

* Zainstaluj moduł`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

W przypadku instalacji lokalnej:

1. Otwórz konsolę programu PowerShell jako administrator
1. Zainstaluj moduł`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Jeśli `-AllowPrerelease` parametr nie jest dostępny, należy wykonać następujące czynności:

1. Uruchom polecenie `Install-Module PowerShellGet -Force`
1. Uruchom polecenie `Update-Module PowerShellGet`
1. Zamykanie konsoli programu PowerShell
1. Ponowne uruchamianie programu PowerShell jako administratora
1. Zainstaluj moduł`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`
