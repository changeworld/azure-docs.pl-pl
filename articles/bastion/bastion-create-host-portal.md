---
title: Tworzenie hosta usługi Azure bastionu | Microsoft Docs
description: W tym artykule dowiesz się, jak utworzyć hosta usługi Azure bastionu
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: 7838e1930a8ccd190c95740a6a8e3dadc68149f0
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422121"
---
# <a name="create-an-azure-bastion-host"></a>Tworzenie hosta usługi Azure bastionu

W tym artykule opisano sposób tworzenia hosta usługi Azure bastionu. Po udostępnieniu usługi Azure bastionu w sieci wirtualnej, bezproblemowe środowisko RDP/SSH jest dostępne dla wszystkich maszyn wirtualnych w tej samej sieci wirtualnej. Ta usługa jest wdrażana w poszczególnych sieciach wirtualnych, a nie w subskrypcjach i na kontach lub maszynach wirtualnych.

Istnieją dwa sposoby tworzenia zasobu hosta bastionu:

* Utwórz zasób bastionu przy użyciu Azure Portal.
* Utwórz zasób bastionu w Azure Portal przy użyciu istniejących ustawień maszyny wirtualnej.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Bastionu jest dostępny w następujących regionach publicznych platformy Azure:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>Tworzenie hosta bastionu

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
    * **Sieć wirtualna**: Sieć wirtualna, w której zostanie utworzony zasób bastionu. Możesz utworzyć nową sieć wirtualną w portalu w trakcie tego procesu, jeśli nie masz lub nie chcesz używać istniejącej sieci wirtualnej. Jeśli używasz istniejącej sieci wirtualnej, upewnij się, że istniejąca sieć wirtualna ma wystarczającą ilość wolnej przestrzeni adresowej, aby pomieścić wymagania dotyczące podsieci bastionu.
    * **Podsieć**: podsieć w sieci wirtualnej, w której zostanie wdrożony nowy zasób hosta bastionu. Należy utworzyć podsieć przy użyciu wartości Name **AzureBastionSubnet**. Ta wartość pozwala platformie Azure wiedzieć, która podsieć, do której mają zostać wdrożone zasoby bastionu. Jest to inna niż podsieć bramy. Należy użyć podsieci z co najmniej/27 lub większą podsiecią (/27,/26 itd.). Utwórz **AzureBastionSubnet** bez żadnych tabel tras ani delegowania. W przypadku używania sieciowych grup zabezpieczeń na **AzureBastionSubnet**zapoznaj się z tematem [współpraca z sieciowych grup zabezpieczeń](bastion-nsg.md).
    * **Publiczny adres IP**: publiczny IP zasobu bastionu, w którym będzie dostępny protokół RDP/SSH (za pośrednictwem portu 443). Utwórz nowy publiczny adres IP lub Użyj istniejącego. Publiczny adres IP musi znajdować się w tym samym regionie co tworzony zasób bastionu.
    * **Nazwa publicznego adresu IP**: nazwa zasobu publicznego adresu IP.
    * **Jednostka SKU publicznego adresu IP**: Wstępnie wypełniona domyślnie do warstwy **standardowa**. Usługa Azure bastionu używa/obsługuje tylko w przypadku standardowej jednostki SKU publicznego adresu IP.
    * **Przypisanie**: domyślnie wypełniono jako **static**.

1. Po zakończeniu określania ustawień kliknij przycisk **Przegląd + Utwórz**. Spowoduje to zweryfikowanie wartości. Po zakończeniu walidacji można rozpocząć proces tworzenia.
1. Na stronie Tworzenie bastionu kliknij pozycję **Utwórz**.
1. Zobaczysz komunikat informujący o tym, że wdrożenie jest w toku. Stan będzie wyświetlany na tej stronie podczas tworzenia zasobów. Utworzenie i wdrożenie zasobu bastionu trwa około 5 minut.

## <a name="createvmset"></a>Tworzenie hosta bastionu przy użyciu ustawień maszyny wirtualnej

Jeśli utworzysz hosta bastionu w portalu przy użyciu istniejącej maszyny wirtualnej, różne ustawienia będą automatycznie odpowiadały maszynom wirtualnym i/lub sieci wirtualnej.

1. Otwórz [portal Azure](https://portal.azure.com). Przejdź do maszyny wirtualnej, a następnie kliknij przycisk **Połącz**.

   ![Połączenie maszyny wirtualnej](./media/bastion-create-host-portal/vmsettings.png)
1. Na prawym pasku bocznym kliknij pozycję **bastionu**, a następnie **Użyj bastionu**.

   ![Bastion](./media/bastion-create-host-portal/vmbastion.png)
1. Na stronie bastionu wypełnij następujące pola ustawień:

   * **Name**: Nazwa hosta bastionu, który chcesz utworzyć.
   * **Podsieć**: podsieć w sieci wirtualnej, do której zostanie wdrożony zasób bastionu. Należy utworzyć podsieć o nazwie **AzureBastionSubnet**. Dzięki temu platforma Azure wie, która podsieć, do której ma zostać wdrożony zasób bastionu. Jest to inna niż podsieć bramy. Kliknij pozycję **Zarządzaj konfiguracją podsieci** , aby utworzyć podsieć usługi Azure bastionu. Zdecydowanie zaleca się użycie co najmniej/27 lub większej podsieci (/27,/26 itp.). Utwórz **AzureBastionSubnet** bez żadnych sieciowych grup zabezpieczeń, tabel tras ani delegowania. Kliknij przycisk **Utwórz** , aby utworzyć podsieć, a następnie przejdź do następnego ustawienia.
   * **Publiczny adres IP**: publiczny IP zasobu bastionu, w którym będzie dostępny protokół RDP/SSH (za pośrednictwem portu 443). Utwórz nowy publiczny adres IP lub Użyj istniejącego. Publiczny adres IP musi znajdować się w tym samym regionie co tworzony zasób bastionu.
   * **Nazwa publicznego adresu IP**: nazwa zasobu publicznego adresu IP.
1. Na ekranie walidacji kliknij pozycję **Utwórz**. Poczekaj około 5 minut na utworzenie i wdrożenie zasobu bastionu.

## <a name="next-steps"></a>Następne kroki

Przeczytaj [często zadawane pytania dotyczące bastionu](bastion-faq.md)