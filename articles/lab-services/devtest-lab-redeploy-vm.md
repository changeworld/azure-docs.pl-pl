---
title: Ponowne wdrażanie maszyny Wirtualnej w laboratorium Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i ponowne wdrażanie maszyny wirtualnej (przejście z jednego węzła platformy Azure do innego) w usłudze Azure DevTest Labs.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561628"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Ponowne wdrażanie maszyny Wirtualnej w laboratorium Azure DevTest Labs
Jeśli nie możesz połączyć z maszyną wirtualną (VM) w laboratorium za pomocą połączenia pulpitu zdalnego, ponowne wdrażanie maszyny Wirtualnej i spróbuj ponownie nawiązać połączenie do niego. Podczas ponownego wdrażania maszyny Wirtualnej usługi DevTest Labs przenosi maszyny Wirtualnej z węzła, na którym jest uruchomiony w nowym węźle w ramach infrastruktury platformy Azure. Następnie uruchamia maszynę Wirtualną przy zachowaniu wszystkich opcji konfiguracji i skojarzonych zasobów. Tej funkcji można zaoszczędzić możesz czas spędzony w rozwiązaniu usługi Podłączanie pulpitu zdalnego lub aplikacji dostęp do maszyn wirtualnych opartych na Windows w środowisku laboratoryjnym. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>Kroki, aby przeprowadzić ponowne wdrożenie maszyny Wirtualnej w laboratorium 
Aby przeprowadzić ponowne wdrożenie maszyny Wirtualnej w laboratorium Azure DevTest Labs, wykonaj następujące czynności: 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
3. Zaznacz na liście laboratoriów laboratorium, który chcesz ponownie wdrożyć maszyny Wirtualnej zawiera.  
4. W panelu po lewej stronie wybierz **Moje maszyny wirtualne**. 
5. Z listy maszyn wirtualnych wybierz Maszynę wirtualną.
6. Na stronie maszyny wirtualnej dla maszyny Wirtualnej, wybierz **ponownie wdrożyć** w obszarze **operacji** w menu po lewej stronie.

    ![Ponowne wdrożenie](media/devtest-lab-redeploy-vm/redeploy.png)
7. Przeczytaj informacje na stronie, a następnie wybierz pozycję **ponownie wdrożyć** przycisku. 9. Sprawdź stan operacji ponownego wdrażania **powiadomienia** okna.

    ![Wdróż ponownie stan](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak zmienić rozmiar maszyny Wirtualnej w usłudze Azure DevTest Labs, zobacz [zmienić rozmiar maszyny Wirtualnej](devtest-lab-resize-vm.md).


