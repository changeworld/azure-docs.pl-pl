---
title: Tworzenie zoned maszyny Wirtualnej systemu Windows za pomocą portalu Azure | Dokumentacja firmy Microsoft
description: Tworzenie maszyny Wirtualnej systemu Windows w strefie dostępności przy użyciu portalu Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 3d3561cf1ad760930821fabeef9839c25d55f2a9
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Utwórz maszynę wirtualną systemu Windows w strefie dostępności przy użyciu portalu Azure

Ta procedura artykułu przy użyciu portalu Azure, aby utworzyć maszynę wirtualną w strefie dostępności Azure. [Strefa dostępności](../../availability-zones/az-overview.md) to fizycznie oddzielona strefa w regionie świadczenia usługi Azure. Strefy dostępności chronią aplikacje i dane, zmniejszając prawdopodobieństwo wystąpienia awarii lub utraty całego centrum danych.

Aby użyć strefy dostępność, należy utworzyć maszyny wirtualnej w [obsługiwany region platformy Azure](../../availability-zones/az-overview.md#regions-that-support-availability-zones).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure. 

Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com.

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.

2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**. 

3. Wprowadź informacje o maszynie wirtualnej. Nazwa użytkownika i hasło wprowadzone w tym miejscu są używane na potrzeby logowania się do maszyny wirtualnej. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](faq.md#what-are-the-password-requirements-when-creating-a-vm). Wybierz lokalizację, takich jak wschodnie stany USA 2 obsługującego stref dostępności. Po zakończeniu kliknij przycisk **OK**.

    ![Wprowadzanie podstawowych informacji o maszynie wirtualnej w bloku portalu](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Wybierz rozmiar maszyny wirtualnej. Wybierz zalecany rozmiar lub filtrowanie w oparciu o funkcje. Upewnij się, że rozmiar jest dostępny w strefie, którego chcesz użyć.

    ![Wybierz rozmiar maszyny Wirtualnej](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. W obszarze **ustawienia** > **wysokiej dostępności**, wybierz jedną z numerem stref z **strefy dostępności** listy rozwijanej, Zachowaj pozostałe wartości domyślne, i Kliknij przycisk **OK**.

    ![Wybierz strefę dostępności](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Na stronie Podsumowanie kliknij **Utwórz** rozpocząć wdrażanie maszyny wirtualnej.

7. Maszyna wirtualna zostanie przypięta do pulpitu nawigacyjnego witryny Azure Portal. Po zakończeniu wdrażania zostanie automatycznie otwarte podsumowanie maszyny wirtualnej.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Potwierdź strefę dla adresów IP i dysków zarządzanych

Po wdrożeniu maszyny Wirtualnej w strefie dostępności zarządzanego dysku dla maszyny Wirtualnej jest tworzony w tej samej strefie dostępności. Domyślnie tworzona jest również publicznego adresu IP w tej strefie.

Można potwierdzić ustawienia strefy dla tych zasobów w portalu.  

1. Kliknij przycisk **grup zasobów** , a następnie nazwę zasobu grupy dla maszyny Wirtualnej, takie jak *myResourceGroup*.

2. Kliknij nazwę zasobu dyskowego. **Omówienie** strona zawiera szczegółowe informacje o strefie lokalizacji i dostępności zasobów.

    ![Dostępność strefę dla dysków zarządzanych](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Kliknij nazwę zasobu adresu publicznego adresu IP. **Omówienie** strona zawiera szczegółowe informacje o strefie lokalizacji i dostępności zasobów.

    ![Dostępność strefie dla adresu IP](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób tworzenia maszyny Wirtualnej w strefie dostępności. Dowiedz się więcej o [regionach i dostępności](regions-and-availability.md) maszyn wirtualnych platformy Azure.
