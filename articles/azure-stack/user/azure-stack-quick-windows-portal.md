---
title: 'Azure Stack: Szybki Start — tworzenie maszyny wirtualnej Windows'
description: 'Azure Stack: Szybki Start — tworzenie maszyny Wirtualnej Windows przy użyciu portalu'
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: unknown
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 1dd5cb9bbd8e197e7654242861e97684f4f378c8
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241988"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>Szybki Start: tworzenie maszyny wirtualnej programu Windows server za pomocą portalu usługi Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Można utworzyć maszynę wirtualną systemu Windows Server 2016 za pomocą portalu usługi Azure Stack. Wykonaj kroki opisane w tym artykule, aby utworzyć maszynę wirtualną.

> [!NOTE]  
> Obrazy ekranu, w tym artykule zostaną zaktualizowane w celu interfejsu użytkownika, który został wprowadzony przy użyciu usługi Azure Stack w wersji 1808. 1808 dodaje obsługę za pomocą *usługi managed disks* oprócz dysków niezarządzanych. Jeśli używasz starszej wersji, niektóre obrazy, podobne zaznaczenie dysku będzie różnić się od wyświetlanych w tym artykule.  


## <a name="sign-in-to-the-azure-stack-portal"></a>Zaloguj się do portalu usługi Azure Stack

Zaloguj się do portalu usługi Azure Stack. Adres portalu usługi Azure Stack jest zależny, dotyczących produktu usługi Azure Stack, który chcesz się połączyć:

* Dla usługi Azure Stack Development Kit (ASDK) przejdź do: https://portal.local.azurestack.external.
* Aby to system zintegrowany z usługi Azure Stack przejdź do adresu URL, który dostarczony operator usługi Azure Stack.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Kliknij przycisk **+ Utwórz zasób** > **obliczenia** > **systemu Windows Server 2016 Datacenter — płatność za użycie**  >   **Utwórz**. Jeśli nie widzisz **systemu Windows Server 2016 Datacenter — płatność za użycie** zapisu, skontaktuj się z operatorem usługi Azure Stack. Poproś o dodanie go do portalu marketplace zgodnie z objaśnieniem w [Dodaj obraz maszyny Wirtualnej systemu Windows Server 2016 w witrynie Marketplace usługi Azure Stack](../azure-stack-add-default-image.md) artykułu.

    ![Kroki, aby utworzyć maszynę wirtualną Windows w witrynie portal](media/azure-stack-quick-windows-portal/image01.png)
2. W obszarze **podstawy**, wpisz **nazwa**, **nazwa_użytkownika**, i **hasło**. Wybierz **subskrypcji**. Tworzenie **grupy zasobów**, lub wybierz istniejący wybierz jedną, **lokalizacji**, a następnie kliknij przycisk **OK**.

    ![Konfigurowanie ustawień podstawowych](media/azure-stack-quick-windows-portal/image02.png)
3. W obszarze **rozmiar** wybierz **standardowa D1**, a następnie kliknij polecenie **wybierz**.  
    ![Wybierz rozmiar maszyny wirtualnej](media/azure-stack-quick-windows-portal/image03.png)

4. Na **ustawienia** strony, wprowadź potrzebne zmiany do ustawień domyślnych.
   - Począwszy od wersji usługi Azure Stack 1808, można skonfigurować **magazynu** której użytkownik może użyć *usługi managed disks*. Przed wersją 1808 można tylko dyski niezarządzane.  
   ![Konfigurowanie ustawień maszyny wirtualnej](media/azure-stack-quick-windows-portal/image04.png)  
   Jeśli konfiguracje są gotowe, wybierz **OK** aby kontynuować.

5. W obszarze **Podsumowanie**, kliknij przycisk **OK** do utworzenia maszyny wirtualnej.
    ![Wyświetl podsumowanie, a następnie utwórz maszynę wirtualną](media/azure-stack-quick-windows-portal/image05.png)

6. Aby wyświetlić swoją nową maszyną wirtualną, kliknij **wszystkie zasoby**, wyszukaj nazwę maszyny wirtualnej, a następnie kliknij jego nazwę w wynikach wyszukiwania.
    ![Zobacz maszyny wirtualnej](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy maszyny wirtualnej należy usunąć maszynę wirtualną i jej zasobami. Aby to zrobić, wybierz grupę zasobów, na stronie maszyny wirtualnej, a następnie kliknij przycisk **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start wdrożono podstawowe maszyny wirtualnej systemu Windows Server. Aby dowiedzieć się więcej o maszynach wirtualnych usługi Azure Stack, w dalszym ciągu [uwagi dotyczące maszyn wirtualnych w usłudze Azure Stack](azure-stack-vm-considerations.md).
