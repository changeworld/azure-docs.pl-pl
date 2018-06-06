---
title: Obsługa usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak plik żądania pomocy technicznej usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 84a2c6bd513fffa562b31070bebfbf2ee8e2d55d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712338"
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Usługi Azure Active Directory B2C: Żądania pomocy technicznej plik
Można pliku żądania pomocy technicznej dla usługi Azure Active Directory (Azure AD) B2C w portalu Azure, wykonując następujące czynności:

1. [Wykonaj następujące kroki, aby przejść do bloku funkcji B2C w portalu Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Przełącz z dzierżawy usługi B2C do innej dzierżawy, która ma subskrypcji platformy Azure skojarzonych z nim. Zazwyczaj jest dzierżawy pracownika lub domyślna dzierżawa utworzony podczas tworzenia subskrypcji platformy Azure. Aby dowiedzieć się więcej, zobacz [jak subskrypcji platformy Azure jest powiązana z usługą Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md).
   
    ![Obsługa - dzierżaw przełącznika](./media/active-directory-b2c-support/support-switch-dir.png)
3. Po przełączeniu dzierżawcy, kliknij przycisk **Pomoc i obsługa techniczna**.
   
    ![Obsługa — Pomoc i obsługa techniczna](./media/active-directory-b2c-support/support-support.png)
4. Kliknij przycisk **nowy obsługuje żądania**.
   
    ![Obsługa — nowy](./media/active-directory-b2c-support/support-new.png)
5. W **podstawy** bloku używać tych danych i kliknij przycisk **dalej**.
   
   * **Wystawianie typu** jest **techniczne**.
   * Wybierz odpowiedni **subskrypcji**.
   * **Usługa** jest **usługi Active Directory**.
   * Wybierz odpowiedni **plan pomocy technicznej**. Jeśli nie masz, możesz można założyć dla jednego [tutaj](https://azure.microsoft.com/support/plans/).
     
     ![Obsługa — podstawy](./media/active-directory-b2c-support/support-basics.png)
6. W **Problem** bloku używać tych danych i kliknij przycisk **dalej**.
   
   * Wybierz odpowiedni **ważność** poziom.
   * **Typ problemu** jest **B2C**.
   * Wybierz odpowiedni **kategorii**.
   * Opisz swój problem w **szczegóły** pola. Zawierają szczegółowe informacje, takie jak nazwa dzierżawy B2C, opis problemu, komunikaty o błędach, korelacji identyfikatorów (jeśli jest dostępny) i tak dalej.
   * W **okresie** Podaj datę i godzinę (w tym strefa czasowa) wystąpił problem.
   * W obszarze **przekazywania pliku**, Przekaż wszystkie zrzuty ekranu i pliki, które należy wziąć pod uwagę może pomóc w rozwiązaniu problemu.
     
     ![Obsługa — Problem](./media/active-directory-b2c-support/support-problem.png)
7. W **informacje kontaktowe** bloku, Dodaj swoje informacje kontaktowe. Kliknij przycisk **Utwórz**.
   
    ![Obsługa — kontakt](./media/active-directory-b2c-support/support-contact.png)
8. Po przesłaniu żądania obsługi, można go monitorować klikając **Pomoc i obsługa techniczna** na tablicy startowej, a następnie **Zarządzaj żądaniami obsługi**.

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>Znany problem: zgłoszenia żądania pomocy technicznej w kontekście dzierżawy B2C
Jeśli pominąć krok 2 opisanych powyżej i spróbuj utworzyć żądania obsługi w ramach dzierżawy usługi B2C, zostanie wyświetlony następujący błąd.

> [!IMPORTANT]
> Nie próbuj zalogowania się do nowej subskrypcji platformy Azure w dzierżawie usługi B2C.  
> 
> 

![Obsługa — Brak subskrypcji](./media/active-directory-b2c-support/support-no-sub.png)

