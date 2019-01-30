---
title: Zarządzanie pojemnością pamięci fizycznej dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: Monitorowanie i zarządzanie miejsce do magazynowania dostępne dla usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 84518E90-75E1-4037-8D4E-497EAC72AAA1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 94431fda03effe7f3bfdbb9e6080cf6d8cf5b344
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238894"
---
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>Zarządzanie pojemnością pamięci fizycznej dla usługi Azure Stack

*Dotyczy: Zintegrowane systemy usługi Azure Stack*

Aby zwiększyć pojemność całkowitej dostępnej pamięci dla usługi Azure Stack, możesz dodać więcej pamięci. W usłudze Azure Stack serwer fizyczny jest również nazywany *węzła jednostki skalowania*. Wszystkie węzły jednostki skalowania, które są elementami członkowskimi jednostki skalowania pojedynczej musi mieć tej samej ilości pamięci.

> [!note]  
> Przed kontynuowaniem zapoznaj się z dokumentacją producenta sprzętu, aby sprawdzić, czy producent obsługuje uaktualnienie pamięci fizycznej. Umowy dotyczącej pomocy technicznej producenta OEM sprzęt dostawcy może wymagać, że dostawca wykonać umieszczania stojak serwerów fizycznych i aktualizacji oprogramowania układowego urządzenia.

Poniższy diagram przepływu przedstawia ogólny proces dodawania pamięci dla każdego węzła jednostki skalowania.

![Zwiększ ilość pamięci do każdego węzła jednostki skalowania](media/azure-stack-manage-storage-physical-capacity/process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>Zwiększ ilość pamięci do istniejącego węzła
W poniższych krokach przedstawiono ogólny przegląd, Dodaj pamięci procesu. 

> [!Warning]  
Nie wykonuj następujące czynności bez odwołujące się do dokumentacji dostarczonego przez producenta OEM.

> [!Warning]  
Musi być zamknięty całej jednostki skalowania, ponieważ uaktualnienie stopniowe pamięci nie jest obsługiwana.

1. Zatrzymaj usługę Azure Stack wykonując kroki opisane w [uruchamianie i zatrzymywanie usługi Azure Stack](azure-stack-start-and-stop.md) artykułu.
2. Uaktualnij pamięci na każdym komputerze fizycznym, korzystając z dokumentacji producenta sprzętu.
3. Uruchom usługę Azure Stack przy użyciu kroków w [uruchamianie i zatrzymywanie usługi Azure Stack](azure-stack-start-and-stop.md) artykułu.

## <a name="next-steps"></a>Kolejne kroki

 - Aby dowiedzieć się, jak zarządzać kontami magazynu w usłudze Azure Stack, aby znaleźć, odzyskiwania i odzyskać pojemność magazynu na podstawie potrzeb biznesowych, zobacz [Zarządzanie kontami magazynu w usłudze Azure Stack](azure-stack-manage-storage-accounts.md).
 - Aby dowiedzieć się, operator chmury Azure Stack, monitoruje i zarządza się pojemności ich wdrożenia usługi Azure Stack, zobacz [Zarządzanie pojemnością magazynu dla usługi Azure Stack](azure-stack-manage-storage-shares.md). 
