---
title: Rozwiązania do monitorowania kierowania w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Rozwiązania do monitorowania kierowania pozwala ograniczyć rozwiązania monitorowania do określonego zestawu agentów.  W tym artykule opisano sposób tworzenia konfiguracji zakresu i stosowania go do rozwiązania.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: dd3279db67fb45aee43cf1b0ef1bebf49433eef4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663236"
---
# <a name="targeting-monitoring-solutions-in-azure-monitor-preview"></a>Rozwiązania do monitorowania kierowania w usłudze Azure Monitor (wersja zapoznawcza)
Po dodaniu rozwiązania monitorowania do subskrypcji jest ono domyślnie automatycznie wdrażane dla wszystkich agentów systemu Windows i systemu Linux połączonych z obszarem roboczym usługi Log Analytics.  Można zarządzać kosztami i ograniczyć ilość danych zebranych dla rozwiązania, ograniczając je do określonego zestawu agentów.  W tym artykule opisano sposób korzystania z **kierowania rozwiązania,** który jest funkcją, która umożliwia zastosowanie zakresu do rozwiązań.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="how-to-target-a-solution"></a>Jak kierować rozwiązanie
Istnieją trzy kroki kierowania rozwiązania, zgodnie z opisem w poniższych sekcjach. 


### <a name="1-create-a-computer-group"></a>1. Tworzenie grupy komputerów
Określ komputery, które mają zostać uwzględnione w zakresie, tworząc [grupę komputerów](../platform/computer-groups.md) w usłudze Azure Monitor.  Grupa komputerów może być oparta na kwerendzie dziennika lub importowana z innych źródeł, takich jak active directory lub grupy WSUS. Jak [opisano poniżej,](#solutions-and-agents-that-cant-be-targeted)tylko komputery, które są bezpośrednio połączone z usługą Azure Monitor zostaną uwzględnione w zakresie.

Po utworzeniu grupy komputerów w obszarze roboczym, następnie należy dołączyć go w konfiguracji zakresu, które mogą być stosowane do jednego lub więcej rozwiązań.
 
 
### <a name="2-create-a-scope-configuration"></a>2. Tworzenie konfiguracji zakresu
 **Konfiguracja zakresu** obejmuje jedną lub więcej grup komputerów i może być stosowana do jednego lub więcej rozwiązań. 
 
 Utwórz konfigurację zakresu przy użyciu następującego procesu.  

 1. W witrynie Azure portal przejdź do **obszarów roboczych usługi Log Analytics** i wybierz obszar roboczy.
 2. We właściwościach obszaru roboczego w obszarze **Źródła danych obszaru roboczego** wybierz pozycję **Konfiguracje zakresu**.
 3. Kliknij **przycisk Dodaj,** aby utworzyć nową konfigurację zakresu.
 4. Wpisz **nazwę** dla konfiguracji zakresu.
 5. Kliknij **pozycję Wybierz grupy komputerów**.
 6. Wybierz utworzoną grupę komputerów i opcjonalnie wszystkie inne grupy, które mają być dodane do konfiguracji.  Kliknij **pozycję Wybierz**.  
 6. Kliknij **przycisk OK,** aby utworzyć konfigurację zakresu. 


### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. Zastosuj konfigurację zakresu do rozwiązania.
Po skonfigurowaniu zakresu można zastosować ją do jednego lub kilku rozwiązań.  Należy zauważyć, że podczas gdy konfiguracja pojedynczego zakresu może być używana z wieloma rozwiązaniami, każde rozwiązanie może używać tylko jednej konfiguracji zakresu.

Zastosuj konfigurację zakresu przy użyciu następującego procesu.  

 1. W witrynie Azure portal przejdź do **obszarów roboczych usługi Log Analytics** i wybierz obszar roboczy.
 2. We właściwościach obszaru roboczego wybierz **opcję Rozwiązania**.
 3. Kliknij rozwiązanie, które chcesz mieć zakres.
 4. We właściwościach rozwiązania w obszarze **Źródła danych obszaru roboczego** wybierz pozycję **Kierowanie na rozwiązania**.  Jeśli opcja nie jest dostępna, [to rozwiązanie nie może być ukierunkowane](#solutions-and-agents-that-cant-be-targeted).
 5. Kliknij **pozycję Dodaj konfigurację zakresu**.  Jeśli masz już konfigurację zastosowaną do tego rozwiązania, ta opcja będzie niedostępna.  Przed dodaniem kolejnej należy usunąć istniejącą konfigurację.
 6. Kliknij utworzoną konfigurację zakresu.
 7. Obserwuj **stan** konfiguracji, aby upewnić się, że jest **wyświetlana liczba powiodła się.**  Jeśli stan wskazuje błąd, kliknij elipsę po prawej stronie konfiguracji i wybierz pozycję **Edytuj konfigurację zakresu,** aby wprowadzić zmiany.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Rozwiązania i agenci, na które nie można kierunkować
Poniżej przedstawiono kryteria dla agentów i rozwiązań, których nie można używać z kierowaniem na rozwiązania.

- Kierowanie na rozwiązania dotyczy tylko rozwiązań wdrażanych dla agentów.
- Kierowanie na rozwiązania dotyczy tylko rozwiązań dostarczanych przez firmę Microsoft.  Nie ma zastosowania do rozwiązań [tworzonych przez ciebie lub partnerów](solutions-creating.md).
- Można odfiltrować tylko agentów, które łączą się bezpośrednio z usługą Azure Monitor.  Rozwiązania zostaną automatycznie wdrożone do wszystkich agentów, które są częścią połączonej grupy zarządzania programu Operations Manager, niezależnie od tego, czy są one uwzględnione w konfiguracji zakresu.

### <a name="exceptions"></a>Wyjątki
Kierowanie na rozwiązania nie może być używane z następującymi rozwiązaniami, nawet jeśli spełniają określone kryteria.

- Ocena stanu agenta

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o rozwiązaniach do monitorowania, w tym rozwiązaniach dostępnych do zainstalowania w twoim środowisku, w [dodatku rozwiązań do monitorowania usługi Azure Log Analytics do obszaru roboczego.](solutions.md)
- Dowiedz się więcej o tworzeniu grup komputerów w [grupach komputerów w kwerendach dziennika usługi Azure Monitor](../platform/computer-groups.md).
