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
ms.date: 02/07/2018
ms.author: spelluru
ms.openlocfilehash: f1194d8385d1e7ddcb906d0c8c3a2b56648e2547
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60696388"
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
    5. Aby uzyskać **równorzędna sieć wirtualna**, wybierz równorzędna sieć wirtualna (VNet) sieci laboratorium. Laboratoria utworzony na tym koncie są połączone z wybraną sieć wirtualną i mają dostęp do zasobów w wybranej sieci wirtualnej. 
    7. W polu **Zezwalaj twórcy laboratorium na wybieranie lokalizacji laboratorium** określ, czy twórcy laboratoriów powinni mieć możliwość wybierania lokalizacji laboratorium. Domyślnie opcja ta jest wyłączona. W przypadku jej włączenia twórcy laboratoriów nie mogą określać lokalizacji dla tworzonych przez siebie laboratoriów. Laboratoria są tworzone w najbliższej lokalizacji geograficznej względem konta laboratorium. W przypadku włączenia tej opcji twórca laboratorium może wybrać lokalizację podczas tworzenia laboratorium.      
    8. Wybierz pozycję **Utwórz**. 

        ![Okno Tworzenie konta laboratorium](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Wybierz **ikonę dzwonka** na pasku narzędzi (**Powiadomienia**), upewnij się, że wdrożenie zakończyło się pomyślnie, a następnie wybierz pozycję **Przejdź do zasobu**. 

    Możesz również wybrać pozycję **Odśwież** na stronie **Konta laboratorium**, a następnie wybierz utworzone konto laboratorium. 

    ![Okno Tworzenie konta laboratorium](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Zobaczysz następującą stronę **konta laboratorium**:

    ![Strona konta laboratorium](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Dodawanie użytkownika do roli twórcy laboratorium
Aby skonfigurować laboratorium na potrzeby zajęć w ramach konta laboratorium, użytkownik musi być członkiem roli **Twórca laboratorium** na koncie laboratorium. Konto, którego użyto do utworzenia konta laboratorium, jest automatycznie dodawane do tej roli. Jeśli planujesz użyć tego samego konta użytkownika w celu utworzenia laboratorium na potrzeby zajęć, możesz pominąć ten krok. Aby użyć innego konta użytkownika do utworzenia laboratorium na potrzeby zajęć, wykonaj następujące czynności: 

Aby przyznać nauczycielom uprawnienie do tworzenia laboratoriów na potrzeby zajęć, dodaj ich do roli **Twórca laboratorium**:

1. Na stronie **Konto laboratorium** wybierz pozycję **Kontrola dostępu (Zarządzanie dostępem i tożsamościami)** i kliknij pozycję **+ Dodaj przypisanie roli** na pasku narzędzi. 

    ![Kontrola dostępu -> przycisk Dodaj przypisanie roli](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na stronie **Dodawanie przypisania roli** wybierz pozycję **Twórca laboratorium** w obszarze **Rola**, wybierz użytkownika do dodania do roli Twórcy laboratorium, a następnie wybierz pozycję **Zapisz**. 

    ![Dodawanie twórcy laboratorium](../media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="specify-marketplace-images-available-to-lab-creators"></a>Określanie obrazów w portalu Marketplace dostępnych dla twórców laboratorium
Jako właściciel konta laboratorium możesz określić obrazy witryny Marketplace, których twórcy laboratorium mogą używać do tworzenia laboratoriów na koncie laboratorium. 

1. W menu po lewej stronie wybierz pozycję **Obrazy w portalu Marketplace**. Domyślnie zobaczysz pełną listę obrazów (włączonych i wyłączonych). Tę listę można filtrować, aby wyświetlić tylko obrazy włączone/wyłączone, wybierając z listy rozwijanej u góry opcję **Tylko włączone**/**Tylko wyłączone**. 
    
    ![Strona Obrazy w portalu Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    Obrazy witryny Marketplace wyświetlane na liście to tylko obrazy spełniające następujące warunki:
        
    - Tworzą jedną maszynę wirtualną
    - Używają usługi Azure Resource Manager do aprowizowania maszyn wirtualnych
    - Nie wymagają zakupu dodatkowego planu licencjonowania
2. Aby w portalu Marketplace **wyłączyć** obraz, który zostały włączony, wykonaj jedną z następujących czynności: 
    1. Wybierz pozycję **... (wielokropek)** w ostatniej kolumnie, a następnie polecenie **Wyłącz obraz**. 

        ![Wyłączanie jednego obrazu](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Wybierz jeden lub większą liczbę obrazów z listy przez zaznaczenie pól wyboru przed nazwami obrazów, a następnie wybierz pozycję **Wyłącz wybrane obrazy**. 

        ![Wyłączanie wielu obrazów](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. Podobnie, aby w portalu Marketplace **włączyć** obraz, wykonaj jedną z następujących czynności: 
    1. Wybierz pozycję **... (wielokropek)** w ostatniej kolumnie, a następnie polecenie **Włącz obraz**. 
    2. Wybierz jeden lub większą liczbę obrazów z listy przez zaznaczenie pól wyboru przed nazwami obrazów, a następnie wybierz pozycję **Włącz wybrane obrazy**. 

## <a name="configure-the-lab-account"></a>Konfigurowanie konta laboratorium
1. Na **konta laboratorium** wybierz opcję **konfiguracji Labs** w menu po lewej stronie.

    ![Strona konfiguracji Labs](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. Dla **równorzędna sieć wirtualna**, wybierz opcję **włączone** lub **wyłączone**. Wartość domyślna to **wyłączone**. Aby włączyć równorzędnej sieci wirtualnej, wykonaj następujące czynności: 
    1. Wybierz **włączone**.
    2. Wybierz **sieci wirtualnej** z listy rozwijanej. 
    3. Wybierz pozycję **Zapisz** na pasku narzędzi. 
    
        Laboratoria utworzony na tym koncie są podłączone do wybranej sieci wirtualnej. Mogą oni dostęp do zasobów w wybranej sieci wirtualnej. 
3. Dla **twórca laboratorium Zezwalaj, aby wybrać lokalizację lab**, wybierz opcję **włączone** chcącym twórca laboratorium, aby można było wybrać lokalizację dla laboratorium. Jeśli jest ono wyłączone, laboratoria są tworzone automatycznie w tej samej lokalizacji, w której istnieje konto laboratorium. 

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
Zobacz następujące artykuły:

- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium dostęp do laboratoriów na potrzeby zajęć](how-to-use-classroom-lab.md)