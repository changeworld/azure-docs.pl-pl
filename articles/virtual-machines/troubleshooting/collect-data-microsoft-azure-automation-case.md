---
title: Dane do zebrania po otwarciu przypadku do automatyzacji Microsoft Azure | Microsoft Docs
description: W tym artykule opisano niektóre podstawowe informacje, które należy zebrać przed otwarciem przypadku Azure Automation z obsługą Microsoft Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: v-miegge
ms.openlocfilehash: 83ee78d369af7fe99de8e7236fe1eb0bc63a942f
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71846682"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Dane do zebrania po otwarciu przypadku Microsoft Azure Automation

W tym artykule opisano niektóre podstawowe informacje, które należy zebrać przed otwarciem przypadku Azure Automation z obsługą Microsoft Azure. Te informacje nie są wymagane do otwarcia przypadku. Może jednak pomóc firmie Microsoft w szybszym rozwiązywaniu problemów. Ponadto użytkownik może zostać poproszony o te dane przez inżyniera pomocy technicznej po otwarciu przypadku.

## <a name="collect-more-information"></a>Zbierz więcej informacji

Przed otwarciem przypadku pomocy technicznej usługi Automatyzacja Microsoft Azure zalecamy zebranie następujących informacji.

### <a name="basic-data-collection"></a>Podstawowe zbieranie danych

Zbierz dane opisane w następującym artykule bazy wiedzy:

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) jak przechwycić diagnostykę Azure Automation-skryptową

## <a name="collect-data-depending-on-issue"></a>Zbieranie danych w zależności od problemu
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>W przypadku problemów, które mają wpływ na Update Management w systemie Linux

1. Oprócz elementów, które są wymienione w KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), uruchom następujące narzędzie do zbierania dzienników:

   [Moduł zbierający dzienników agenta pakietu OMS Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Kompresuj zawartość następującego folderu, a następnie Wyślij skompresowany plik do pomocy technicznej platformy Azure:

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. Sprawdź, czy identyfikator obszaru roboczego, do którego jest raportowany Agent pakietu OMS systemu Linux, jest taki sam jak obszar roboczy monitorowany w poszukiwaniu aktualizacji.

### <a name="for-issues-that-affect-update-management-on-windows"></a>W przypadku problemów, które mają wpływ na Update Management w systemie Windows

Oprócz elementów, które są wymienione w [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), wykonaj następujące czynności:

1. Wyeksportuj następujące dzienniki zdarzeń do formatu EVTX:

   * System
   * Aplikacja
   * Zabezpieczenia
   * Operations Manager
   * Microsoft — SMA/Operational

2. Sprawdź, czy identyfikator obszaru roboczego, do którego jest raportowany przez agenta, jest taki sam jak obszar roboczy monitorowany przez aktualizacje systemu Windows.

### <a name="for-issues-that-affect-a-job"></a>W przypadku problemów, które mają wpływ na zadanie

Oprócz elementów wymienionych w [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)należy zebrać następujące informacje:

1. Zbierz numer **JobID** (dla zadania, w którym występuje problem):

   1. W Azure Portal przejdź do bloku **konta usługi Automation** .
   2. Wybierz **konto usługi Automation** , którego będziesz rozwiązywać problemy.
   3. Wybierz pozycję **zadania**.
   4. Wybierz zadanie, które ma być rozwiązywane.
   5. W obszarze **Podsumowanie zadania**Znajdź **Identyfikator zadania** (GUID).

   ![Identyfikator zadania w okienku podsumowania zadania](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. Zbierz nazwę konta:

   1. W Azure Portal przejdź do bloku **konta usługi Automation** .
   2. Pobierz nazwę **konta usługi Automation** , którego jesteś w trakcie rozwiązywania problemów.

3. Zbierz próbkę uruchomionego skryptu.

4. Zbierz pliki dziennika:

   1. W Azure Portal przejdź do bloku **konta usługi Automation** .
   2. Wybierz **konto usługi Automation** , którego będziesz rozwiązywać problemy.
   3. Wybierz pozycję **zadania**.
   4. Wybierz zadanie, które ma być rozwiązywane.
   5. Wybierz pozycję **wszystkie dzienniki**.
   6. W bloku wyniki Zbieraj dane.

   ![Dane wymienione w obszarze wszystkie dzienniki](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>W przypadku problemów, które mają wpływ na moduły

Oprócz elementów w obszarze "podstawowe zbieranie danych" Zbierz następujące informacje:

* Kroki, które należy wykonać, aby można było odtworzyć problem.
* Zrzut ekranu przedstawiający wszystkie komunikaty o błędach.
* Zrzut ekranu przedstawiający bieżące moduły i numery wersji.

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, skontaktuj się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/).

Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.