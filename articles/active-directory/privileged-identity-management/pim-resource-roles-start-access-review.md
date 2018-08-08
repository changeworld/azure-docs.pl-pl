---
title: Przeprowadź przeglądów dostępu w ramach zasobów platformy Azure, używając Privileged Identity Management | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak rozpocząć Przegląd dostępu w Privileged Identity Management dla zasobów platformy Azure
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: f88c4a2f7e6eb569c9c0de33ab86e8b484a923e3
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622885"
---
# <a name="perform-access-reviews-in-azure-resources-by-using-privileged-identity-management"></a>Przeprowadź przeglądów dostępu w ramach zasobów platformy Azure, używając Privileged Identity Management
Przypisania ról stają się "starych", gdy użytkownicy mają uprzywilejowany dostęp, które nie potrzebują już. Aby zmniejszyć ryzyko, że jest skojarzona z te przypisania roli starych, Administratorzy ról uprzywilejowanych regularnie należy przejrzeć role. W tym dokumencie opisano kroki do uruchamiania przeglądu dostępu w Privileged Identity Management (PIM) dla zasobów platformy Azure.

Na stronie głównej aplikacji PIM przejdź do:

* **Przeglądów dostępu** > **Dodaj**

![Dodaj przeglądów dostępu](media/azure-pim-resource-rbac/rbac-access-review-home.png)

Po wybraniu **Dodaj** przycisku **tworzenie przeglądu dostępu** zostanie wyświetlony blok. W tym bloku Konfigurowanie przeglądu przy użyciu nazwy i limit czasu, wybierz rolę, aby przejrzeć i zdecydować, kto wykonuje przeglądu.

![Utwórz przegląd dostępu](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>Konfigurowanie przeglądu
Aby utworzyć przeglądu dostępu, najpierw nadaj jej nazwę, a następnie ustaw datę początkową i końcową.

![Konfigurowanie przeglądu — zrzut ekranu](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

Wprowadź długość przeglądu wystarczająco długi, służący do jego wykonania. Jeśli kończą się wcześniejsza od daty zakończenia, są zawsze zatrzymać przeglądu wcześnie.

### <a name="choose-a-role-to-review"></a>Wybierz rolę, aby zapoznać się z
Przejrzyj każdy koncentruje się na tylko jedną rolę. Jeśli nie rozpoczęto przeglądu dostępu z poziomu bloku konkretnej roli, musisz wybrać rolę teraz.

1. Przejdź do **przeglądanie członkostwa w roli**
   
    ![Przeglądanie członkostwa w roli — zrzut ekranu](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. Wybierz jedną rolę z listy.

### <a name="decide-who-will-perform-the-review"></a>Zdecyduj, który będzie wykonywać przeglądu
Istnieją trzy opcje dla przeprowadzania przeglądu. Przegląd można przypisać do kogoś innego, aby zakończyć, możesz zrobić to samodzielnie, lub każdy użytkownik może dokonać przeglądu własnego dostępu.

1. Wybierz jedną z opcji:
   
   * **Wybranych użytkowników**: Użyj tej opcji, kiedy nie wiesz, który wymaga dostępu. Po wybraniu tej opcji można przypisać przeglądu właściciel zasobu lub kierownik grupy, aby zakończyć.
   * **Przypisany (własne)**: Użyj tej opcji, aby użytkownicy mogli przejrzeć swoje własne przypisań ról.
   
2. Przejdź do **wybór recenzentów**.
   
    ![Wybór recenzentów — zrzut ekranu](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>Rozpocznij Przegląd
Na koniec można wymagać użytkowników podania przyczyny zatwierdzania dostępu. Jeśli chcesz dodać opis przeglądu. Następnie wybierz pozycję **Start**.

Upewnij się, możesz umożliwić użytkownikom wiedzieć, że jest oczekiwanie na ich przeglądu dostępu i pokazać je [jak przeprowadzić przegląd dostępu](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Zarządzanie przeglądu dostępu
Na pulpicie nawigacyjnym zasobów PIM Azure postęp można śledzić, jak recenzenci wykonać ich przeglądów. Nie prawa dostępu zostaną zmienione w katalogu, dopóki [zakończył przegląd](pim-resource-roles-complete-access-review.md).

Do momentu okres przeglądu za pośrednictwem można Przypomnij przeprowadzenie ich przeglądu lub Zakończ przegląd wcześnie w sekcji przeglądów dostępu.

