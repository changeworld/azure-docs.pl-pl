---
title: Ponowne wdrożenie maszyny Wirtualnej w laboratorium w laboratorium usługi Azure DevTest Labs | Dokumenty firmy Microsoft
description: Dowiedz się, jak ponownie wdrożyć maszynę wirtualną (przenieść z jednego węzła platformy Azure do innego) w laboratorium usługi Azure DevTest.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: 4500fcfa6fbfb346a6e5c7fd045ba0046a901b91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561628"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Ponowne wdrożenie maszyny Wirtualnej w laboratorium w laboratorium usługi Azure DevTest Labs
Jeśli nie można połączyć się z maszyną wirtualną (VM) w laboratorium za pośrednictwem połączenia pulpitu zdalnego, ponownie wdrożyć maszynę wirtualną i spróbuj ponownie połączyć się z nią. Po ponowne wdrożenie maszyny Wirtualnej DevTest Labs przenosi maszynę wirtualną z węzła, w którym jest uruchomiona do nowego węzła w ramach infrastruktury platformy Azure. Następnie uruchamia maszynę wirtualną przy zachowaniu wszystkich opcji konfiguracji i skojarzonych zasobów. Ta funkcja pozwala zaoszczędzić czas poświęcony na rozwiązywanie problemów z połączeniem pulpitu zdalnego lub dostępem aplikacji do maszyn wirtualnych z systemem Windows w laboratorium. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>Kroki ponownego rozmieszczenia maszyny Wirtualnej w laboratorium 
Aby ponownie wdrożyć maszynę wirtualną w laboratorium w laboratorium usługi Azure DevTest Labs, wykonaj następujące kroki: 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**
3. Z listy laboratoriów wybierz laboratorium, które zawiera maszynę wirtualną, którą chcesz ponownie wdrożyć.  
4. W lewym panelu wybierz pozycję **Moje maszyny wirtualne**. 
5. Z listy maszyn wirtualnych wybierz maszynę wirtualną.
6. Na stronie Maszyna wirtualna maszyny wirtualnej wybierz pozycję **Ponownie rozmieszczaj** w obszarze **OPERACJE** w menu po lewej stronie.

    ![Ponowne wdrożenie](media/devtest-lab-redeploy-vm/redeploy.png)
7. Przeczytaj informacje na stronie i wybierz przycisk **Ponownie wdrożyć.** 9. Sprawdź stan operacji ponownego rozmieszczania w oknie **Powiadomienia.**

    ![Stan ponownego rozmieszczania](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak zmienić rozmiar maszyny Wirtualnej w laboratoriach devtest platformy Azure, zobacz [Ponowne rozmiary maszyny Wirtualnej](devtest-lab-resize-vm.md).


