---
title: Dane zbierane po otwarciu sprawy dla usługi Microsoft Azure Automation| Dokumenty firmy Microsoft
description: W tym artykule opisano niektóre z podstawowych informacji, które należy zebrać przed otwarciem sprawy dla usługi Azure Automation z pomocą techniczną platformy Microsoft Azure.
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
ms.openlocfilehash: 4839ce7a0188c782656fd3a4c42cbdd116b165e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849381"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Dane, które należy zebrać podczas otwierania przypadku dla usługi Microsoft Azure Automation

W tym artykule opisano niektóre z podstawowych informacji, które należy zebrać przed otwarciem sprawy dla usługi Azure Automation z pomocą techniczną platformy Microsoft Azure. Informacje te nie są wymagane do otwarcia sprawy. Może to jednak pomóc firmie Microsoft w rozwiązaniu problemu szybciej. Ponadto po otwarciu sprawy inżynier pomocy technicznej może zostać poproszony o podanie tych danych.

## <a name="collect-more-information"></a>Zbierz więcej informacji

Przed otwarciem sprawy dla pomocy technicznej automatyzacji platformy Microsoft Azure zaleca się zebranie następujących informacji.

### <a name="basic-data-collection"></a>Podstawowe gromadzenie danych

Zbieranie danych opisanych w następującym artykule z bazy wiedzy Knowledge Base:

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) Jak przechwytywać diagnostykę skryptową usługi Azure Automation

## <a name="collect-data-depending-on-issue"></a>Zbieranie danych w zależności od problemu
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>W przypadku problemów, które wpływają na zarządzanie aktualizacjami w systemie Linux

1. Oprócz elementów wymienionych w KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)uruchom następujące narzędzie do zbierania dzienników:

   [Moduł zbierający dzienniki agenta systemu OMS Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Skompresuj zawartość następującego folderu, a następnie wyślij skompresowany plik do pomocy technicznej platformy Azure:

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. Sprawdź, czy identyfikator obszaru roboczego, który zgłasza agent systemu OMS Linux, jest taki sam jak obszar roboczy monitorowany pod kątem aktualizacji.

### <a name="for-issues-that-affect-update-management-on-windows"></a>W przypadku problemów, które wpływają na zarządzanie aktualizacjami w systemie Windows

Oprócz elementów wymienionych w [pozycji 4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)wykonaj następujące czynności:

1. Wyeksportuj następujące dzienniki zdarzeń do formatu EVTX:

   * System
   * Aplikacja
   * Zabezpieczenia
   * Operations Manager
   * Microsoft-SMA/Operacyjna

2. Sprawdź, czy identyfikator obszaru roboczego, do który zgłasza agent, jest taki sam jak obszar roboczy monitorowany przez aktualizacje systemu Windows.

### <a name="for-issues-that-affect-a-job"></a>W przypadku problemów, które wpływają na pracę

Oprócz pozycji wymienionych w [pozycji 4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)należy zebrać następujące informacje:

1. Zbieranie numeru **JobID** (dla zadania, w którym występuje problem):

   1. W witrynie Azure portal przejdź do **bloku Konta automatyzacji.**
   2. Wybierz **konto automatyzacji,** które rozwiązujesz problemy.
   3. Wybierz pozycję **Zadania**.
   4. Wybierz zadanie, które rozwiązujesz problemy.
   5. W obszarze **Podsumowanie zadania**poszukaj **identyfikatora zadania** (GUID).

   ![Identyfikator zadania w okienku podsumowania zadania](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. Zbierz nazwę konta:

   1. W witrynie Azure portal przejdź do **bloku Konta automatyzacji.**
   2. Uzyskaj nazwę **konta automatyzacji,** które rozwiązujesz problemy.

3. Zbierz przykład skryptu, który jest uruchomiony.

4. Zbieranie plików dziennika:

   1. W witrynie Azure portal przejdź do **bloku Konta automatyzacji.**
   2. Wybierz **konto automatyzacji,** które rozwiązujesz problemy.
   3. Wybierz pozycję **Zadania**.
   4. Wybierz zadanie, które rozwiązujesz problemy.
   5. Wybierz **pozycję Wszystkie dzienniki**.
   6. Na wynikowym bloku zbierz dane.

   ![Dane wymienione w obszarze Wszystkie dzienniki](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>W przypadku problemów, które wpływają na moduły

Oprócz pozycji w części "Podstawowe gromadzenie danych" należy zebrać następujące informacje:

* Kroki, które zostały następnie, tak aby problem może być odtworzony.
* Zrzut ekranu z wszelkimi komunikatami o błędach.
* Zrzut ekranu przedstawiający bieżące moduły i ich numery wersji.

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, skontaktuj się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/).

Alternatywnie złóż błąd pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.
