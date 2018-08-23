---
title: 'Azure Stack: Szybki Start — tworzenie maszyny wirtualnej Windows'
description: 'Azure Stack: Szybki Start — tworzenie maszyny Wirtualnej Windows przy użyciu portalu'
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 08/15/2018
ms.author: mabrigg
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: efe6213e5c0261fb26ac40e74c2b0f6e0c9252dd
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139403"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>Szybki Start: tworzenie maszyny wirtualnej programu Windows server za pomocą portalu usługi Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Można utworzyć maszynę wirtualną systemu Windows Server 2016 za pomocą portalu usługi Azure Stack. Wykonaj kroki opisane w tym artykule, aby utworzyć maszynę wirtualną.

## <a name="sign-in-to-the-azure-stack-portal"></a>Zaloguj się do portalu usługi Azure Stack

Zaloguj się do portalu usługi Azure Stack. Adres portalu usługi Azure Stack jest zależny, dotyczących produktu usługi Azure Stack, który chcesz się połączyć:

* Dla usługi Azure Stack Development Kit (ASDK) przejdź do: https://portal.local.azurestack.external.
* Aby to system zintegrowany z usługi Azure Stack przejdź do adresu URL, który dostarczony operator usługi Azure Stack.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Kliknij przycisk **nowe** > **obliczenia** > **systemu Windows Server 2016 Datacenter — wersja próbna** > **tworzenie**. Jeśli nie widzisz **systemu Windows Server 2016 Datacenter — wersja próbna** zapisu, skontaktuj się z operatorem usługi Azure Stack. Poproś o dodanie go do portalu marketplace zgodnie z objaśnieniem w [Dodaj obraz maszyny Wirtualnej systemu Windows Server 2016 w witrynie Marketplace usługi Azure Stack](../azure-stack-add-default-image.md) artykułu.

    ![Kroki, aby utworzyć maszynę wirtualną Windows w witrynie portal](media/azure-stack-quick-windows-portal/image01.png)
2. W obszarze **podstawy**, wpisz **nazwa**, **nazwa_użytkownika**, i **hasło**. Wybierz **subskrypcji**. Tworzenie **grupy zasobów**, lub wybierz istniejący wybierz jedną, **lokalizacji**, a następnie kliknij przycisk **OK**.

    ![Konfigurowanie ustawień podstawowych](media/azure-stack-quick-windows-portal/image02.png)
3. W obszarze **wybierz rozmiar**, kliknij przycisk **standardowa D1** > **wybierz**.
    ![Wybierz rozmiar maszyny wirtualnej](media/azure-stack-quick-windows-portal/image03.png)
4. W obszarze **ustawienia**, zaakceptuj wartości domyślne i kliknij przycisk **OK**.
    ![Konfigurowanie ustawień maszyny wirtualnej](media/azure-stack-quick-windows-portal/image04.png)
5. W obszarze **Podsumowanie**, kliknij przycisk **OK** do utworzenia maszyny wirtualnej.
    ![Wyświetl podsumowanie, a następnie utwórz maszynę wirtualną](media/azure-stack-quick-windows-portal/image05.png)
6. Aby wyświetlić swoją nową maszyną wirtualną, kliknij **wszystkie zasoby**, wyszukaj nazwę maszyny wirtualnej, a następnie kliknij jego nazwę w wynikach wyszukiwania.
    ![Zobacz maszyny wirtualnej](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy maszyny wirtualnej należy usunąć maszynę wirtualną i jej zasobami. Aby to zrobić, wybierz grupę zasobów, na stronie maszyny wirtualnej, a następnie kliknij przycisk **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start wdrożono podstawowe maszyny wirtualnej systemu Windows Server. Aby dowiedzieć się więcej o maszynach wirtualnych usługi Azure Stack, w dalszym ciągu [uwagi dotyczące maszyn wirtualnych w usłudze Azure Stack](azure-stack-vm-considerations.md).
