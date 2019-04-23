---
title: Utwórz Maszynę wirtualną Windows nieupakowaną w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Tworzenie maszyny Wirtualnej z systemem Windows w strefie dostępności przy użyciu witryny Azure portal
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: e813b26a91d25fbaa1298acd455f27d2cac705f6
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997057"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Utwórz maszynę wirtualną Windows w strefie dostępności przy użyciu witryny Azure portal

Tym artykule omówiono tworzenie maszyny wirtualnej w strefie dostępności platformy Azure przy użyciu witryny Azure portal. [Strefa dostępności](../../availability-zones/az-overview.md) to fizycznie oddzielona strefa w regionie świadczenia usługi Azure. Strefy dostępności chronią aplikacje i dane, zmniejszając prawdopodobieństwo wystąpienia awarii lub utraty całego centrum danych.

Aby użyć strefy dostępność, utwórz maszynę wirtualną w [obsługiwanym regionie platformy Azure](../../availability-zones/az-overview.md#services-support-by-region).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.

2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**. 

3. Wprowadź informacje o maszynie wirtualnej. Nazwa użytkownika i hasło wprowadzone w tym miejscu są używane na potrzeby logowania się do maszyny wirtualnej. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](faq.md#what-are-the-password-requirements-when-creating-a-vm). Wybierz lokalizację, na przykład wschodnie stany USA 2, która obsługuje stref dostępności. Po zakończeniu kliknij przycisk **OK**.

    ![Wprowadzanie podstawowych informacji o maszynie wirtualnej w bloku portalu](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Wybierz rozmiar maszyny wirtualnej. Wybierz zalecany rozmiar lub filtru na podstawie funkcji. Upewnij się, że rozmiar jest dostępny w strefy, w której chcesz użyć.

    ![Wybierz rozmiar maszyny Wirtualnej](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. W obszarze **ustawienia** > **wysokiej dostępności**, wybierz jedną z numerowane stref z **strefy dostępności** listy rozwijanej, Zachowaj pozostałe wartości domyślne i Kliknij przycisk **OK**.

    ![Wybierz strefę dostępności](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Na stronie podsumowania kliknij **Utwórz** rozpocząć wdrażanie maszyny wirtualnej.

7. Maszyna wirtualna zostanie przypięta do pulpitu nawigacyjnego witryny Azure Portal. Po zakończeniu wdrażania zostanie automatycznie otwarte podsumowanie maszyny wirtualnej.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Potwierdzanie strefy dla dysku zarządzanego i adresu IP

Po wdrożeniu maszyny Wirtualnej w strefie dostępności dysku zarządzanego dla maszyny Wirtualnej jest tworzony w tej samej strefie dostępności. Domyślnie publiczny adres IP jest tworzona w tej strefie.

Można potwierdzić ustawienia strefy dla tych zasobów w portalu.  

1. Kliknij przycisk **grup zasobów** , a następnie nazwę zasobu zgrupować dla maszyny Wirtualnej, takie jak *myResourceGroup*.

2. Kliknij nazwę zasobu dysku. **Przegląd** strona zawiera szczegółowe informacje o lokalizacji i strefie dostępności zasobu.

    ![Strefy dostępności dla dysku zarządzanego](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Kliknij nazwę zasób publicznego adresu IP. **Przegląd** strona zawiera szczegółowe informacje o lokalizacji i strefie dostępności zasobu.

    ![Strefa dostępności adresu IP](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób tworzenia maszyny wirtualnej w strefie dostępności. Dowiedz się więcej o [regionach i dostępności](regions-and-availability.md) maszyn wirtualnych platformy Azure.
