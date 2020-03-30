---
title: Konfigurowanie zarządzania potencjalnymi klientami w Marketo | Azure Marketplace
description: Skonfiguruj zarządzanie potencjalnymi klientami dla marketo dla platformy Azure.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: dsindona
ms.openlocfilehash: 35d57d117f6308863965ffd789c0e28bedd0f301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281514"
---
# <a name="configure-lead-management-in-marketo"></a>Konfigurowanie zarządzania potencjalnymi klientami w Marketo

W tym artykule opisano, jak skonfigurować system Marketo CRM do przetwarzania potencjalnych klientów sprzedaży z oferty marketplace.

## <a name="set-up-your-marketo-crm-system"></a>Skonfiguruj swój system Marketo CRM

1. Zaloguj się do Marketo.
2. Wybierz **Design Studio**.
    ![Studio Projektowe Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  Wybierz **pozycję Nowy formularz**.
    ![Marketo nowa forma](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  Wypełnij wymagane pola w nowym formularzu, a następnie wybierz pozycję **Utwórz**.
    ![Marketo tworzenie nowego formularza](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  W obszarze Szczegóły pola wybierz pozycję **Zakończ**.
    ![Formularz wykończenia Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  Zatwierdź i zamknij.

7. Na karcie *MarketplaceLeadBacked* wybierz pozycję **Osadź kod**. 

    ![Kod osadzania](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. Marketo Embed Code wyświetla kod podobny do poniższego przykładu.

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. Skopiuj wartości poniższych pól wyświetlanych w formularzu Kod osadzania. Użyjesz tych wartości, aby skonfigurować ofertę do odbierania potencjalnych klientów w następnym kroku. Użyj następnego przykładu jako przewodnika do uzyskiwania identyfikatorów, których potrzebujesz w przykładzie Marketo Embed Code.

    - Identyfikator serwera = **ys12**
    - Identyfikator Munchkin = **123-PQR-789**
    - Identyfikator formularza = **1179**

    **Innym sposobem, aby dowiedzieć się tych wartości**

    - Identyfikator serwera znajduje się w adresie URL wystąpienia Marketo, na przykład "`serverID.marketo.com`".
    - Uzyskaj identyfikator Munching ID subskrypcji, przechodząc do menu Administrator>Munchkin w polu "Identyfikator konta Munchkin" lub z pierwszej części poddomeny hosta interfejsu Marketo REST: `https://{Munchkin ID}.mktorest.com`.
    - Identyfikator formularza to identyfikator formularza Embed Code utworzonego w kroku 7 w celu kierowania potencjalnych klientów z naszego rynku.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Skonfiguruj ofertę wysyłania potencjalnych klientów do Marketo

Gdy będziesz gotowy skonfigurować informacje o zarządzaniu potencjalnymi klientami dla oferty w portalu publikowania, wykonaj poniższe czynności: 

1. Przejdź do strony **Ustawienia oferty** dla swojej oferty.
1. Wybierz **pozycję Połącz** w sekcji Zarządzanie potencjalnymi klientami. 

    ![Zarządzanie potencjalnymi klientami - Połącz](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. W oknie podręcznym Szczegóły połączenia wybierz **pozycję Marketo** dla miejsca docelowego potencjalnego klienta.

    ![Wybieranie miejsca docelowego potencjalnego klienta](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. Podaj **identyfikator serwera,** **identyfikator konta munching**i **identyfikator formularza**.

    >[!Note]
    >Musisz zakończyć konfigurowanie pozostałej części oferty i opublikować ją, zanim będzie można odbierać potencjalnych klientów dla oferty. 

