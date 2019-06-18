---
title: Tworzenie maszyny Wirtualnej za pomocą statyczny publiczny adres IP — witryna Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć maszyny Wirtualnej ze statycznym publicznym adresem IP przy użyciu witryny Azure portal.
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
ms.openlocfilehash: f6914a9894db07a40b372a8c247a7623c3957d86
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64692422"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Tworzenie maszyny wirtualnej ze statycznym publicznym adresem IP przy użyciu witryny Azure portal

Można utworzyć maszyny wirtualnej ze statycznym publicznym adresem IP. Publiczny adres IP umożliwia komunikację z maszyną wirtualną z Internetu. Przypisz statyczny publiczny adres IP, a nie adres dynamiczny, aby upewnić się, że adres nigdy nie zmienia się. Dowiedz się więcej o [statyczne publiczne adresy IP](virtual-network-ip-addresses-overview-arm.md#allocation-method). Aby zmienić publiczny adres IP przypisane do istniejącej maszyny wirtualnej z dynamicznego na statyczne lub pracować w prywatnych adresów IP, zobacz [Dodawanie, zmienianie lub usuwanie adresów IP](virtual-network-network-interface-addresses.md). Publiczne adresy IP zostały [za symboliczną cenę](https://azure.microsoft.com/pricing/details/ip-addresses)i ma [limit](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) do liczby publicznych adresów IP używanych na subskrypcję.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.
2. Wybierz **obliczenia**, a następnie wybierz pozycję **maszyny Wirtualnej systemu Windows Server 2016**, lub innego systemu operacyjnego wybrane.
3. Wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myVM|
    |Nazwa użytkownika| Wprowadź wybraną nazwę użytkownika.|
    |Hasło| Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Subskrypcja| Wybierz subskrypcję.|
    |Grupa zasobów| Wybierz pozycję **Użyj istniejącej** i wybierz grupę **myResourceGroup**.|
    |Lokalizacja| Wybierz pozycję **Wschodnie stany USA**|

4. Wybierz rozmiar maszyny wirtualnej, a następnie wybierz pozycję **Wybierz**.
5. W obszarze **ustawienia**, wybierz opcję **publiczny adres IP**.
6. Wprowadź *myPublicIpAddress*, wybierz opcję **statyczne**, a następnie wybierz pozycję **OK**, jak pokazano na poniższej ilustracji:

   ![Wybierz statyczny](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Jeśli publiczny adres IP musi być standardowej jednostki SKU, wybierz **standardowa** w obszarze **jednostki SKU**. Dowiedz się więcej o [publicznego adresu IP jednostki SKU](virtual-network-ip-addresses-overview-arm.md#sku). Jeśli maszyna wirtualna zostanie dodany do puli zaplecza publicznej usługi Azure Load Balancer, jednostki SKU publicznego adresu IP maszyny wirtualnej musi być zgodna z jednostką SKU publicznego adresu IP modułu równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [usługi Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus).

6. Wybierz port lub porty w podfolderze **Dodaj publiczne porty wejściowe**. Portal 3389 jest zaznaczona, aby włączyć dostęp zdalny do maszyny wirtualnej systemu Windows Server z Internetu. Otwieranie portu 3389 z Internetu nie jest zalecane w przypadku obciążeń produkcyjnych.

   ![Wybierz port](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Zaakceptuj pozostałe domyślne ustawienia i wybierz **OK**.
8. Na stronie **Podsumowanie** wybierz pozycję **Utwórz**. Maszyny wirtualnej zajmuje kilka minut, aby wdrożyć.
9. Po wdrożeniu maszyny wirtualnej, wprowadź *myPublicIpAddress* w polu wyszukiwania w górnej części portalu. Gdy **myPublicIpAddress** pojawi się w wynikach wyszukiwania, wybierz ją.
10. Można wyświetlić publiczny adres IP, która jest przypisana, a adres przypisany do **myVM** maszyny wirtualnej, jak pokazano na poniższej ilustracji:

    ![Wyświetl publiczny adres IP](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Azure przypisany publiczny adres IP z adresów używanych w danym regionie utworzono maszynę wirtualną w. Możesz pobrać listę zakresów (prefiksów) dla następujących chmur platformy Azure: [Publiczna](https://www.microsoft.com/download/details.aspx?id=56519), [Instytucje rządowe USA](https://www.microsoft.com/download/details.aspx?id=57063), [Chiny](https://www.microsoft.com/download/details.aspx?id=57062) i [Niemcy](https://www.microsoft.com/download/details.aspx?id=57064).

11. Wybierz **konfiguracji** aby upewnić się, że przydział jest **statyczne**.

    ![Wyświetl publiczny adres IP](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> Nie należy modyfikować ustawienia adresu IP w ramach systemu operacyjnego maszyny wirtualnej. System operacyjny jest niezależnych od platformy Azure publicznych adresów IP. Chociaż można było dodać prywatnej ustawienia adresu IP do systemu operacyjnego, zaleca się nie ten sposób, chyba że to konieczne, a nie dopiero po odczytu [Dodaj prywatny adres IP do systemu operacyjnego](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów i wszystkie znajdujące się w niej zasoby nie będą już potrzebne, usuń je:

1. Wprowadź ciąg *myResourceGroup* w polu **Szukaj** w górnej części portalu. Gdy pozycja **myResourceGroup** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wprowadź nazwę *myResourceGroup*, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [publiczne adresy IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) na platformie Azure
- Dowiedz się więcej o wszystkich [ustawienia publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Dowiedz się więcej o [prywatnych adresów IP](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) i przypisywanie [statyczny prywatny adres IP](virtual-network-network-interface-addresses.md#add-ip-addresses) na maszynie wirtualnej platformy Azure
- Dowiedz się więcej na temat tworzenia [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) maszyn wirtualnych