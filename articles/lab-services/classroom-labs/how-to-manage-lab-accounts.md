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
ms.date: 11/15/2018
ms.author: spelluru
ms.openlocfilehash: 20412efac553458f3028f873bcc6d918a673f261
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52838821"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Zarządzanie kontami laboratorium w usłudze Azure Lab Services 
W usługach Azure Lab Services konta laboratorium jest kontenerem dla zarządzanych laboratoriów, takich jak laboratorium na potrzeby zajęć. Administrator konfiguruje konta laboratorium przy użyciu usługi Azure Lab Services i zapewnia dostęp do laboratorium właścicieli, którzy mogą tworzyć laboratoriów w ramach konta. W tym artykule opisano sposób tworzenia konta laboratorium, są wyświetlane wszystkie konta laboratorium lub usuwanie konta laboratorium.

## <a name="create-a-lab-account"></a>Tworzenie konta laboratorium
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Z menu głównego po lewej stronie wybierz polecenie **Utwórz zasób**.
3. Wyszukaj pozycję **Lab Services** w witrynie Azure Marketplace, a następnie wybierz pozycję **Lab Services** z listy rozwijanej. 
4. Wybierz pozycję **Lab Services (wersja zapoznawcza)** z przefiltrowanej listy usług. 
5. W oknie **Tworzenie konta laboratorium** wybierz pozycję **Utwórz**.
7. W oknie **Konto laboratorium** wykonaj następujące czynności: 
    1. W obszarze **Nazwa konta laboratorium** wprowadź nazwę. 
    2. Wybierz **subskrypcję platformy Azure**, w której chcesz utworzyć konto laboratorium.
    3. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** i wprowadź nazwę grupy zasobów.
    4. W obszarze **Lokalizacja** wybierz lokalizację/region, w których chcesz utworzyć konto laboratorium. 
    5. Wybierz pozycję **Utwórz**. 

        ![Okno Tworzenie konta laboratorium](../media/how-to-manage-lab-accounts/lab-account-settings.png)
5. Jeśli nie widzisz strony konta laboratorium, wybierz przycisk **powiadomień**, a następnie kliknij przycisk **Przejdź do zasobu** w obszarze powiadomień. 

    ![Okno Tworzenie konta laboratorium](../media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. Zobaczysz następującą stronę **konta laboratorium**:

    ![Strona konta laboratorium](../media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Dodawanie użytkownika do roli twórcy laboratorium
Aby skonfigurować laboratorium na potrzeby zajęć w ramach konta laboratorium, użytkownik musi być członkiem roli **Twórca laboratorium** na koncie laboratorium. Konto, którego użyto do utworzenia konta laboratorium, jest automatycznie dodawane do tej roli. Jeśli planujesz użyć tego samego konta użytkownika w celu utworzenia laboratorium na potrzeby zajęć, możesz pominąć ten krok. Aby użyć innego konta użytkownika do utworzenia laboratorium na potrzeby zajęć, wykonaj następujące czynności: 

1. Na **konta laboratorium** wybierz opcję **kontrola dostępu (IAM)** i kliknij przycisk **+ Dodaj przypisanie roli** na pasku narzędzi. 
2. Na stronie **Dodawanie uprawnień** wybierz pozycję **Twórca laboratorium** w obszarze **Rola**, wybierz użytkownika do dodania do roli Twórcy laboratorium, a następnie wybierz pozycję **Zapisz**.

## <a name="specify-marketplace-images-available-to-lab-owners"></a>Określanie obrazów w portalu Marketplace dostępnych dla właścicieli laboratorium
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

## <a name="view-lab-accounts"></a>Wyświetl konta laboratorium
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkie zasoby** z menu. 
3. Wybierz **Lab Services** dla **typu**. 
    Można również filtrować według subskrypcji, grupy zasobów, lokalizacje i tagów. 

## <a name="delete-a-lab-account"></a>Usuwanie konta laboratorium
Wykonaj instrukcje z poprzedniej sekcji, która wyświetla kontami laboratorium na liście. Użyj poniższych instrukcji, aby usunąć konto laboratorium: 

1. Wybierz **konta laboratorium** , którą chcesz usunąć. 
2. Wybierz **Usuń** na pasku narzędzi. 
3. Typ **tak** o potwierdzenie.
4. Wybierz pozycję **Usuń**. 

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



## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły:

- [As a lab owner, create and manage labs (Tworzenie laboratoriów i zarządzanie nimi jako właściciel laboratorium)](how-to-manage-classroom-labs.md)
- [As a lab owner, set up and publish templates (Konfigurowanie i tworzenie szablonów jako właściciel laboratorium)](how-to-create-manage-template.md)
- [As a lab owner, configure and control usage of a lab (Konfigurowanie i kontrolowanie użycia laboratorium jako właściciel laboratorium)](how-to-configure-student-usage.md)
- [Jako użytkownik laboratorium dostęp do laboratoriów na potrzeby zajęć](how-to-use-classroom-lab.md)