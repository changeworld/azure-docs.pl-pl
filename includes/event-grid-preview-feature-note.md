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
ms.openlocfilehash: 85a1579e32b4c216f234f77c76316bedeaea77b0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119543"
---
Ta funkcja jest dostępna w wersji zapoznawczej. Aby go użyć, należy zainstalować rozszerzenia w wersji zapoznawczej lub modułu.

### <a name="install-extension-for-azure-cli"></a>Zainstaluj rozszerzenie wiersza polecenia platformy Azure

W przypadku wiersza polecenia platformy Azure, należy [rozszerzenia usługi Event Grid](/cli/azure/azure-cli-extensions-list).

W [CloudShell](/azure/cloud-shell/quickstart):

* Jeśli wcześniej zainstalowano rozszerzenie, zaktualizuj go `az extension update -n eventgrid`
* Jeśli wcześniej nie zainstalowano rozszerzenie, zainstaluj go `az extension add -n eventgrid`

W przypadku instalacji lokalnej:

1. Lokalnie Odinstaluj wiersza polecenia platformy Azure.
1. Zainstaluj [najnowszej wersji](/cli/azure/install-azure-cli) z wiersza polecenia platformy Azure.
1. Uruchom okno poleceń.
1. Odinstalowywanie poprzednich wersji rozszerzenia `az extension remove -n eventgrid`
1. Instalowanie rozszerzenia `az extension add -n eventgrid`

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
