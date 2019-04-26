---
title: Rozpoczynanie przeglądu dostępu dla ról usługi Azure AD w usłudze PIM — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozpocząć Przegląd dostępu dla ról usługi Azure AD w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/21/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5cbf96c165d79c26985663ef5a9d64bbf8f9892
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60438003"
---
# <a name="start-an-access-review-for-azure-ad-roles-in-pim"></a>Rozpoczynanie przeglądu dostępu dla ról usługi Azure AD w usłudze PIM
Przypisania ról stają się "starych", gdy użytkownicy mają uprzywilejowany dostęp, które nie potrzebują już. W celu zmniejszenia ryzyka związanego z te przypisania roli starych ról uprzywilejowanych administratorów lub Administratorzy globalni należy regularnie tworzyć przeglądów dostępu, aby zadać Administratorzy, aby zapoznać się z ról, które użytkownicy nadali. W tym dokumencie opisano kroki do uruchamiania przeglądu dostępu w usłudze Azure Active Directory (Azure AD) Privileged Identity Management (PIM).

## <a name="start-an-access-review"></a>Rozpoczynanie przeglądu dostępu
> [!NOTE]
> Jeśli jeszcze nie została dodana aplikacji PIM do pulpitu nawigacyjnego w witrynie Azure portal, zobacz kroki w [wprowadzenie do usługi Azure Privileged Identity Management](pim-getting-started.md)
> 
> 

Na stronie głównej aplikacji PIM istnieją trzy sposoby uruchamiania przeglądu dostępu:

* **Przeglądów dostępu** > **Dodaj**
* **Role** > **przeglądu** przycisku
* Wybierz określoną rolę do przeglądu z listy ról > **przeglądu** przycisku

Po kliknięciu **Przejrzyj** przycisku **Rozpoczynanie przeglądu dostępu** zostanie wyświetlony blok. W tym bloku zamierzasz skonfigurować przeglądu przy użyciu nazwy i limit czasu, wybierz rolę, aby przejrzeć i zdecydować, kto będzie wykonywać przeglądu.

![Uruchamianie przeglądu dostępu — zrzut ekranu](./media/pim-how-to-start-security-review/PIM_start_review.png)

### <a name="configure-the-review"></a>Konfigurowanie przeglądu
Aby utworzyć przeglądu dostępu, musisz nadaj jej nazwę i Ustaw daty rozpoczęcia i zakończenia.

![Konfigurowanie przeglądu — zrzut ekranu](./media/pim-how-to-start-security-review/PIM_review_configure.png)

Wprowadź długość przeglądu wystarczająco długi, służący do jego wykonania. Jeśli zakończy się wcześniejsza od daty zakończenia, możesz zawsze zatrzymać przeglądu wcześnie.

### <a name="choose-a-role-to-review"></a>Wybierz rolę, aby zapoznać się z
Przejrzyj każdy koncentruje się na tylko jedną rolę. Jeśli nie rozpoczęto przeglądu dostępu z poziomu bloku konkretnej roli, musisz teraz wybierz rolę.

1. Przejdź do **przeglądanie członkostwa w roli**
   
    ![Przeglądanie członkostwa w roli — zrzut ekranu](./media/pim-how-to-start-security-review/PIM_review_role.png)
2. Wybierz jedną rolę z listy.

### <a name="decide-who-will-perform-the-review"></a>Zdecyduj, który będzie wykonywać przeglądu
Istnieją trzy opcje dla przeprowadzania przeglądu. Przegląd można przypisać do kogoś innego, aby zakończyć, możesz zrobić to samodzielnie lub może mieć dokonać przeglądu własnego dostępu.

1. Przejdź do **wybór recenzentów**
   
    ![Wybór recenzentów — zrzut ekranu](./media/pim-how-to-start-security-review/PIM_review_reviewers.png)
2. Wybierz jedną z opcji:
   
   * **Wybierz osoby dokonującej przeglądu**: Użyj tej opcji, kiedy nie wiesz, który wymaga dostępu. Po wybraniu tej opcji można przypisać przeglądu właściciel zasobu lub kierownik grupy, aby zakończyć.
   * **Mnie**: Parametr jest przydatne, jeśli chcesz obejrzeć, jak przeglądy dostępu w pracy lub, aby przejrzeć w imieniu osoby, które nie.
   * **Przejrzyj elementy członkowskie, samodzielnie**: Użyj tej opcji, aby użytkownicy mogli przejrzeć swoje własne przypisań ról.

### <a name="start-the-review"></a>Rozpocznij Przegląd
Na koniec masz możliwość wymagają od użytkowników podania przyczyny, jeśli udzielają dostępu. Jeśli chcesz dodać opis przeglądu, a następnie wybierz pozycję **Start**.

Upewnij się, możesz umożliwić użytkownikom wiedzieć, że jest oczekiwanie na ich przeglądu dostępu i pokazać je [jak przeprowadzić przegląd dostępu](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Zarządzanie przeglądu dostępu
Postęp można śledzić po recenzentów zakończeniu ich recenzje na pulpicie nawigacyjnym usługi Azure AD PIM w sekcji przeglądów dostępu. Nie prawa dostępu zostaną zmienione w katalogu, dopóki [zakończeniu przeglądu](pim-how-to-complete-review.md).

Do momentu okres przeglądu za pośrednictwem można Przypomnij przeprowadzenie ich przeglądu lub Zakończ przegląd wcześnie w sekcji przeglądów dostępu.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki

- [Kończenie przeglądu dostępu dla ról usługi Azure AD w usłudze PIM](pim-how-to-complete-review.md)
- [Wykonywanie przeglądu dostępu Moje ról usługi Azure AD w usłudze PIM](pim-how-to-perform-security-review.md)
- [Uruchamianie przeglądu dostępu dla ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-start-access-review.md)
