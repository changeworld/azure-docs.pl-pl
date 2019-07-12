---
title: Tworzenie hosta platformy Azure bastionu | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące tworzenia hostem bastionu platformy Azure
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: cherylmc
ms.openlocfilehash: 65116ebbd6a66241a5b35a39f3dfb8f826a3745f
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594270"
---
# <a name="create-an-azure-bastion-host-preview"></a>Utwórz host bastionu Azure (wersja zapoznawcza)

W tym artykule pokazano, jak utworzyć hostem bastionu platformy Azure. Po zainicjowaniu usługę Azure bastionu w Twojej sieci wirtualnej, nie zakłóca pracy RDP/SSH jest dostępny dla wszystkich maszyn wirtualnych w tej samej sieci wirtualnej. Ta usługa jest wdrażana w poszczególnych sieciach wirtualnych, a nie w subskrypcjach i na kontach lub maszynach wirtualnych.

Istnieją dwa sposoby, które można utworzyć zasobu hosta bastionu:

* Utwórz zasób bastionu za pomocą witryny Azure portal.
* Utwórz zasób bastionu w witrynie Azure portal przy użyciu istniejących ustawień maszyny Wirtualnej.

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Przed rozpoczęciem

Publiczna wersja zapoznawcza jest ograniczone do następujących publicznych regionach platformy Azure:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>Utwórz host bastionu

Ten rozdział pomaga utworzyć nowy zasób bastionu platformy Azure w witrynie Azure portal.

1. Na stronie głównej w [portal Azure — wersja zapoznawcza bastionu](https://aka.ms/BastionHost), kliknij przycisk **+ Utwórz zasób**. Upewnij się, że używasz podany link, aby uzyskać dostęp do portalu dla tej wersji zapoznawczej, a nie regularne witryny Azure portal.

1. Na **nowy** stronie *Przeszukaj witrynę Marketplace* wpisz **bastionu**, następnie kliknij przycisk **Enter** Aby przejść do wyników wyszukiwania.

1. Na liście wyników, kliknij przycisk **bastionu (wersja zapoznawcza)** . Upewnij się, że wydawca jest *Microsoft* i kategorii *sieć*.

1. Na **bastionu (wersja zapoznawcza)** kliknij **Utwórz** otworzyć **tworzenie bastionu** strony.

1. Na **tworzenie bastionu** strony, skonfigurowania nowego zasobu bastionu. Określ ustawienia konfiguracji dla zasobu bastionu.

    ![Utwórz bastionu](./media/bastion-create-host-portal/settings.png)

    * **Subskrypcja**: Subskrypcja platformy Azure, którego chcesz użyć do utworzenia nowego zasobu bastionu.
    * **Grupa zasobów**: Grupa zasobów platformy Azure, w której zostanie utworzony nowy zasób bastionu w. Jeśli nie masz istniejącej grupy zasobów, możesz utworzyć nowy.
    * **Nazwa**: Nazwa nowego zasobu bastionu
    * **Region**: Region platformy Azure publicznych, zasób zostanie utworzony w.
    * **Sieć wirtualna**: Sieć wirtualna, w której zostanie utworzony zasób bastionu w. Można utworzyć nową sieć wirtualną w portalu w trakcie tego procesu, w przypadku, gdy nie ma lub nie chcesz używać istniejącej sieci wirtualnej. Jeśli używasz istniejącej sieci wirtualnej, upewnij się, że istniejąca sieć wirtualna ma wystarczającą ilość miejsca wolnego adresu, aby uwzględnić wymagania dotyczące podsieci bastionu.
    * **Podsieć**: Podsieć w sieci wirtualnej, do której zostanie wdrożony nowy zasób hostem bastionu. Należy utworzyć podsieci za pomocą wartości nazwy **AzureBastionSubnet**. Ta wartość umożliwia platformie Azure pożądanego podsieć, która do wdrażania zasobów bastionu. Stanowi to odmianę podsieć bramy. Zdecydowanie zaleca się użycie co najmniej/27 lub większej podsieci (/ 27, / 26 i tak dalej). Tworzenie **AzureBastionSubnet** bez żadnej grupy zabezpieczeń sieci trasy tabele lub delegowanie.
    * **Publiczny adres IP**: Publiczny adres IP zasobu bastionu, na którym będzie dostępna RDP/SSH (za pośrednictwem portu 443). Tworzenie nowego publicznego adresu IP lub użyć istniejącego. Publiczny adres IP musi być w tym samym regionie, co zasób bastionu, który tworzysz.
    * **Nazwa publicznego adresu IP**: Nazwa zasobu publicznego adresu IP.
    * **Publiczny adres IP jednostki SKU**: Wstępnie wypełnione, aby domyślnie **standardowa**. Używa platformy Azure bastionu/obsługuje tylko standardowego publicznego adresu IP jednostki SKU.
    * **Przypisanie**: Wstępnie wypełnione, aby domyślnie **statyczne**.

1. Po określeniu ustawień, kliknij przycisk **przeglądu + Utwórz**. To sprawdzenie poprawności wartości. Po pomyślnej weryfikacji, można rozpocząć procesu tworzenia.
1. Tworzenie strony bastionu, wybierz polecenie **Utwórz**.
1. Zostanie wyświetlony komunikat, dzięki czemu będzie wiadomo, że wdrożenie jest w toku. Na tej stronie będzie wyświetlany stan, zgodnie z którym są tworzone zasoby. Trwa około 5 minut dla zasobu bastionu może zostać utworzona i wdrożona.

## <a name="createvmset"></a>Tworzenie przy użyciu ustawień maszyny Wirtualnej host bastionu

Jeśli tworzysz hostem bastionu w portalu przy użyciu istniejącej maszyny Wirtualnej, różne ustawienia zostaną automatycznie domyślnie odpowiadający Twojej maszyny wirtualnej i/lub sieci wirtualnej.

1. W [portal Azure — wersja zapoznawcza bastionu](https://aka.ms/BastionHost), przejdź do maszyny wirtualnej, a następnie kliknij przycisk **Connect**.

    ![VM Connect](./media/bastion-create-host-portal/vmsettings.png)

1. Na prawym pasku bocznym, kliknij przycisk **bastionu**, następnie **bastionu użyj**.

    ![Bastion](./media/bastion-create-host-portal/vmbastion.png)

1. Na stronie bastionu wypełnij następujące pola ustawienia:

    * **Nazwa**: Nazwa hostem bastionu, który chcesz utworzyć.
    * **Podsieć**: Podsieć w Twojej sieci wirtualnej, do których bastionu zostanie wdrożony zasób. Należy utworzyć podsieć o nazwie **AzureBastionSubnet**. Dzięki temu wiedzieć, które podsieci w celu wdrożenia zasobów bastionu do platformy Azure. Stanowi to odmianę podsieć bramy. Kliknij przycisk **konfigurację podsieci Zarządzaj** utworzyć podsieć bastionu platformy Azure. Zdecydowanie zaleca się użycie co najmniej/27 lub większej podsieci (/ 27, / 26, itp.). Tworzenie **AzureBastionSubnet** bez żadnej grupy zabezpieczeń sieci trasy tabele lub delegowanie. Kliknij przycisk **Utwórz** utworzyć podsieć, a następnie wykonaj następny ustawienia.

      ![Bastion](./media/bastion-create-host-portal/subnet.png)
      
    * **Publiczny adres IP**: Publiczny adres IP zasobu bastionu, na którym będzie dostępna RDP/SSH (za pośrednictwem portu 443). Tworzenie nowego publicznego adresu IP lub użyć istniejącego. Publiczny adres IP musi być w tym samym regionie, co zasób bastionu, który tworzysz.
    * **Nazwa publicznego adresu IP**: Nazwa zasobu publicznego adresu IP.
1. Na ekranie weryfikacji kliknij **Utwórz**. Poczekaj około 5 minut dla zasobu bastionu może zostać utworzona i wdrożona.

## <a name="next-steps"></a>Kolejne kroki

Odczyt [bastionu — często zadawane pytania](bastion-faq.md)
