---
title: Plik dyrektywy include
titleSuffix: Azure
description: Plik dyrektywy include
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: b967c844ab145074490e931122cbe092d67de0c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774551"
---
Po pomyślnym wdrożeniu zasobu **komunikacji równorzędnej** można go wyświetlić, wykonując poniższe czynności.

1. Przejdź do **grup zasobów** i kliknij grupę zasobów wybraną podczas tworzenia **zasobu komunikacji równorzędnej.** Można użyć *pola Filtr,* jeśli masz zbyt wiele grup zasobów.

    > [!div class="mx-imgBorder"]
    > ![Grupa zasobów komunikacji równorzędnej](../media/setup-direct-get-resourcegroup.png)

1. Kliknij utworzony zasób **komunikacji równorzędnej.**

    > [!div class="mx-imgBorder"]
    > ![Widok zasobów komunikacji równorzędnej](../media/setup-direct-get-open.png)

1. Na stronie **Przegląd** są wyświetlane informacje wysokiego poziomu. Obserwuj informacje wyróżnione poniżej.

    > [!div class="mx-imgBorder"]
    > ![Widok zasobów komunikacji równorzędnej](../media/setup-exchange-get-overview.png)

1. Po lewej stronie kliknij **informacje ASN,** aby wyświetlić informacje przesłane podczas tworzenia PeerAsn

    > [!div class="mx-imgBorder"]
    > ![Widok zasobów komunikacji równorzędnej](../media/setup-direct-get-asninfo.png)

1. Po lewej stronie kliknij przycisk **Połączenia**. Obserwuj na górze podsumowanie połączeń komunikacji równorzędnej między asn i microsoft, w różnych obiektach w metrze. Możesz również dojść do podsumowania połączeń ze strony **Przegląd,** klikając **opcję Połączenia** w środkowym okienku, jak zaznaczono powyżej.

    > [!div class="mx-imgBorder"]
    > ![Widok zasobów komunikacji równorzędnej](../media/setup-exchange-get-connectionssummary.png)

    * **Stan połączenia** odpowiada stanowi skonfigurowanego połączenia równorzędnego. Stany wyświetlane w tym polu są zgodne z diagramem stanu pokazanym w [instruktażu komunikacji równorzędnej programu Exchange](../walkthrough-exchange-all.md)
    * **Stan sesji IPv4** i **stan sesji IPv6** odpowiadają odpowiednio stanom sesji Protokołu IPv4 i IPv6.  
    * Po wybraniu wiersza u góry w sekcji ***Połączenie*** u dołu są wyświetlane szczegóły dla każdego połączenia. Możesz kliknąć na znaki strzałek, aby rozwinąć ***konfigurację***podsekcji, ***adres IPv4*** i ***adres IPv6***

    > [!div class="mx-imgBorder"]
    > ![Widok zasobów komunikacji równorzędnej](../media/setup-exchange-get-connectionsipv4.png)
