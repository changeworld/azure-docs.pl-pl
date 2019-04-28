---
title: Wiele adresów IP dla maszyn wirtualnych platformy Azure — Portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przypisać wiele adresów IP do maszyny wirtualnej przy użyciu witryny Azure portal | Menedżer zasobów.
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
ms.openlocfilehash: b1873b770a6b4280b7098c68ecb75cc1411fe453
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746992"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Przypisywanie wielu adresów IP do maszyn wirtualnych przy użyciu witryny Azure portal

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> W tym artykule wyjaśniono, jak utworzyć maszynę wirtualną (VM) za pomocą modelu wdrażania usługi Azure Resource Manager przy użyciu witryny Azure portal. Wiele adresów IP nie można przypisać do zasobów utworzonych za pomocą klasycznego modelu wdrażania. Aby dowiedzieć się więcej na temat modeli wdrażania platformy Azure, przeczytaj [omówienie modeli wdrażania](../resource-manager-deployment-model.md) artykułu.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Tworzenie maszyny Wirtualnej z wieloma adresami IP

Jeśli chcesz utworzyć maszynę Wirtualną z wieloma adresami IP lub statyczny prywatny adres IP, należy utworzyć je przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się, jak to zrobić, kliknij przycisk Opcje programu PowerShell lub interfejsu wiersza polecenia, w górnej części tego artykułu. Możesz utworzyć Maszynę wirtualną za pomocą pojedynczego dynamiczny prywatny adres IP oraz (opcjonalnie) jeden publiczny adres IP. Korzystanie z portalu, wykonując kroki opisane w [tworzenie maszyny Wirtualnej z systemem Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) lub [tworzenie maszyny Wirtualnej z systemem Linux](../virtual-machines/linux/quick-create-portal.md) artykułów. Po utworzeniu maszyny Wirtualnej, można zmienić typ adresu IP z dynamicznej na statyczną i dodać kolejne adresy IP przy użyciu portalu, wykonując kroki w [adresów Dodaj adres IP maszyny Wirtualnej](#add) dalszej części tego artykułu.

## <a name="add"></a>Dodaj adresy IP do maszyny Wirtualnej

Możesz dodać prywatnych i publicznych adresów IP do interfejsu sieci platformy Azure, wykonując poniższe kroki. Przykłady podane w poniższych sekcjach przyjęto założenie, że masz już Maszynę wirtualną przy użyciu trzech konfiguracji adresów IP, opisane w [scenariusza](#scenario), ale nie jest to wymagane.

### <a name="coreadd"></a>Podstawowe kroki

1. Przejdź do witryny Azure portal pod https://portal.azure.com i zaloguj się do niej, jeśli to konieczne.
2. W portalu, kliknij przycisk **więcej usług** > typ *maszyn wirtualnych* w polu filtru, a następnie kliknij przycisk **maszyn wirtualnych**.
3. W **maszyn wirtualnych** okienku, kliknij maszynę Wirtualną, aby dodać adres IP adresów. Kliknij przycisk **interfejsy sieciowe** na maszynie wirtualnej okienko, w którym pojawia się, a następnie wybierz interfejs sieciowy, aby dodać adres IP adresów. W przykładzie pokazano na poniższej ilustracji, karta sieciowa o nazwie *myNIC* z maszyny Wirtualnej o nazwie *myVM* wybrano:

    ![Interfejs sieciowy](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

4. W okienku są wyświetlane dla wybranego interfejsu Sieciowego, kliknij przycisk **konfiguracje adresów IP**.

Wykonaj kroki opisane w sekcji, które należy wykonać, na podstawie typu adresu IP, który chcesz dodać.

### <a name="add-a-private-ip-address"></a>**Dodaj prywatny adres IP**

Wykonaj poniższe kroki, aby dodać nowego prywatnego adresu IP:

1. Wykonaj kroki [podstawowe kroki](#coreadd) dalszej części tego artykułu.
2. Kliknij pozycję **Add** (Dodaj). W **konfiguracji Dodaj adres IP** okienko zostanie wyświetlone, Utwórz konfigurację adresu IP o nazwie *IPConfig 4* z *10.0.0.7* jako *statyczne* prywatny adres IP adres, a następnie kliknij przycisk **OK**.

    > [!NOTE]
    > Podczas dodawania statyczny adres IP, musisz określić adres nieużywany, prawidłowy w podsieci, z którą jest połączona karta sieciowa. Jeśli wybrany adres nie jest dostępna, portalu jest wyświetlany znak X dla adresu IP i musisz wybrać inne konto.

3. Po kliknięciu przycisku OK okienko zostanie zamknięte, a zobaczysz nową konfigurację adresu IP na liście. Kliknij przycisk **OK** zamknąć **konfiguracji Dodaj adres IP** okienka.
4. Możesz kliknąć pozycję **Dodaj** Aby dodać dodatkowe konfiguracje adresów IP lub zamknąć wszystkie otwarte bloki, aby zakończyć dodawanie adresów IP.
5. Dodawanie prywatnych adresów IP do systemu operacyjnego maszyny Wirtualnej, wykonując kroki opisane w [adresy IP Dodaj do systemu operacyjnego maszyny Wirtualnej](#os-config) dalszej części tego artykułu.

### <a name="add-a-public-ip-address"></a>Dodawanie publicznego adresu IP

Publiczny adres IP jest dodawany przez skojarzenie publicznego zasobu adresu IP na konfigurację adresu IP nowej lub istniejącej konfiguracji adresu IP.

> [!NOTE]
> Publiczne adresy IP mają nominalnej. Aby dowiedzieć się więcej o cenach adresów IP, przeczytaj [cennik adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses) strony. Istnieje limit liczby publicznych adresów IP, które mogą być używane w ramach subskrypcji. Aby uzyskać więcej informacji o limitach, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-subscription-service-limits.md#networking-limits).
> 

### <a name="create-public-ip"></a>Utwórz zasób publicznego adresu IP

Publiczny adres IP to jedno z ustawień zasobu publicznego adresu IP. Jeśli masz zasób publicznego adresu IP, który nie jest obecnie skojarzone z konfiguracji adresu IP, który chcesz skojarzyć z konfiguracji adresu IP, pominęli następujące kroki, a następnie wykonaj kroki opisane w sekcji, które należy wykonać, ile potrzebujesz. Jeśli nie masz dostępnych zasób publicznego adresu IP, wykonaj następujące kroki, aby utworzyć:

1. Przejdź do witryny Azure portal pod https://portal.azure.com i zaloguj się do niej, jeśli to konieczne.
3. W portalu, kliknij przycisk **Utwórz zasób** > **sieć** > **publiczny adres IP**.
4. W **tworzenie publicznego adresu IP** okienko zostanie wyświetlone, wprowadź **nazwa**, wybierz opcję **przypisywania adresów IP** typu **subskrypcji**, **Grupy zasobów**, a **lokalizacji**, następnie kliknij przycisk **Utwórz**, jak pokazano na poniższej ilustracji:

    ![Utwórz zasób publicznego adresu IP](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Wykonaj kroki w sekcji, które należy wykonać, aby skojarzyć zasób publicznego adresu IP do konfiguracji adresu IP.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Skojarz zasób publicznego adresu IP do nowej konfiguracji adresu IP

1. Wykonaj kroki [podstawowe kroki](#coreadd) dalszej części tego artykułu.
2. Kliknij pozycję **Add** (Dodaj). W **konfiguracji Dodaj adres IP** okienko zostanie wyświetlone, Utwórz konfigurację adresu IP o nazwie *IPConfig 4*. Włącz **publiczny adres IP** i wybierz istniejący, dostępne zasób publicznego adresu IP z **wybierz publiczny adres IP** wyświetlonym okienku.

    Po wybraniu zasobu publicznego adresu IP, kliknij przycisk **OK** i zamyka okienka. Jeśli nie masz istniejącego publicznego adresu IP, możesz utworzyć jeden, wykonując kroki opisane w [Utwórz zasób publicznego adresu IP](#create-public-ip) dalszej części tego artykułu. 

3. Przejrzyj nową konfigurację adresu IP. Nawet jeśli jawnie nie został przypisany prywatny adres IP, jedną automatycznie przypisano z konfiguracją adresów IP, ponieważ wszystkie konfiguracje adresów IP musi mieć prywatny adres IP.
4. Możesz kliknąć pozycję **Dodaj** Aby dodać dodatkowe konfiguracje adresów IP lub zamknąć wszystkie otwarte bloki, aby zakończyć dodawanie adresów IP.
5. Dodaj jako prywatny adres IP do systemu operacyjnego maszyny Wirtualnej, wykonując kroki dla systemu operacyjnego w [adresy IP Dodaj do systemu operacyjnego maszyny Wirtualnej](#os-config) dalszej części tego artykułu. Nie należy dodawać publicznego adresu IP do systemu operacyjnego.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Skojarz zasób publicznego adresu IP do istniejącej konfiguracji adresu IP

1. Wykonaj kroki [podstawowe kroki](#coreadd) dalszej części tego artykułu.
2. Kliknij pozycję Konfiguracja protokołu IP, które chcesz dodać zasób publicznego adresu IP do.
3. W wyświetlonym okienku IPConfig kliknij **adresu IP**.
4. W **wybierz publiczny adres IP** okienko zostanie wyświetlone, wybierz publiczny adres IP.
5. Kliknij przycisk **Zapisz** i zamknij okienka. Jeśli nie masz istniejącego publicznego adresu IP, możesz utworzyć jeden, wykonując kroki opisane w [Utwórz zasób publicznego adresu IP](#create-public-ip) dalszej części tego artykułu.
3. Przejrzyj nową konfigurację adresu IP.
4. Możesz kliknąć pozycję **Dodaj** Aby dodać dodatkowe konfiguracje adresów IP lub zamknąć wszystkie otwarte bloki, aby zakończyć dodawanie adresów IP. Nie należy dodawać publicznego adresu IP do systemu operacyjnego.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
