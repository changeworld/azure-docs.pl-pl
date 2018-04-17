---
title: Uruchamianie i zatrzymywanie stosu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uruchomić i zamknąć stosu Azure.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 43BF9DCF-F1B7-49B5-ADC5-1DA3AF9668CA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 53015ba5c282bbe9c7b8185b080ffb6d834b6c75
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="start-and-stop-azure-stack"></a>Uruchamianie i zatrzymywanie Azure stosu
Należy wykonać procedury opisane w tym artykule, aby poprawnie zamykania i ponownego uruchamiania usług Azure stosu. 

## <a name="stop-azure-stack"></a>Zatrzymaj Azure stosu 

Stos Azure należy zamknąć następujące czynności:

1. Otwórz uprzywilejowanych punktu końcowego sesji (program ten) z komputera z dostępem do sieci, aby maszyny wirtualne Azure stosu ERCS. Aby uzyskać instrukcje, zobacz [przy użyciu punktu końcowego uprzywilejowanych w stosie Azure](azure-stack-privileged-endpoint.md).

2. Program ten Uruchom:

    ```powershell
      Stop-AzureStack
    ```

3. Poczekaj na wszystkich fizycznych węzłach stosu Azure do potęgi.

> [!Note]  
> Zgodnie z instrukcjami z Original Equipment Manufacturer (OEM) który dostarczony sprzętu stosu Azure można zweryfikować stan zasilania węzła fizycznego. 

## <a name="start-azure-stack"></a>Start Azure stosu 

Uruchom stosu Azure następujące kroki. Wykonaj następujące kroki, niezależnie od tego, jak zatrzymać stosu Azure.

1. Włącz na wszystkich węzłach fizycznych w środowisku Azure stosu. Sprawdź zasilania w instrukcjach dla węzłów fizycznych zgodnie z instrukcjami z Original Equipment Manufacturer (OEM) który podany sprzętu dla stosu Azure.

2. Poczekaj, aż uruchamia usługi infrastruktury platformy Azure stosu. Usług infrastruktury platformy Azure stosu może wymagać dwóch godzin w celu zakończenia procesu uruchamiania. Można sprawdzić stan początkowy stosu Azure z [ **Get-ActionStatus** polecenia cmdlet](#get-the-startup-status-for-azure-stack).


## <a name="get-the-startup-status-for-azure-stack"></a>Pobierz stan uruchomienia Azure stosu

Pobierz początkową dla procedury uruchomienia stosu Azure następujące czynności:

1. Otwórz sesję punktu końcowego uprzywilejowanego na komputerze z dostępem do sieci, z maszynami wirtualnymi Azure stosu ERCS.

2. Program ten Uruchom:

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>Rozwiązywanie problemów z uruchamiania i wyłączania Azure stosu

Jeśli usługi infrastruktury i dzierżawcy nie pomyślnie uruchomić 2 godziny po wyłączysz w środowisku Azure stosu, wykonaj następujące kroki. 

1. Otwórz sesję punktu końcowego uprzywilejowanego na komputerze z dostępem do sieci, z maszynami wirtualnymi Azure stosu ERCS.

2. Uruchom: 

    ```powershell
      Test-AzureStack
      ```

3. Przejrzyj dane wyjściowe i usuń wszelkie błędy kondycji. Aby uzyskać więcej informacji, zobacz [uruchomienie testu poprawności stosu Azure](azure-stack-diagnostic-test.md).

4. Uruchom:

    ```powershell
      Start-AzureStack
    ```

5. Jeśli uruchomiona **Start AzureStack** powoduje błąd, skontaktuj się z pomocą techniczną usługi firmy Microsoft. 

## <a name="next-steps"></a>Kolejne kroki 

Dowiedz się więcej na temat narzędzia diagnostycznego stosu Azure i wystawiania rejestrowania, zobacz [narzędzia diagnostyczne stosu Azure](azure-stack-diagnostics.md).
