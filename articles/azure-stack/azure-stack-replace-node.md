---
title: Zastąp węzeł jednostki skalowania w zintegrowanym systemie Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zastąpić węzła jednostki fizyczne skalowania w zintegrowanym systemie Azure Stack.
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
ms.date: 12/06/2018
ms.author: mabrigg
ms.lastreviewed: 12/06/2018
ms.openlocfilehash: 83dff31ea32c4fe8797c3725a7e28490134bb27f
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251592"
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Zastąp węzeł jednostki skalowania w zintegrowanym systemie Azure Stack

*Dotyczy: Zintegrowane systemy usługi Azure Stack*

W tym artykule opisano ogólny proces w celu zastąpienia komputera fizycznego (nazywane również węzeł jednostki skalowania) w zintegrowanym systemie Azure Stack. Rzeczywiste skalowanie jednostek węzeł zastępczy, który kroki będą się różnić na podstawie z dostawcą sprzętu producenta sprzętu (OEM). W dokumentacji dostawcy pola jednostkę (FRU) replaceable unit szczegółowy opis kroków, które są specyficzne dla systemu.

Poniższy diagram przepływu przedstawia ogólny proces FRU, Zastąp węzeł jednostki skali całego.

![Schemat blokowy Zastąp węzła procesu](media/azure-stack-replace-node/replacenodeflow.png)

* Ta akcja może nie być wymagane na podstawie fizycznej warunku sprzętu.

> [!Note]  
> Operacja zamykania nie powiedzie się, zalecane jest za pomocą operacji opróżniania następuje operacji zatrzymania. Więcej informacji na ten temat można znaleźć w węźle dostępne operacje  

## <a name="review-alert-information"></a>Przejrzyj informacje o alertach

W przypadku węzła jednostki skalowania w dół, otrzymasz następujący alerty krytyczne:

- Węzeł nie jest podłączony do kontrolera sieci
- Węzeł jest niedostępny do umieszczania maszyny wirtualnej
- Węzeł jednostka skalowania jest w trybie offline

![Lista alertów dla jednostki skalowania w dół](media/azure-stack-replace-node/nodedownalerts.png)

Jeśli otworzysz **węzła jednostki skalowania jest w trybie offline** alertu, opis alertu zawiera węzeł jednostki skalowania, który jest niedostępny. Może zostać wyświetlony dodatkowych alertów w rozwiązaniu monitorowania specyficzne dla producenta OEM, która została uruchomiona na hoście cyklu życia sprzętu.

![Szczegóły alertu w trybie offline węzła](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>Proces zastępczy węzła jednostki skalowania

Poniższe kroki są dostarczane jako ogólne omówienie procesu wymiany węzła jednostki skalowania. W dokumentacji dostawcy sprzętu OEM FRU szczegółowy opis kroków, które są specyficzne dla systemu. Nie wykonuj następujące czynności bez odwołujące się do dokumentacji dostarczonego przez producenta OEM.

1. Użyj **zamknięcia** działania w celu poprawnego działania zamknięcia węzła jednostki skalowania. Ta akcja może nie być wymagane na podstawie fizycznej warunku sprzętu. 

2. W mało prawdopodobne, zamierzone, Zapisz zamykania kończy się niepowodzeniem, należy użyć [opróżnić](azure-stack-node-actions.md#drain) akcji, aby przełączyć węzeł jednostki skalowania w trybie konserwacji. Ta akcja może nie być wymagane na podstawie fizycznej warunku sprzętu.

   > [!NOTE]  
   > W każdym przypadku, tylko jeden węzeł można wyłączyć i wyłączona w tym samym czasie, bez przerywania S2D (bezpośrednimi miejscami do magazynowania).

3. Po węźle jednostka skalowania jest w trybie konserwacji, użyj [zatrzymać](azure-stack-node-actions.md#stop) akcji. Ta akcja może nie być wymagane na podstawie fizycznej warunku sprzętu.

   > [!NOTE]  
   > W rzadkich przypadkach wyłączanie akcji nie rozwiąże problemu należy zamiast tego użyj interfejsu sieci web uzyskiwania informacji na temat kontrolera zarządzania płytą główną.

4. Zastąp komputera fizycznego. Zazwyczaj jest to realizowane przez dostawcę sprzętu OEM.
5. Użyj [naprawy](azure-stack-node-actions.md#repair) akcji, aby dodać nowy komputer fizyczny do jednostki skalowania.
6. Użyj uprzywilejowanych punktu końcowego do [sprawdzić stan naprawy dysku wirtualnego](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Za pomocą nowych dysków z danymi zadanie naprawy pełną magazynu może potrwać kilka godzin w zależności od obciążenia systemu i używane miejsce.
7. Akcja naprawy zakończy działanie, aby zweryfikować, że wszystkie aktywne alerty zostało automatycznie zamknięte.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak zastąpienie dysku fizycznego, gdy system jest włączony, zobacz [wymienić dysk](azure-stack-replace-disk.md). 
- Aby dowiedzieć się, jak zastąpienie składnik sprzętowy, który wymaga systemu, wyłączanie, zobacz [wymienić składnik sprzętowy](azure-stack-replace-component.md).
