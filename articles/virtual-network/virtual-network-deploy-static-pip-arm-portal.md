---
title: Tworzenie maszyny Wirtualnej ze statycznym publicznym adresem IP — witryna Azure portal | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć maszynę wirtualną ze statycznym publicznym adresem IP przy użyciu witryny Azure portal.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 66050c16f40e0a06117327ef53e3aae87d03c5db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76043546"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Tworzenie maszyny wirtualnej ze statycznym publicznym adresem IP przy użyciu witryny Azure portal

Można utworzyć maszynę wirtualną ze statycznym publicznym adresem IP. Publiczny adres IP umożliwia komunikację z maszyną wirtualną z Internetu. Przypisz statyczny publiczny adres IP, a nie adres dynamiczny, aby upewnić się, że adres nigdy się nie zmienia. Dowiedz się więcej o [statycznych publicznych adresach IP](virtual-network-ip-addresses-overview-arm.md#allocation-method). Aby zmienić publiczny adres IP przypisany do istniejącej maszyny wirtualnej z dynamicznego na statyczny lub pracować z prywatnymi adresami IP, zobacz [Dodawanie, zmienianie lub usuwanie adresów IP](virtual-network-network-interface-addresses.md). Publiczne adresy IP mają [opłatę nominalną](https://azure.microsoft.com/pricing/details/ip-addresses)i istnieje [limit](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) liczby publicznych adresów IP, których można używać na subskrypcję.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.
2. Wybierz **pozycję Oblicz**, a następnie wybierz pozycję Windows Server **2016 VM**lub inny wybrany system operacyjny.
3. Wprowadź lub wybierz następujące informacje, zaakceptuj ustawienia domyślne dla pozostałych ustawień, a następnie wybierz **przycisk OK:**

    |Ustawienie|Wartość|
    |---|---|
    |Nazwa|myVM ( myVM )|
    |Nazwa użytkownika| Wprowadź wybraną nazwę użytkownika.|
    |Hasło| Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Subskrypcja| Wybierz subskrypcję.|
    |Grupa zasobów| Wybierz pozycję **Użyj istniejącej** i wybierz grupę **myResourceGroup**.|
    |Lokalizacja| Wybierz **wschodnie stany USA**|

4. Wybierz rozmiar maszyny wirtualnej, a następnie wybierz pozycję **Wybierz**.
5. W obszarze **Ustawienia**wybierz **publiczny adres IP**.
6. Wprowadź *myPublicIpAddress*, wybierz **pozycję Statyczny**, a następnie wybierz **OK**, jak pokazano na poniższym rysunku:

   ![Wybierz statyczne](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Jeśli publiczny adres IP musi być standardową jednostką SKU, wybierz opcję **Standardowa** w obszarze **Jednostka SKU**. Dowiedz się więcej o [publicznych jednostkach SKU adresów IP](virtual-network-ip-addresses-overview-arm.md#sku). Jeśli maszyna wirtualna zostanie dodana do puli zaplecza publicznego modułu równoważenia obciążenia platformy Azure, jednostka SKU publicznego adresu IP maszyny wirtualnej musi być zgodna z jednostką SKU publicznego adresu IP modułu równoważenia obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Azure Load Balancer](../load-balancer/concepts-limitations.md#skus).

6. Wybierz port lub nie ma portów w obszarze **Wybierz publiczne porty przychodzące**. Wybrano portal 3389, aby umożliwić zdalny dostęp do maszyny wirtualnej systemu Windows Server z Internetu. Otwarcie portu 3389 z Internetu nie jest zalecane dla obciążeń produkcyjnych.

   ![Wybieranie portu](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Zaakceptuj pozostałe ustawienia domyślne i wybierz **przycisk OK**.
8. Na stronie **Podsumowanie** wybierz pozycję **Utwórz**. Wdrożenie maszyny wirtualnej zajmuje kilka minut.
9. Po wdrożeniu maszyny wirtualnej wprowadź *adres myPublicIpAddress* w polu wyszukiwania u góry portalu. Gdy w wynikach wyszukiwania pojawi się **myPublicIpAddress,** wybierz go.
10. Można wyświetlić publiczny adres IP, który jest przypisany, i że adres jest przypisany do maszyny wirtualnej **myVM,** jak pokazano na poniższej ilustracji:

    ![Wyświetlanie publicznego adresu IP](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Platforma Azure przypisała publiczny adres IP z adresów używanych w regionie, w którego utworzono maszynę wirtualną. Możesz pobrać listę zakresów (prefiksów) dla następujących chmur platformy Azure: [Publiczna](https://www.microsoft.com/download/details.aspx?id=56519), [Instytucje rządowe USA](https://www.microsoft.com/download/details.aspx?id=57063), [Chiny](https://www.microsoft.com/download/details.aspx?id=57062) i [Niemcy](https://www.microsoft.com/download/details.aspx?id=57064).

11. Wybierz **opcję Konfiguracja,** aby potwierdzić, że przypisanie jest **statyczne**.

    ![Wyświetlanie publicznego adresu IP](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> Nie należy modyfikować ustawień adresu IP w systemie operacyjnym maszyny wirtualnej. System operacyjny nie jest świadomy publicznych adresów IP platformy Azure. Chociaż można dodać ustawienia prywatnego adresu IP do systemu operacyjnego, zalecamy, aby nie robić tego, chyba że jest to konieczne, a dopiero po [przeczytaniu Dodaj prywatny adres IP do systemu operacyjnego](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów i wszystkie znajdujące się w niej zasoby nie będą już potrzebne, usuń je:

1. Wprowadź ciąg *myResourceGroup* w polu **Szukaj** w górnej części portalu. Gdy pozycja **myResourceGroup** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. Wprowadź *myResourceGroup* dla **TYPU NAZWA GRUPY ZASOBÓW:** i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [publicznych adresach IP na](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) platformie Azure
- Dowiedz się więcej o wszystkich [ustawieniach publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Dowiedz się więcej o [prywatnych adresach IP](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) i przypisywaniu [statycznego prywatnego adresu IP](virtual-network-network-interface-addresses.md#add-ip-addresses) do maszyny wirtualnej platformy Azure
- Dowiedz się więcej o tworzeniu maszyn wirtualnych [z systemem Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)