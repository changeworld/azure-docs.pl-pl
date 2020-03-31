---
title: Wdrażanie dedykowanych hostów platformy Azure przy użyciu portalu Azure
description: Wdrażanie maszyn wirtualnych do dedykowanych hostów przy użyciu witryny Azure portal.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.openlocfilehash: 195a19ef881f235ad8e42f23b53da9e667ef88d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086756"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Wdrażanie maszyn wirtualnych na dedykowanych hostach za pomocą portalu

W tym artykule opisano, jak utworzyć [dedykowany host](dedicated-hosts.md) platformy Azure do obsługi maszyn wirtualnych (maszyn wirtualnych). 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu okna witryny Azure Portal.
1. W polu wyszukiwania nad listą zasobów Microsoft Azure Marketplace wpisz **Ubuntu Server 16.04 LTS by Canonical**, wybierz odpowiednią pozycję i kliknij **Utwórz**.
1. Na karcie **Podstawy** w obszarze **Szczegóły projektu**upewnij się, że jest wybrana poprawna subskrypcja, a następnie wybierz *myDedicatedHostsRG* jako **grupę Zasobów**. 
1. W obszarze **Szczegóły wystąpienia** wpisz *myVM* w polu **Nazwa maszyny wirtualnej** i wybierz *Wschodnie stany USA* w polu **Lokalizacja **.
1. W **obszarze Opcje dostępności** wybierz **strefę Dostępność**, wybierz *1* z listy rozwijanej.
1. Dla rozmiaru wybierz pozycję **Zmień rozmiar**. Na liście dostępnych rozmiarów wybierz jeden z serii Esv3, takich jak **Standard E2s v3**. Może być konieczne wyczyszczenie filtru, aby wyświetlić wszystkie dostępne rozmiary.
1. W obszarze **Konto administratora** wybierz opcję **klucz publiczny SSH**, wpisz nazwę użytkownika, a następnie wklej swój klucz publiczny w polu tekstowym. Usuń wszystkie wiodące i końcowe białe znaki z klucza publicznego.

    ![Konto administratora](./media/quick-create-portal/administrator-account.png)

1. W obszarze **Reguły portów** > **przychodzących Publiczne porty przychodzące**wybierz pozycję **Zezwalaj na wybrane porty,** a następnie wybierz z listy rozwijanej pozycję **SSH (22).** 
1. U góry strony wybierz kartę **Zaawansowane,** a następnie w sekcji **Host** wybierz **grupę** *myHostGroup* for Host i *myHost* dla **hosta**. 
    ![Wybieranie grupy hostów i hosta](./media/dedicated-hosts-portal/advanced.png)
1. Pozostaw pozostałe wartości domyślne, a następnie wybierz przycisk **Przejrzyj + utwórz** znajdujący się u dołu strony.
1. Po wyświetleniu komunikatu, że weryfikacja została przekazana, wybierz pozycję **Utwórz**.

Wdrożenie maszyny wirtualnej potrwa kilka minut.

## <a name="add-an-existing-vm"></a>Dodawanie istniejącej maszyny wirtualnej 

Można dodać wyjście maszyny Wirtualnej do dedykowanego hosta, ale maszyna wirtualna musi najpierw stop\Deallocated. Przed przeniesieniem maszyny Wirtualnej na dedykowany host upewnij się, że konfiguracja maszyny Wirtualnej jest obsługiwana:

- Rozmiar maszyny Wirtualnej musi znajdować się w tej samej rodzinie rozmiarów co dedykowany host. Na przykład jeśli dedykowany host jest DSv3, rozmiar maszyny Wirtualnej może być Standard_D4s_v3, ale nie może być Standard_A4_v2. 
- Maszyna wirtualna musi znajdować się w tym samym regionie co dedykowany host.
- Maszyna wirtualna nie może być częścią grupy miejsc docelowych zbliżeniowych. Usuń maszynę wirtualną z grupy miejsc docelowych zbliżeniowych przed przeniesieniem jej do dedykowanego hosta. Aby uzyskać więcej informacji, zobacz [Przenoszenie maszyny Wirtualnej z grupy miejsc docelowych zbliżeniowych](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- Maszyna wirtualna nie może być w zestawie dostępności.
- Jeśli maszyna wirtualna znajduje się w strefie dostępności, musi być tą samą strefą dostępności co grupa hosta. Ustawienia strefy dostępności dla maszyny Wirtualnej i grupy hostów muszą być zgodne.

Przenieś maszynę wirtualną na dedykowany host za pomocą [portalu](https://portal.azure.com).

1. Otwórz stronę maszyny Wirtualnej.
1. Wybierz **pozycję Zatrzymaj,** aby zatrzymać\zdeterminowaćlokalizuj maszynę wirtualną.
1. Z lewego menu **wybierz polecenie Konfiguracja.**
1. Wybierz grupę hostów i hosta z menu rozwijanych.
1. Po zakończeniu wybierz pozycję **Zapisz** u góry strony.
1. Po dodaniu maszyny Wirtualnej do hosta wybierz **opcję Przegląd** z lewego menu.
1. U góry strony wybierz pozycję **Start,** aby ponownie uruchomić maszynę wirtualną.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zobacz [przegląd dedykowanych hostów.](dedicated-hosts.md)

- Istnieje przykładowy szablon, znaleziono [tutaj,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)który używa zarówno stref i domen błędów dla maksymalnej odporności w regionie.

- Można również wdrożyć dedykowanego hosta przy użyciu [interfejsu wiersza polecenia platformy Azure](dedicated-hosts-cli.md).



