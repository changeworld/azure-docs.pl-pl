---
title: Tworzenie hosta usługi Azure bastionu | Microsoft Docs
description: W tym artykule dowiesz się, jak utworzyć hosta usługi Azure bastionu
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: f907dcb4427fd07a2c212e5de91ccce5e8198960
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990423"
---
# <a name="create-an-azure-bastion-host"></a>Tworzenie hosta usługi Azure bastionu

W tym artykule opisano sposób tworzenia hosta usługi Azure bastionu przy użyciu Azure Portal. Po udostępnieniu usługi Azure bastionu w sieci wirtualnej, bezproblemowe środowisko RDP/SSH jest dostępne dla wszystkich maszyn wirtualnych w tej samej sieci wirtualnej. Wdrożenie usługi Azure bastionu odbywa się na sieć wirtualną, a nie na subskrypcję/konto lub maszynę wirtualną.

Nowy zasób hosta bastionu można utworzyć w portalu, określając wszystkie ustawienia ręcznie lub używając ustawień, które odpowiadają istniejącej maszynie wirtualnej. Opcjonalnie można użyć [programu Azure PowerShell](bastion-create-host-powershell.md) do utworzenia hosta usługi Azure bastionu.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Bastionu jest dostępny w następujących regionach publicznych platformy Azure:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>Tworzenie hosta bastionu — Określ ustawienia

Ta sekcja ułatwia tworzenie nowego zasobu usługi Azure bastionu z poziomu Azure Portal.

1. W menu [Azure Portal](https://portal.azure.com) lub na stronie **głównej** wybierz pozycję **Utwórz zasób**.

1. Na stronie **Nowy** w polu Wyszukaj w *witrynie Marketplace* wpisz **bastionu**, a następnie kliknij przycisk **Enter** , aby przejść do wyników wyszukiwania.

1. Na podstawie wyników kliknij pozycję **bastionu**. Upewnij się, że Wydawca jest *firmą Microsoft* , a kategoria to *Sieć*.

1. Na stronie **bastionu** kliknij przycisk **Utwórz** , aby otworzyć stronę **Tworzenie bastionu** .

1. Na stronie **Tworzenie bastionu** Skonfiguruj nowy zasób bastionu. Określ ustawienia konfiguracji dla zasobu bastionu.

    ![Utwórz bastionu](./media/bastion-create-host-portal/settings.png)

    * **Subskrypcja**: subskrypcja platformy Azure, która ma zostać użyta do utworzenia nowego zasobu bastionu.
    * **Grupa zasobów**: Grupa zasobów platformy Azure, w której zostanie utworzony nowy zasób bastionu. Jeśli nie masz istniejącej grupy zasobów, możesz utworzyć nową.
    * **Name**: Nazwa nowego zasobu bastionu
    * **Region**: region publiczny platformy Azure, w którym zostanie utworzony zasób.
    * **Sieć wirtualna**: Sieć wirtualna, w której zostanie utworzony zasób bastionu. Możesz utworzyć nową sieć wirtualną w portalu w trakcie tego procesu lub użyć istniejącej sieci wirtualnej. Jeśli używasz istniejącej sieci wirtualnej, upewnij się, że istniejąca sieć wirtualna ma wystarczającą ilość wolnej przestrzeni adresowej, aby pomieścić wymagania dotyczące podsieci bastionu.
    * **Podsieć**: podsieć w sieci wirtualnej, w której zostanie wdrożony nowy zasób hosta bastionu. Należy utworzyć podsieć przy użyciu wartości Name **AzureBastionSubnet**. Ta wartość pozwala platformie Azure wiedzieć, która podsieć, do której mają zostać wdrożone zasoby bastionu. Jest to inna niż podsieć bramy. Należy użyć podsieci o wartości co najmniej/27 lub większej (/27,/26 itd.).
    
       Utwórz **AzureBastionSubnet** bez żadnych tabel tras ani delegowania. Jeśli używasz sieciowych grup zabezpieczeń w **AzureBastionSubnet**, zapoznaj się z artykułem [Pracuj z sieciowych grup zabezpieczeń](bastion-nsg.md) .
    * **Publiczny adres IP**: publiczny IP zasobu bastionu, w którym będzie dostępny protokół RDP/SSH (za pośrednictwem portu 443). Utwórz nowy publiczny adres IP lub Użyj istniejącego. Publiczny adres IP musi znajdować się w tym samym regionie co tworzony zasób bastionu.
    * **Nazwa publicznego adresu IP**: nazwa zasobu publicznego adresu IP.
    * **Jednostka SKU publicznego adresu IP**: to ustawienie jest wstępnie wypełniane domyślnie **standardem**. Usługa Azure bastionu używa/obsługuje tylko w przypadku standardowej jednostki SKU publicznego adresu IP.
    * **Przypisanie**: to ustawienie jest wstępnie wypełniane domyślnie **statycznie**.

1. Po zakończeniu określania ustawień kliknij przycisk **Przegląd + Utwórz**. Spowoduje to zweryfikowanie wartości. Po zakończeniu walidacji można rozpocząć proces tworzenia.
1. Na stronie **Tworzenie bastionu** kliknij pozycję **Utwórz**.
1. Zobaczysz komunikat informujący o tym, że wdrożenie jest w toku. Stan będzie wyświetlany na tej stronie podczas tworzenia zasobów. Utworzenie i wdrożenie zasobu bastionu trwa około 5 minut.

## <a name="createvmset"></a>Tworzenie hosta bastionu — Użyj ustawień maszyny wirtualnej

Jeśli utworzysz hosta bastionu w portalu przy użyciu istniejącej maszyny wirtualnej, różne ustawienia będą automatycznie odpowiadały maszynom wirtualnym i/lub sieci wirtualnej.

1. Otwórz [Portalu Azure](https://portal.azure.com). Przejdź do maszyny wirtualnej, a następnie kliknij przycisk **Połącz**.

   ![Połączenie maszyny wirtualnej](./media/bastion-create-host-portal/vmsettings.png)
1. Na prawym pasku bocznym kliknij pozycję **bastionu**, a następnie **Użyj bastionu**.

   ![Bastion](./media/bastion-create-host-portal/vmbastion.png)
1. Na stronie bastionu wypełnij następujące pola ustawień:

   * **Name**: Nazwa hosta bastionu, który chcesz utworzyć.
   * **Podsieć**: podsieć w sieci wirtualnej, do której zostanie wdrożony zasób bastionu. Należy utworzyć podsieć o nazwie **AzureBastionSubnet**. Dzięki temu platforma Azure wie, która podsieć, do której ma zostać wdrożony zasób bastionu. Jest to inna niż podsieć bramy. Należy użyć podsieci o wartości co najmniej/27 lub większej (/27,/26 itd.). Utwórz podsieć bez żadnych sieciowych grup zabezpieczeń, tabel tras ani delegowania. Jeśli później zdecydujesz się użyć sieciowych grup zabezpieczeń na **AzureBastionSubnet**, zobacz [Work with sieciowych grup zabezpieczeń](bastion-nsg.md).
   
     Kliknij pozycję **Zarządzaj konfiguracją podsieci** , aby utworzyć **AzureBastionSubnet**.  Kliknij przycisk **Utwórz** , aby utworzyć podsieć, a następnie przejdź do następnego ustawienia.
   * **Publiczny adres IP**: publiczny IP zasobu bastionu, w którym będzie dostępny protokół RDP/SSH (za pośrednictwem portu 443). Utwórz nowy publiczny adres IP lub Użyj istniejącego. Publiczny adres IP musi znajdować się w tym samym regionie co tworzony zasób bastionu.
   * **Nazwa publicznego adresu IP**: nazwa zasobu publicznego adresu IP.
1. Na ekranie walidacji kliknij pozycję **Utwórz**. Poczekaj około 5 minut na utworzenie i wdrożenie zasobu bastionu.

## <a name="next-steps"></a>Następne kroki

* Przeczytaj [często zadawane pytania](bastion-faq.md) dotyczące usługi bastionu, aby uzyskać dodatkowe informacje.

* Aby użyć sieciowych grup zabezpieczeń z podsiecią usługi Azure bastionu, zobacz [Work with sieciowych grup zabezpieczeń](bastion-nsg.md).