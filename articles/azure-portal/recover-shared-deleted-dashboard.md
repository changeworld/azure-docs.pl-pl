---
title: Odzyskaj usunięty pulpit nawigacyjny w Azure Portal | Microsoft Docs
description: Po usunięciu opublikowanego pulpitu nawigacyjnego w Azure Portal można odzyskać pulpit nawigacyjny.
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: ead9dab61e0cb60505aed9db43c61eb73a0e22a9
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760202"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Odzyskiwanie usuniętego pulpitu nawigacyjnego w Azure Portal

Po usunięciu _opublikowanego_ pulpitu nawigacyjnego w Azure Portal można odzyskać ten pulpit nawigacyjny w ciągu 14 dni od usunięcia. Jeśli pulpit nawigacyjny nie jest opublikowany, nie można go odzyskać i należy go ponownie skompilować. Aby uzyskać więcej informacji na temat publikowania pulpitu nawigacyjnego, zobacz [Publikowanie pulpitu nawigacyjnego](azure-portal-dashboard-share-access.md#publish-dashboard). Wykonaj następujące kroki, aby odzyskać opublikowany pulpit nawigacyjny:

1. Z menu Azure Portal wybierz pozycję **grupy zasobów**, a następnie wybierz grupę zasobów, w której opublikowano pulpit nawigacyjny (domyślnie są to nazwanych **pulpitów nawigacyjnych**).

1. W obszarze **Dziennik aktywności**rozwiń operację **Usuń pulpit nawigacyjny** . Wybierz kartę **historia zmian** , a następnie wybierz pozycję **\<usunięte zasoby\>** .

    ![Zrzut ekranu karty Historia zmian](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Wybierz i skopiuj zawartość okienka po lewej stronie, a następnie Zapisz w pliku tekstowym z rozszerzeniem _. JSON_ . Portal używa pliku JSON do ponownego utworzenia pulpitu nawigacyjnego.

    ![Zrzut ekranu różnic historii zmian](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. Z menu Azure Portal wybierz pozycję **pulpity nawigacyjne**, a następnie wybierz pozycję **Przekaż**.

    ![Zrzut ekranu przedstawiający przekazywanie pulpitu nawigacyjnego](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Wybierz zapisany plik JSON. Portal utworzy pulpit nawigacyjny z taką samą nazwą i elementami jak usunięty pulpit nawigacyjny.

1. Wybierz pozycję **Udostępnij** , aby opublikować pulpit nawigacyjny i ponownie nawiązać odpowiednią kontrolę dostępu.

    ![Zrzut ekranu przedstawiający udział pulpitu nawigacyjnego](media/recover-shared-deleted-dashboard/dashboard-share.png)
