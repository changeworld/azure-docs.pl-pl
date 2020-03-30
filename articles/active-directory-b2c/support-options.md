---
title: Obsługa usługi Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Jak składać żądania pomocy technicznej dla usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5195241003b1ce4ea505002e2cc3c10410e6cde1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183724"
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Usługa Azure Active Directory B2C: żądania obsługi plików
Żądania pomocy technicznej dla usługi Azure Active Directory B2C (Azure AD B2C) można składać w witrynie Azure Portal, wykonując następujące kroki:

1. Przełącz się z dzierżawy usługi B2C do innej dzierżawy, która ma skojarzoną z nią subskrypcję platformy Azure. Zazwyczaj ten ostatni jest dzierżawy pracownika lub domyślnej dzierżawy utworzonej dla Ciebie podczas konfigurowania się do subskrypcji platformy Azure. Aby dowiedzieć się więcej, zobacz, [jak subskrypcja platformy Azure jest powiązana z usługą Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

    ![Portal Azure z wyróżnionym wyborem dzierżawy](./media/support-options/support-switch-dir.png)

1. Po przełączeniu dzierżawy kliknij przycisk **Pomoc + obsługa .**

    ![Kafelek Pomoc + Pomoc wyróżniona w witrynie Azure portal](./media/support-options/support-support.png)

1. Kliknij **pozycję Nowe żądanie pomocy technicznej**.

    ![Nowy kafelek żądania pomocy technicznej wyróżniony w witrynie Azure portal](./media/support-options/support-new.png)

1. W bloku **Podstawy** użyj tych szczegółów i kliknij przycisk **Dalej**.

    * **Typ problemu** to **Techniczne**.
    * Wybierz odpowiednią **subskrypcję**.
    * **Usługa** to **Active Directory**.
    * Wybierz odpowiedni **plan pomocy technicznej**. Jeśli go nie masz, możesz zapisać się na jeden [tutaj](https://azure.microsoft.com/support/plans/).

     ![Strona Podstawy z wyróżnionym przyciskiem Dalej w witrynie Azure Portal](./media/support-options/support-basics.png)

1. W bloku **Problem** użyj tych szczegółów i kliknij przycisk **Dalej**.

    * Wybierz odpowiedni poziom **ważności.**
    * **Typ problemu** to **B2C**.
    * Wybierz odpowiednią **kategorię**.
    * Opisz problem w polu **Szczegóły.** Podaj szczegóły, takie jak nazwa dzierżawy B2C, opis problemu, komunikaty o błędach, identyfikatory korelacji (jeśli są dostępne) i tak dalej.
    * W polu **Przedział czasu** podaj datę i godzinę (w tym strefę czasową), która wystąpiła.
    * W **obszarze Przesyłanie plików**prześlij wszystkie zrzuty ekranu i pliki, które uważasz, że pomogą w rozwiązaniu problemu.

     ![Strona problemowa z wyróżnionym przyciskiem Dalej w witrynie Azure Portal](./media/support-options/support-problem.png)

1. W bloku **Informacje kontaktowe** dodaj informacje kontaktowe. Kliknij przycisk **Utwórz**.

    ![Strona informacji kontaktowych z wyróżnionym przyciskiem Utwórz w portalu](./media/support-options/support-contact.png)

1. Po przesłaniu żądania pomocy technicznej można go monitorować, klikając **przycisk Pomoc + pomoc na** tablicy startowej, a następnie **zarządzaj żądaniami pomocy technicznej**.

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>Znany problem: Składanie żądania pomocy technicznej w kontekście dzierżawy B2C

Jeśli pominięto krok 2 opisane powyżej i spróbuj utworzyć żądanie pomocy technicznej w kontekście dzierżawy B2C, zobaczysz następujący błąd.

> [!IMPORTANT]
> Nie próbuj zarejestrować się w nowej subskrypcji platformy Azure w dzierżawie usługi B2C.

![W witrynie Azure Portal nie jest wyświetlany błąd subskrypcji](./media/support-options/support-no-sub.png)
