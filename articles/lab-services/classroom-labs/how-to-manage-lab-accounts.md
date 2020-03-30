---
title: Zarządzanie kontami laboratorium w usługach Azure Lab Services | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć konto laboratorium, wyświetlić wszystkie konta w laboratorium lub usunąć konto laboratorium w ramach subskrypcji platformy Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: c82b5d02ab3928eb0472f2a047cdca2238bf0b63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284293"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Zarządzanie kontami laboratorium w usługach Azure Lab Services 
W usłudze Azure Lab Services konto laboratorium jest kontenerem dla zarządzanych typów laboratoriów, takich jak laboratoria w klasie. Administrator konfiguruje konto laboratorium za pomocą usługi Azure Lab Services i zapewnia dostęp do właścicieli laboratoriów, którzy mogą tworzyć laboratoria na koncie. W tym artykule opisano sposób tworzenia konta laboratorium, wyświetlania wszystkich kont laboratorium lub usuwania konta laboratorium.

## <a name="create-a-lab-account"></a>Tworzenie konta laboratorium
Następujące kroki ilustrują tworzenie konta laboratorium w usłudze Azure Lab Services przy użyciu witryny Azure Portal. 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi** w menu po lewej stronie. Wybierz **konta laboratoryjne** w sekcji **DevOps.** Jeśli wybierzesz gwiazdkę (`*`) obok pozycji **Konta laboratorium**, zostanie ona dodana do sekcji **ULUBIONE** w menu po lewej stronie. Od następnego razu będziesz wybierać pozycję **Konta laboratorium** w sekcji **ULUBIONE**.

    ![Wszystkie usługi -> Konta laboratorium](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Na stronie **Konta laboratoryjne** wybierz pozycję **Dodaj** na pasku narzędzi lub **Utwórz konto laboratorium** na stronie. 

    ![Wybieranie pozycji Dodaj na stronie Konta laboratorium](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Na karcie **Podstawy** na stronie **Tworzenie konta w laboratorium** wykonaj następujące czynności: 
    1. W obszarze **Nazwa konta laboratorium** wprowadź nazwę. 
    2. Wybierz **subskrypcję platformy Azure**, w której chcesz utworzyć konto laboratorium.
    3. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** i wprowadź nazwę grupy zasobów.
    4. W obszarze **Lokalizacja** wybierz lokalizację/region, w których chcesz utworzyć konto laboratorium.
    5. W polu **Zezwalaj twórcy laboratorium na wybieranie lokalizacji laboratorium** określ, czy twórcy laboratoriów powinni mieć możliwość wybierania lokalizacji laboratorium. Domyślnie opcja ta jest wyłączona. W przypadku jej włączenia twórcy laboratoriów nie mogą określać lokalizacji dla tworzonych przez siebie laboratoriów. Laboratoria są tworzone w najbliższej lokalizacji geograficznej względem konta laboratorium. W przypadku włączenia tej opcji twórca laboratorium może wybrać lokalizację podczas tworzenia laboratorium. Aby uzyskać więcej informacji, zobacz [Zezwalanie twórcy laboratorium na wybieranie lokalizacji laboratorium](allow-lab-creator-pick-lab-location.md). 

        ![Tworzenie konta laboratorium - > Podstawy](../media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. Wybierz **dalej: Zaawansowane** u dołu strony, aby przejść do karty **Zaawansowane,** a następnie wykonaj następujące czynności: 
    1. Wybierz istniejącą **galerię obrazów udostępnionych** lub utwórz jej. Szablon maszyny Wirtualnej można zapisać w galerii obrazów udostępnionych, aby była ponownie usuowana przez inne osoby. Aby uzyskać szczegółowe informacje na temat udostępnionych galerii obrazów, zobacz [Używanie galerii obrazów udostępnionych w usłudze Azure Lab Services](how-to-use-shared-image-gallery.md).
    2. Określ, czy chcesz **automatycznie zamknąć maszyny wirtualne systemu Windows,** gdy użytkownicy rozłączają się z nimi. Określ, jak długo maszyny wirtualne powinny czekać na ponowne połączenie użytkownika przed automatycznym zamknięciem. 
    3. W przypadku **sieci wirtualnej równorzędnej**wybierz sieć wirtualną równorzędną dla sieci laboratoryjnej. Laboratoria utworzone na tym koncie są połączone z wybraną siecią wirtualną i mają dostęp do zasobów w wybranej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Łączenie sieci wirtualnej laboratorium z siecią wirtualną równorzędną](how-to-connect-peer-virtual-network.md).    
    8. Określ **zakres adresów** dla maszyn wirtualnych w laboratorium. Zakres adresów powinien znajdować się w notacji cidr (cidr) bezklasowej międzydomenowej (przykład: 10.20.0.0/23). Maszyny wirtualne w laboratorium zostaną utworzone w tym zakresie adresów. Aby uzyskać więcej informacji, zobacz [Określanie zakresu adresów dla maszyn wirtualnych w laboratorium](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab)  

        ![Tworzenie konta laboratorium - > Zaawansowane](../media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. Wybierz **dalej: Tagi** u dołu strony, aby przełączyć się na kartę **Znaczniki.** Tagi to pary nazw i wartości, które umożliwiają kategoryzowanie zasobów i wyświetlanie skonsolidowanych rozliczeń przez zastosowanie tego samego tagu do wielu zasobów i grup zasobów. Aby uzyskać więcej informacji, zobacz [Organizowanie zasobów platformy Azure za pomocą tagów.](../../azure-resource-manager/management/tag-resources.md)

    ![Tworzenie tagów konta > laboratorium](../media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. Wybierz **pozycję Recenzja + utwórz** u dołu tej strony, aby przełączyć się na kartę Recenzja + **utwórz.** 
4. Przejrzyj informacje podsumowujące na tej stronie i wybierz pozycję **Utwórz**. 

    ![Tworzenie tagów konta > laboratorium](../media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. Poczekaj na zakończenie wdrożenia, rozwiń węzeł **Następne kroki**i wybierz pozycję Przejdź **do zasobu,** jak pokazano na poniższej ilustracji: 

    Można również wybrać **ikonę dzwonka** na pasku narzędzi **(Powiadomienia),** potwierdzić, że wdrożenie zakończyło się pomyślnie, a następnie wybierz pozycję **Przejdź do zasobu**. 

    Możesz również wybrać pozycję **Odśwież** na stronie **Konta laboratorium**, a następnie wybierz utworzone konto laboratorium. 

    ![Okno Tworzenie konta laboratorium](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Zobaczysz następującą stronę **konta laboratorium**:

    ![Strona konta laboratorium](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Wyświetlanie kont laboratorium
1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz **wszystkie zasoby** z menu. 
3. Wybierz **konta laboratoryjne** dla **typu**. 
    Można również filtrować według subskrypcji, grupy zasobów, lokalizacji i tagów. 

    ![Wszystkie zasoby -> konta laboratoryjne](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Wyświetlanie laboratoriów i zarządzanie nimi na koncie laboratorium

1. Na stronie **Konto laboratorium** wybierz pozycję **Wszystkie laboratoria** w menu po lewej stronie.

    ![Laboratoria na koncie](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. Na koncie widoczna **jest lista laboratoriów** z następującymi informacjami: 
    1. Nazwa laboratorium.
    2. Data utworzenia laboratorium. 
    3. Adres e-mail użytkownika, który utworzył laboratorium. 
    4. Maksymalna liczba użytkowników dopuszczonych do laboratorium. 
    5. Stan laboratorium. 
    6. Przypisania ról. 

## <a name="delete-a-lab-in-the-lab-account"></a>Usuwanie laboratorium z konta laboratorium
Postępuj zgodnie z instrukcjami w poprzedniej sekcji, aby wyświetlić listę laboratoriów na koncie laboratorium.

1. Wybierz **... (wielokropek)** i wybierz pozycję **Usuń**. 

    ![Przycisk Usuń laboratorium](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. Wybierz **pozycję Tak** w komunikacie ostrzegawczym. 

    ![Potwierdź usunięcie laboratorium](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>Usuwanie konta laboratorium
Postępuj zgodnie z instrukcjami z poprzedniej sekcji, która wyświetla konta laboratoryjne na liście. Użyj następujących instrukcji, aby usunąć konto laboratorium: 

1. Wybierz **konto laboratorium,** które chcesz usunąć. 
2. Z paska narzędzi wybierz **pozycję Usuń.** 

    ![Przycisk Usuń konta laboratoryjne - >](../media/how-to-manage-lab-accounts/delete-button.png)
1. Wpisz **tak, aby** potwierdzić.
1. Wybierz pozycję **Usuń**. 

    ![Usuń konto laboratorium - potwierdzenie](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> Modułu Az.LabServices PowerShell (wersja zapoznawcza) można również użyć do zarządzania kontami laboratoryjnymi. Aby uzyskać więcej informacji, zobacz [stronę główną Az.LabServices w serwisie GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Zezwalanie twórcy laboratorium na wybieranie lokalizacji laboratorium](allow-lab-creator-pick-lab-location.md)
- [Łączenie sieci laboratorium za pomocą sieci wirtualnej równorzędnej](how-to-connect-peer-virtual-network.md)
- [Dołączanie udostępnionej galerii obrazów do laboratorium](how-to-attach-detach-shared-image-gallery.md)
- [Dodawanie użytkownika jako właściciela laboratorium](how-to-add-user-lab-owner.md)
- [Wyświetlanie ustawień zapory dla laboratorium](how-to-configure-firewall-settings.md)
- [Konfigurowanie innych ustawień dla laboratorium](how-to-configure-lab-accounts.md)