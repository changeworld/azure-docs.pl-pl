---
title: Uzyskiwanie dostępu do laboratorium w usłudze Azure DevTest Labs | Microsoft Docs
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
ms.date: 01/18/2019
ms.author: spelluru
ms.openlocfilehash: ee9a68df685095244fc9471b7d4ab0f6cee0642d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68360258"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>Samouczek: uzyskiwanie dostępu do laboratorium w usłudze Azure DevTest Labs
W tym samouczku będziesz używać laboratorium, które zostało utworzone w temacie [Samouczek: tworzenie laboratorium w usłudze Azure DevTest Labs](tutorial-create-custom-lab.md).

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Przejmowanie maszyny wirtualnej w laboratorium
> * Łączenie z maszyną wirtualną
> * Cofanie przejęcia maszyny wirtualnej

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="access-the-lab"></a>Uzyskiwanie dostępu do laboratorium

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
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

    Aby można było nawiązać połączenie z maszyną wirtualną z systemem Linux, musi być włączony dostęp do maszyny wirtualnej za pomocą protokołu SSH i/lub RDP. Kroki procedury nawiązywania połączenia z maszyną wirtualną z systemem Linux zawiera artykuł [Instalowanie i konfigurowanie pulpitu zdalnego w celu nawiązania połączenia z maszyna wirtualną z systemem Linux na platformie Azure](../virtual-machines/linux/use-remote-desktop.md). 

    > [!NOTE]
    > Istnieją inne sposoby, aby uzyskać do maszyny wirtualnej strony dla maszyny wirtualnej. Oto niektóre z nich: 
    > 
    > 1. Wyszukaj wszystkie maszyny wirtualne w ramach subskrypcji. Wybierz maszynę wirtualną na liście maszyn wirtualnych, aby przejść do strony **maszyny wirtualnej.**
    > 2. Przejdź do strony **Grupa zasobów** dla grupy zasobów. Następnie wybierz maszynę wirtualną z listy zasobów w grupie zasobów, aby przejść do strony **Maszyny wirtualnej.** 
    >
    > Nie używaj przycisku **Połącz** na pasku narzędzi na stronie **Maszyna wirtualna,** do której można uzyskać, korzystając z tych opcji. Zamiast tego przejdź do strony **Maszyny wirtualnej** ze strony **DevTest Labs,** jak pokazano w tym artykule, a następnie użyj przycisku **Połącz** na pasku narzędzi.


## <a name="unclaim-the-vm"></a>Cofanie przejęcia maszyny wirtualnej
Po zakończeniu korzystania z maszyny wirtualnej cofnij jej przejęcie, wykonując następujące czynności: 

1. Na stronie maszyny wirtualnej wybierz pozycję **Cofnij przejęcie** na pasku narzędzi. 

    ![Cofanie przejęcia maszyny wirtualnej](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. Maszyna wirtualna jest zamykana przed cofnięciem przejęcia. Stan tej operacji można zobaczyć w powiadomieniach.  
3. Przejdź z powrotem do strony laboratorium DevTest Lab, klikając nazwę laboratorium w menu linków do stron nadrzędnych, znajdującym się u góry. 
    
    ![Przechodzenie z powrotem do laboratorium](./media/tutorial-use-custom-lab/breadcrumb-to-lab.png)
1. Upewnij się, że maszyna wirtualna jest widoczna na liście **Maszyny wirtualne możliwe do przejęcia** w dolnej części.

    
## <a name="next-steps"></a>Następne kroki
W tym samouczku pokazano, jak uzyskiwać dostęp do laboratorium, które zostało utworzone za pomocą usługi Azure DevTest Labs, i korzystać z niego. Aby uzyskać więcej informacji na temat uzyskiwania dostępu do maszyn wirtualnych i używania ich w laboratorium, zobacz 

> [!div class="nextstepaction"]
> [Instrukcje: korzystanie z maszyn wirtualnych w laboratorium](devtest-lab-add-vm.md)

