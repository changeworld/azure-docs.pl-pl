---
title: Plik dyrektywy include
titleSuffix: Azure
description: Plik dyrektywy include
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 60752cf1b3c05ab7817083e70310ba7b40227dec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129922"
---
1. Kliknij na połączenie komunikacji równorzędnej, które chcesz włączyć dla usługi komunikacji równorzędnej, a następnie kliknij **na ...**  > Edytuj przycisk **połączenia.**
    > [!div class="mx-imgBorder"]
    > ![Edycja połączenia komunikacji równorzędnej](../media/setup-direct-modify-editconnection.png)
1. W sekcji ***Użyj do usługi komunikacji***kliknij **włączone** i **zapisz**.
    > [!div class="mx-imgBorder"]
    > ![Usługa komunikacji równorzędnej włącz połączenie równorzędne](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. Na ekranie Przegląd zobaczysz szczegóły wdrożenia. Po zakończeniu wdrażania kliknij **przejdź do zasobu**.
    > [!div class="mx-imgBorder"]
    > ![Wdrożenie jest zakończone](../media/setup-direct-modify-overview-deployment-complete.png)

1. Następnie zobaczysz w ustawieniach **zarejestrowanych prefiksów**. Kliknij **dodaj zarejestrowany prefiks**.
    > [!div class="mx-imgBorder"]
    > ![Zarejestrowane prefiksy i połączenia](../media/setup-direct-modify-add-registered-prefix.png)
1. Zarejestruj prefiks, zaznaczając **nazwę** i **prefiks,** a następnie kliknij przycisk **Zapisz**
    > [!div class="mx-imgBorder"]
    >  ![Zarejestruj prefiks](../media/setup-direct-modify-register-a-prefix.png) 

1. Po utworzeniu prefiksu zobaczysz go na liście zarejestrowanych prefiksów. Kliknij **nazwę** prefiksu, aby zobaczyć więcej szczegółów.
    > [!div class="mx-imgBorder"]
    > ![Zarejestrowane prefiksy i połączenia](../media/setup-direct-modify-registered-prefixes.png)
1. Na zarejestrowanej stronie prefiksu zobaczysz pełne szczegóły, aby dołączyć **klucz prefiks** dla każdego prefiksu. Ten klucz musi zostać dostarczony klientowi, który przydzielił ten prefiks od usługodawcy zewnętrznego dostawcy. Klient może następnie zarejestrować swój prefiks w ramach subskrypcji za pomocą tego klucza.
    > [!div class="mx-imgBorder"]
    > ![Prefiks z kluczem prefiksu](../media/setup-direct-modify-registered-prefix-detail.png)