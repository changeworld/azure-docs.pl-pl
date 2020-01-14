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
ms.openlocfilehash: 2e8938f270ed175c687d975b0b248275ad92f8e4
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775084"
---
1. Przejdź do pozycji **grupy zasobów** i kliknij wybraną grupę zasobów podczas tworzenia zasobu **komunikacji równorzędnej** . Jeśli masz zbyt wiele grup zasobów, możesz użyć pola *Filtr* .

    > [!div class="mx-imgBorder"]
    > ![grupę zasobów komunikacji równorzędnej](../media/setup-direct-get-resourcegroup.png)

1. Kliknij utworzony zasób **komunikacji równorzędnej** .

    > [!div class="mx-imgBorder"]
    > ![widoku zasobów komunikacji równorzędnej](../media/setup-direct-get-open.png)

1. Na stronie **Przegląd** są wyświetlane informacje wysokiego poziomu. Zwróć uwagę na poniższe informacje.

    > [!div class="mx-imgBorder"]
    > ![widoku zasobów komunikacji równorzędnej](../media/setup-direct-get-overview.png)

1. Po lewej stronie kliknij pozycję **informacje ASN** , aby wyświetlić informacje przesłane podczas tworzenia PeerAsn

    > [!div class="mx-imgBorder"]
    > ![widoku zasobów komunikacji równorzędnej](../media/setup-direct-get-asninfo.png)

1. Po lewej stronie kliknij pozycję **połączenia**. Zaobserwuj pierwsze podsumowanie połączeń komunikacji równorzędnej między numerem ASN i firmą Microsoft, w różnych obiektach w linii metra. Możesz również uzyskać dostęp do podsumowania połączeń na stronie **Przegląd** , klikając pozycję **połączenia** w środkowym okienku, jak zostało to wyróżnione powyżej.

    > [!div class="mx-imgBorder"]
    > ![widoku zasobów komunikacji równorzędnej](../media/setup-direct-get-connectionssummary.png)

    * **Stan połączenia** odpowiada stanie skonfigurowanego połączenia komunikacji równorzędnej. Stany wyświetlane w tym polu są zgodne z diagramem stanu pokazanym w [przewodniku bezpośrednim komunikacji równorzędnej](../walkthrough-direct-all.md)
    * Stan sesji **IPv4** i **stan sesji protokołu IPv6** odpowiadają odpowiednio stanom sesji protokołu IPv4 i IPv6.  
    * Po wybraniu wiersza w górnej części sekcja Connection ( ***połączenie*** ) na dole zostanie wyświetlona wartość szczegóły dla każdego połączenia. Możesz kliknąć pozycję znaczniki strzałek, aby rozwinąć ***konfigurację***podsekcji, ***adres IPv4*** i ***adres IPv6***
