---
title: Konfigurowanie zarządzania liderem w usłudze Salesforce | Portal Azure Marketplace
description: Skonfiguruj zarządzanie potencjalnymi klientami w usłudze Salesforce dla klientów portalu Azure Marketplace.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: bdd3bb50fc69083c73eb01a84bf0fb0db82a8a65
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812152"
---
# <a name="configure-lead-management-for-salesforce"></a>Konfigurowanie zarządzania liderem w usłudze Salesforce

W tym artykule opisano sposób konfigurowania systemu Salesforce do przetwarzania potencjalnych klientów z oferty w portalu Marketplace.

## <a name="set-up-your-salesforce-system"></a>Konfigurowanie systemu Salesforce

1. Zaloguj się do usługi Salesforce.
2. Jeśli używasz środowiska oświetlenia usługi Salesforce.
    1. Wybierz pozycję **Instalator** ze strony głównej usługi Salesforce.
    ![](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png) instalacji usługi Salesforce

    1. Na stronie Instalator przejdź do okienka nawigacji po lewej stronie, aby uzyskać dostęp do **narzędzi platformy — > ustawienia funkcji — > Marketing-> sieć Web-potencjalny klient**.
    ![](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png) sieci Web do potencjalnego klienta usługi Salesforce

3. Jeśli korzystasz z klasycznego środowiska Salesforce:
    1. Wybierz pozycję **Instalator** ze strony głównej usługi Salesforce.
    ![klasycznej konfiguracji usługi Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Na stronie Instalator przejdź do okienka nawigacji po lewej stronie, aby **skompilować > dostosowywanie > potencjalni klienci > sieci Web do potencjalnego klienta**.
    ![klasyczne](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png) sieci Web dla usługi Salesforce

Pozostałe instrukcje są takie same, niezależnie od tego, którego środowiska usługi Salesforce używasz.

4. Na **stronie Konfiguracja sieci Web-potencjalny klient**wybierz przycisk **Utwórz formularz sieci Web-potencjalny klient** .
5. W obszarze **Konfiguracja sieci Web-klient**wybierz pozycję **Utwórz formularz sieć Web-potencjalny klient**.
    ![Salesforce — konfiguracja sieci Web-klient](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. Na **formularzu Utwórz sieć Web-potencjalny klient**upewnij się, że ustawienie `the Include reCAPTCHA in HTML` nie jest zaznaczone, a następnie wybierz pozycję **Generuj**. 
    ![Salesforce — Tworzenie formularza sieci Web do potencjalnego klienta](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. Zostanie wyświetlony jakiś tekst HTML. Wyszukaj tekst "OID" i skopiuj **wartość identyfikatora OID** z tekstu HTML (tylko tekst między znakami cudzysłowu) i Zapisz go. Ta wartość zostanie wklejona w polu **identyfikator organizacji** w portalu wydawców.
    ![Salesforce — Tworzenie formularza sieci Web do potencjalnego klienta](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. Wybrano **Zakończono**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Konfigurowanie oferty do wysyłania potencjalnych klientów do usługi Salesforce

Gdy wszystko jest gotowe do skonfigurowania informacji dotyczących zarządzania potencjalnym liderem oferty w portalu wydawców, wykonaj następujące czynności:

1. Przejdź do strony **Konfiguracja oferty** dla swojej oferty.
1. Wybierz pozycję **Połącz** w sekcji Zarządzanie potencjalnymi klientami.
    ![zarządzanie potencjalnymi klientami — łączenie](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. W oknie podręcznym szczegóły połączenia wybierz pozycję **Salesforce** dla **miejsca docelowego potencjalnego klienta** i wklej w `oid` z formularza Web-to-potencjalny klient utworzony w ramach wcześniejszych kroków w polu **identyfikator organizacji** .

1. Wybierz pozycję **Zapisz**. 

    >[!Note]
    >Musisz zakończyć konfigurowanie reszty oferty i opublikować ją przed odebraniem potencjalnych klientów do oferty.

    ![Szczegóły połączenia — wybierz miejsce docelowe potencjalnego klienta](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

    ![Szczegóły połączenia — wybierz miejsce docelowe potencjalnego klienta](./media/commercial-marketplace-lead-management-instructions-salesforce/connection-details.png)