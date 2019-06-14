---
title: Konfigurowanie ustawień obrazu portalu Azure Marketplace w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Konfigurowanie obrazów portalu Azure Marketplace, które mogą być używane, podczas tworzenia maszyny Wirtualnej w usłudze Azure DevTest Labs
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
ms.openlocfilehash: d0375713c4881c0b73b91fc07bda3ceac2dbc620
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60201888"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Konfigurowanie ustawień obrazu portalu Azure Marketplace w usłudze Azure DevTest Labs
DevTest Labs obsługuje tworzenia maszyn wirtualnych opartych na obrazach portalu Azure Marketplace, w zależności od sposobu skonfigurowania obrazów portalu Azure Marketplace do użycia w środowisku laboratoryjnym. W tym artykule pokazano, jak określić, które ewentualnie obrazów portalu Azure Marketplace, mogą być używane podczas tworzenia maszyn wirtualnych w laboratorium. Zapewnia to, że Twój zespół ma dostęp tylko do obrazów portalu Marketplace, które są im potrzebne. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Wybieranie obrazów portalu Azure Marketplace, które są dozwolone podczas tworzenia maszyny Wirtualnej
1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
3. Z listy labs wybierz żądane laboratorium. 
4. W bloku laboratorium wybierz **konfiguracji i zasad**.
5. W laboratorium **konfiguracji i zasad** bloku w obszarze **bazami maszyn wirtualnych**, wybierz opcję **obrazów z witryny Marketplace**.
6. Określ, czy wszystkie kwalifikowaną obrazów portalu Azure Marketplace dostępne do użycia jako podstawa nowych maszyn wirtualnych. Jeśli wybierzesz **tak**, a następnie Azure Marketplace obrazy, które spełniają wszystkie następujące kryteria są dozwolone w laboratorium:
   
   * Obraz, który tworzy jedną maszynę Wirtualną, **i**
   * Obraz, który używa usługi Azure Resource Manager do aprowizacji maszyn wirtualnych, **i**
   * Obraz, który nie wymaga zakupu dodatkowych licencji planu
     
     Jeśli chcesz, aby żadnych obrazów, które mają być dozwolone lub, aby określić, które obrazy mogą być używane, wybierz **nie**.
     
     ![Opcję, aby umożliwić wszystkich obrazów Marketplace, które ma być używany jako obrazy podstawowe dla maszyn wirtualnych](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Jeśli wybierzesz **nie** do poprzedniego kroku **dozwolone obrazów/wybrać wszystkie** pole wyboru jest włączone. 
   Szybko zaznacz lub usuń zaznaczenie wszystkich elementów, które są wyświetlane na liście, można użyć tej opcji wraz z pola wyszukiwania.
   * Wybierz obrazów portalu Azure Marketplace, które chcesz zezwolić na potrzeby tworzenia maszyn wirtualnych pojedynczo, zaznaczając odpowiednie pole wyboru każdego obrazu.
   * Wybierz nothing z listy, jeśli nie chcesz umożliwić żadnych obrazów portalu Azure Marketplace do użycia w środowisku laboratoryjnym.
   
     ![Można określić, które obrazów portalu Azure Marketplace może służyć jako obrazy podstawowe dla maszyn wirtualnych](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Kolejne kroki
Po skonfigurowaniu, jak obrazów portalu Azure Marketplace są dozwolone podczas tworzenia maszyny Wirtualnej, następnym krokiem jest [Dodaj Maszynę wirtualną do środowiska laboratoryjnego](devtest-lab-add-vm.md).

