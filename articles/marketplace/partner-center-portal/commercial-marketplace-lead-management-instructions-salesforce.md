---
title: Konfigurowanie zarządzania potencjalnymi klientami dla Salesforce | Azure Marketplace
description: Konfigurowanie zarządzania potencjalnymi klientami w salesforce dla klientów w portalu Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 087cdafe8b819e4929e1608ed7e00be2c1169414
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263098"
---
# <a name="configure-lead-management-for-salesforce"></a>Konfigurowanie zarządzania potencjalnymi klientami dla Salesforce

W tym artykule opisano sposób konfigurowania systemu Salesforce do przetwarzania potencjalnych klientów z oferty komercyjnego rynku.

> [!Note]
> Marketplace nie obsługuje wstępnie wypełnionych list, takich jak lista wartości dla pola **Kraj.** Przed kontynuowaniem upewnij się, że nie skonfigurowane są żadne listy. Alternatywnie można skonfigurować [punkt końcowy HTTPS](./commercial-marketplace-lead-management-instructions-https.md) lub [tabelę platformy Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) do odbierania potencjalnych klientów.

## <a name="set-up-your-salesforce-system"></a>Konfigurowanie systemu Salesforce

1. Zaloguj się do Salesforce.
2. Jeśli korzystasz z środowiska oświetlenia Salesforce.
    1. Wybierz **pozycję Ustawienia** ze strony głównej salesforce.
    ![Konfiguracja salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Na stronie Ustawienia przejdź przez lewą nawigację do **platform Narzędzia >Ustawienia funkcji >Marketing->Web-to-Lead**.

        ![Salesforce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. Jeśli korzystasz z usługi Salesforce Classic:
    1. Wybierz **pozycję Ustawienia** ze strony głównej salesforce.
    ![Konfiguracja klasyczna Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Na stronie Ustawienia przejdź przez lewą nawigację do **build ->Customize->Leads->Web-to-Lead**.

        ![Salesforce klasyczny web-to-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

Pozostałe instrukcje są takie same, niezależnie od tego, którego doświadczenia Salesforce używasz.

4. Na **stronie Ustawienia web-to-lead**wybierz przycisk **Utwórz formularz web-to-lead.**
5. W **ustawieniach web-to-lead**wybierz pozycję **Utwórz formularz Web-to-Lead**.
    ![Salesforce — konfiguracja sieci Web do potencjalnych osób](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. W formularzu **Utwórz formularz web-to-lead**upewnij się, że `the Include reCAPTCHA in HTML` ustawienie jest niezaznaczone i wybierz pozycję **Generuj**. 
    ![Salesforce — tworzenie formularza web-to-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. Zostanie wyświetlony tekst HTML. Wyszukaj tekst "oid" i skopiuj **wartość oid** z tekstu HTML (tylko tekst pomiędzy cudzysłowami) i zapisz go. Tę wartość wkleisz w polu **Identyfikator organizacji** w portalu publikowania.
    ![Salesforce — tworzenie formularza web-to-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. Wybrane **zaznaczone zakończone**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Konfigurowanie oferty do wysyłania potencjalnych klientów do Salesforce

Gdy będziesz gotowy skonfigurować informacje o zarządzaniu potencjalnymi klientami dla oferty w portalu publikowania, wykonaj poniższe czynności:

1. Przejdź do strony **Ustawienia oferty** dla swojej oferty.
1. Wybierz **pozycję Połącz** w sekcji Zarządzanie potencjalnymi klientami.
    ![Zarządzanie potencjalnymi klientami - Połącz](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. W oknie podręcznym Szczegóły połączenia wybierz pozycję **Salesforce** dla miejsca `oid` **docelowego potencjalnego klienta** i wklej formularz z sieci Web do potencjalnego klienta, który został utworzony, wykonując wcześniejsze kroki w polu Identyfikator **organizacji.**

1. **Kontaktowy adres e-mail** — udostępniaj wiadomości e-mail osobom w firmie, które powinny otrzymywać powiadomienia e-mail po otrzymaniu nowego potencjalnego klienta. Możesz dostarczyć wiele wiadomości e-mail, oddzielając je średnikiem.

1. Wybierz **ok**.

Aby upewnić się, że udało ci się połączyć z miejscem docelowym potencjalnego klienta, kliknij przycisk sprawdź poprawność. Jeśli się powiedzie, będziesz miał potencjalnego klienta testowego w głównym miejscu docelowym.

>[!Note]
>Musisz zakończyć konfigurowanie pozostałej części oferty i opublikować ją, zanim będzie można odbierać potencjalnych klientów dla oferty.

![Szczegóły połączenia — wybieranie miejsca docelowego potencjalnego klienta](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

![Szczegóły połączenia — wybieranie miejsca docelowego potencjalnego klienta](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)
