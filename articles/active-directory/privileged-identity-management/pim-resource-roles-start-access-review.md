---
title: Wykonaj przeglądami dostępu w zasobów platformy Azure przy użyciu Privileged Identity Management | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak rozpocząć Przegląd dostępu w Privileged Identity Management zasobów Azure
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: b5c2f13386a996a6c7895bd4755b6cf609a5df72
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233258"
---
# <a name="perform-access-reviews-in-azure-resources-by-using-privileged-identity-management"></a>Wykonaj przeglądami dostępu w zasobów platformy Azure przy użyciu Privileged Identity Management
Przypisania ról nieodświeżone "", gdy użytkownicy mają uprzywilejowany dostęp, które nie wymagają już. Aby zmniejszyć ryzyko skojarzoną z tych przypisań ról starych, ról uprzywilejowanych powinni regularnie sprawdzić ról. W tym dokumencie opisano kroki do uruchamiania w zarządzania tożsamości uprzywilejowanych (PIM) Przegląd dostępu do zasobów platformy Azure.

Na stronie głównej aplikacji PIM przejdź do:

* **Dostęp do przeglądu** > **Dodaj**

![Dodaj przeglądami dostępu](media/azure-pim-resource-rbac/rbac-access-review-home.png)

Po wybraniu **Dodaj** przycisku **utworzyć Przegląd dostępu** zostanie wyświetlony blok. W tym bloku należy skonfigurować przeglądu przy użyciu nazwy i limit czasu, wybierz rolę, aby przejrzeć i zdecydować, kto wykonuje przeglądu.

![Utwórz przegląd dostępu](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>Skonfiguruj przeglądu
Aby utworzyć Przegląd dostępu, najpierw nadaj jej nazwę, a następnie ustaw daty początkową i końcową.

![Skonfiguruj przeglądu — zrzut ekranu](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

Należy długość przeglądu wystarczająco długo, użytkowników ją zakończyć. Jeśli kończyły się wcześniejsza od daty zakończenia one zawsze wyłączyć przeglądu wcześniej.

### <a name="choose-a-role-to-review"></a>Wybierz rolę do przeglądu
Każdego przeglądu koncentruje się na tylko jedną rolę. O ile nie został uruchomiony Przegląd dostępu bloku konkretnej roli, należy wybrać rolę teraz.

1. Przejdź do **Przejrzyj członkostwo roli**
   
    ![Przejrzyj członkostwo roli — zrzut ekranu](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. Wybierz jedną rolę z listy.

### <a name="decide-who-will-perform-the-review"></a>Zdecyduj, który przeprowadzi przeglądu
Dostępne są trzy opcje umożliwiające wykonywanie przeglądu. Przegląd można przypisać do innej osoby, aby zakończyć, możesz to zrobić samodzielnie lub każdy użytkownik może przeglądać własne dostępu.

1. Wybierz jedną z opcji:
   
   * **Wybranych użytkowników**: Użyj tej opcji, jeśli nie wiesz, który musi mieć dostęp. Po wybraniu tej opcji można przypisać do właściciela zasobów lub menedżera grupy do ukończenia przeglądu.
   * **Przypisany (samoobsługowego)**: Użyj tej opcji, aby przejrzeć swoje własne przypisań ról użytkowników.
   
2. Przejdź do **Wybierz recenzentów**.
   
    ![Wybierz osoby dokonujące przeglądu — zrzut ekranu](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>Uruchom przeglądu
Na koniec można wymagać podania Przyczyna zatwierdzania dostępu użytkowników. Jeśli chcesz dodać opis przeglądu. Następnie wybierz **Start**.

Upewnij się, że poinformować użytkowników, wiadomo, że jest przegląd dostępu oczekiwanie na ich i wyświetlić je [jak przeprowadzić przegląd dostępu](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Zarządzanie Przegląd dostępu
Na pulpicie nawigacyjnym zasobów PIM Azure postęp można śledzić w recenzentów zakończenia uwag. Nie prawa dostępu są zmieniane w katalogu do [zakończył przegląd](pim-resource-roles-complete-access-review.md).

Aż do okresu przeglądu Przypomnij użytkowników przeprowadzenie ich przeglądu, lub Zatrzymaj przeglądu wcześniej w sekcji przeglądami dostępu.

