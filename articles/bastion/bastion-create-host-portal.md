---
title: 'Tworzenie hosta Bastion platformy Azure: portal'
description: W tym artykule dowiesz się, jak utworzyć hosta Bastionu Platformy Azure przy użyciu portalu
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 14a596d78fb1f560c62013e7e439ed60d3a29b8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366147"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>Tworzenie hosta Bastionu platformy Azure przy użyciu portalu

W tym artykule pokazano, jak utworzyć hosta bastionu platformy Azure przy użyciu witryny Azure portal. Po aprowizyjce usługi Azure Bastion w sieci wirtualnej bezproblemowe środowisko RDP/SSH jest dostępne dla wszystkich maszyn wirtualnych w tej samej sieci wirtualnej. Wdrożenie usługi Azure Bastion jest na sieć wirtualną, a nie na subskrypcję/konto lub maszynę wirtualną.

Można utworzyć nowy zasób hosta bastionu w portalu, określając wszystkie ustawienia ręcznie lub za pomocą ustawień, które odpowiadają istniejącej maszynie wirtualnej. Aby utworzyć host bastionu przy użyciu ustawień maszyny Wirtualnej, zobacz artykuł [szybki start.](quickstart-host-portal.md) Opcjonalnie można użyć [programu Azure PowerShell](bastion-create-host-powershell.md) do utworzenia hosta bastionu platformy Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Bastion jest dostępny w następujących regionach publicznych platformy Azure:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Tworzenie hosta bastionu

Ta sekcja ułatwia tworzenie nowego zasobu Bastion platformy Azure z witryny Azure Portal.

1. W menu [portalu azure](https://portal.azure.com) lub na stronie **głównej** wybierz pozycję **Utwórz zasób**.

1. Na stronie **Nowy** w polu *Wyszukaj w portalu Marketplace* wpisz polecenie **Bastion**, a następnie kliknij przycisk **Enter,** aby przejść do wyników wyszukiwania.

1. W wynikach kliknij pozycję **Bastion**. Upewnij się, że wydawcą jest *firma Microsoft,* a kategorią jest *Sieć*.

1. Na stronie **Bastion** kliknij pozycję **Utwórz,** aby otworzyć stronę **Utwórz bastion.**

1. Na stronie **Tworzenie bastionu** skonfiguruj nowy zasób Bastion. Określ ustawienia konfiguracji zasobu Bastion.

    ![tworzenie bastionu](./media/bastion-create-host-portal/settings.png)

    * **Subskrypcja:** subskrypcja platformy Azure, której chcesz użyć do utworzenia nowego zasobu Bastion.
    * **Grupa zasobów:** Grupa zasobów platformy Azure, w której zostanie utworzony nowy zasób Bastion. Jeśli nie masz istniejącej grupy zasobów, możesz utworzyć nową.
    * **Nazwa**: Nazwa nowego zasobu Bastion
    * **Region:** Region publiczny platformy Azure, w których zostanie utworzony zasób.
    * **Sieć wirtualna:** sieć wirtualna, w której zostanie utworzony zasób Bastion. Można utworzyć nową sieć wirtualną w portalu podczas tego procesu lub użyć istniejącej sieci wirtualnej. Jeśli używasz istniejącej sieci wirtualnej, upewnij się, że istniejąca sieć wirtualna ma wystarczającą ilość wolnego miejsca adresowego, aby spełnić wymagania podsieci Bastion.
    * **Podsieć:** podsieć w sieci wirtualnej, do której zostanie wdrożony nowy zasób hosta bastionu. Należy utworzyć podsieć przy użyciu wartości nazwy **AzureBastionSubnet**. Ta wartość umożliwia platformie Azure wiedzieć, które podsieci wdrożyć zasoby bastionu. Jest to inna niż podsieć bramy. Należy użyć podsieci co najmniej /27 lub większej (/27, /26 i tak dalej).
    
       Utwórz **usługę AzureBastionSubnet** bez żadnych tabel tras lub delegacji. Jeśli używasz sieciowych grup zabezpieczeń w **usłudze AzureBastionSubnet,** zapoznaj się z artykułem [Praca z sieciami roboczymi.](bastion-nsg.md)
    * **Publiczny adres IP:** Publiczny adres IP zasobu Bastion, na którym będzie dostępny protokół RDP/SSH (za portem 443). Utwórz nowy publiczny adres IP lub użyj istniejącego. Publiczny adres IP musi znajdować się w tym samym regionie co zasób Bastion, który tworzysz.
    * **Nazwa publicznego adresu IP**: Nazwa zasobu publicznego adresu IP.
    * **Publiczna jednostka SKU adresu IP:** To ustawienie jest wstępnie wypełniane domyślnie na **standard**. Usługa Azure Bastion używa/obsługuje tylko standardową publiczną jednostkę SKU IP.
    * **Przypisanie:** To ustawienie jest wstępnie wypełniane jako **statyczne**.

1. Po zakończeniu określania ustawień kliknij przycisk **Recenzja + Utwórz**. To sprawdza poprawność wartości. Po przejściu weryfikacji można rozpocząć proces tworzenia.
1. Na stronie **Tworzenie bastionu** kliknij pozycję **Utwórz**.
1. Zostanie wyświetlony komunikat informujący, że wdrożenie jest w toku. Stan będzie wyświetlany na tej stronie podczas tworzenia zasobów. Trwa około 5 minut dla zasobu Bastion do utworzenia i wdrożenia.

## <a name="next-steps"></a>Następne kroki

* Przeczytaj często zadawane pytania dotyczące [bastionu, aby](bastion-faq.md) uzyskać dodatkowe informacje.

* Aby używać grup zabezpieczeń sieciowych w podsieci Bastion platformy Azure, zobacz [Praca z sieciami roboczymi](bastion-nsg.md).