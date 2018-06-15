---
title: Azure stosu Szybki Start - Utwórz maszynę wirtualną systemu Windows
description: Azure stosu Szybki Start - Utwórz maszynę Wirtualną z systemem Windows przy użyciu portalu
services: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 04/23/2018
ms.author: brenduns
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 5776fc472483018eb2c9e4f8962d0b1e8bce8081
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32155585"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>Szybki Start: Utwórz maszynę wirtualną systemu Windows server przy użyciu portalu Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Można utworzyć maszyny wirtualnej systemu Windows Server 2016 przy użyciu portalu Azure stosu. Wykonaj kroki opisane w tym artykule do utworzenia i użycia maszyny wirtualnej.

## <a name="sign-in-to-the-azure-stack-portal"></a>Zaloguj się do portalu Azure stosu

Zaloguj się do portalu Azure stosu. Adres w portalu Azure stosu zależy od tego, na produkt stosu Azure, dla którego chcesz się połączyć:

* Dla usługi Azure stosu Development Kit (ASDK), przejdź do: https://portal.local.azurestack.external.
* System Azure stosu zintegrowane przejdź do adresu URL, zapewnianej przez operatora sieci Azure stosu.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Kliknij przycisk **nowe** > **obliczeniowe** > **systemu Windows Server 2016 Datacenter Eval** > **utworzyć**. Jeśli nie widzisz **Eval centrum danych systemu Windows Server 2016** wpisu, skontaktuj się z operatorem stosu Azure. Poproś o dodanie go do witryny marketplace zgodnie z objaśnieniem w [Dodaj obraz maszyny Wirtualnej systemu Windows Server 2016 do stosu Azure marketplace](../azure-stack-add-default-image.md) artykułu.

    ![Kroki, aby utworzyć maszynę wirtualną systemu Windows w portalu](media/azure-stack-quick-windows-portal/image01.png)
2. W obszarze **podstawy**, wpisz **nazwa**, **nazwy użytkownika**, i **hasło**. Wybierz **subskrypcji**. Utwórz **grupy zasobów**, lub wybierz istniejący jeden, kliknij **lokalizacji**, a następnie kliknij przycisk **OK**.

    ![Konfigurowanie ustawień podstawowych](media/azure-stack-quick-windows-portal/image02.png)
3. W obszarze **wybierz rozmiar**, kliknij przycisk **D1 standardowe** > **wybierz**.
    ![Wybierz rozmiar maszyny wirtualnej](media/azure-stack-quick-windows-portal/image03.png)
4. W obszarze **ustawienia**, zaakceptuj ustawienia domyślne i kliknij przycisk **OK**.
    ![Skonfiguruj ustawienia maszyny wirtualnej](media/azure-stack-quick-windows-portal/image04.png)
5. W obszarze **Podsumowanie**, kliknij przycisk **OK** można utworzyć maszyny wirtualnej.
    ![Wyświetl podsumowanie i utworzenie maszyny wirtualnej](media/azure-stack-quick-windows-portal/image05.png)
6. Aby zapoznać się z nowej maszyny wirtualnej, kliknij przycisk **wszystkie zasoby**, wyszukaj nazwę maszyny wirtualnej, a następnie kliknij jego nazwę w wynikach wyszukiwania.
    ![Zobacz maszyny wirtualnej](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy maszyny wirtualnej należy usunąć maszynę wirtualną i jej zasobach. Aby to zrobić, wybierz grupę zasobów, na stronie maszyny wirtualnej, a następnie kliknij przycisk **usunąć**.

## <a name="next-steps"></a>Kolejne kroki

W tym szybki start wdrożono podstawowej maszyny wirtualnej systemu Windows Server. Aby dowiedzieć się więcej o maszynach wirtualnych Azure stosu, nadal [zagadnienia dotyczące maszyn wirtualnych w stosie Azure](azure-stack-vm-considerations.md).
