---
title: Wiele adresów IP dla maszyn wirtualnych platformy Azure — portal | Dokumenty firmy Microsoft
description: Dowiedz się, jak przypisać wiele adresów IP do maszyny wirtualnej za pomocą witryny Azure Portal | Menedżer zasobów.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: anavin
ms.openlocfilehash: a5bb9bb9c584aef8ac79b3c275d01d3c498da843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060630"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Przypisywanie wielu adresów IP do maszyn wirtualnych za pomocą portalu Azure

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> W tym artykule wyjaśniono, jak utworzyć maszynę wirtualną (VM) za pośrednictwem modelu wdrażania usługi Azure Resource Manager przy użyciu witryny Azure portal. Wielu adresów IP nie można przypisać do zasobów utworzonych za pośrednictwem klasycznego modelu wdrażania. Aby dowiedzieć się więcej o modelach wdrażania platformy Azure, przeczytaj artykuł [Zrozumieć modele wdrażania.](../resource-manager-deployment-model.md)

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Tworzenie maszyny wirtualnej z wieloma adresami IP

Jeśli chcesz utworzyć maszynę wirtualną z wieloma adresami IP lub statyczny prywatny adres IP, należy utworzyć ją przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się, jak to zrobić, kliknij opcje programu PowerShell lub interfejsu wiersza polecenia u góry tego artykułu. Maszynę Wirtualną można utworzyć z jednym dynamicznym prywatnym adresem IP i (opcjonalnie) pojedynczym publicznym adresem IP. Użyj portalu, wykonując kroki opisane w artykułu [Tworzenie maszyny Wirtualnej systemu Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) lub Tworzenie artykułów na [maszynie VM z systemem Linux.](../virtual-machines/linux/quick-create-portal.md) Po utworzeniu maszyny Wirtualnej można zmienić typ adresu IP z dynamicznego na statyczny i dodać dodatkowe adresy IP za pomocą portalu, wykonując kroki opisane w sekcji [Dodawanie adresów IP do](#add) maszyny Wirtualnej w tym artykule.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Dodawanie adresów IP do maszyny Wirtualnej

Możesz dodać prywatne i publiczne adresy IP do interfejsu sieciowego platformy Azure, wykonując następujące kroki. Przykłady w poniższych sekcjach zakładają, że masz już maszynę wirtualną z trzema konfiguracjami IP opisanymi w [scenariuszu,](#scenario)ale nie jest to wymagane.

### <a name="core-steps"></a><a name="coreadd"></a>Podstawowe kroki

1. Przejdź do witryny https://portal.azure.com Azure portal at i zaloguj się do niego, jeśli to konieczne.
2. W portalu kliknij pozycję **Więcej usług** > wpisz *maszyny wirtualne* w polu filtru, a następnie kliknij pozycję **Maszyny wirtualne**.
3. W okienku **Maszyny wirtualne** kliknij maszynę wirtualną, do której chcesz dodać adresy IP. Przejdź do karty **Sieć.** Kliknij **pozycję Interfejs sieciowy** na stronie. Jak pokazano na rysunku poniżej: 


    ![Dodawanie publicznego adresu IP do maszyny wirtualnej](./media/virtual-network-multiple-ip-addresses-portal/figure200319.png)
4. W okienku **Interfejs sieci** kliknij przycisk **Konfiguracje IP**.

5. W okienku wyświetlanym dla wybranej karty sieciowej kliknij pozycję **Konfiguracje adresów IP**. Kliknij **przycisk Dodaj**, wykonaj kroki w jednej z następnych sekcji, na podstawie typu adresu IP, który chcesz dodać, a następnie kliknij przycisk **OK**. 

### <a name="add-a-private-ip-address"></a>**Dodawanie prywatnego adresu IP**

Wykonaj następujące kroki, aby dodać nowy prywatny adres IP:

1. Wykonaj kroki opisane w sekcji [Podstawowe kroki](#coreadd) tego artykułu.
2. Kliknij przycisk **Dodaj**. W wyświetlonym okienku **konfiguracji Dodaj adres IP** utwórz konfigurację IP o nazwie *IPConfig-4* z *10.0.0.7* jako *statyczny* prywatny adres IP, a następnie kliknij przycisk **OK**.

    > [!NOTE]
    > Podczas dodawania statycznego adresu IP należy określić nieużyny, prawidłowy adres w podsieci, z którym jest podłączona karta sieciowa. Jeśli wybrany adres jest niedostępny, w portalu zostanie wyświetlony znak X dla adresu IP i należy wybrać inny adres.

3. Po kliknięciu przycisku OK okienko zostanie zamknięte i zostanie wyświetlona nowa konfiguracja IP na liście. Kliknij **przycisk OK,** aby zamknąć okienko **konfiguracji Dodawanie adresu IP.**
4. Można kliknąć przycisk **Dodaj,** aby dodać dodatkowe konfiguracje adresów IP, lub zamknąć wszystkie otwarte ostrza, aby zakończyć dodawanie adresów IP.
5. Dodaj prywatne adresy IP do systemu operacyjnego maszyny Wirtualnej, wykonując kroki opisane w sekcji [Dodawanie adresów IP do systemu operacyjnego maszyny Wirtualnej](#os-config) w tym artykule.

### <a name="add-a-public-ip-address"></a>Dodawanie publicznego adresu IP

Publiczny adres IP jest dodawany przez skojarzenie zasobu publicznego adresu IP z nową konfiguracją IP lub istniejącą konfiguracją IP.

> [!NOTE]
> Publiczne adresy IP mają symboliczną opłatę. Aby dowiedzieć się więcej o cenach adresów IP, przeczytaj stronę [cennika adresu IP.](https://azure.microsoft.com/pricing/details/ip-addresses) Istnieje ograniczenie liczby publicznych adresów IP, które mogą być używane w subskrypcji. Aby uzyskać więcej informacji o limitach, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
> 

### <a name="create-a-public-ip-address-resource"></a><a name="create-public-ip"></a>Tworzenie zasobu publicznego adresu IP

Publiczny adres IP jest jednym z ustawień dla zasobu publicznego adresu IP. Jeśli masz publiczny zasób adresu IP, który nie jest obecnie skojarzony z konfiguracją IP, którą chcesz skojarzyć z konfiguracją IP, pomiń następujące kroki i wykonaj kroki w jednej z poniższych sekcji zgodnie z wymaganiami. Jeśli nie masz dostępnego zasobu publicznego adresu IP, wykonaj następujące kroki, aby go utworzyć:

1. Przejdź do witryny https://portal.azure.com Azure portal at i zaloguj się do niego, jeśli to konieczne.
3. W portalu kliknij pozycję **Utwórz publiczny** > **adres IP**sieci **.** > 
4. W wyświetlonym okienku **Tworzenie publicznego adresu IP** wprowadź pozycję **Nazwa**, wybierz typ **przypisania adresu IP,** **subskrypcję,** **grupę zasobów**i **lokalizację**, a następnie kliknij pozycję **Utwórz**, jak pokazano na poniższym rysunku:

    ![Tworzenie zasobu publicznego adresu IP](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Wykonaj kroki opisane w jednej z sekcji, aby skojarzyć publiczny zasób adresu IP z konfiguracją adresu IP.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Skojarzenie zasobu publicznego adresu IP z nową konfiguracją adresu IP

1. Wykonaj kroki opisane w sekcji [Podstawowe kroki](#coreadd) tego artykułu.
2. Kliknij przycisk **Dodaj**. W wyświetlonym okienku **konfiguracji Dodaj adres IP** utwórz konfigurację IP o nazwie *IPConfig-4*. Włącz **publiczny adres IP** i wybierz istniejący, dostępny publiczny zasób adresu IP z **wyświetlanego** okienka Wybierz publiczny adres IP.

    Po wybraniu zasobu publicznego adresu IP kliknij przycisk **OK,** a okienko zostanie zamknięte. Jeśli nie masz istniejącego publicznego adresu IP, możesz go utworzyć, wykonując kroki opisane w sekcji [Tworzenie zasobu publicznego adresu IP](#create-public-ip) w tym artykule. 

3. Przejrzyj nową konfigurację IP. Mimo że prywatny adres IP nie został jawnie przypisany, jeden został automatycznie przypisany do konfiguracji IP, ponieważ wszystkie konfiguracje IP muszą mieć prywatny adres IP.
4. Można kliknąć przycisk **Dodaj,** aby dodać dodatkowe konfiguracje adresów IP, lub zamknąć wszystkie otwarte ostrza, aby zakończyć dodawanie adresów IP.
5. Dodaj prywatny adres IP do systemu operacyjnego maszyny Wirtualnej, wykonując kroki dotyczące systemu operacyjnego w sekcji [Dodaj adresy IP do systemu operacyjnego maszyny Wirtualnej](#os-config) w tym artykule. Nie należy dodawać publicznego adresu IP do systemu operacyjnego.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Skojarzenie zasobu publicznego adresu IP z istniejącą konfiguracją adresu IP

1. Wykonaj kroki opisane w sekcji [Podstawowe kroki](#coreadd) tego artykułu.
2. Kliknij konfigurację IP, do której chcesz dodać publiczny zasób adresu IP.
3. W wyświetlonym okienku IPConfig kliknij pozycję **Adres IP**.
4. W wyświetlonym okienku **Wybieranie publicznego adresu IP** wybierz publiczny adres IP.
5. Kliknij **przycisk Zapisz** i okienka są zamykane. Jeśli nie masz istniejącego publicznego adresu IP, możesz go utworzyć, wykonując kroki opisane w sekcji [Tworzenie zasobu publicznego adresu IP](#create-public-ip) w tym artykule.
3. Przejrzyj nową konfigurację IP.
4. Można kliknąć przycisk **Dodaj,** aby dodać dodatkowe konfiguracje adresów IP, lub zamknąć wszystkie otwarte ostrza, aby zakończyć dodawanie adresów IP. Nie należy dodawać publicznego adresu IP do systemu operacyjnego.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
