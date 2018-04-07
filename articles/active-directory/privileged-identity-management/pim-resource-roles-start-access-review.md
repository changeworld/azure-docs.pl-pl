---
title: Uruchom Przegląd dostępu w PIM zasobów Azure | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak rozpocząć Przegląd dostępu w Privileged Identity Management zasobów Azure
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 61ed4e82e0b782b423668564dae6efb272967702
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---start-access-review"></a>Przegląd dostępu Start zarządzania - role zasobów - tożsamości uprzywilejowanych
Przypisania ról nieodświeżone "", gdy użytkownicy mają uprzywilejowany dostęp, które nie wymagają już. W celu zmniejszenia ryzyka związanego z tych przypisań ról starych, ról uprzywilejowanych powinni regularnie sprawdzić ról, którym przyznano użytkowników. W tym dokumencie opisano kroki do uruchamiania w zarządzania tożsamości uprzywilejowanych (PIM) Przegląd dostępu do zasobów platformy Azure.

Na stronie głównej aplikacji PIM przejdź do:

* **Dostęp do przeglądu** > **Dodaj**

![](media/azure-pim-resource-rbac/rbac-access-review-home.png)

Po kliknięciu **Dodaj** przycisku **utworzyć Przegląd dostępu** zostanie wyświetlony blok. W tym bloku użytkownik chce skonfigurować przeglądu przy użyciu nazwy i limit czasu, wybierz rolę, aby przejrzeć i zdecydować, kto będzie wykonywać przeglądu.

![](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>Skonfiguruj przeglądu
Aby utworzyć Przegląd dostępu, należy nadaj jej nazwę i ustawiania datę początkową i końcową.

![Skonfiguruj przeglądu — zrzut ekranu](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

Należy długość przeglądu wystarczająco długo, użytkowników ją zakończyć. Jeśli zakończy się wcześniejsza od daty zakończenia, można zawsze zatrzymać przeglądu wcześniej.

### <a name="choose-a-role-to-review"></a>Wybierz rolę do przeglądu
Każdego przeglądu koncentruje się na tylko jedną rolę. Chyba, że przegląd dostępu został uruchomiony z bloku konkretnej roli, należy wybrać rolę teraz.

1. Przejdź do **Przejrzyj członkostwo roli**
   
    ![Przejrzyj członkostwo roli — zrzut ekranu](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. Wybierz jedną rolę z listy.

### <a name="decide-who-will-perform-the-review"></a>Zdecyduj, który przeprowadzi przeglądu
Dostępne są trzy opcje umożliwiające wykonywanie przeglądu. Przegląd można przypisać do innej osoby, aby zakończyć, możesz zrobić to samodzielnie lub może mieć każdy użytkownik, Przejrzyj swoje własne dostępu.

1. Wybierz jedną z opcji:
   
   * **Wybranych użytkowników**: Użyj tej opcji, jeśli nie wiesz, który musi mieć dostęp. Po wybraniu tej opcji można przypisać do właściciela zasobów lub menedżera grupy do ukończenia przeglądu.
   * **Przypisany (samoobsługowego)**: Użyj tej opcji, aby przejrzeć swoje własne przypisań ról użytkowników.
   
2. Przejdź do **wybierz osób dokonujących przeglądu**
   
    ![Wybierz osoby dokonujące przeglądu — zrzut ekranu](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>Uruchom przeglądu
Na koniec masz opcję, aby wymagać podania użytkowników przyczynę Jeśli udzielają dostępu. Jeśli chcesz dodać opis przeglądu, a następnie wybierz **Start**.

Upewnij się, że poinformować użytkowników, wiadomo, że jest przegląd dostępu oczekiwanie na ich i wyświetlić je [jak przeprowadzić przegląd dostępu](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Zarządzanie Przegląd dostępu
Postęp można śledzić w recenzentów zakończenia ich przeglądami na pulpicie nawigacyjnym zasobów Azure PIM w sekcji przeglądami dostęp. Brak praw dostępu zostaną zmienione w katalogu do [zakończeniu przeglądu](pim-resource-roles-complete-access-review.md).

Aż do okresu przeglądu Przypomnij użytkowników przeprowadzenie ich przeglądu, lub Zatrzymaj przeglądu wcześniej w sekcji przeglądami dostępu.

