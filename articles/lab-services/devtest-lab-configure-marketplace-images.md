---
title: Skonfiguruj ustawienia obrazu portalu Azure Marketplace w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Konfigurowanie obrazów Azure Marketplace, których można użyć w przypadku tworzenia maszyny Wirtualnej w usłudze Azure DevTest Labs
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
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 90df853eeca89e61fa69e6967218c4c18274ac1b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787395"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Skonfiguruj ustawienia obrazu portalu Azure Marketplace w usłudze Azure DevTest Labs
DevTest Labs obsługuje tworzenia maszyn wirtualnych oparte na obrazach portalu Azure Marketplace w zależności od sposobu konfiguracji portalu Azure Marketplace obrazów do wykorzystania w laboratorium. W tym artykule przedstawiono sposób określić, które, portalu Azure Marketplace obrazy mogą być używane podczas tworzenia maszyn wirtualnych w laboratorium. Dzięki temu, że zespół ma dostęp tylko do obrazów Marketplace, które są im potrzebne. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Wybierz obrazy portalu Azure Marketplace, które są dozwolone podczas tworzenia maszyny Wirtualnej
1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz **wszystkie usługi**, a następnie wybierz **DevTest Labs** z listy.
3. Z listy labs wybierz żądany laboratorium. 
4. W bloku laboratorium, wybierz **konfiguracji i zasadach**.
5. W laboratorium w **konfiguracji i zasadach** bloku w obszarze **baz maszyn wirtualnych**, wybierz pozycję **obrazów Marketplace**.
6. Określ, czy wszystkie kwalifikowaną obrazów Azure Marketplace do użycia jako baza dla nowej maszyny Wirtualnej. W przypadku wybrania **tak**, a następnie Azure Marketplace obrazy, które spełniają następujące kryteria są dozwolone w środowisku laboratoryjnym:
   
   * Obraz tworzy jednej maszyny Wirtualnej, **i**
   * Obraz używa usługi Azure Resource Manager w celu udostępniania maszyn wirtualnych, **i**
   * Obraz nie wymaga zakup planu licencjonowania bardzo
     
    Obrazy nie mogą być, lub chcesz określić obrazów, które mogą być używane, wybierz **nr**.
     
     ![Opcję, aby umożliwić wszystkie obrazy Marketplace do użycia jako podstawowy obrazów maszyn wirtualnych](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. W przypadku wybrania **nr** do poprzedniego kroku **dozwolone obrazów/wybierz wszystkie** pole wyboru jest włączone. 
   Tej opcji wraz z pola wyszukiwania umożliwia szybkie zaznacz lub usuń zaznaczenie wszystkich elementów, które są wyświetlane na liście.
   * Wybierz obrazy portalu Azure Marketplace, który chcesz zezwolić na utworzenie maszyny Wirtualnej pojedynczo, zaznaczając wyboru odpowiednich każdego obrazu.
   * Wybierz nic z listy, jeśli nie chcesz umożliwić żadnych obrazów Azure Marketplace do użycia w środowisku laboratoryjnym.
   
    ![Można określić, które obrazy portalu Azure Marketplace może służyć jako podstawowy obrazów maszyn wirtualnych](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Kolejne kroki
Po skonfigurowaniu, jak obrazy portalu Azure Marketplace są dozwolone podczas tworzenia maszyny Wirtualnej, następnym krokiem jest [Dodaj Maszynę wirtualną do laboratorium](devtest-lab-add-vm.md).

