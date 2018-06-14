---
title: Rozwiązywanie problemów z trybu failover na awarie Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposoby rozwiązywania typowych problemów w przypadku awarii na platformie Azure
services: site-recovery
documentationcenter: ''
author: ponatara
manager: abhemraj
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/09/2018
ms.author: ponatara
ms.openlocfilehash: 5c94e26c4639284f7e4c53d924f16040118d996c
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2018
ms.locfileid: "29874363"
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>Rozwiązywanie problemów z błędami przechodzenie w tryb failover maszyny wirtualnej na platformie Azure
Może pojawić się jeden z następujących błędów podczas wykonywania pracy w trybie failover maszyny wirtualnej na platformie Azure. Aby rozwiązać problemy, użyj opisane kroki dla każdego warunku błędu.


## <a name="failover-failed-with-error-id-28031"></a>Tryb failover nie powiodło się z Identyfikator błędu 28031

Usługa Site Recovery nie mógł utworzyć nieudanej przez maszynę wirtualną na platformie Azure. Może się zdarzyć z jednego z następujących powodów:

* Nie ma wystarczającego przydziału do utworzenia maszyny wirtualnej: Sprawdź dostępny limit przydziału, przechodząc do subskrypcji -> użycia + przydziałów. Możesz otworzyć [nowe żądanie pomocy technicznej](http://aka.ms/getazuresupport) Aby zwiększyć przydział.

* Próbujesz trybu failover maszyny wirtualne rodzin inny rozmiar w tym samym zestawie dostępności. Upewnij się, wybrać tej samej rodziny rozmiar dla wszystkich maszyn wirtualnych w tym samym zestawie dostępności. Zmień rozmiar, przechodząc do ustawień obliczania i sieci maszyny wirtualnej, a następnie ponów próbę pracy awaryjnej.

* Brak zasad na subskrypcję, która uniemożliwia tworzenie maszyny wirtualnej. Zmień zasady Zezwalaj na tworzenie maszyny wirtualnej, a następnie spróbuj ponownie trybu failover.

## <a name="failover-failed-with-error-id-28092"></a>Tryb failover nie powiodło się z Identyfikator błędu 28092

Usługa Site Recovery nie mógł utworzyć interfejsu sieciowego dla nieudane przez maszynę wirtualną. Upewnij się, że masz wystarczającego przydziału do utworzenia interfejsów sieciowych w subskrypcji. Sprawdź dostępny limit przydziału, przechodząc do subskrypcji -> użycia + przydziałów. Możesz otworzyć [nowe żądanie pomocy technicznej](http://aka.ms/getazuresupport) Aby zwiększyć przydział. Jeśli masz wystarczającego limitu przydziału, może to być tymczasowy wystawiania, spróbuj ponownie wykonać operację. Jeśli problem będzie nadal występował mimo ponownych prób, następnie zostaw komentarz na końcu tego dokumentu.  

## <a name="failover-failed-with-error-id-70038"></a>Tryb failover nie powiodło się z Identyfikator błędu 70038

Usługa Site Recovery nie mógł utworzyć nieudanej za pośrednictwem klasycznego maszyny wirtualnej platformy Azure. Może się zdarzyć, ponieważ:

* Jeden z zasobów, takich jak sieć wirtualna, która jest wymagana do utworzenia maszyny wirtualnej nie istnieje. Tworzenie sieci wirtualnej, zgodnie z ustawień obliczania i sieci maszyny wirtualnej lub zmodyfikować to ustawienie, aby sieć wirtualna, która już istnieje, a następnie ponów próbę pracy awaryjnej.


## <a name="next-steps"></a>Kolejne kroki

Jeśli potrzebujesz więcej pomocy, na następnie przesłanie kwerendy [forum usługi Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) lub zostaw komentarz na końcu tego dokumentu. Mamy aktywnej społeczności, który powinien być w stanie ułatwi.
