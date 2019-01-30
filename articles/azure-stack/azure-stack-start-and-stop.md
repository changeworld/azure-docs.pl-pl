---
title: Uruchamianie i zatrzymywanie usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uruchomić, a następnie Zamknij usługę Azure Stack.
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
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: b8d13377b2fb1545ecf88645a5e19bf5eab24c74
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238139"
---
# <a name="start-and-stop-azure-stack"></a>Uruchamianie i zatrzymywanie usługi Azure Stack
Należy wykonać procedury opisane w tym artykule, aby poprawnie zamknąć i ponownie uruchomić usługi Azure Stack. Zamknięcie fizycznie będzie wyłączyć całego środowiska usługi Azure Stack. Uruchamianie włącza wszystkie role infrastruktury i zwraca zasoby dzierżawcy do jego stanu zasilania, w jakim były przed zamknięciem.

## <a name="stop-azure-stack"></a>Zatrzymaj usługę Azure Stack 

Zamknij usługi Azure Stack wykonując następujące kroki:

1. Przygotowanie wszystkich obciążeń uruchomionych na zasoby dzierżawcy środowiskiem usługi Azure Stack nadchodzących zamknięcia systemu. 

2. Otwórz uprzywilejowanych punktu końcowego sesji (program ten) z komputera z dostępem do sieci, aby maszyny wirtualne Azure Stack ERCS. Aby uzyskać instrukcje, zobacz [przy użyciu uprzywilejowanych punktu końcowego w usłudze Azure Stack](azure-stack-privileged-endpoint.md).

3. Program ten Uruchom:

    ```powershell
      Stop-AzureStack
    ```

4. Poczekaj wszystkich fizycznych węzłach usługi Azure Stack do potęgi wyłączone.

> [!Note]  
> Aby sprawdzić stan zasilania fizycznego węzła, postępując zgodnie z instrukcjami z Original Equipment Manufacturer (OEM) który podano sprzętu usługi Azure Stack. 

## <a name="start-azure-stack"></a>Uruchom usługę Azure Stack 

Uruchom usługę Azure Stack wykonując następujące kroki. Wykonaj następujące kroki, niezależnie od tego, jak zatrzymać usługi Azure Stack.

1. Włącz na wszystkich węzłach fizycznych w danym środowisku usługi Azure Stack. Sprawdź możliwości zgodnie z instrukcjami w węzłach fizycznych, postępując zgodnie z instrukcjami z Original Equipment Manufacturer (OEM), który dostarczono sprzętu dla usługi Azure Stack.

2. Poczekaj, aż zostaną uruchomione usługi infrastruktury Azure Stack. Usługi infrastruktury platformy Azure Stack może wymagać 2 godziny zakończenia procesu uruchamiania. Można sprawdzić stanu uruchamiania usługi Azure Stack przy użyciu [ **Get ActionStatus** polecenia cmdlet](#get-the-startup-status-for-azure-stack).

3. Upewnij się, że wszystkie zasoby dzierżawcy mają stan, w jakim były przed zamknięciem. Obciążenia uruchomione na zasoby dzierżawcy może być wymagana ponowna konfiguracja po uruchomieniu przez Menedżera obciążenia.

## <a name="get-the-startup-status-for-azure-stack"></a>Pobierz stan uruchomienia usługi Azure Stack

Pobierz początkową dla procedury uruchomienia usługi Azure Stack wykonując następujące kroki:

1. Otwórz sesję punktu końcowego uprzywilejowanego na komputerze z dostępem do sieci, do maszyn wirtualnych usługi Azure Stack ERCS.

2. Program ten Uruchom:

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>Rozwiązywanie problemów z uruchamiania i zamykania usługi Azure Stack

Wykonaj następujące czynności, jeśli usług infrastruktury i dzierżawcy nie pomyślnie uruchomić 2 godzin po użytkownik zasilania w środowisku usługi Azure Stack. 

1. Otwórz sesję punktu końcowego uprzywilejowanego na komputerze z dostępem do sieci, do maszyn wirtualnych usługi Azure Stack ERCS.

2. Uruchomienie: 

    ```powershell
      Test-AzureStack
      ```

3. Przejrzyj dane wyjściowe i usuń wszelkie błędy kondycji. Aby uzyskać więcej informacji, zobacz [uruchomienie testu poprawności z usługi Azure Stack](azure-stack-diagnostic-test.md).

4. Uruchomienie:

    ```powershell
      Start-AzureStack
    ```

5. Jeśli uruchomiona **Start AzureStack** powoduje błąd, skontaktuj się z pomocą techniczną usługi firmy Microsoft. 

## <a name="next-steps"></a>Kolejne kroki 

Dowiedz się więcej o [narzędzi diagnostycznych w usłudze Azure Stack](azure-stack-diagnostics.md)
