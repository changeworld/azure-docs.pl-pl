---
title: Skalowanie akcji jednostek node w stosie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyświetlić stan węzła i wykorzystaj możliwości na wyłączenie opróżniania i wznowić węzeł akcji w systemie Azure stosu zintegrowany.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 3ecc8885a30a11472fe93bbda60c39131c6b3bd7
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801419"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Akcje węzła jednostki skali w stosie Azure

*Dotyczy: Azure stosu zintegrowane systemy*

W tym artykule opisano sposób wyświetlania stanu jednostki skalowania i jego skojarzony węzłów i sposobu użycia węzła dostępne akcje. Węzeł działania obejmują włączania zasilania poza, opróżnienia wznowić i napraw. Zazwyczaj korzystają z tych akcji węzła, podczas wymiany pole części lub dla scenariuszy odzyskiwania węzła.

> [!Important]  
> Wszystkie akcje węzła opisane w tym artykule należy tylko docelowy jeden węzeł naraz.


## <a name="view-the-status-of-a-scale-unit-and-its-nodes"></a>Wyświetlanie stanu jednostki skalowania i jego węzły

W portalu administratora można łatwo wyświetlić stan jednostki skalowania i jego skojarzony węzłów.

Aby wyświetlić stan jednostki skali:

1. Na **zarządzania Region** kafelka, wybierz region.
2. Po lewej stronie w obszarze **zasobów infrastruktury**, wybierz pozycję **jednostek skalowania**.
3. W wynikach wybierz jednostki skalowania.
 
W tym miejscu można wyświetlić następujące informacje:

- Nazwa regionu. Nazwa regionu jest przywoływana za pomocą **-lokalizacji** w module środowiska PowerShell.
- Typ systemu
- Całkowita liczba rdzeni logicznych
- Całkowity rozmiar pamięci
- Lista poszczególne węzły i ich stan; albo **systemem** lub **zatrzymana**.

![Kafelek jednostki skali przedstawiający stan uruchomione dla każdego węzła](media/azure-stack-node-actions/ScaleUnitStatus.PNG)

## <a name="view-information-about-a-scale-unit-node"></a>Wyświetl informacje o węźle jednostki skalowania

W przypadku wybrania jednego węzła, można wyświetlić następujące informacje:

- Nazwa regionu
- Serwer modelu
- Adres IP kontrolera zarządzania płytą główną (BMC)
- Stan operacyjny
- Całkowita liczba rdzeni
- Całkowita ilość pamięci
 
![Kafelek jednostki skali przedstawiający stan uruchomione dla każdego węzła](media/azure-stack-node-actions/NodeActions.PNG)

W tym miejscu, można również wykonywać akcje węzła jednostki skalowania.

## <a name="scale-unit-node-actions"></a>Akcje węzła jednostki skalowania

Podczas wyświetlania informacji na temat węzła jednostki skalowania można również wykonywać akcje węzła takich jak:

- Włączenie i wyłączenie zasilania
- opróżniania i Wznów
- Napraw

Stan operacyjny węzła Określa, które opcje są dostępne.

### <a name="power-off"></a>Wyłącz zasilanie

**Wyłączenie** akcja wyłącza węzła. Jest to ten sam tak, jakby naciśnij przycisk zasilania. Robi **nie** wysłać sygnału zamknięcia systemu operacyjnego. Dla planowanego wyłączy operacje upewnij się, że najpierw opróżniania węzła jednostki skalowania.

Ta akcja jest zwykle używana, gdy węzeł jest w stanie zawieszone i nie odpowiada na żądania.

> [!Important] 
> Ta funkcja jest dostępna tylko za pośrednictwem programu PowerShell. Będzie dostępna w portalu administratora stosu Azure ponownie w późniejszym czasie.


Aby uruchomić wyłączy akcji za pomocą programu PowerShell:

````PowerShell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```` 

W rzadkich przypadkach wyłączy akcji nie działa należy użyć interfejsu sieci web BMC.

### <a name="power-on"></a>Włącz

**Włączają** Akcja włącza się w węźle. Jest to ten sam tak, jakby naciśnij przycisk zasilania. 

> [!Important] 
> Ta funkcja jest dostępna tylko za pośrednictwem programu PowerShell. Będzie dostępna w portalu administratora stosu Azure ponownie w późniejszym czasie.

Aby uruchomić zasilania akcji za pomocą programu PowerShell:

````PowerShell
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
````

W rzadkich przypadkach włączenie akcji nie działa należy użyć interfejsu sieci web BMC.

### <a name="drain"></a>Opróżniania

**Opróżnienia** akcji evacuates wszystkich aktywnych obciążeń rozkładając między pozostałych węzłów w tej jednostce skalowania konkretnego.

Ta akcja jest zwykle używany podczas wymiany pole części, takie jak zastąpienie całego węzła.

> [!IMPORTANT]  
> Upewnij się, opróżniania węzła tylko podczas okna zaplanowanej konserwacji, gdzie zostały zgłoszone użytkowników. W niektórych warunkach aktywnych obciążeń może wystąpić przerwy.

Aby uruchomić akcję opróżniania za pomocą programu PowerShell:

  ````PowerShell
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
  ````

### <a name="resume"></a>Resume

**Wznów** akcji wznawia węzeł opuszczona i oznacza je active rozmieszczenia obciążeń. Wcześniej obciążeń, które były uruchomione w węźle nastąpił powrót po awarii. (Jeśli zostaną opróżnione w węźle, a następnie wyłącz, kiedy zasilanie węzeł ponownie, nie jest oznaczony jako aktywne rozmieszczenia obciążeń. Po wykonaniu tych czynności można użyć wznowienie akcji można oznaczyć jako aktywny węzeł).

Aby uruchomić wznowienie akcji za pomocą programu PowerShell:

  ````PowerShell
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
  ````

### <a name="repair"></a>Napraw

**Naprawy** akcji naprawia węzła. Używać go tylko jedną z następujących scenariuszy:

- Zastąpienie pełne węzła (lub nie nowych dysków danych)
- Po awarii składników sprzętowych i zastąpienia (jeśli jest to zalecane w dokumentacji pola jednostkę (FRU) replaceable unit).

> [!IMPORTANT]  
> W dokumentacji dostawcy sprzętu OEM FRU dla kolejnych kroków, gdy trzeba wymienić węzła lub składników sprzętowych. Dokumentacja FRU określi, czy musisz uruchomić akcję naprawy po zastąpieniu składnik sprzętowy.  

Po uruchomieniu akcji naprawy, należy określić adres IP kontrolera BMC. 

Do uruchomienia akcji naprawy przy użyciu programu PowerShell:

  ````PowerShell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPAddress <BMCIPAddress>
  ````

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o module administrator sieci szkieletowej stosu Azure, zobacz [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.3.0).