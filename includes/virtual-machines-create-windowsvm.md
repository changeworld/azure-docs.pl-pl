---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: b0b5e817d5e39dd7800a1482d40c56db5f2be6ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61127146"
---
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Uruchamianie w lewym górnym rogu, kliknij przycisk **Utwórz zasób** > **obliczenia** > **systemu Windows Server 2016 Datacenter**.

    ![Nawigowanie do obrazów maszyn wirtualnych Azure w portalu](./media/virtual-machines-common-portal-create-fqdn/marketplace-new.png)

3. W obszarze Windows Server 2016 Datacenter wybierz klasyczny model wdrożenia. Kliknij pozycję Utwórz.

    ![Zrzut ekranu pokazujący dostępne w portalu obrazy maszyn wirtualnych Azure](./media/virtual-machines-common-portal-create-fqdn/deployment-classic-model.png)

## <a name="1-basics-blade"></a>1. Blok Podstawowe

W bloku Podstawowe należy podać informacje administracyjne dotyczące maszyny wirtualnej.

1. Wprowadź wartość **Nazwa** dla maszyny wirtualnej. W tym przykładzie nazwa maszyny wirtualnej to _HeroVM_. Nazwa musi składać się z 1–15 znaków i nie może zawierać znaków specjalnych.

2. Podaj nazwę użytkownika w polu **Nazwa użytkownika** i silne hasło w polu **Hasło**. Zostaną one użyte do utworzenia konta lokalnego na maszynie wirtualnej. Konto lokalne jest używane do logowania się do maszyny wirtualnej i zarządzania nią. W tym przykładzie nazwa użytkownika to _azureuser_.

   Hasło musi mieć długość od 8 do 123 znaków i spełniać trzy z czterech następujących wymagań dotyczących złożoności: mała litera, wielka litera, cyfra i znak specjalny. Więcej informacji na temat [wymagań dotyczących nazwy użytkownika i hasła](../articles/virtual-machines/windows/faq.md).

3. Pole **Subskrypcja** jest opcjonalne. Często używaną opcją jest „Płatność zgodnie z rzeczywistym użyciem”.

4. Wybierz istniejącą **grupę zasobów** lub wprowadź nazwę nowej grupy zasobów. W tym przykładzie nazwa grupy zasobów to _HeroVMRG_.

5. Wybierz wartość **Lokalizacja** dla centrum danych Azure, w którym ma być uruchamiana maszyna wirtualna. W tym przykładzie lokalizacja to **East US**.

6. Gdy skończysz, kliknij przycisk **Dalej**, aby przejść do następnego bloku.

    ![Zrzut ekranu pokazujący ustawienia w bloku Podstawowe służące do konfigurowania maszyny wirtualnej Azure](./media/virtual-machines-common-portal-create-fqdn/basics-blade-classic.png)

## <a name="2-size-blade"></a>2. Blok Rozmiar

Blok Rozmiar identyfikuje szczegóły konfiguracji maszyny wirtualnej i zawiera różne opcje obejmujące system operacyjny, liczbę procesorów, typ magazynu dyskowego i szacowane miesięczne koszty użytkowania.  

Wybierz rozmiar maszyny wirtualnej, a następnie kliknij przycisk **Wybierz**, aby kontynuować. W tym przykładzie rozmiar maszyny wirtualnej to _DS1_\__V2 Standardowa_.

  ![Zrzut ekranu bloku Rozmiar pokazujący dostępne do wyboru rozmiary maszyn wirtualnych Azure](./media/virtual-machines-common-portal-create-fqdn/vm-size-classic.png)


## <a name="3-settings-blade"></a>3. Blok Ustawienia

W bloku Ustawienia należy podać opcje magazynu i sieci. Można zaakceptować ustawienia domyślne. Platforma Azure utworzy odpowiednie wpisy tam, gdzie to konieczne.

Jeśli został wybrany rozmiar maszyny wirtualnej, który obsługuję tę funkcję, możesz wypróbować usługę Azure Premium Storage, wybierając opcję Premium (SSD) w obszarze Typ dysku.

Po zakończeniu wprowadzania zmian kliknij przycisk **OK**.

## <a name="4-summary-blade"></a>4. Blok Podsumowanie

W bloku Podsumowanie prezentowane są ustawienia określone w poprzednich blokach. Aby utworzyć obraz, kliknij przycisk **OK**.

 ![Raport przekazanych ustawień maszyny wirtualnej w bloku Podsumowanie](./media/virtual-machines-common-portal-create-fqdn/summary-blade-classic.png)

Po utworzeniu maszyny wirtualnej jest ona wyświetlana w portalu w obszarze **Wszystkie zasoby**, a jej kafelek jest wyświetlany na pulpicie nawigacyjnym. Utworzone zostają też odpowiednia usługa w chmurze oraz konto magazynu i są one wyświetlane w portalu. Zarówno maszyna wirtualna, jak i usługa w chmurze są uruchamiane automatycznie, a ich stany są wskazywane jako **Uruchomiono**.

 ![Konfigurowanie agenta maszyny wirtualnej i punktów końcowych maszyny wirtualnej](./media/virtual-machines-common-portal-create-fqdn/portal-with-new-vm.png)
