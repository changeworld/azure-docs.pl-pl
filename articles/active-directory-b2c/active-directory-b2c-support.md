---
title: Obsługa usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak zgłosić żądania pomocy technicznej dla usługi Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ae5d04b7fac89cefb40d5542e2c6e83533afcfce
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835770"
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Azure Active Directory B2C: Plik żądania pomocy technicznej
Mogą zgłaszać żądania pomocy technicznej dla usługi Azure Active Directory (Azure AD) B2C w witrynie Azure portal wykonując następujące czynności:

1. Przełącz się z dzierżawą B2C do innej dzierżawy, który ma subskrypcję platformy Azure skojarzone z nią. Zazwyczaj jest Twoja pracownika lub dzierżawą domyślne tworzone podczas tworzenia konta dla subskrypcji platformy Azure. Aby dowiedzieć się więcej, zobacz [jak związane z subskrypcją platformy Azure do usługi Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

    ![Witryna Azure portal z zaznaczenia dzierżawy wyróżniony](./media/active-directory-b2c-support/support-switch-dir.png)

3. Po przełączeniu dzierżaw, kliknij przycisk **Pomoc i obsługa techniczna**.

    ![Pomoc i obsługa kafelka wyróżnione w witrynie Azure portal](./media/active-directory-b2c-support/support-support.png)

4. Kliknij przycisk **nowe żądanie obsługi**.

    ![Nowy Kafelek żądania pomocy technicznej, wyróżnioną pozycją w witrynie Azure portal](./media/active-directory-b2c-support/support-new.png)
5. W **podstawy** bloku używać tych danych i kliknij przycisk **dalej**.

   * **Typ problemu** jest **techniczne**.
   * Wybierz odpowiedni **subskrypcji**.
   * **Usługa** jest **usługi Active Directory**.
   * Wybierz odpowiedni **plan pomocy technicznej**. Jeśli nie masz, możesz zasubskrybować jeden [tutaj](https://azure.microsoft.com/support/plans/).

     ![Podstawowe informacje o stronie z wyróżnionym dalej w witrynie Azure portal](./media/active-directory-b2c-support/support-basics.png)
6. W **Problem** bloku używać tych danych i kliknij przycisk **dalej**.

   * Wybierz odpowiedni **ważność** poziom.
   * **Typ problemu** jest **B2C**.
   * Wybierz odpowiedni **kategorii**.
   * Opisz swój problem w **szczegóły** pola. Podaj szczegóły, takie jak nazwa dzierżawy B2C, opis problemu, komunikaty o błędach, korelacji identyfikatorów (jeśli jest dostępny) i tak dalej.
   * W **przedział czasu** , podaj datę i godzinę (takie jak strefy czasowej), który wystąpił problem.
   * W obszarze **przekazywanie pliku**Przekaż wszystkie zrzuty ekranu i pliki, które prawdopodobnie będą pomocne przy rozwiązywaniu problemu.

     ![Strona problem z wyróżnionym dalej w witrynie Azure portal](./media/active-directory-b2c-support/support-problem.png)
7. W **informacje kontaktowe** bloku Dodaj swoje informacje kontaktowe. Kliknij przycisk **Utwórz**.

    ![Strona informacji kontaktowych z wyróżnionym Utwórz w witrynie Azure portal](./media/active-directory-b2c-support/support-contact.png)
8. Po przesłaniu żądania pomocy technicznej, można go monitorować, klikając **Pomoc i obsługa techniczna** na tablicy startowej, a następnie **Zarządzaj żądaniami obsługi**.

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>Znany problem: Zgłaszanie żądania pomocy technicznej w ramach dzierżawy usługi B2C
Jeśli brakuje kroku 2 powyżej i spróbuj utworzyć żądanie pomocy technicznej w kontekście swojej dzierżawy usługi B2C, zostanie wyświetlony następujący błąd.

> [!IMPORTANT]
> Nie próbuj w celu uzyskania nowej subskrypcji platformy Azure w ramach dzierżawy usługi B2C.
>
>

![Pomoc techniczna — Brak subskrypcji](./media/active-directory-b2c-support/support-no-sub.png)

