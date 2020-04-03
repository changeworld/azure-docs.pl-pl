---
title: 'Szybki start: łączenie się z maszyną wirtualną przy użyciu prywatnego adresu IP: Bastion platformy Azure'
description: W tym artykule dowiesz się, jak utworzyć hosta bastionu platformy Azure z maszyny wirtualnej i bezpiecznie połączyć się przy użyciu prywatnego adresu IP.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: charwen
ms.openlocfilehash: a420a3253040fff8b767a81f298ede283c1d214b
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619250"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Szybki start: łączenie się z maszyną wirtualną przy użyciu prywatnego adresu IP i bastionu platformy Azure

W tym artykule przewodnika Szybki start pokazano, jak połączyć się z maszyną wirtualną przy użyciu prywatnego adresu IP. Po nawiązaniu połączenia za pośrednictwem bastionu maszyny wirtualne nie potrzebują publicznego adresu IP. Kroki opisane w tym artykule pomagają wdrożyć bastion w sieci wirtualnej za pośrednictwem maszyny wirtualnej w portalu. Po zainicjowaniu obsługi administracyjnej usługi środowisko RDP/SSH jest dostępne dla wszystkich maszyn wirtualnych w tej samej sieci wirtualnej.

## <a name="prerequisites"></a><a name="prereq"></a>Wymagania wstępne

* Sieć wirtualna platformy Azure.
* Maszyna wirtualna platformy Azure znajdująca się w sieci wirtualnej z otwartym portem 3389.

### <a name="example-values"></a>Przykładowe wartości

|**Nazwa** | **Wartość** |
| --- | --- |
| Nazwa |  W sieci wirtualnej1Bastion |
| Region | eastus |
| Sieć wirtualna |  Sieć wirtualna 1 |
| + Nazwa podsieci | Usługa AzureBastionSubnet |
| Adresy usługi AzureBastionSubnet |  10.1.254.0/27 |
| Publiczny adres IP |  Tworzenie nowego elementu |
| Nazwa publicznego adresu IP | VNet1BastionPIP  |
| Publiczna jednostka SKU adresu IP |  Standardowa  |
| Przypisanie  | Statyczny |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Tworzenie hosta bastionu

Podczas tworzenia hosta bastionu w portalu przy użyciu istniejącej maszyny wirtualnej, różne ustawienia będą automatycznie domyślnie odpowiadać maszynie wirtualnej i/lub sieci wirtualnej.

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). Przejdź do maszyny wirtualnej, a następnie kliknij przycisk **Połącz**.

   ![ustawienia maszyny wirtualnej](./media/quickstart-host-portal/vm-settings.png)
1. Z listy rozwijanej wybierz **bastion**.
1. Na stronie Łączenie wybierz pozycję **Użyj bastionu**.

   ![wybierz Bastion](./media/quickstart-host-portal/select-bastion.png)

1. Na stronie Bastion wypełnij następujące pola ustawień:

   * **Nazwa**: Nazwa hosta bastionu
   * **Podsieć:** Podsieć w sieci wirtualnej, do której zostanie wdrożony zasób Bastion. Podsieć musi zostać utworzona pod nazwą **AzureBastionSubnet**. Nazwa informuje platformę Azure, do której podsieci należy wdrożyć zasób Bastion. Jest to inna niż podsieć bramy. Użyj podsieci co najmniej /27 lub większej (/27, /26, /25 itd.).
   
      * Wybierz **pozycję Zarządzaj konfiguracją podsieci**, a następnie wybierz pozycję **+ Podsieć**.
      * Na stronie Dodaj podsieci wpisz **AzureBastionSubnet**.
      * Określ zakres adresów w notacji CIDR. Na przykład 10.1.254.0/27.
      * Wybierz **przycisk OK,** aby utworzyć podsieć. W górnej części strony przejdź z powrotem do Bastionu, aby ukończyć pozostałe ustawienia.

         ![przechodzenie do ustawień bastionu](./media/quickstart-host-portal/navigate-bastion.png)
   * **Publiczny adres IP:** Jest to publiczny adres IP zasobu Bastion, na którym będzie dostępny protokół RDP/SSH (za portem 443). Utwórz nowy publiczny adres IP lub użyj istniejącego. Publiczny adres IP musi znajdować się w tym samym regionie co zasób Bastion, który tworzysz.
   * **Nazwa publicznego adresu IP**: Nazwa zasobu publicznego adresu IP.
1. Na ekranie sprawdzania poprawności kliknij pozycję **Utwórz**. Poczekaj około 5 minut na utworzenie i wdrożenie zasobu Bastion.

   ![tworzenie hosta bastionu](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>Połącz

Po wdrożeniu bastionu w sieci wirtualnej ekran zmieni się na stronę połączenia.

1. Wpisz nazwę użytkownika i hasło dla maszyny wirtualnej. Następnie wybierz pozycję **Połącz**.

   ![nawiązywania połączenia](./media/quickstart-host-portal/connect.png)
1. Połączenie RDP z tą maszyną wirtualną za pośrednictwem bastionu zostanie otwarte bezpośrednio w witrynie Azure portal (ponad HTML5) przy użyciu portu 443 i usługi Bastion.

   ![Połączenie z PROW](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu korzystania z sieci wirtualnej i maszyn wirtualnych usuń grupę zasobów i wszystkie zasoby, które zawiera:

1. Wprowadź *TestRG1* w polu **wyszukiwania** u góry portalu i wybierz **TestRG1** z wyników wyszukiwania.

2. Wybierz pozycję **Usuń grupę zasobów**.

3. Wprowadź *TestRG1* dla **TYPU NAZWA GRUPY ZASOBÓW** i wybierz **usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono host bastionu dla sieci wirtualnej, a następnie bezpiecznie połączony z maszyną wirtualną za pośrednictwem hosta Bastion.

* Aby dowiedzieć się więcej o usłudze Azure Bastion, przeczytaj [omówienie bastionu](bastion-overview.md) i często zadawane pytania dotyczące [bastionu.](bastion-faq.md)
* Aby używać grup zabezpieczeń sieciowych w podsieci Bastion platformy Azure, zobacz [Praca z sieciami roboczymi](bastion-nsg.md).
* Instrukcje zawierające objaśnienia ustawień hosta bastionu platformy Azure można znaleźć w [samouczku](bastion-create-host-portal.md).
* Aby połączyć się z zestawem skalowania maszyny wirtualnej, zobacz [Łączenie z zestawem skalowania maszyny wirtualnej przy użyciu usługi Azure Bastion](bastion-connect-vm-scale-set.md).