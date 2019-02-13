---
title: Uruchamianie i zatrzymywanie usługi Azure Stack Development Kit (ASDK) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uruchamianie i zamykanie szczegółów usługi Azure Stack Development Kit (ASDK).
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
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 0073427028d20ab969b8d0135b1c831d7fdf8c73
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191973"
---
# <a name="start-and-stop-the-azure-stack-development-kit-asdk"></a>Uruchamianie i zatrzymywanie usługi Azure Stack Development Kit (ASDK)
Wystarczy ponownie uruchomić komputer-host ASDK jest niezalecane. Zamiast tego należy wykonać procedury opisane w tym artykule, aby poprawnie zamknąć i ponownie uruchomić usługi ASDK. 

## <a name="stop-azure-stack"></a>Zatrzymaj usługę Azure Stack 
Aby prawidłowo zamknięta i komputer-host ASDK usługi Azure Stack, użyj następujących poleceń programu PowerShell:

1. Zaloguj się jako AzureStack\CloudAdmin na komputerze-hoście ASDK.
2. Otwórz program PowerShell jako administrator (nie PowerShell ISE).
3. Uruchom następujące polecenia, aby ustanowić sesję uprzywilejowanych punktu końcowego (program ten): 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Następnie w sesji program ten, użyj **Stop AzureStack** polecenia cmdlet, aby zatrzymać usługi Azure Stack i zamykanie komputera hosta ASDK:

   ```powershell
   Stop-AzureStack
   ```
5. Przejrzyj dane wyjściowe programu PowerShell, aby upewnić się, że wszystkie usługi Azure Stack jest zamknięta pomyślnie przed zamknięciem ASDK komputera hosta. Proces zamykania trwa kilka minut.

## <a name="start-azure-stack"></a>Uruchom usługę Azure Stack 
Usługi ASDK powinna być uruchamiana automatycznie podczas uruchamiania komputera hosta. Jednak czas uruchamiania usług infrastruktury ASDK różni się w zależności od konfiguracji sprzętowej komputera hosta ASDK wydajności. Może upłynąć kilka godzin dla wszystkich usług pomyślnie uruchomić w niektórych przypadkach.

Niezależnie od tego, jak ASDK został zamknięty powinny wykonaj następujące kroki, aby zweryfikować, że wszystkie usługi Azure Stack są uruchomione i w pełni funkcjonalne po komputer-host jest włączony: 

1. Zasilania na komputerze-hoście ASDK. 
2. Zaloguj się jako AzureStack\CloudAdmin na komputerze-hoście ASDK.
3. Otwórz program PowerShell jako administrator (nie PowerShell ISE).
4. Uruchom następujące polecenia, aby ustanowić sesję uprzywilejowanych punktu końcowego (program ten):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. Następnie w sesji program ten, uruchom następujące polecenia, aby sprawdzić stan uruchomienia usługi Azure Stack:

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. Przejrzyj dane wyjściowe, aby upewnić się, że usługi Azure Stack ponownym uruchomieniu pomyślnie.

Aby dowiedzieć się więcej na temat procedury zalecane poprawnie zamknąć i ponownie uruchomić usługi Azure Stack, zobacz [uruchamianie i zatrzymywanie usługi Azure Stack](../azure-stack-start-and-stop.md). 

## <a name="troubleshoot-startup-and-shutdown"></a>Rozwiązywanie problemów z uruchamiania i zamykania 
Jeśli usługi Azure Stack nie uruchamia się pomyślnie w ciągu dwóch godzin od zasilania na komputerze hosta ASDK, wykonaj następujące kroki:

1. Zaloguj się jako AzureStack\CloudAdmin na komputerze-hoście ASDK.
2. Otwórz program PowerShell jako administrator (nie PowerShell ISE).
3. Uruchom następujące polecenia, aby ustanowić sesję uprzywilejowanych punktu końcowego (program ten):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Następnie w sesji program ten, uruchom następujące polecenia, aby sprawdzić stan uruchomienia usługi Azure Stack:

   ```powershell
   Test-AzureStack
   ```
5. Przejrzyj dane wyjściowe i usuń wszelkie błędy. Aby uzyskać więcej informacji, zobacz [uruchomienie testu poprawności z usługi Azure Stack](../azure-stack-diagnostic-test.md).
6. Uruchom ponownie usługi Azure Stack w ramach sesji program ten uruchamiając **Start AzureStack** polecenia cmdlet:

   ```powershell
   Start-AzureStack
   ```

Jeśli uruchomiona **Start AzureStack** powoduje błąd, odwiedź stronę [forum pomocy technicznej usługi Azure Stack](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack) można pobrać ASDK Rozwiązywanie problemów z pomocy technicznej. 

## <a name="next-steps"></a>Kolejne kroki 
Dowiedz się więcej na temat narzędzia diagnostycznego usługi Azure Stack i wystawiać rejestrowania, zobacz [narzędzi diagnostycznych w usłudze Azure Stack](../azure-stack-diagnostics.md).
