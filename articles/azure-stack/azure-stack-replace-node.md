---
title: Zastąp węzeł jednostki skali w systemie Azure stosu zintegrowane | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zamienić węzła jednostki skali fizycznych w systemie Azure stosu zintegrowany.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: f9434689-ee66-493c-a237-5c81e528e5de
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: mabrigg
ms.openlocfilehash: 468af385833395963ef8acad905b99a9b7e6b8fa
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2018
ms.locfileid: "27598777"
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Zastąp węzeł jednostki skali w systemie Azure stosu zintegrowane

*Dotyczy: Azure stosu zintegrowane systemy*

W tym artykule opisano ogólny proces Zastąp komputera fizycznego (zwaną także *węzła jednostki skali*) na stosie Azure zintegrowany system. Wymiana węzła rzeczywiste skali przez kroki będą się różnić w oparciu z dostawcą sprzętu producenta sprzętu (OEM). W dokumentacji udostępnianej przez dostawcę pola jednostkę (FRU) replaceable unit szczegółowy opis kroków, które są specyficzne dla systemu.

Poniższy diagram przepływu przedstawia ogólny proces FRU Zastąp węzeł jednostki skali całego.

![Schemat blokowy procesu węzła Zamień](media/azure-stack-replace-node/replacenodeflow.png)

* Tej akcji nie może być wymagany na podstawie warunku fizycznego sprzętu.

## <a name="review-alert-information"></a>Przeglądanie informacji o alertach

Jeśli węzeł jednostki skalowania nie działa, otrzymasz następujący alerty krytyczne:

- Węzeł nie jest podłączony do kontrolera sieci
- Węzeł jest niedostępny do umieszczania maszyny wirtualnej
- Węzeł jednostki skalowania jest w trybie offline

![Lista alertów dla jednostki skali w dół](media/azure-stack-replace-node/nodedownalerts.png)

Po otwarciu **węzeł jednostki skalowania jest w trybie offline** alertu, opis alertu zawiera węzeł jednostki skalowania, który jest niedostępny. Dodatkowe alerty może zostać wyświetlony w specyficzne dla producenta OEM rozwiązanie monitorowania, która jest uruchomiona na hoście cyklu życia sprzętu.

![Szczegóły alertu w trybie offline węzła](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>Proces zmiany węzła jednostki skalowania

Następujące kroki służą jako ogólne omówienie procesu wymiany węzła jednostki skalowania. W dokumentacji dostawcy sprzętu OEM FRU szczegółowy opis kroków, które są specyficzne dla systemu. Bez odwołujących się do dokumentacji przez producentów OEM nie wykonaj następujące kroki.

1. Użyj [opróżnienia](azure-stack-node-actions.md#scale-unit-node-actions) akcji w celu uruchomienia trybu konserwacji węzła jednostki skalowania. Ta akcja nie może być wymagany na podstawie warunku fizycznego sprzętu.

   > [!NOTE]
   > W każdym przypadku tylko jeden węzeł można opróżnione i odłączony od zasilania w tym samym czasie bez przerywania S2D (bezpośrednie miejsca do magazynowania).

2. Jeśli węzeł nadal jest włączona, należy użyć [wyłączenie](azure-stack-node-actions.md#scale-unit-node-actions) akcji. Ta akcja nie może być wymagany na podstawie warunku fizycznego sprzętu.
 
   > [!NOTE]
   > W rzadkich przypadkach wyłączy akcji nie działa w zamian użyj interfejsu sieci web uzyskiwania informacji na temat kontrolera zarządzania płytą główną.

1. Zamień na komputerze fizycznym. Zazwyczaj jest to realizowane przez producenta OEM komputera.
2. Użyj [naprawy](azure-stack-node-actions.md#scale-unit-node-actions) akcji, aby dodać nowy komputer fizyczny do jednostki skalowania.
3. Użyj uprzywilejowanych punktu końcowego [sprawdzić stan naprawy dysku wirtualnego](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Nowe dyski danych zadania naprawy pełne magazynu może zająć kilka godzin w zależności od obciążenia systemu i zużytego miejsca.
4. Akcja naprawy zakończy działanie, aby zweryfikować, że wszystkie aktywne alerty zostało automatycznie zamknięte.

## <a name="next-steps"></a>Kolejne kroki

- Uzyskać informacji dotyczących zastępowania wyłączania dysku fizycznego, zobacz [wymienić dysk](azure-stack-replace-disk.md). 
- Aby dowiedzieć się, jak wymiana sprzętu z systemem innym niż wyłączania składnika, zobacz [Zastąp składnik sprzętowy](azure-stack-replace-component.md).
