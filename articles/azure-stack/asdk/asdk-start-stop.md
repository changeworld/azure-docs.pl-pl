---
title: Uruchamianie i zatrzymywanie Azure stosu Development Kit (ASDK) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uruchomić i zamknięcie dół Azure stosu Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: dfb565803746ecdda9b36a4e12a3c3f2b4d9e0d0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="start-and-stop-the-azure-stack-development-kit-asdk"></a>Uruchamianie i zatrzymywanie Azure stosu Development Kit (ASDK)
Wystarczy ponownie uruchomić komputer-host ASDK jest niezalecane. Zamiast tego należy wykonać procedury opisane w tym artykule, aby poprawnie zamknięte i ponownie uruchomić usługi ASDK. 

## <a name="stop-azure-stack"></a>Zatrzymaj Azure stosu 
Do poprawnego zamknięcia usług Azure stosu i komputer-host ASDK, użyj następujących poleceń programu PowerShell:

1. Zaloguj się jako AzureStack\CloudAdmin na komputerze-hoście ASDK.
2. Otwórz program PowerShell jako administrator (nie PowerShell ISE).
3. Uruchom następujące polecenia, aby ustanowić sesję uprzywilejowanych punktu końcowego (program ten): 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Następnie w sesji program ten, użyj **Stop AzureStack** polecenia cmdlet do zatrzymywania usług Azure stosu i zamykanie komputera hosta ASDK:

   ```powershell
   Stop-AzureStack
   ```
5. Przejrzyj dane wyjściowe programu PowerShell, aby upewnić się, że wszystkie usługi Azure stosu pomyślnie są zamknięte przed zamknięciem ASDK komputera hosta. Proces zamykania trwa kilka minut.

## <a name="start-azure-stack"></a>Start Azure stosu 
Usługi ASDK powinna być uruchamiana automatycznie po uruchomieniu komputera-hosta. Jednak czas uruchamiania usług infrastruktury ASDK w zależności od wydajności ASDK konfiguracja sprzętowa komputera hosta. Może upłynąć kilka godzin dla wszystkich usług do pomyślnego uruchomienia w niektórych przypadkach.

Niezależnie od tego, jak ASDK został zamknięty sprawdzenie wszystkich usług Azure stosu uruchomiony i w pełni funkcjonalna po komputer-host jest włączony powinien wykonaj następujące kroki: 

1. Włącz komputer-host ASDK. 
2. Zaloguj się jako AzureStack\CloudAdmin na komputerze-hoście ASDK.
3. Otwórz program PowerShell jako administrator (nie PowerShell ISE).
4. Uruchom następujące polecenia, aby ustanowić sesję uprzywilejowanych punktu końcowego (program ten):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. W sesji program ten, uruchom następujące polecenia, aby sprawdzić stan uruchomienia usług Azure stosu:

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. Przejrzyj dane wyjściowe, aby upewnić się, czy usług Azure stosu przebiegło pomyślnie.

Aby dowiedzieć się więcej na temat zalecanych procedur prawidłowo zamknięcia i ponownego uruchomienia usług Azure stosu, zobacz [rozpoczęcie i zakończenie stosu Azure](.\.\azure-stack-start-and-stop.md). 

## <a name="troubleshoot-startup-and-shutdown"></a>Rozwiązywanie problemów z uruchamiania i wyłączania 
Jeśli usług Azure stosu nie można pomyślnie uruchomić w ciągu dwóch godzin od zasilania na komputerze hosta ASDK, wykonaj następujące kroki:

1. Zaloguj się jako AzureStack\CloudAdmin na komputerze-hoście ASDK.
2. Otwórz program PowerShell jako administrator (nie PowerShell ISE).
3. Uruchom następujące polecenia, aby ustanowić sesję uprzywilejowanych punktu końcowego (program ten):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. W sesji program ten, uruchom następujące polecenia, aby sprawdzić stan uruchomienia usług Azure stosu:

   ```powershell
   Test-AzureStack
   ```
5. Przejrzyj dane wyjściowe i usuń wszelkie błędy. Aby uzyskać więcej informacji, zobacz [uruchomienie testu poprawności stosu Azure](.\.\azure-stack-diagnostic-test.md).
6. Uruchom ponownie z usług Azure stosu w ramach sesji program ten, uruchamiając **Start AzureStack** polecenia cmdlet:

   ```powershell
   Start-AzureStack
   ```

Jeśli uruchomiona **Start AzureStack** powoduje błąd, odwiedź stronę [forum pomocy technicznej platformy Azure stosu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack) uzyskanie ASDK dotyczącą rozwiązywania problemów z. 

## <a name="next-steps"></a>Kolejne kroki 
Dowiedz się więcej na temat narzędzia diagnostycznego stosu Azure i wystawiania rejestrowania, zobacz [narzędzia diagnostyczne stosu Azure](.\.\azure-stack-diagnostics.md).
