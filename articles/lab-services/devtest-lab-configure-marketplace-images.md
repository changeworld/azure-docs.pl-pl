---
title: Konfigurowanie ustawień obrazu portalu Azure Marketplace w laboratoriach devtest azure
description: Konfigurowanie obrazów usługi Azure Marketplace, które mogą być używane podczas tworzenia maszyny wirtualnej w laboratoriach devtest azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 804c6af2-17e9-4320-af3a-f454bd398379
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 2dc3aa000cefc0e65305b58f8fdce93b94bfd35f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169548"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Konfigurowanie ustawień obrazu portalu Azure Marketplace w laboratoriach devtest azure
DevTest Labs obsługuje tworzenie maszyn wirtualnych na podstawie obrazów usługi Azure Marketplace w zależności od sposobu skonfigurowania obrazów portalu Azure Marketplace do użycia w laboratorium. W tym artykule pokazano, jak określić, które, jeśli istnieją, obrazy portalu Azure Marketplace mogą być używane podczas tworzenia maszyn wirtualnych w laboratorium. Dzięki temu twój zespół ma dostęp tylko do obrazów z Marketplace, których potrzebują. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Wybieranie obrazów portalu Azure Marketplace dozwolonych podczas tworzenia maszyny Wirtualnej
1. Zaloguj się do [Portalu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**
3. Z listy laboratoriów wybierz żądane laboratorium. 
4. Na bloku laboratorium wybierz **pozycję Konfiguracja i zasady**.
5. W bloku **Konfiguracja i zasady** laboratorium w obszarze **Bazy maszyn wirtualnych**wybierz pozycję **Obrazy marketplace**.
6. Określ, czy chcesz, aby wszystkie kwalifikowane obrazy portalu Azure Marketplace były dostępne do użycia jako baza nowej maszyny Wirtualnej. Jeśli wybierzesz **Tak**, wszystkie obrazy portalu Azure Marketplace, które spełniają wszystkie następujące kryteria są dozwolone w laboratorium:
   
   * Obraz tworzy pojedynczą maszynę wirtualną **i**
   * Obraz używa usługi Azure Resource Manager do aprowizowania maszyn wirtualnych **i**
   * Obraz nie wymaga zakupu dodatkowego planu licencjonowania
     
     Jeśli nie chcesz, aby obrazy nie były dozwolone lub chcesz określić, które obrazy mogą być używane, wybierz **opcję Nie**.
     
     ![Opcja zezwalania na wykorzystanie wszystkich obrazów z marketplace jako obrazów podstawowych dla maszyn wirtualnych](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Jeśli wybierzesz **opcję Nie** dla poprzedniego kroku, zostanie włączone pole wyboru **Dozwolone obrazy/Zaznacz wszystkie.** 
   Tej opcji można użyć razem z polem wyszukiwania, aby szybko zaznaczyć lub usunąć zaznaczenie wszystkich elementów wyświetlanych na liście.
   * Wybierz obrazy portalu Azure Marketplace, które mają umożliwić tworzenie maszyn wirtualnych indywidualnie, zaznaczając odpowiednie pole wyboru każdego obrazu.
   * Wybierz nic z listy, jeśli nie chcesz zezwalać na obrazy usługi Azure Marketplace do użycia w laboratorium.
   
     ![Można określić, które obrazy portalu Azure Marketplace mogą być używane jako obrazy podstawowe dla maszyn wirtualnych](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Następne kroki
Po skonfigurowaniu sposobu, w jaki obrazy portalu Azure Marketplace są dozwolone podczas tworzenia maszyny Wirtualnej, następnym krokiem jest [dodanie maszyny Wirtualnej do laboratorium.](devtest-lab-add-vm.md)

