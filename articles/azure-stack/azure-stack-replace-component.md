---
title: Wymienić składnik sprzętowy w węźle jednostki skalowania usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wymienić składnik sprzętowy w zintegrowanym systemie Azure Stack.
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
ms.date: 09/10/2018
ms.author: mabrigg
ms.openlocfilehash: df9470813f3f9c3bff58882879c06e7b7b0fc15b
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379608"
---
# <a name="replace-a-hardware-component-on-an-azure-stack-scale-unit-node"></a>Wymienić składnik sprzętowy w węźle jednostki skalowania usługi Azure Stack

*Dotyczy: zintegrowane systemy usługi Azure Stack*

W tym artykule opisano ogólny proces, aby zastąpić składniki sprzętowe, które są bez wyłączania. Rzeczywiste zastąpienia, które kroki różnią się od na podstawie z dostawcą sprzętu producenta sprzętu (OEM). W dokumentacji dostawcy pola jednostkę (FRU) replaceable unit szczegółowy opis kroków, które są specyficzne dla systemu Azure Stack, zintegrowane.

Składniki bez wyłączania obejmują następujące czynności:

- PROCESOR CPU *
- Pamięć *
- Płyty głównej/kontrolera zarządzania płytą główną (BMC) / wideo karty
- Karty magistrali hosta/kontrolera dysku (HBA) / płyty montażowej
- Karty sieciowej (NIC)
- Dysk systemu operacyjnego *
- Dyski (dyski, które nie obsługują wymiany na gorąco, na przykład PCIe kart) *

* Te składniki mogą obsługiwać wymiany na gorąco, ale mogą się różnić w zależności od implementacji dostawcy. W dokumentacji producenta OEM FRU szczegółowy opis kroków.

Poniższy diagram przepływu przedstawia ogólny proces FRU, wymienić składnik sprzętowy bez wyłączania.

![Diagram przepływu pokazujący przepływ zastąpienie składnika](media/azure-stack-replace-component/replacecomponentflow.PNG)

* Ta akcja może nie być wymagane na podstawie fizycznej warunku sprzętu.

** Czy z dostawcą sprzętu OEM wykonuje wymiana składników i aktualizacje oprogramowania układowego może się różnić w oparciu o umowę na pomoc techniczną.

## <a name="review-alert-information"></a>Przejrzyj informacje o alertach

Kondycja usługi Azure Stack i systemu monitorowania Śledź kondycję kart sieciowych i dysków z danymi w wartości clientauthtrustmode bezpośrednimi miejscami do magazynowania. Żądania nie Śledź innych składników sprzętowych. Dla innych składników sprzętowych alerty są wyświetlane na liście sprzętu specyficzne dla dostawcy, rozwiązanie monitorujące, które jest uruchamiane na hoście cyklu życia sprzętu.  

## <a name="component-replacement-process"></a>Proces wymiany składnika

W poniższych krokach przedstawiono ogólny przegląd procesu wymiany składnika. Nie wykonuj następujące czynności bez odwołujące się do dokumentacji FRU dostarczonego przez producenta OEM.

1. Użyj [opróżnić](azure-stack-node-actions.md#scale-unit-node-actions) akcji, aby przełączyć węzeł jednostki skalowania w trybie konserwacji. Ta akcja może nie być wymagane na podstawie fizycznej warunku sprzętu.

   > [!NOTE]
   > W każdym przypadku, tylko jeden węzeł można opróżniane i wyłączona w tym samym czasie, bez przerywania S2D (bezpośrednimi miejscami do magazynowania).

2. Po węźle jednostka skalowania jest w trybie konserwacji, użyj [Wyłącz zasilanie](azure-stack-node-actions.md#scale-unit-node-actions) akcji. Ta akcja może nie być wymagane na podstawie fizycznej warunku sprzętu.

   > [!NOTE]
   > W rzadkich przypadkach wyłączanie akcji nie rozwiąże problemu należy zamiast tego użyj interfejsu sieci web uzyskiwania informacji na temat kontrolera zarządzania płytą główną.

3. Wymienić uszkodzony sprzęt. Czy z dostawcą sprzętu OEM wykonuje wymiana składników może zależeć od Twojej umowy dotyczącej pomocy technicznej.  
4. Zaktualizuj oprogramowanie układowe. Wykonaj proces aktualizacji oprogramowania układowego specyficzne dla dostawcy, upewnij się, że składnik sprzętowy zamieniono ma zatwierdzonego oprogramowania układowego, poziom stosowane przy użyciu sprzętu hosta cyklu życia. Czy z dostawcą sprzętu OEM wykonuje ten krok może zależeć od Twojej umowy dotyczącej pomocy technicznej.  
5. Użyj [naprawy](azure-stack-node-actions.md#scale-unit-node-actions) akcji przywrócić węzła jednostki skalowania jednostki skalowania.
6. Użyj uprzywilejowanych punktu końcowego do [sprawdzić stan naprawy dysku wirtualnego](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Za pomocą nowych dysków z danymi zadanie naprawy pełną magazynu może potrwać kilka godzin w zależności od obciążenia systemu i używane miejsce.
7. Akcja naprawy zakończy działanie, aby zweryfikować, że wszystkie aktywne alerty zostało automatycznie zamknięte.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak zastąpienie wyłączania dysku fizycznego, zobacz [wymienić dysk](azure-stack-replace-disk.md).
- Aby dowiedzieć się, jak zastąpienie węźle fizycznym, zobacz [zamienić węzła jednostki skalowania](azure-stack-replace-node.md).
