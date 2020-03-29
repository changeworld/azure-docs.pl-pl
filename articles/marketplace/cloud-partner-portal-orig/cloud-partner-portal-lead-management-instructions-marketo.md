---
title: Konfigurowanie zarządzania potencjalnymi klientami w Marketo | Azure Marketplace
description: Skonfiguruj zarządzanie potencjalnymi klientami dla marketo dla platformy Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 9fa05eae2d297cbd6ae7243d191cae5a7a3f990e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288533"
---
# <a name="configure-lead-management-in-marketo"></a>Konfigurowanie zarządzania potencjalnymi klientami w Marketo

W tym artykule opisano sposób konfigurowania marketo do obsługi potencjalnych klientów firmy Microsoft.

1. Zaloguj się do Marketo.
2. Wybierz **Design Studio**.
    ![Studio Projektowe Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Wybierz **pozycję Nowy formularz**.
    ![Marketo nowa forma](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Wypełnij wymagane pola w nowym formularzu, a następnie wybierz pozycję **Utwórz**.
    ![Marketo tworzenie nowego formularza](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  W obszarze Szczegóły pola wybierz pozycję **Zakończ**.
    ![Formularz wykończenia Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Zatwierdź i zamknij.

6.  Na karcie MarketplaceLeadBacked wybierz pozycję **Osadź kod**.
    ![Opcja kodu osadzania Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Marketo Embed Code wyświetla kod podobny do poniższego przykładu.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Skopiuj wartości wyświetlane w polu Kod osadzania, aby skonfigurować **identyfikator serwera**, **identyfikator Munchkin**i **identyfikator formularza** w polach Marketo w portalu Cloud Partner Portal.

Użyj następnego przykładu jako przewodnika do uzyskiwania identyfikatorów, których potrzebujesz w przykładzie Marketo Embed Code.

- Identyfikator serwera = **ys12**
- Munchkin Id = **123-PQR-789**
- Identyfikator formularza = **1179**
