---
title: Tworzenie klastra usługi Azure Data Explorer & bazy danych w sieci wirtualnej
description: W tym artykule dowiesz się, jak utworzyć klaster usługi Azure Data Explorer w sieci wirtualnej.
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: 4dff471fa0f2194756409e01512ed223a1d46024
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241441"
---
# <a name="create-an-azure-data-explorer-cluster-in-your-virtual-network"></a>Tworzenie klastra usługi Azure Data Explorer w sieci wirtualnej

Usługa Azure Data Explorer obsługuje wdrażanie klastra w podsieci w sieci wirtualnej(VNet). Ta funkcja umożliwia dostęp do klastra prywatnie z sieci wirtualnej platformy Azure lub lokalnie, dostęp do zasobu, takich jak Centrum zdarzeń i magazynu wewnątrz sieci wirtualnej i ograniczyć ruch przychodzący i wychodzący.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Zaloguj się do [Portalu Azure](https://portal.azure.com/).

## <a name="create-network-security-group-nsg"></a>Tworzenie sieciowej grupy zabezpieczeń

[Sieciowe grupy zabezpieczeń umożliwiają](/azure/virtual-network/security-overview) kontrolowanie dostępu do sieci wirtualnej. Dostęp do usługi Azure Data Explorer można uzyskać przy użyciu dwóch punktów końcowych HTTPs (443) i TDS (1433). Następujące reguły sieciowej grupy danych sieciowych muszą być skonfigurowane tak, aby umożliwić dostęp do tych punktów końcowych do zarządzania, monitorowania i prawidłowego działania klastra.

Aby utworzyć grupę zabezpieczeń sieciowych:

1. Wybierz przycisk **+ Utwórz zasób** w lewym górnym rogu portalu.
1. Wyszukaj *sieć grupy zabezpieczeń*.
1. W obszarze **Grupa zabezpieczeń sieciowych**u dołu ekranu wybierz pozycję **Utwórz**.
1. W oknie **Tworzenie sieciowej grupy zabezpieczeń** wypełnij następujące informacje.

   ![Tworzenie formularza NSG](media/vnet-create-cluster-portal/network-security-group.png)

    **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Subskrypcja | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, która ma być używana dla klastra.|
    | Grupa zasobów | Twoja grupa zasobów | Użyj istniejącej grupy zasobów lub utwórz nową. |
    | Nazwa | Usługa AzureDataExplorerNsg | Wybierz nazwę identyfikującą sieciową grupę zabezpieczeń (NSG) w grupie zasobów.  |
    | Region | *Zachodnie stany USA* | Wybierz region, który najlepiej odpowiada Twoim potrzebom.
    | | | |

1. Wybierz pozycję **Przeglądanie + tworzenie**, aby przejrzeć szczegóły swojego klastra, a następnie wybierz pozycję **Utwórz**, aby aprowizować klaster.

1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.

    ![Przechodzenie do zasobu](media/vnet-create-cluster-portal/notification-nsg.png)

1. Na karcie **Przychodzące reguły zabezpieczeń** wybierz pozycję **+Dodaj**.
1. W oknie **Dodaj regułę zabezpieczeń przychodzących** wypełnij następujące informacje.

    ![Tworzenie formularza reguły przychodzącej nsg](media/vnet-create-cluster-portal/nsg-inbound-rule.png)

    **Ustawienie** | **Sugerowana wartość** 
    |---|---|
    | Element źródłowy | Tag usługi
    | Tag usługi źródłowej | AzureDataExplorerManagement (AzureDataExplorerManagement)
    | Zakresy portów źródłowych | *
    | Element docelowy | VirtualNetwork
    | Zakresy portów docelowych | *
    | Protocol (Protokół) | TCP
    | Akcja | Zezwalaj
    | Priorytet | 100
    | Nazwa | AllowAzureDataExplorerManagement (Zezwalaj na dostęp do danych)
    | | |
    
1. Powtórz poprzednie dwa kroki dla wszystkich zależności przychodzących i wychodzących zgodnie z [zależnościami dla wdrożenia sieci wirtualnej](/azure/data-explorer/vnet-deloyment#dependencies-for-vnet-deployment). Alternatywnie reguły ruchu wychodzącego można zastąpić pojedynczą regułą, aby umożliwić *dostęp do Internetu* dla portów 443 i 80.
    
    Reguły sieciowej sieciowej sieciowej dla zależności przychodzących i wychodzących powinny wyglądać następująco:

    ![Reguły NSG](media/vnet-create-cluster-portal/nsg-rules.png)

## <a name="create-public-ip-addresses"></a>Tworzenie publicznych adresów IP

Aby utworzyć publiczne adresy IP kwerendy (Engine):

1. Wybierz przycisk **+ Utwórz zasób** w lewym górnym rogu portalu.
1. Wyszukaj *sieć grupy zabezpieczeń*.
1. W obszarze **Publiczny adres IP**u dołu ekranu wybierz pozycję **Utwórz**.
1. W okienku **Tworzenie publicznego adresu IP** wykonaj następujące informacje.
   
   ![Tworzenie publicznego formularza IP](media/vnet-create-cluster-portal/public-ip-blade.png)

    **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Wersja IP | Protokół IPv4 | Wybierz wersję IP. Obsługujemy tylko IPv4.|
    | SKU | Standardowa | Wymagamy **standardu** dla punktu końcowego identyfikatora URI kwerendy (aparat). |
    | Nazwa | silnik-pip | Wybierz nazwę identyfikującą publiczny adres IP w grupie zasobów.
    | Subskrypcja | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, której chcesz użyć dla publicznego adresu IP.|
    | Grupa zasobów | Twoja grupa zasobów | Użyj istniejącej grupy zasobów lub utwórz nową. |
    | Lokalizacja | *Zachodnie stany USA* | Wybierz region, który najlepiej odpowiada Twoim potrzebom.
    | | | |

1. Wybierz **pozycję Utwórz,** aby utworzyć publiczny adres IP.

1. Aby utworzyć publiczny adres IP pozyskiwania (Zarządzanie danymi) należy wykonać te same instrukcje i wybrać 
    * **Sku**: Podstawowe
    * **Przypisanie adresu IP**: Statyczne

## <a name="create-virtual-network-and-subnet"></a>Tworzenie sieci wirtualnej i podsieci

Aby utworzyć sieć wirtualną i podsieć:

1. Wybierz przycisk **+ Utwórz zasób** w lewym górnym rogu portalu.
1. Wyszukaj *sieć wirtualną*.
1. W **obszarze Sieć wirtualna**u dołu ekranu wybierz pozycję **Utwórz**.
1. W oknie **Utwórz sieć wirtualną** wykonaj następujące informacje.

   ![Tworzenie formularza sieci wirtualnej](media/vnet-create-cluster-portal/vnet-blade.png)

    **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Subskrypcja | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, która ma być używana dla klastra.|
    | Grupa zasobów | Twoja grupa zasobów | Użyj istniejącej grupy zasobów lub utwórz nową. |
    | Nazwa | Usługa AzureDataExplorerVnet | Wybierz nazwę identyfikującą sieć wirtualną w grupie zasobów.
    | Region | *Zachodnie stany USA* | Wybierz region, który najlepiej odpowiada Twoim potrzebom.
    | | | |

    > [!NOTE]
    > W przypadku obciążeń produkcyjnych zaplanuj rozmiar podsieci zgodnie z [rozmiarem podsieci planu w sieci wirtualnej](/azure/data-explorer/vnet-deloyment#plan-subnet-size-in-your-vnet)

1. Wybierz pozycję **Przeglądanie + tworzenie**, aby przejrzeć szczegóły swojego klastra, a następnie wybierz pozycję **Utwórz**, aby aprowizować klaster.

1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.
1. Przejdź do **bloku Podsieci** i wybierz **domyślną** podsieć.
    
    ![Blok podsieci](media/vnet-create-cluster-portal/subnets.png)

1. W **domyślnym** oknie podsieci:
    1. Wybierz **grupę zabezpieczeń sieciowych** z menu rozwijanego. 
    1. Wybierz nazwę sieciowej grupy zabezpieczeń, w tym przypadku **azuredataExplorerNsg**. 
    1. **Zapisz**

    ![Konfigurowanie podsieci](media/vnet-create-cluster-portal/subnet-nsg.png)

## <a name="create-a-cluster"></a>Tworzenie klastra

Utwórz klaster usługi Azure Data Explorer ze zdefiniowanym zestawem zasobów obliczeniowych i magazynowych w grupie zasobów platformy Azure, zgodnie z [opisem](create-cluster-database-portal.md#create-a-cluster)w programie Create a cluster .

1. Przed sfinalizowaniem tworzenia klastra w oknie **Tworzenie eksploratora danych platformy Azure** wybierz kartę **Sieć,** aby podać szczegóły sieci wirtualnej przy użyciu zasobów utworzonych na poprzednich kartach:

   ![Tworzenie formularza sieci wirtualnej klastra](media/vnet-create-cluster-portal/create-cluster-form-vnet.png)

    **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Subskrypcja | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, której chcesz użyć do zasobów sieciowych.|
    | Virtual Network | Usługa AzureDataExplorerVnet | Wybierz sieć wirtualną utworzoną w poprzednich krokach.
    | Podsieć | default | Wybierz podsieć utworzoną w poprzednich krokach.
    | Zapytanie publiczny adres IP | silnik-pip | Wybierz publiczny adres IP kwerendy utworzony w poprzednich krokach.
    | Publiczny adres IP pozyskiwania danych | dm-pip | Wybierz publiczny adres IP pozyskiwania utworzony w poprzednich krokach.
    | | | |

1. Wybierz **pozycję Recenzja + utwórz,** aby utworzyć klaster.
1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.

Aby wdrożyć klaster Usługi Azure Data Explorer w sieci wirtualnej, użyj [klastra Wdrażanie usługi Azure Data Explorer w szablonie](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) usługi Azure Resource Manager sieci wirtualnej.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wdrażanie Eksploratora danych platformy Azure w sieci wirtualnej](vnet-create-cluster-portal.md)