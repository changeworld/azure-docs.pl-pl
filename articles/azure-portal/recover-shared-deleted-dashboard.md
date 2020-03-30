---
title: Odzyskiwanie usuniętego pulpitu nawigacyjnego w witrynie Azure | Dokumenty firmy Microsoft
description: Jeśli usuniesz opublikowany pulpit nawigacyjny w witrynie Azure portal, możesz odzyskać pulpit nawigacyjny.
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: af0c72f0bc5dd8f3a3cbae7b82b1ac56447cbb08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77133289"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Odzyskiwanie usuniętego pulpitu nawigacyjnego w witrynie Azure portal

Jeśli korzystasz z publicznej chmury platformy Azure i usuwasz _opublikowany_ pulpit nawigacyjny w witrynie Azure portal, możesz odzyskać ten pulpit nawigacyjny w ciągu 14 dni od usunięcia. Jeśli jesteś w chmurze platformy Azure dla instytucji rządowych lub pulpit nawigacyjny nie jest publikowany, nie można go odzyskać i należy go odbudować. Aby uzyskać więcej informacji na temat publikowania pulpitu nawigacyjnego, zobacz [Publikowanie pulpitu nawigacyjnego](azure-portal-dashboard-share-access.md#publish-dashboard). Wykonaj następujące kroki, aby odzyskać opublikowany pulpit nawigacyjny:

1. Z menu Portalu platformy Azure wybierz **pozycję Grupy zasobów**, a następnie wybierz grupę zasobów, w której opublikowano pulpit nawigacyjny (domyślnie **nazwane pulpity nawigacyjne).**

1. W obszarze **Dziennik aktywności**rozwiń operację Usuń **pulpit nawigacyjny.** Wybierz kartę **Zmieniaj historię,** a następnie wybierz pozycję ** \<usunięty zasób\>**.

    ![Zrzut ekranu przedstawiający kartę historia zmian](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Zaznacz i skopiuj zawartość lewego okienka, a następnie zapisz w pliku tekstowym z rozszerzeniem pliku _.json._ Portal używa pliku JSON do ponownego utworzenia pulpitu nawigacyjnego.

    ![Zrzut ekranu przedstawiający historię zmian](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. Z menu portalu platformy Azure wybierz pozycję **Pulpity nawigacyjne**, a następnie wybierz pozycję **Przekaż**.

    ![Zrzut ekranu przedstawiający przekazywanie pulpitu nawigacyjnego](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Wybierz zapisany plik JSON. Portal ponownie tworzy pulpit nawigacyjny o tej samej nazwie i elementach co usunięty pulpit nawigacyjny.

1. Wybierz **pozycję Udostępnij,** aby opublikować pulpit nawigacyjny i ponownie ustanowić odpowiednią kontrolę dostępu.

    ![Zrzut ekranu przedstawiający udział na pulpicie nawigacyjnym](media/recover-shared-deleted-dashboard/dashboard-share.png)
