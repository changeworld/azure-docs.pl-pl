---
title: Konfigurowanie zarządzania liderem w programie Marketo | Portal Azure Marketplace
description: Skonfiguruj zarządzanie potencjalnymi klientami dla programu Marketo dla klientów portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 7949507c8c7ef57cded25cde8579c1945aa93a81
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825213"
---
# <a name="configure-lead-management-in-marketo"></a>Konfigurowanie zarządzania potencjalnymi klientami w programie Marketo

W tym artykule opisano sposób konfigurowania programu Marketo do obsługi potencjalnych klientów sprzedaży firmy Microsoft.

1. Zaloguj się do programu Marketo.
2. Wybierz pozycję **projekt Studio**.
    ![projekt Market](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Wybierz pozycję **nowy formularz**.
    nowy formularz ![Market](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Wypełnij wymagane pola w nowym formularzu, a następnie wybierz pozycję **Utwórz**.
    ![Marketo Utwórz nowy formularz](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  W polu Szczegóły pola wybierz pozycję **Zakończ**.
    formularz zakończenia ![Marketa](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Zatwierdź i Zamknij.

6.  Na karcie MarketplaceLeadBacked wybierz pozycję **Osadź kod**.
    ![opcji kodu osadzania programu Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Kod osadzania programu Marketo wyświetla kod podobny do poniższego przykładu.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Skopiuj wartości wyświetlane w polu osadź kod, aby skonfigurować **Identyfikator serwera**, **Identyfikator Munchkin**i **Identyfikator formularza** w polach Marketo na portal Cloud partner.

W następnym przykładzie przedstawiono Przewodnik dotyczący uzyskiwania wymaganych identyfikatorów z przykładu kodu osadzania programu Marketo.

- Identyfikator serwera = **ys12**
- Identyfikator Munchkin = **123-PQR-789**
- Identyfikator formularza = **1179**\
