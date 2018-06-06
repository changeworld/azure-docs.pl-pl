---
title: Zarządzanie kontami laboratorium w usłudze Azure laboratorium Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć konto laboratorium, Wyświetl wszystkie konta laboratorium i usuwania konta laboratorium w subskrypcji platformy Azure.
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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 6039ea482b0968d48fc21ff3dfec82a2ff0db43d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715330"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Zarządzanie kontami laboratorium w usłudze Azure Services laboratorium 
W usługach Azure laboratorium konta laboratorium jest kontenerem w laboratoriach zarządzanych, takich jak laboratoria klasy. Administrator konfiguruje konto laboratorium z usług Azure laboratorium i zapewnia dostęp do laboratorium właścicieli, którzy mogą tworzyć laboratoriów w ramach konta. W tym artykule opisano, jak utworzyć konto laboratorium, wyświetlania wszystkich kont laboratorium lub usunąć konto laboratorium.

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
Aby przyznać nauczycielom uprawnienie do tworzenia laboratoriów na potrzeby zajęć, dodaj ich do roli Twórca laboratorium:

1. Na stronie **Konto laboratorium** wybierz pozycję **(Kontrola dostępu (IAM))** i kliknij pozycję **+ Dodaj** na pasku narzędzi. 

    ![Strona konta laboratorium](../media/tutorial-setup-lab-account/access-control.png)
2. Na stronie **Dodawanie uprawnień** wybierz pozycję **Twórca laboratorium** w obszarze **Rola**, wybierz użytkownika do dodania do roli Twórcy laboratorium, a następnie wybierz pozycję **Zapisz**. 

    ![Dodawanie użytkownika do roli twórcy laboratorium](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="view-lab-accounts"></a>Wyświetlanie kont laboratorium
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkie zasoby** z menu. 
3. Wybierz **usługi laboratorium** dla **typu**. 
    Można również filtrować według subskrypcji, grupy zasobów, lokalizacji i tagów. 

## <a name="delete-a-lab-account"></a>Usuwanie konta laboratorium
Wykonaj instrukcje z poprzedniej sekcji, która wyświetla na liście kont laboratorium. Użyj poniższych instrukcji, aby usunąć konto laboratorium: 

1. Wybierz **konta laboratorium** przewidzianą do usunięcia. 
2. Wybierz **usunąć** z paska narzędzi. 
3. Typ **tak** o potwierdzenie.
4. Wybierz pozycję **Usuń**. 

## <a name="next-steps"></a>Kolejne kroki
Wprowadzenie do konfigurowania laboratorium przy użyciu usługi Azure Lab Services:

- [Konfigurowanie laboratorium na potrzeby zajęć](tutorial-setup-classroom-lab.md)
- [Konfigurowanie laboratorium](../tutorial-create-custom-lab.md)
