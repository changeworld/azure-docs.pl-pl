---
title: Skalowanie akcji węzła jednostki w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyświetlić stan węzła i użyj potęgi na wyłączanie opróżniania i wznowić węzła akcji w zintegrowanym systemie Azure Stack.
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
ms.date: 10/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: a792bc083c3a2c78b24d5895c34420b86b0863bb
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959771"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Liczba akcji skalowania jednostki węzła w usłudze Azure Stack

*Dotyczy: zintegrowane systemy usługi Azure Stack*

W tym artykule opisano sposób wyświetlania stanu jednostki skalowania i jego skojarzone węzły i jak za pomocą akcji znaleźć dostępnego węzła. Węzeł akcje obejmują włączenia zasilania poza, opróżnić, wznowić i naprawy. Zazwyczaj korzystają z tych akcji węzła, podczas zastępowania pola części lub dla scenariuszy odzyskiwania węzła.

> [!Important]  
> Wszystkie akcje węzła opisanych w tym artykule należy tylko docelowego jednego węzła w danym momencie.


## <a name="view-the-node-status"></a>Widok stanu węzła

W portalu administratora, można łatwo wyświetlić stan jednostki skalowania i jego skojarzone węzły.

Aby wyświetlić stan jednostki skalowania:

1. Na **zarządzanie regionami** kafelka, wybierz region.
2. Po lewej stronie w obszarze **zasoby infrastruktury**, wybierz opcję **jednostek skalowania**.
3. W wynikach wybierz jednostkę skalowania.
 
W tym miejscu można wyświetlić następujące informacje:

- Nazwa regionu. Nazwa regionu jest wskazywane poprzez **— lokalizacja** w module programu PowerShell.
- Typ systemu
- Łączna liczba rdzeni logicznych
- Całkowity rozmiar pamięci
- Lista pojedynczych węzłów i ich stan; albo **systemem** lub **zatrzymana**

![Kafelek jednostki skalowania przedstawiający stan uruchomione dla każdego węzła](media/azure-stack-node-actions/ScaleUnitStatus.PNG)

## <a name="view-node-information"></a>Wyświetl informacje na węzeł

Jeśli wybierzesz oddzielnego węzła, można wyświetlić następujące informacje:

- Nazwa regionu
- model serwera
- Adres IP kontrolera zarządzania płytą główną (BMC)
- Stan operacyjny
- Łączna liczba rdzeni
- Całkowita ilość pamięci
 
![Kafelek jednostki skalowania przedstawiający stan uruchomione dla każdego węzła](media/azure-stack-node-actions/NodeActions.PNG)

Można również wykonać akcji węzła jednostki skalowania, w tym miejscu.

## <a name="scale-unit-node-actions"></a>Liczba akcji węzła jednostki skalowania

Podczas wyświetlania informacji na temat węzła jednostki skalowania można również wykonać akcje węzła takich jak:

- Opróżniania i Wznów
- Napraw

Stan operacyjny węzła Określa, które opcje są dostępne.

### <a name="power-off"></a>Zasilanie wyłączone

**Wyłącz zasilanie** akcja powoduje wyłączenie węzła. Jest to ten sam tak, jakby naciśnij przycisk zasilania. Robi **nie** wysyłać sygnał zamknięcia systemu operacyjnego. Dla planowanego Wyłącz operacje upewnij się, że najpierw opróżnić węzeł jednostki skalowania.

Ta akcja służy zazwyczaj gdy węzeł jest w stanie zawieszone i już nie odpowiada na żądania.

> [!Important] 
> Ta funkcja jest dostępna tylko za pośrednictwem programu PowerShell. Będzie on dostępny w portalu administratora usługi Azure Stack ponownie w późniejszym czasie.


Aby uruchomić wyłączanie akcji za pomocą programu PowerShell:

````PowerShell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```` 

W rzadkich przypadkach wyłączanie akcji nie rozwiąże problemu należy zamiast tego użyj interfejsu sieci web kontrolera BMC.

### <a name="power-on"></a>Włącz

**Włącz** Akcja włącza się w węźle. Jest to ten sam tak, jakby naciśnij przycisk zasilania. 

> [!Important] 
> Ta funkcja jest dostępna tylko za pośrednictwem programu PowerShell. Będzie on dostępny w portalu administratora usługi Azure Stack ponownie w późniejszym czasie.

Aby uruchomić możliwości w przypadku akcji za pomocą programu PowerShell:

````PowerShell
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
````

W mało prawdopodobnym przypadku, Włącz akcji nie rozwiąże problemu należy zamiast tego użyj interfejsu sieci web kontrolera BMC.

### <a name="drain"></a>Opróżniania

**Opróżnić** akcji evacuates wszystkie aktywne obciążenia przez przydzielanie ich wśród pozostałych węzłów w tej jednostce skalowania określonej.

Ta akcja jest zwykle używany podczas zastępowania pola części, takie jak zastąpienie całego węzła.

> [!IMPORTANT]  
> Upewnij się, opróżnić węzeł tylko podczas okna zaplanowanej konserwacji, gdzie użytkownicy zostali powiadomieni. W niektórych warunkach aktywne obciążenia mogą występować przerw w działaniu.

Aby uruchomić akcję opróżniania za pomocą programu PowerShell:

  ````PowerShell
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
  ````

### <a name="resume"></a>Resume

**Wznów** akcji wznawia węzeł opuszczona i oznacza je jako aktywny rozmieszczenia obciążeń. Wcześniej obciążeń, które były uruchomione w węźle nie powrót po awarii. (Jeśli można opróżnić węzeł, a następnie wyłącz, kiedy power węzeł ponownie, nie jest oznaczony jako aktywny rozmieszczenia obciążeń. Gdy wszystko będzie gotowe, należy użyć akcji Wznów można oznaczyć węzeł jako aktywny.)

Uruchamianie akcji Wznów za pomocą programu PowerShell:

  ````PowerShell
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
  ````

### <a name="repair"></a>Napraw

**Naprawy** akcji naprawia węzła. Używać tylko w przypadku jednego z następujących scenariuszy:

- Zastąpienie pełną węzła (z lub bez nowych dysków danych)
- Po awarii składników sprzętowych i zamieniania (jeśli jest to zalecane w pole dokumentacji jednostkę (FRU) replaceable unit).

> [!IMPORTANT]  
> W dokumentacji dostawcy sprzętu OEM FRU dokładne kroki gdy trzeba wymienić węzła lub składników sprzętowych. Dokumentacja jednostki FRU określą, czy istnieje potrzeba Uruchom akcję naprawy po zastąpieniu składnik sprzętowy.  

Po uruchomieniu akcji naprawy, należy określić adres IP kontrolera BMC. 

Uruchamianie akcji naprawy przy użyciu programu PowerShell:

  ````PowerShell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ````

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o module administratora usługi Azure Stack w sieci szkieletowej, zobacz [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.5.0).
