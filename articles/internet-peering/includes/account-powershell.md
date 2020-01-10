---
title: Plik dyrektywy include
titleSuffix: Azure
description: Plik dyrektywy include
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3a5f7157ef8f3645dd03ec93684238dd8bbc067e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774226"
---
Przed rozpoczęciem konfiguracji należy zainstalować i zaimportować wymagane moduły. Do zainstalowania modułów w programie PowerShell wymagane są uprawnienia administratora.

1. Zainstaluj i Importuj AZ module
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Instalowanie i importowanie modułu AZ. peering
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Sprawdź, czy moduły zostały zaimportowane przy użyciu poniższego polecenia.
    ```powershell
    Get-Module
    ```
1. Zaloguj się do konta platformy Azure przy użyciu następującego polecenia.
    ```powershell
    Connect-AzAccount
    ```
1. Sprawdź subskrypcje konta i wybierz subskrypcję, w której chcesz utworzyć komunikację równorzędną.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Jeśli nie masz jeszcze grupy zasobów, musisz ją utworzyć przed utworzeniem komunikacji równorzędnej. Możesz to zrobić, uruchamiając następujące polecenie:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Jeśli nie masz jeszcze skojarzonego numeru ASN i subskrypcji, wykonaj czynności opisane w sekcji [Skojarz równorzędny numer ASN](../howto-subscription-association-powershell.md). Jest to wymagane do zażądania komunikacji równorzędnej.

> [!NOTE]
> Lokalizacja grupy zasobów jest niezależna od lokalizacji, w której użytkownik zdecyduje się skonfigurować komunikację równorzędną.
&nbsp;
