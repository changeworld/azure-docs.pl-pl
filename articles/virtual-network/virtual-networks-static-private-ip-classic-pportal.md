---
title: Konfigurowanie prywatnych adresów IP dla maszyny wirtualne (klasyczne) — witryna Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować prywatnych adresów IP dla maszyny wirtualne (klasyczne) w witrynie Azure portal.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 72d1c4d2ea3adf7d8751adfbb013435f8f2530f0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62125750"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Skonfiguruj prywatne adresy IP dla maszyny wirtualnej (model klasyczny) przy użyciu witryny Azure portal

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano klasyczny model wdrażania. Możesz również [Zarządzanie statyczny prywatny adres IP w modelu wdrażania usługi Resource Manager](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Przykładowe opisanych poniżej oczekiwać proste środowisko już utworzony. Jeśli chcesz uruchomić kroki, jak są one wyświetlane w tym dokumencie, najpierw utworzyć środowisko testowe opisane w [tworzenie sieci wirtualnej](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Jak określić statyczny prywatny adres IP podczas tworzenia maszyny Wirtualnej
Aby utworzyć Maszynę wirtualną o nazwie *DNS01* w *frontonu* podsieci sieci wirtualnej o nazwie *TestVNet* za pomocą statycznego prywatnego adresu IP z *192.168.1.101*pełne następujące kroki:

1. W przeglądarce przejdź do witryny https://portal.azure.com i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure.
2. Wybierz **NEW** > **obliczenia** > **systemu Windows Server 2012 R2 Datacenter**, zwróć uwagę, że **wybierz model wdrożenia** już liście widać **klasycznego**, a następnie wybierz pozycję **Utwórz**.
   
    ![Tworzenie maszyny Wirtualnej w witrynie Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. W obszarze **Utwórz maszynę Wirtualną**, wprowadź nazwę maszyny Wirtualnej, która ma zostać utworzony (*DNS01* w scenariuszu), konto administratora lokalnego i hasło.
   
    ![Tworzenie maszyny Wirtualnej w witrynie Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Wybierz **opcjonalna konfiguracja** > **sieci** > **sieci wirtualnej**, a następnie wybierz pozycję **TestVNet** . Jeśli **TestVNet** jest niedostępny, upewnij się, że używasz *środkowe stany USA* lokalizacji i utworzyli środowisko testowe opisane na początku tego artykułu.
   
    ![Tworzenie maszyny Wirtualnej w witrynie Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. W obszarze **sieci**, upewnij się, podsieć aktualnie wybrany jest *frontonu*, a następnie wybierz **adresów IP**w obszarze **przypisywania adresów IP** Wybierz **statyczne**, a następnie wprowadź *192.168.1.101* dla **adresu IP** jak pokazano poniżej.
   
    ![Tworzenie maszyny Wirtualnej w witrynie Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Wybierz **OK** w obszarze **adresów IP**, wybierz opcję **OK** w obszarze **sieci**, a następnie wybierz pozycję **OK** w obszarze **Konfiguracja opcjonalna**.
7. W obszarze **Utwórz maszynę Wirtualną**, wybierz opcję **Utwórz**. Zwróć uwagę, poniżej Kafelek wyświetlany na pulpicie nawigacyjnym:
   
    ![Tworzenie maszyny Wirtualnej w witrynie Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak pobrać statyczne prywatne informacje o adresie IP dla maszyny Wirtualnej
Aby wyświetlić statyczne prywatne informacje o adresie IP dla maszyny Wirtualnej utworzone za pomocą powyższych kroków, należy wykonać poniższe kroki.

1. W witrynie Azure portal, wybierz **PRZEGLĄDAJ wszystkie** > **maszyny wirtualne (klasyczne)**  > **DNS01** > **wszystkie ustawienia** > **adresów IP** i zwróć uwagę przypisywania adresów IP i adres IP, jak pokazano poniżej.
   
    ![Tworzenie maszyny Wirtualnej w witrynie Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Jak usunąć statyczny prywatny adres IP z maszyny Wirtualnej

W obszarze **adresów IP**, wybierz opcję **dynamiczne** po prawej stronie **przypisywania adresów IP**, wybierz opcję **Zapisz**, a następnie wybierz  **Tak**, jak pokazano na poniższej ilustracji:
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Jak dodać statyczny prywatny adres IP do istniejącej maszyny Wirtualnej

1. W obszarze **adresów IP**, jak pokazano wcześniej, wybierz opcję **statyczne** po prawej stronie **przypisywania adresów IP**.
2. Typ *192.168.1.101* dla **adresu IP**, wybierz opcję **Zapisz**, a następnie wybierz pozycję **tak**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Ustawić adresy IP w ramach systemu operacyjnego

Zalecane jest, że nie zostanie statycznie przypisany prywatny adres IP, przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej, o ile to konieczne. Jeśli ręcznie ustawić jako prywatny adres IP w ramach systemu operacyjnego, upewnij się, że jest ten sam adres jako prywatny adres IP przypisany do maszyny Wirtualnej platformy Azure, lub można utracić łączność z maszyną wirtualną. Należy nigdy ręcznie przypisać publiczny adres IP przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [publiczny adres IP zarezerwowany](virtual-networks-reserved-public-ip.md) adresów.
* Dowiedz się więcej o [na poziomie wystąpienia publicznego adresu IP (ILPIP)](virtual-networks-instance-level-public-ip.md) adresów.
* Zapoznaj się z [zarezerwowanych adresów IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx).

