---
title: Określanie wartości docelowej monitorowanie rozwiązań w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Określanie wartości docelowej rozwiązania do monitorowania umożliwia ograniczenie rozwiązania do monitorowania do określonego zestawu agentów.  W tym artykule opisano sposób tworzenia konfiguracji zakresu i zastosować go do rozwiązania.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: 4082847e1871fc03713471b0c043dddb80f91b0d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62110348"
---
# <a name="targeting-monitoring-solutions-in-azure-monitor-preview"></a>Określanie wartości docelowej rozwiązania do monitorowania w usłudze Azure Monitor (wersja zapoznawcza)
Po dodaniu rozwiązania do monitorowania ze swoją subskrypcją, jest automatycznie wdrażane domyślnie wszyscy agenci Windows i Linux, połączonego z obszarem roboczym usługi Log Analytics.  Można zarządzać koszty i ograniczyć ilość danych zebranych dla rozwiązania przez ograniczenie go do określonego zestawu agentów.  W tym artykule opisano sposób używania **określania celu rozwiązania** czyli funkcja umożliwiająca można zastosować zakres do rozwiązania.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="how-to-target-a-solution"></a>Jak docelowego rozwiązania
Istnieją trzy kroki, aby przeznaczonych dla rozwiązania, zgodnie z opisem w poniższych sekcjach. 


### <a name="1-create-a-computer-group"></a>1. Utwórz grupę komputerów
Określ komputery, które mają zostać uwzględnione w zakresie, tworząc [grupa](../platform/computer-groups.md) w usłudze Azure Monitor.  Grupy komputerów można opartych na zapytaniach dzienników lub zaimportowanych z innych źródeł, takich jak grupy usługi Active Directory lub programu WSUS. Jako [opisanych poniżej](#solutions-and-agents-that-cant-be-targeted), tylko te komputery, które są podłączone bezpośrednio do usługi Azure Monitor, które zostaną uwzględnione w zakresie.

Raz masz grupa utworzona w obszarze roboczym, a następnie zostaną objęte konfiguracji zakresu, który można zastosować do jednego lub więcej rozwiązań.
 
 
### <a name="2-create-a-scope-configuration"></a>2. Tworzenie konfiguracji zakresu
 A **konfiguracji zakresu** zawiera jeden lub więcej grup komputerów i mogą być stosowane do jednej lub więcej rozwiązań. 
 
 Tworzenie konfiguracji zakresu, za pomocą poniższego procesu.  

 1. W witrynie Azure portal przejdź do **obszarów roboczych usługi Log Analytics** i wybierz swój obszar roboczy.
 2. We właściwościach obszaru roboczego w ramach **źródła danych obszaru roboczego** wybierz **konfiguracji zakresu**.
 3. Kliknij przycisk **Dodaj** można utworzyć nowej konfiguracji zakresu.
 4. Wpisz **nazwa** dla konfiguracji zakresu.
 5. Kliknij przycisk **wybierz grupy komputerów**.
 6. Wybierz grupę komputerów, który został utworzony i opcjonalnie innych grup, Dodaj do konfiguracji.  Kliknij pozycję **Wybierz**.  
 6. Kliknij przycisk **OK** można utworzyć konfiguracji zakresu. 


### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. Zastosuj konfigurację zakresu do rozwiązania.
Po utworzeniu konfiguracji zakresu, następnie można zastosować go do jednego lub więcej rozwiązań.  Należy pamiętać, że konfiguracja pojedynczy zakres można zastosować wiele rozwiązań, każde z tych rozwiązań można używać tylko jedną konfigurację zakresu.

Zastosuj konfigurację zakresu, za pomocą poniższego procesu.  

 1. W witrynie Azure portal przejdź do **obszarów roboczych usługi Log Analytics** i wybierz swój obszar roboczy.
 2. We właściwościach dla obszaru roboczego wybierz **rozwiązania**.
 3. Kliknij rozwiązanie nad którym chcesz zakresu.
 4. We właściwościach rozwiązania w folderze **źródła danych obszaru roboczego** wybierz **określania celu rozwiązania**.  Jeśli nie jest dostępna opcja następnie [tego rozwiązania nie mogą być celem](#solutions-and-agents-that-cant-be-targeted).
 5. Kliknij przycisk **Dodaj konfigurację zakresu**.  Jeśli masz już konfigurację zastosowane do tego rozwiązania, a następnie ta opcja będzie niedostępna.  Należy usunąć istniejącą konfigurację, przed dodaniem kolejnego.
 6. Kliknij pozycję konfiguracja zakresu utworzony.
 7. Obejrzyj **stan** konfiguracji, aby upewnić się, że zawiera **Powodzenie**.  Jeśli stan wskazuje na błąd, kliknij przycisk wielokropka z prawej strony konfiguracji, a następnie wybierz pozycję **konfiguracji zakresu edycji** zmian.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Rozwiązania i agenci, którzy nie mogą być jednak celem
Poniżej przedstawiono kryteria agentów i ich rozwiązania, które nie mogą być używane z określania celu rozwiązania.

- Określanie celu rozwiązania ma zastosowanie tylko do rozwiązania, które wdrożyć agentów.
- Określanie celu rozwiązania ma zastosowanie tylko do rozwiązania firmy Microsoft.  Nie ma zastosowania do rozwiązania [utworzonych przez siebie lub partnerów](solutions-creating.md).
- Można odfiltrować tylko agentów, które łączą się bezpośrednio do usługi Azure Monitor.  Rozwiązania automatycznie wdroży go do wszystkich agentów, które są częścią podłączonej grupy zarządzania programu Operations Manager, czy są one dołączone w konfiguracji zakresu.

### <a name="exceptions"></a>Wyjątki
Określanie celu rozwiązania nie można używać z następujących rozwiązań, mimo że mieściły się podanych kryteriów.

- Ocena kondycji agenta

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej na temat rozwiązania, które są dostępne do zainstalowania w Twoim środowisku, w tym rozwiązania do monitorowania [Dodawanie usługi Azure Log Analytics monitorowanie rozwiązań do obszaru roboczego](solutions.md).
- Dowiedz się więcej na temat tworzenia grup komputerów na [grup komputerów w usłudze Azure Monitor rejestrowania zapytań](../platform/computer-groups.md).
