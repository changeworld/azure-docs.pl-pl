---
title: Tworzenie hosta usługi Azure bastionu | Microsoft Docs
description: W tym artykule dowiesz się, jak utworzyć hosta usługi Azure bastionu
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: cherylmc
ms.openlocfilehash: 881d89170c82731fca3f6977da486eac5be772f8
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466992"
---
# <a name="create-an-azure-bastion-host-preview"></a>Tworzenie hosta usługi Azure bastionu (wersja zapoznawcza)

W tym artykule opisano sposób tworzenia hosta usługi Azure bastionu. Po udostępnieniu usługi Azure bastionu w sieci wirtualnej, bezproblemowe środowisko RDP/SSH jest dostępne dla wszystkich maszyn wirtualnych w tej samej sieci wirtualnej. Ta usługa jest wdrażana w poszczególnych sieciach wirtualnych, a nie w subskrypcjach i na kontach lub maszynach wirtualnych.

Istnieją dwa sposoby tworzenia zasobu hosta bastionu:

* Utwórz zasób bastionu przy użyciu Azure Portal.
* Utwórz zasób bastionu w Azure Portal przy użyciu istniejących ustawień maszyny wirtualnej.

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Przed rozpoczęciem

Publiczna wersja zapoznawcza jest ograniczona do następujących publicznych regionów platformy Azure:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>Tworzenie hosta bastionu

Ta sekcja ułatwia tworzenie nowego zasobu usługi Azure bastionu z poziomu Azure Portal.

1. Na stronie głównej w [Azure Portal-bastionu wersja](https://aka.ms/BastionHost)zapoznawcza kliknij pozycję **+ Utwórz zasób**. Upewnij się, że używasz dostępnego linku, aby uzyskać dostęp do portalu w tej wersji zapoznawczej, a nie na zwykłych Azure Portal.

1. Na stronie **Nowy** w polu Wyszukaj w *witrynie Marketplace* wpisz **bastionu**, a następnie kliknij przycisk **Enter** , aby przejść do wyników wyszukiwania.

1. Na podstawie wyników kliknij pozycję **bastionu (wersja zapoznawcza)** . Upewnij się, że Wydawca jest *firmą Microsoft* , a kategoria to *Sieć*.

1. Na stronie **bastionu (wersja zapoznawcza)** kliknij pozycję **Utwórz** , aby otworzyć stronę **Tworzenie bastionu** .

1. Na stronie **Tworzenie bastionu** Skonfiguruj nowy zasób bastionu. Określ ustawienia konfiguracji dla zasobu bastionu.

    ![Utwórz bastionu](./media/bastion-create-host-portal/settings.png)

    * **Subskrypcja**: Subskrypcja platformy Azure, która ma zostać użyta do utworzenia nowego zasobu bastionu.
    * **Grupa zasobów**: Grupa zasobów platformy Azure, w której zostanie utworzony nowy zasób bastionu. Jeśli nie masz istniejącej grupy zasobów, możesz utworzyć nową.
    * **Nazwa**: Nazwa nowego zasobu bastionu
    * **Region**: Region publiczny platformy Azure, w którym zostanie utworzony zasób.
    * **Sieć wirtualna**: Sieć wirtualna, w której zostanie utworzony zasób bastionu. Możesz utworzyć nową sieć wirtualną w portalu w trakcie tego procesu, jeśli nie masz lub nie chcesz używać istniejącej sieci wirtualnej. Jeśli używasz istniejącej sieci wirtualnej, upewnij się, że istniejąca sieć wirtualna ma wystarczającą ilość wolnej przestrzeni adresowej, aby pomieścić wymagania dotyczące podsieci bastionu.
    * **Podsieć**: Podsieć w sieci wirtualnej, w której zostanie wdrożony nowy zasób hosta bastionu. Należy utworzyć podsieć przy użyciu wartości Name **AzureBastionSubnet**. Ta wartość pozwala platformie Azure wiedzieć, która podsieć, do której mają zostać wdrożone zasoby bastionu. Jest to inna niż podsieć bramy. Zdecydowanie zaleca się użycie co najmniej/27 lub większej podsieci (/27,/26 itd.). Utwórz **AzureBastionSubnet** bez żadnych tabel tras ani delegowania. W przypadku używania sieciowych grup zabezpieczeń na **AzureBastionSubnet**zapoznaj się z tematem [współpraca z sieciowych grup zabezpieczeń](bastion-nsg.md).
    * **Publiczny adres IP**: Publiczny adres IP zasobu bastionu, w którym będzie dostępny protokół RDP/SSH (za pośrednictwem portu 443). Utwórz nowy publiczny adres IP lub Użyj istniejącego. Publiczny adres IP musi znajdować się w tym samym regionie co tworzony zasób bastionu.
    * **Nazwa publicznego adresu IP**: Nazwa zasobu publicznego adresu IP.
    * **Jednostka SKU publicznego adresu IP**: Wstępnie wypełniony domyślnie na **Standard**. Usługa Azure bastionu używa/obsługuje tylko w przypadku standardowej jednostki SKU publicznego adresu IP.
    * **Przypisanie**: Wstępnie wypełniony domyślnie na **static**.

1. Po zakończeniu określania ustawień kliknij przycisk **Przegląd + Utwórz**. Spowoduje to zweryfikowanie wartości. Po zakończeniu walidacji można rozpocząć proces tworzenia.
1. Na stronie Tworzenie bastionu kliknij pozycję **Utwórz**.
1. Zobaczysz komunikat informujący o tym, że wdrożenie jest w toku. Stan będzie wyświetlany na tej stronie podczas tworzenia zasobów. Utworzenie i wdrożenie zasobu bastionu trwa około 5 minut.

## <a name="createvmset"></a>Tworzenie hosta bastionu przy użyciu ustawień maszyny wirtualnej

Jeśli utworzysz hosta bastionu w portalu przy użyciu istniejącej maszyny wirtualnej, różne ustawienia będą automatycznie odpowiadały maszynom wirtualnym i/lub sieci wirtualnej.

1. W [Azure Portal bastionu — wersja](https://aka.ms/BastionHost)zapoznawcza, przejdź do maszyny wirtualnej, a następnie kliknij przycisk **Połącz**.

    ![Połączenie maszyny wirtualnej](./media/bastion-create-host-portal/vmsettings.png)

1. Na prawym pasku bocznym kliknij pozycję **bastionu**, a następnie **Użyj bastionu**.

    ![Bastion](./media/bastion-create-host-portal/vmbastion.png)

1. Na stronie bastionu wypełnij następujące pola ustawień:

    * **Nazwa**: Nazwa hosta bastionu, który ma zostać utworzony.
    * **Podsieć**: Podsieć w sieci wirtualnej, do której zostanie wdrożony zasób bastionu. Należy utworzyć podsieć o nazwie **AzureBastionSubnet**. Dzięki temu platforma Azure wie, która podsieć, do której ma zostać wdrożony zasób bastionu. Jest to inna niż podsieć bramy. Kliknij pozycję **Zarządzaj konfiguracją podsieci** , aby utworzyć podsieć usługi Azure bastionu. Zdecydowanie zaleca się użycie co najmniej/27 lub większej podsieci (/27,/26 itp.). Utwórz **AzureBastionSubnet** bez żadnych sieciowych grup zabezpieczeń, tabel tras ani delegowania. Kliknij przycisk **Utwórz** , aby utworzyć podsieć, a następnie przejdź do następnego ustawienia.

      ![Bastion](./media/bastion-create-host-portal/subnet.png)
      
    * **Publiczny adres IP**: Publiczny adres IP zasobu bastionu, w którym będzie dostępny protokół RDP/SSH (za pośrednictwem portu 443). Utwórz nowy publiczny adres IP lub Użyj istniejącego. Publiczny adres IP musi znajdować się w tym samym regionie co tworzony zasób bastionu.
    * **Nazwa publicznego adresu IP**: Nazwa zasobu publicznego adresu IP.
1. Na ekranie walidacji kliknij pozycję **Utwórz**. Poczekaj około 5 minut na utworzenie i wdrożenie zasobu bastionu.

## <a name="next-steps"></a>Następne kroki

Przeczytaj [często zadawane pytania dotyczące bastionu](bastion-faq.md)
