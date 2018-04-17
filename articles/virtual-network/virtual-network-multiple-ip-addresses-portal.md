---
title: Wiele adresów IP dla maszyn wirtualnych platformy Azure - Portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można przypisać wiele adresów IP do maszyny wirtualnej przy użyciu portalu Azure | Menedżer zasobów.
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
ms.author: annahar
ms.openlocfilehash: 85eefd0d15ed08eaa82983c6901faa0aa1ff303c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Przypisz wielu adresów IP do maszyn wirtualnych za pomocą portalu Azure

>[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
>
W tym artykule opisano sposób tworzenia maszyny wirtualnej (VM) za pośrednictwem modelu wdrażania usługi Azure Resource Manager przy użyciu portalu Azure. Nie można przypisać wiele adresów IP do zasobów została utworzona za pośrednictwem klasycznego modelu wdrażania. Aby dowiedzieć się więcej na temat modeli wdrażania platformy Azure, przeczytaj [zrozumieć modele wdrażania](../resource-manager-deployment-model.md) artykułu.

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Utwórz maszynę Wirtualną z wielu adresów IP

Jeśli chcesz utworzyć Maszynę wirtualną z wielu adresów IP lub statycznego prywatnego adresu IP, należy utworzyć ją przy użyciu programu PowerShell lub wiersza polecenia platformy Azure. Aby dowiedzieć się, jak to zrobić, kliknij przycisk Opcje programu PowerShell lub interfejsu wiersza polecenia w górnej części tego artykułu. Można utworzyć Maszynę wirtualną z jednego dynamicznego prywatnego adresu IP i (opcjonalnie) jeden publiczny adres IP. Użyj portalu, wykonując kroki opisane w [utworzyć Maszynę wirtualną systemu Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) lub [Utwórz Maszynę wirtualną systemu Linux](../virtual-machines/linux/quick-create-portal.md) artykułów. Po utworzeniu maszyny Wirtualnej, można zmienić typu adresu IP z dynamicznego statyczne i dodawania dodatkowych adresów IP przy użyciu portalu, wykonując kroki w [adresów IP dodać do maszyny Wirtualnej](#add) sekcji tego artykułu.

## <a name="add"></a>Dodaj adresy IP do maszyny Wirtualnej

Prywatne i publiczne adresy IP można dodać do interfejsu sieci platformy Azure, wykonując kroki, które należy wykonać. Przykłady w poniższych sekcjach założono, że już istnieje maszyna wirtualna z tych trzech konfiguracji adresu IP, opisane w [scenariusza](#Scenario), ale nie jest to wymagane.

### <a name="coreadd"></a>Podstawowe kroki

1. Przejdź do portalu Azure pod adresem https://portal.azure.com i zaloguj się do niego, jeśli to konieczne.
2. W portalu kliknij **więcej usług** > typ *maszyn wirtualnych* w polu filtru, a następnie kliknij przycisk **maszyn wirtualnych**.
3. W **maszyn wirtualnych** okienku, kliknij maszynę Wirtualną, aby dodać IP adresów do. Kliknij przycisk **interfejsy sieciowe** na maszynie wirtualnej adresy okienko zostanie wyświetlony, a następnie wybierz interfejs sieciowy, aby dodać adres IP. W przykładzie pokazano na poniższej ilustracji, karta sieciowa o nazwie *myNIC* z maszyny Wirtualnej o nazwie *myVM* wybrano:

    ![Interfejs sieciowy](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

4. W okienku wyświetlonym wybranym karty sieciowej, kliknij **konfiguracje adresów IP**.

Wykonaj kroki opisane w sekcji, które należy wykonać, na podstawie typu adresu IP, który chcesz dodać.

### <a name="add-a-private-ip-address"></a>**Dodaj prywatnego adresu IP**

Wykonaj poniższe kroki, aby dodać nowy prywatnego adresu IP:

1. Wykonaj kroki [podstawowe kroki](#coreadd) sekcji tego artykułu.
2. Kliknij pozycję **Add** (Dodaj). W **konfiguracji IP dodać** okienko zostanie wyświetlone, utworzyć konfigurację IP o nazwie *IPConfig 4* z *10.0.0.7* jako *statycznych* prywatnego adresu IP adresów, a następnie kliknij przycisk **OK**.

    > [!NOTE]
    > Podczas dodawania statycznego adresu IP, należy określić nieużywanych, prawidłowy adres podsieci, karta sieciowa jest połączona z. Jeśli wybrany adres nie jest dostępna, portal zawiera znak X dla adresu IP i należy wybrać inny.

3. Po kliknięciu przycisku OK okienko zostanie zamknięte, a następnie zobacz nowej konfiguracji IP na liście. Kliknij przycisk **OK** zamknąć **konfiguracji IP dodać** okienka.
4. Możesz kliknąć **Dodaj** Aby dodać dodatkowe konfiguracje adresów IP, lub zamknij wszystkie otwarte bloków, aby zakończyć dodawanie adresów IP.
5. Dodaj prywatnych adresów IP do systemu operacyjnego maszyny Wirtualnej, wykonując kroki opisane w [adresów IP Dodaj do systemu operacyjnego maszyny Wirtualnej](#os-config) sekcji tego artykułu.

### <a name="add-a-public-ip-address"></a>Dodaj publiczny adres IP

Publiczny adres IP jest dodawany przez skojarzenie publicznego zasobu adresu IP do nowej konfiguracji IP lub istniejącej konfiguracji adresu IP.

> [!NOTE]
> Publiczne adresy IP ma nominalnego opłatą. Aby dowiedzieć się więcej o cenach adresu IP, przeczytaj [cennik adres IP](https://azure.microsoft.com/pricing/details/ip-addresses) strony. Istnieje ograniczona liczba publicznych adresów IP, których można użyć w ramach subskrypcji. Aby uzyskać więcej informacji o limitach, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-subscription-service-limits.md#networking-limits).
> 

### <a name="create-public-ip"></a>Utwórz zasób publicznego adresu IP

Publiczny adres IP jest jedno ustawienie zasobu publicznego adresu IP. Jeśli masz zasobu publicznego adresu IP, który nie jest obecnie skojarzony z konfigurację protokołu IP, który ma zostać skojarzona z konfiguracją protokołu IP pominęli następujące kroki i wykonaj kroki jednej z kolejnych sekcjach, ile potrzebujesz. Jeśli nie masz dostępnych zasób publicznego adresu IP, wykonaj następujące kroki, aby utworzyć:

1. Przejdź do portalu Azure pod adresem https://portal.azure.com i zaloguj się do niego, jeśli to konieczne.
3. W portalu kliknij **Utwórz zasób** > **sieci** > **publicznego adresu IP**.
4. W **tworzenie publicznego adresu IP** okienko zostanie wyświetlone, wprowadź **nazwa**, wybierz pozycję **przypisywania adresów IP** typu **subskrypcji**, **Grupy zasobów**, a **lokalizacji**, następnie kliknij przycisk **Utwórz**, jak pokazano na poniższej ilustracji:

    ![Utwórz zasób publicznego adresu IP](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Wykonaj kroki opisane w sekcji, które należy wykonać, aby skojarzyć zasób publicznego adresu IP do konfiguracji adresu IP.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Skojarz zasób publicznego adresu IP nowej konfiguracji adresu IP

1. Wykonaj kroki [podstawowe kroki](#coreadd) sekcji tego artykułu.
2. Kliknij pozycję **Add** (Dodaj). W **konfiguracji IP dodać** okienko zostanie wyświetlone, utworzyć konfigurację IP o nazwie *IPConfig 4*. Włącz **publicznego adresu IP** i wybierz istniejący, dostępne publicznego zasobu adresu IP z **wybierz publiczny adres IP** okienko zostanie wyświetlone.

    Po wybraniu zasobu publicznego adresu IP, kliknij przycisk **OK** i okienku zostanie zamknięta. Jeśli nie masz istniejącego publicznego adresu IP, możesz utworzyć jedną, wykonując kroki opisane w [Utwórz zasób publiczny adres IP](#create-public-ip) sekcji tego artykułu. 

3. Przejrzyj nowej konfiguracji adresu IP. Mimo że jawnie nie został przypisany prywatny adres IP, co zostało automatycznie przydzielone konfiguracji adresów IP, ponieważ wszystkie konfiguracje adresów IP muszą mieć prywatnego adresu IP.
4. Możesz kliknąć **Dodaj** Aby dodać dodatkowe konfiguracje adresów IP, lub zamknij wszystkie otwarte bloków, aby zakończyć dodawanie adresów IP.
5. Dodaj prywatnego adresu IP do systemu operacyjnego maszyny Wirtualnej, wykonując kroki odpowiednie dla systemu operacyjnego w [adresów IP Dodaj do systemu operacyjnego maszyny Wirtualnej](#os-config) sekcji tego artykułu. Publiczny adres IP nie należy dodawać do systemu operacyjnego.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Skojarz zasób publicznego adresu IP do istniejącej konfiguracji adresu IP

1. Wykonaj kroki [podstawowe kroki](#coreadd) sekcji tego artykułu.
2. Kliknij pozycję konfiguracji IP, które chcesz dodać zasobu publicznego adresu IP do.
3. W wyświetlonym okienku IPConfig kliknij **adres IP**.
4. W **wybierz publiczny adres IP** okienko zostanie wyświetlone, wybierz publiczny adres IP.
5. Kliknij przycisk **zapisać** i zamknij okienka. Jeśli nie masz istniejącego publicznego adresu IP, możesz utworzyć jedną, wykonując kroki opisane w [Utwórz zasób publiczny adres IP](#create-public-ip) sekcji tego artykułu.
3. Przejrzyj nowej konfiguracji adresu IP.
4. Możesz kliknąć **Dodaj** Aby dodać dodatkowe konfiguracje adresów IP, lub zamknij wszystkie otwarte bloków, aby zakończyć dodawanie adresów IP. Publiczny adres IP nie należy dodawać do systemu operacyjnego.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
