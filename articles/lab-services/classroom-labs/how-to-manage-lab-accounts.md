---
title: Zarządzanie kontami laboratorium w usłudze Azure Lab Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć konto laboratorium, są wyświetlane wszystkie konta laboratorium lub Usuń konto laboratorium w subskrypcji platformy Azure.
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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 6f283ce007e96547e01a01a3753ddcb60574bfc3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65412799"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Zarządzanie kontami laboratorium w usłudze Azure Lab Services 
W usługach Azure Lab Services konta laboratorium jest kontenerem dla typów laboratorium zarządzanych, takich jak laboratorium na potrzeby zajęć. Administrator konfiguruje konta laboratorium przy użyciu usługi Azure Lab Services i zapewnia dostęp do laboratorium właścicieli, którzy mogą tworzyć laboratoriów w ramach konta. W tym artykule opisano sposób tworzenia konta laboratorium, są wyświetlane wszystkie konta laboratorium lub usuwanie konta laboratorium.

## <a name="create-a-lab-account"></a>Tworzenie konta laboratorium
Następujące kroki ilustrują tworzenie konta laboratorium w usłudze Azure Lab Services przy użyciu witryny Azure Portal. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi** w menu po lewej stronie. Wybierz pozycję **Konta laboratorium** w sekcji **DEVOPS**. Jeśli wybierzesz gwiazdkę (`*`) obok pozycji **Konta laboratorium**, zostanie ona dodana do sekcji **ULUBIONE** w menu po lewej stronie. Od następnego razu będziesz wybierać pozycję **Konta laboratorium** w sekcji **ULUBIONE**.

    ![Wszystkie usługi -> Konta laboratorium](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Na stronie **Konta laboratorium** wybierz pozycję **Dodaj** na pasku narzędzi. 

    ![Wybieranie pozycji Dodaj na stronie Konta laboratorium](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Na stronie **Konto laboratorium** wykonaj następujące czynności: 
    1. W obszarze **Nazwa konta laboratorium** wprowadź nazwę. 
    2. Wybierz **subskrypcję platformy Azure**, w której chcesz utworzyć konto laboratorium.
    3. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** i wprowadź nazwę grupy zasobów.
    4. W obszarze **Lokalizacja** wybierz lokalizację/region, w których chcesz utworzyć konto laboratorium. 
    5. Wybierz istniejącą **galerii obrazów udostępnionych** lub utworzyć nowe. Szablon maszyny Wirtualnej można zapisać w galerii obrazów udostępnionych, aby mogła zostać ponownie użyte przez inne osoby. Aby uzyskać szczegółowe informacje, galerie obrazów udostępnionych, zobacz [użycia galerii udostępnionego obrazu w usłudze Azure Lab Services](how-to-use-shared-image-gallery.md).
    6. Aby uzyskać **równorzędna sieć wirtualna**, wybierz równorzędna sieć wirtualna (VNet) sieci laboratorium. Laboratoria utworzony na tym koncie są połączone z wybraną sieć wirtualną i mają dostęp do zasobów w wybranej sieci wirtualnej. 
    7. Określ **zakres adresów** dla maszyn wirtualnych w środowisku laboratoryjnym. Zakres adresów musi należeć do notacji bezklasowego routingu międzydomenowego (CIDR) (przykład: 10.20.0.0/23). Maszyny wirtualne w laboratorium, zostaną utworzone w tym zakresie adresów. Aby uzyskać więcej informacji, zobacz [Określ zakres adresów dla maszyn wirtualnych w środowisku laboratoryjnym](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab).    
    8. W polu **Zezwalaj twórcy laboratorium na wybieranie lokalizacji laboratorium** określ, czy twórcy laboratoriów powinni mieć możliwość wybierania lokalizacji laboratorium. Domyślnie opcja ta jest wyłączona. W przypadku jej włączenia twórcy laboratoriów nie mogą określać lokalizacji dla tworzonych przez siebie laboratoriów. Laboratoria są tworzone w najbliższej lokalizacji geograficznej względem konta laboratorium. W przypadku włączenia tej opcji twórca laboratorium może wybrać lokalizację podczas tworzenia laboratorium.      
    9. Wybierz pozycję **Utwórz**. 

        ![Okno Tworzenie konta laboratorium](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Wybierz **ikonę dzwonka** na pasku narzędzi (**Powiadomienia**), upewnij się, że wdrożenie zakończyło się pomyślnie, a następnie wybierz pozycję **Przejdź do zasobu**. 

    Możesz również wybrać pozycję **Odśwież** na stronie **Konta laboratorium**, a następnie wybierz utworzone konto laboratorium. 

    ![Okno Tworzenie konta laboratorium](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Zobaczysz następującą stronę **konta laboratorium**:

    ![Strona konta laboratorium](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Wyświetl konta laboratorium
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkie zasoby** z menu. 
3. Wybierz **kontami laboratorium** dla **typu**. 
    Można również filtrować według subskrypcji, grupy zasobów, lokalizacje i tagów. 

    ![Wszystkie zasoby -> kontami laboratorium](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Wyświetlanie i zarządzanie laboratoriami na koncie laboratorium

1. Na **konta laboratorium** wybierz opcję **Labs** w menu po lewej stronie.

    ![Laboratoria w ramach konta](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. Zostanie wyświetlony **liście laboratoriów** w ramach konta, z następującymi informacjami: 
    1. Nazwa laboratorium.
    2. Data, w której utworzono laboratorium. 
    3. Adres e-mail użytkownika, który utworzył laboratorium. 
    4. Maksymalna liczba użytkowników uprawnionych do laboratorium. 
    5. Stan laboratorium. 

## <a name="delete-a-lab-in-the-lab-account"></a>Usuń laboratorium w ramach konta laboratorium
Postępuj zgodnie z instrukcjami w poprzedniej sekcji, aby wyświetlić listę laboratoriów w ramach konta laboratorium.

1. Wybierz **... (wielokropek)** i wybierz **Usuń**. 

    ![Usuń laboratorium — przycisk](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. Wybierz **tak** w komunikacie ostrzegawczym. 

    ![Potwierdź usunięcie laboratorium](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>Usuwanie konta laboratorium
Wykonaj instrukcje z poprzedniej sekcji, która wyświetla kontami laboratorium na liście. Użyj poniższych instrukcji, aby usunąć konto laboratorium: 

1. Wybierz **konta laboratorium** , którą chcesz usunąć. 
2. Wybierz **Usuń** na pasku narzędzi. 

    ![Kontami laboratorium -> przycisk Usuń](../media/how-to-manage-lab-accounts/delete-button.png)
1. Typ **tak** o potwierdzenie.
1. Wybierz pozycję **Usuń**. 

    ![Usuń konto laboratorium — potwierdzenie](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)


## <a name="next-steps"></a>Kolejne kroki
Zobacz następujący artykuł: [Konfigurowanie konta laboratorium](how-to-configure-lab-accounts.md).