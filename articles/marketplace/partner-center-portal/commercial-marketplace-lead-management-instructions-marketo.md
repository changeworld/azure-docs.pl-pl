---
title: Konfigurowanie zarządzania liderem w programie Marketo | Portal Azure Marketplace
description: Skonfiguruj zarządzanie potencjalnymi klientami dla programu Marketo dla klientów portalu Azure Marketplace.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 8d13e8c3aeabf6d3fdea80ffddbae47b80adc139
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812134"
---
# <a name="configure-lead-management-in-marketo"></a>Konfigurowanie zarządzania potencjalnymi klientami w programie Marketo

W tym artykule opisano, jak skonfigurować system CRM dla programu Marketo, aby przetwarzać potencjalni klienci z oferty w portalu Marketplace.

## <a name="set-up-your-marketo-crm-system"></a>Skonfiguruj system CRM dla programu Marketo

1. Zaloguj się do programu Marketo.
2. Wybierz pozycję **projekt Studio**.
    ![projekt Market](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  Wybierz pozycję **nowy formularz**.
    nowy formularz ![Market](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  Wypełnij wymagane pola w nowym formularzu, a następnie wybierz pozycję **Utwórz**.
    ![Marketo Utwórz nowy formularz](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  W polu Szczegóły pola wybierz pozycję **Zakończ**.
    formularz zakończenia ![Marketa](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  Zatwierdź i Zamknij.

7. Na karcie *MarketplaceLeadBacked* wybierz pozycję **Osadź kod**. 

    ![Osadź kod](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. Kod osadzania programu Marketo wyświetla kod podobny do poniższego przykładu.

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. Skopiuj wartości poniższych pól wyświetlanych w formularzu kodu osadzania. Te wartości zostaną użyte do skonfigurowania oferty, aby otrzymywać potencjalni klienci w następnym kroku. W następnym przykładzie przedstawiono Przewodnik dotyczący uzyskiwania wymaganych identyfikatorów z przykładu kodu osadzania programu Marketo.

    - Identyfikator serwera = **ys12**
    - Identyfikator Munchkin = **123-PQR-789**
    - Identyfikator formularza = **1179**

    **Inny sposób ustalenia tych wartości**

    - Identyfikator serwera znajduje się w adresie URL wystąpienia programu Marketo, na przykład "`serverID.marketo.com`".
    - Aby uzyskać identyfikator munching subskrypcji, przejdź do menu Administrator > Munchkin w polu "Identyfikator konta Munchkin" lub z pierwszej części poddomeny hosta interfejsu API REST usługi Marketo: `https://{Munchkin ID}.mktorest.com`.
    - Identyfikator formularza jest IDENTYFIKATORem formularza kodu osadzania utworzonego w kroku 7 w celu kierowania potencjalnych klientów z portalu Marketplace.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Konfigurowanie oferty do wysyłania potencjalnych klientów do programu Marketo

Gdy wszystko jest gotowe do skonfigurowania informacji dotyczących zarządzania potencjalnym liderem oferty w portalu wydawców, wykonaj następujące czynności: 

1. Przejdź do strony **Konfiguracja oferty** dla swojej oferty.
1. Wybierz pozycję **Połącz** w sekcji Zarządzanie potencjalnymi klientami. 

    ![Zarządzanie potencjalnymi klientami — łączenie](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. W oknie podręcznym szczegóły połączenia wybierz pozycję **Marketo** dla miejsca docelowego potencjalnego klienta.

    ![Wybierz miejsce docelowe potencjalnego klienta](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. Podaj **Identyfikator serwera**, **Identyfikator konta munching**i **Identyfikator formularza**.

    >[!Note]
    >Musisz zakończyć konfigurowanie reszty oferty i opublikować ją przed odebraniem potencjalnych klientów do oferty. 

