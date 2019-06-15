---
title: Skonfiguruj zarządzanie potencjalnymi klientami w Marketo | Portal Azure Marketplace
description: Skonfiguruj zarządzanie potencjalnymi klientami dla usługi Marketo dla klientów w witrynie Azure marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: d749a92a1837bad053dc586477bfc27fc65299fa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935198"
---
# <a name="configure-lead-management-in-marketo"></a>Skonfiguruj zarządzanie potencjalnymi klientami w programu Marketo

W tym artykule opisano sposób konfigurowania programu Marketo, do obsługi potencjalnych klientów firmy Microsoft.

1. Zaloguj się do usługi Marketo.
2. Wybierz **projektowania Studio**.
    ![Marketo Design Studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Wybierz **nowy formularz**.
    ![Nowy formularz programu Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Wypełnij wymagane pola w nowym formularzu, a następnie wybierz pozycję **Utwórz**.
    ![Marketo Tworzenie nowego formularza](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  Szczegóły pola wybierz **Zakończ**.
    ![Formularz zakończenia programu Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Zatwierdź i zamknij.

6.  Na karcie MarketplaceLeadBacked wybierz **kodu osadzania**.
    ![Marketo Osadź kod — opcja](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Kod osadzania usługi Marketo Wyświetla kod podobny do poniższego przykładu.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Skopiuj wartości widocznych na kod osadzania, dzięki czemu możesz skonfigurować **identyfikator serwera**, **identyfikator Munchkin**, i **identyfikator formularza** w portalu Cloud Partner pola programu Marketo.

Na użytek następnym przykładzie jako przewodnik wprowadzenie identyfikatorów, o których należy z przykładu kodu osadzania usługi Marketo.

- Identyfikator serwera = **ys12**
- Munchkin Id = **123-PQR-789**
- Identyfikator formularza = **1179**\
