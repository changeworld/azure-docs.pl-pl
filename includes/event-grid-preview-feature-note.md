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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66814755"
---
Ta funkcja jest dostępna w wersji zapoznawczej. Aby go użyć, należy zainstalować rozszerzenia w wersji zapoznawczej lub modułu.

### <a name="install-extension-for-azure-cli"></a>Zainstaluj rozszerzenie wiersza polecenia platformy Azure

W przypadku wiersza polecenia platformy Azure, należy [rozszerzenia usługi Event Grid](/cli/azure/azure-cli-extensions-list).

W [CloudShell](/azure/cloud-shell/quickstart):

* Jeśli wcześniej zainstalowano rozszerzenie, zaktualizuj go `az extension update -n eventgrid`
* Jeśli wcześniej nie zainstalowano rozszerzenie, zainstaluj go `az extension add -n eventgrid`

W przypadku instalacji lokalnej:

1. [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Upewnij się, że masz najnowszą wersję, sprawdzając z `az --version`.
1. Odinstalowywanie poprzednich wersji rozszerzenia `az extension remove -n eventgrid`
1. Zainstaluj `eventgrid` rozszerzenie `az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>Instalowanie modułu programu PowerShell

W przypadku programu PowerShell należy [modułu AzureRM.EventGrid](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

W [CloudShell](/azure/cloud-shell/quickstart-powershell):

* Zainstaluj moduł `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

W przypadku instalacji lokalnej:

1. Otwórz konsolę programu PowerShell jako administrator
1. Zainstaluj moduł `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Jeśli `-AllowPrerelease` parametr nie jest dostępna, wykonaj następujące czynności:

1. Uruchom polecenie `Install-Module PowerShellGet -Force`
1. Uruchom polecenie `Update-Module PowerShellGet`
1. Zamknij konsolę programu PowerShell
1. Ponownie uruchom program PowerShell jako administrator
1. Zainstaluj moduł `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`
