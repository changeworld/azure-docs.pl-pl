---
title: Celem rozwiązania do zarządzania na platformie Azure | Dokumentacja firmy Microsoft
description: Przeznaczonych dla rozwiązań do zarządzania pozwala ograniczyć do określonego zestawu agentów rozwiązania do zarządzania.  W tym artykule opisano sposób tworzenia konfiguracji zakresu i zastosować go do rozwiązania.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: 65585e6c09def23101d9735c8b9c719d213938ac
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887846"
---
# <a name="targeting-management-solutions-in-azure-preview"></a>Celem rozwiązania do zarządzania na platformie Azure (wersja zapoznawcza)
Po dodaniu rozwiązania do zarządzania do subskrypcji jest automatycznie wdrażane domyślnie do wszystkich agentów systemu Windows i Linux podłączone do obszaru roboczego analizy dzienników.  Możesz zarządzać kosztów i ograniczyć ilość danych zebranych przez rozwiązanie ograniczając go z określonym zestawem agentów.  W tym artykule opisano sposób użycia **przeznaczonych dla rozwiązania** która to funkcja, która pozwala zastosować zakres do rozwiązań.

## <a name="how-to-target-a-solution"></a>Jak pod kątem rozwiązania
Istnieją trzy kroki umożliwiające rozwiązanie elementów docelowych, zgodnie z opisem w poniższych sekcjach. 


### <a name="1-create-a-computer-group"></a>1. Tworzenie grupy komputerów
Określ komputery, które chcesz uwzględnić w zakresie tworząc [grupy komputerów](../log-analytics/log-analytics-computer-groups.md) w analizy dzienników.  Grupy komputerów można oparte na wyszukiwania dziennika lub zaimportowane z innych źródeł, takich jak grupy usługi Active Directory lub usług WSUS. Jako [opisanych poniżej](#solutions-and-agents-that-cant-be-targeted), tylko te komputery, które są podłączone bezpośrednio do analizy dzienników zostaną uwzględnione w zakresie.

Raz masz utworzony w obszarze roboczym grupy komputerów, a następnie zostaną wykluczone w konfiguracji zakresu, który można zastosować do co najmniej jedno rozwiązanie.
 
 
 ### <a name="2-create-a-scope-configuration"></a>2. Utwórz konfigurację zakresu
 A **konfigurację zakresu** zawiera jeden lub więcej grup komputerów i można zastosować do co najmniej jedno rozwiązanie. 
 
 Utwórz konfigurację zakresu przy użyciu poniższej procedury.  

 1. W portalu Azure, przejdź do **analizy dzienników** i wybierz obszar roboczy.
 2. W obszarze roboczym właściwości **źródeł danych obszaru roboczego** wybierz **konfiguracji zakresu**.
 3. Kliknij przycisk **Dodaj** Aby utworzyć nową konfigurację zakresu.
 4. Wpisz **nazwa** konfigurację zakresu.
 5. Kliknij przycisk **wybierz grupy komputerów**.
 6. Wybierz grupy komputerów, które utworzono i opcjonalnie inne grupy, aby dodać do konfiguracji.  Kliknij pozycję **Wybierz**.  
 6. Kliknij przycisk **OK** utworzyć konfigurację zakresu. 


 ### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. Zastosuj konfigurację zakresu do rozwiązania.
Po utworzeniu konfiguracji zakresu, następnie można użyć jej do co najmniej jedno rozwiązanie.  Należy pamiętać, że podczas konfiguracji jednego zakresu, może być używany z kilku rozwiązań w zakresie, każde rozwiązanie można używać tylko jedną konfigurację zakresu.

Zastosowanie konfiguracji zakresu przy użyciu poniższej procedury.  

 1. W portalu Azure, przejdź do **analizy dzienników** i wybierz obszar roboczy.
 2. W oknie właściwości obszaru roboczego wybierz **rozwiązań**.
 3. Kliknij rozwiązanie, które chcesz zakresu.
 4. We właściwościach rozwiązań w obszarze **źródeł danych obszaru roboczego** wybierz **przeznaczonych dla rozwiązania**.  Jeśli nie jest dostępna opcja następnie [tego rozwiązania nie może być celem](#solutions-and-agents-that-cant-be-targeted).
 5. Kliknij przycisk **Dodaj konfigurację zakresu**.  Jeśli masz już konfiguracji stosowane do tego rozwiązania, a następnie ta opcja będzie niedostępna.  Należy usunąć istniejącą konfigurację przed dodaniem kolejnego.
 6. Kliknij pozycję konfiguracja zakresu utworzony.
 7. Obejrzyj **stan** konfiguracji, aby upewnić się, że widoczny jest **zakończyło się pomyślnie**.  Jeśli stan wskazuje na błąd, kliknij przycisk wielokropka z prawej strony konfigurację i wybierz **konfigurację zakresu edycji** do wprowadzania zmian.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Rozwiązania i agenci, którzy nie mogą być celem
Poniżej przedstawiono kryteria agentów i rozwiązań, które nie można używać z przeznaczonych dla rozwiązania.

- Celem rozwiązanie ma zastosowanie tylko do rozwiązania, które wdrożyć agentów.
- Celem rozwiązanie ma zastosowanie tylko do rozwiązań firmy Microsoft.  Nie ma zastosowania do rozwiązania [utworzony przez siebie lub partnerów](monitoring-solutions-creating.md).
- Można odfiltrować tylko agentów, które łączą się bezpośrednio do analizy dzienników.  Rozwiązania zostaną automatycznie wdrożone do wszystkich agentów, które są częścią podłączonej grupy zarządzania programu Operations Manager, czy są one dołączone do konfiguracji zakresu.

### <a name="exceptions"></a>Wyjątki
Przeznaczonych dla rozwiązania nie można używać z następujących rozwiązań, nawet jeśli mieściły się podanych kryteriów.

- Oceny kondycji agenta

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej na temat rozwiązania, które są dostępne do zainstalowania w danym środowisku, w tym rozwiązania do zarządzania [rozwiązań do zarządzania dodać Analiza dzienników Azure do swojego obszaru roboczego](../log-analytics/log-analytics-add-solutions.md).
- Dowiedz się więcej o tworzeniu grup komputerów na [grup komputerów w analizy dzienników dziennika wyszukiwania](../log-analytics/log-analytics-computer-groups.md).