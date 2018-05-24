---
title: Uzyskiwanie dostępu do laboratorium niestandardowego w usłudze Azure DevTest Labs | Microsoft Docs
description: W tym samouczku będziesz uzyskiwać dostęp do laboratorium tworzonego przy użyciu usługi Azure DevTest Labs, przejmować maszyny wirtualne, korzystać z nich, a następnie cofać ich przejęcie.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: be4bde6bd320e8af7cd3119ff4ccdabd942963ca
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361842"
---
# <a name="tutorial-access-a-custom-lab-in-azure-devtest-labs"></a>Samouczek: uzyskiwanie dostępu do laboratorium niestandardowego w usłudze Azure DevTest Labs
W tym samouczku używasz laboratorium niestandardowego, które zostało utworzone w temacie [Samouczek: tworzenie laboratorium niestandardowego](tutorial-create-custom-lab.md).

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Przejmowanie maszyny wirtualnej w laboratorium niestandardowym
> * Łączenie z maszyną wirtualną
> * Cofanie przejęcia maszyny wirtualnej

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="access-the-lab"></a>Uzyskiwanie dostępu do laboratorium

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie. 
3. Wybierz typ zasobu **DevTest Labs**. 
4. Wybierz laboratorium. 

    ![Wybieranie laboratorium](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>Przejmowanie maszyny wirtualnej

1. Na liście **Maszyny wirtualne możliwe do przejęcia** wybierz pozycję **...** (wielokropek) i wybierz pozycję **Przejmij maszynę**.

    ![Przejmowanie maszyny wirtualnej](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. Upewnij się, że maszyna wirtualna jest widoczna na liście **Moje maszyny wirtualne**.

    ![Moje maszyny wirtualne](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

1. Wybierz maszynę wirtualną z listy. Zostanie wyświetlona **strona maszyny wirtualnej** dla Twojej maszyny wirtualnej. Wybierz pozycję **Połącz** na pasku narzędzi.

    ![Nawiązywanie połączenia z maszyną wirtualną](./media/tutorial-use-custom-lab/connect-button.png)
2. Zapisz pobrany plik **RDP** na dysku twardym i użyj go do nawiązania połączenia z maszyną wirtualną. Podaj nazwę użytkownika i hasło zastosowane podczas tworzenia maszyny wirtualnej w poprzedniej sekcji. 

## <a name="unclaim-the-vm"></a>Cofanie przejęcia maszyny wirtualnej
Po zakończeniu korzystania z maszyny wirtualnej cofnij jej przejęcie, wykonując następujące czynności: 

1. Na stronie maszyny wirtualnej wybierz pozycję **Cofnij przejęcie** na pasku narzędzi. 

    ![Cofanie przejęcia maszyny wirtualnej](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. Maszyna wirtualna jest zamykana przed cofnięciem przejęcia. 

    ![Stan cofania przejęcia](./media/tutorial-use-custom-lab/unclaim-status.png) 
1. Po ukończeniu operacji cofania przejęcia maszyna wirtualna zostanie wyświetlona na liście **Maszyny wirtualne możliwe do przejęcia** w dolnej części strony. 
    
## <a name="next-steps"></a>Następne kroki
W tym samouczku pokazano, jak uzyskiwać dostęp do laboratorium niestandardowego, które zostało utworzone za pomocą usługi Azure DevTest Labs, i korzystać z niego. Aby uzyskać więcej informacji na temat uzyskiwania dostępu do maszyn wirtualnych i używania ich w laboratorium niestandardowym, zobacz 

> [!div class="nextstepaction"]
> [How to: Use VMs in a custom lab (Jak: używanie maszyn wirtualnych w laboratorium niestandardowym)](devtest-lab-add-vm.md)

