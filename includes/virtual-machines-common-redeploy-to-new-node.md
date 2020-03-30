---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 1c3996c3f40da496af0cd795d0873864667a1f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183236"
---
## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal
1. Wybierz maszynę wirtualną, którą chcesz ponownie wdrożyć, a następnie wybierz przycisk *Ponownie wdrożyć* w *ustawieniach* bloku. Może być konieczne przewinięcie w dół, aby wyświetlić sekcję **Pomoc techniczna i rozwiązywanie problemów** zawierającą przycisk "Ponownie wdrożyć", jak w poniższym przykładzie:
   
    ![Blok maszyny Wirtualnej platformy Azure](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. Aby potwierdzić operację, wybierz przycisk *Ponownie wdrożyć:*
   
    ![Ponowne rozmieszczanie bloku maszyny Wirtualnej](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. **Stan** maszyny Wirtualnej zmienia się na *aktualizacja,* gdy maszyna wirtualna przygotowuje się do ponownego rozmieszczenia, jak pokazano w poniższym przykładzie:
   
    ![Aktualizacja maszyny Wirtualnej](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. **Stan** następnie zmienia się *na Uruchamianie* jako maszyna wirtualna uruchamia się na nowym hoście platformy Azure, jak pokazano w poniższym przykładzie:
   
    ![Maszyna wirtualna od początku](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. Po zakończeniu procesu rozruchu maszyna wirtualna **stan** powraca do *uruchomionego,* wskazując, że maszyna wirtualna została pomyślnie ponownie rozmieszczona:
   
    ![Maszyna wirtualna uruchomiona](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

