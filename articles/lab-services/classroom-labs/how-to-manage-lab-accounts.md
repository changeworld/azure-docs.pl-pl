---
title: Zarządzanie kontami laboratorium w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak utworzyć konto laboratorium, wyświetlić wszystkie konta laboratorium lub usunąć konto laboratorium w ramach subskrypcji platformy Azure.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: ee64780bca13bf497793dc90ad22d3eaf91949a6
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606331"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Zarządzanie kontami laboratorium w Azure Lab Services 
W Azure Lab Services konto laboratorium jest kontenerem dla zarządzanych typów laboratorium, takich jak pracownie. Administrator konfiguruje konto laboratorium przy użyciu Azure Lab Services i zapewnia dostęp do właścicieli laboratorium, którzy mogą tworzyć laboratoria na koncie. W tym artykule opisano sposób tworzenia konta laboratorium, wyświetlania wszystkich kont laboratorium lub usuwania konta laboratorium.

## <a name="create-a-lab-account"></a>Tworzenie konta laboratorium
Następujące kroki ilustrują tworzenie konta laboratorium w usłudze Azure Lab Services przy użyciu witryny Azure Portal. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi** w menu po lewej stronie. Wybierz pozycję **konta laboratorium** w sekcji **DevOps** . Jeśli wybierzesz gwiazdkę (`*`) obok pozycji **Konta laboratorium**, zostanie ona dodana do sekcji **ULUBIONE** w menu po lewej stronie. Od następnego razu będziesz wybierać pozycję **Konta laboratorium** w sekcji **ULUBIONE**.

    ![Wszystkie usługi -> Konta laboratorium](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Na stronie **Konta laboratorium** wybierz pozycję **Dodaj** na pasku narzędzi. 

    ![Wybieranie pozycji Dodaj na stronie Konta laboratorium](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Na stronie **Konto laboratorium** wykonaj następujące czynności: 
    1. W obszarze **Nazwa konta laboratorium** wprowadź nazwę. 
    2. Wybierz **subskrypcję platformy Azure**, w której chcesz utworzyć konto laboratorium.
    3. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** i wprowadź nazwę grupy zasobów.
    4. W obszarze **Lokalizacja** wybierz lokalizację/region, w których chcesz utworzyć konto laboratorium. 
    5. Wybierz istniejącą **galerię obrazów udostępnionych** lub utwórz ją. Możesz zapisać maszynę wirtualną szablonu w galerii obrazów udostępnionych, aby mogła ona zostać ponownie użyta przez inne osoby. Aby uzyskać szczegółowe informacje na temat udostępnionych galerii obrazów, zobacz [Używanie galerii obrazów udostępnionych w Azure Lab Services](how-to-use-shared-image-gallery.md).
    6. W przypadku **równorzędnej sieci wirtualnej**wybierz równorzędną sieć wirtualną (VNET) dla sieci laboratorium. Laboratoria utworzone w ramach tego konta są połączone z wybraną siecią wirtualną i mają dostęp do zasobów w wybranej sieci wirtualnej. 
    7. Określ **zakres adresów** dla maszyn wirtualnych w laboratorium. Zakres adresów powinien znajdować się w notacji Classless Inter-Domain Routing (CIDR) (przykład: 10.20.0.0/23). Maszyny wirtualne w laboratorium zostaną utworzone w tym zakresie adresów. Aby uzyskać więcej informacji, zobacz [Określanie zakresu adresów dla maszyn wirtualnych w laboratorium](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab).    
    8. W polu **Zezwalaj twórcy laboratorium na wybieranie lokalizacji laboratorium** określ, czy twórcy laboratoriów powinni mieć możliwość wybierania lokalizacji laboratorium. Domyślnie opcja ta jest wyłączona. W przypadku jej włączenia twórcy laboratoriów nie mogą określać lokalizacji dla tworzonych przez siebie laboratoriów. Laboratoria są tworzone w najbliższej lokalizacji geograficznej względem konta laboratorium. W przypadku włączenia tej opcji twórca laboratorium może wybrać lokalizację podczas tworzenia laboratorium.      
    9. Wybierz pozycję **Utwórz**. 

        ![Okno Tworzenie konta laboratorium](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Wybierz **ikonę dzwonka** na pasku narzędzi (**Powiadomienia**), upewnij się, że wdrożenie zakończyło się pomyślnie, a następnie wybierz pozycję **Przejdź do zasobu**. 

    Możesz również wybrać pozycję **Odśwież** na stronie **Konta laboratorium**, a następnie wybierz utworzone konto laboratorium. 

    ![Okno Tworzenie konta laboratorium](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Zobaczysz następującą stronę **konta laboratorium**:

    ![Strona konta laboratorium](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Wyświetlanie kont laboratorium
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **wszystkie zasoby** z menu. 
3. Wybierz opcję **konta laboratorium** dla tego **typu**. 
    Można również filtrować według subskrypcji, grupy zasobów, lokalizacji i tagów. 

    ![Wszystkie zasoby — > konta laboratorium](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Wyświetl laboratoria na koncie laboratorium i zarządzaj nimi

1. Na stronie **konto laboratorium** wybierz pozycję **laboratoria** w menu po lewej stronie.

    ![Laboratoria na koncie](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. Zostanie wyświetlona **Lista laboratoriów** na koncie z następującymi informacjami: 
    1. Nazwa laboratorium.
    2. Data utworzenia laboratorium. 
    3. Adres e-mail użytkownika, który utworzył laboratorium. 
    4. Maksymalna liczba użytkowników, którzy mogą uzyskać dostęp do laboratorium. 
    5. Stan laboratorium. 

## <a name="delete-a-lab-in-the-lab-account"></a>Usuwanie laboratorium na koncie laboratorium
Postępuj zgodnie z instrukcjami podanymi w poprzedniej sekcji, aby wyświetlić listę laboratoriów na koncie laboratorium.

1. Wybierz **... (wielokropek)** , a następnie wybierz pozycję **Usuń**. 

    ![Usuwanie przycisku Lab](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. W komunikacie ostrzegawczym wybierz pozycję **tak** . 

    ![Potwierdź usunięcie laboratorium](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>Usuwanie konta laboratorium
Postępuj zgodnie z instrukcjami podanymi w poprzedniej sekcji, która wyświetla konta laboratorium na liście. Aby usunąć konto laboratorium, wykonaj następujące instrukcje: 

1. Wybierz **konto laboratorium** , które chcesz usunąć. 
2. Na pasku narzędzi wybierz pozycję **Usuń** . 

    ![Konta laboratorium — > przycisk usuwania](../media/how-to-manage-lab-accounts/delete-button.png)
1. Wpisz **tak** w celu potwierdzenia.
1. Wybierz pozycję **Usuń**. 

    ![Usuwanie konta laboratorium — potwierdzenie](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> Aby zarządzać kontami laboratorium, można również użyć polecenia AZ. LabServices PowerShell module (wersja zapoznawcza). Aby uzyskać więcej informacji, zobacz [stronę główną AZ. LabServices w witrynie GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującym artykułem: [jak skonfigurować konta laboratorium](how-to-configure-lab-accounts.md).