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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774226"
---
Przed rozpoczęciem konfiguracji należy zainstalować i zaimportować wymagane moduły. Do zainstalowania modułów w programie PowerShell będą potrzebne uprawnienia administratora.

1. Instalowanie i importowanie modułu Az
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Instalowanie i importowanie modułu Az.Peering
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Sprawdź, czy moduły są importowane poprawnie za pomocą polecenia poniżej.
    ```powershell
    Get-Module
    ```
1. Zaloguj się do konta platformy Azure przy użyciu następującego polecenia.
    ```powershell
    Connect-AzAccount
    ```
1. Sprawdź subskrypcje dla konta i wybierz subskrypcję, w której chcesz utworzyć komunikację równorzędną.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Jeśli nie masz jeszcze grupy zasobów, należy utworzyć jej przed utworzeniem komunikacji równorzędnej. Można to zrobić, uruchamiając następujące polecenie:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Jeśli nie masz jeszcze skojarzonego asn i subskrypcji, wykonaj kroki dla [skojarzenia peer ASN](../howto-subscription-association-powershell.md). Jest to wymagane do żądania komunikacji równorzędnej.

> [!NOTE]
> Lokalizacja grupy zasobów jest niezależna od lokalizacji, w której można skonfigurować komunikację równorzędną.
&nbsp;
