---
title: "Zarządzanie wydajnością pamięci fizycznej stosu Azure | Dokumentacja firmy Microsoft"
description: "Monitorowanie i zarządzanie nimi dostępnego miejsca na stosie Azure."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 84518E90-75E1-4037-8D4E-497EAC72AAA1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/13/2018
ms.author: mabrigg
ms.reviewer: Thomas.Roettinger
ms.openlocfilehash: b922790d51c7028c37bb5863d43e99e19790488c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
---
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>Zarządzanie wydajnością pamięci fizycznej Azure stosu

*Dotyczy: Azure stosu zintegrowane systemy*

Aby zwiększyć pojemność całkowitej dostępnej pamięci stosu Azure, możesz dodać więcej pamięci. W stosie Azure serwera fizycznego jest również nazywany *węzła jednostki skali*. Wszystkie węzły jednostki skalowania, będących członkami jednostki skalowania pojedynczego musi mieć tego samego ilość pamięci.

> [!note]  
> Przed kontynuowaniem zapoznaj się z dokumentacją producenta sprzętu, aby zobaczyć, czy uaktualnia pamięci producenta obsługuje uaktualnienie pamięci fizycznej. Umowę dotyczącą pomocy technicznej producenta OEM sprzętu dostawcy może wymagać, że dostawcy wykonać rozmieszczenia stojak serwerów fizycznych i aktualizacji oprogramowania układowego urządzenia.

Poniższy diagram przepływu przedstawia ogólny proces dodawania pamięci do każdego węzła jednostki skalowania.

![Zwiększ ilość pamięci w każdym węźle jednostki skalowania](media\azure-stack-manage-storage-physical-capacity\process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>Dodaj więcej pamięci do istniejący węzeł
W poniższych krokach przedstawiono ogólne omówienie procesu Dodaj pamięci. 

> [!Warning]  
Bez odwołujących się do dokumentacji przez producentów OEM nie wykonaj następujące kroki.

> [!Warning]  
Jednostki skalowania całego muszą zostać wyłączone ponieważ uaktualnienia stopniowego pamięci nie jest obsługiwana.

1. Zatrzymaj stosu Azure, wykonując kroki opisane w [rozpoczęcie i zakończenie stosu Azure](azure-stack-start-and-stop.md) artykułu.
2. Rozszerzenie pamięci na każdym komputerze fizycznym, korzystając z dokumentacji producenta sprzętu.
3. Uruchom stosu Azure, korzystając z procedury w [rozpoczęcie i zakończenie stosu Azure](azure-stack-start-and-stop.md) artykułu.

## <a name="next-steps"></a>Kolejne kroki

 - Aby dowiedzieć się, jak zarządzać kontami magazynu Azure stosu, odzyskiwanie, a odzyskać pojemność odpowiednio do potrzeb działalności, zobacz [Zarządzanie kontami magazynu Azure stosu](azure-stack-manage-storage-accounts.md).
 - Aby dowiedzieć się operatorowi chmury Azure stosu monitoruje i zarządza pojemności ich wdrożenia stosu Azure, zobacz [Zarządzanie pojemność magazynu dla usługi Azure stosu](azure-stack-manage-storage-shares.md). 