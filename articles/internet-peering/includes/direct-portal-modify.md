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
ms.openlocfilehash: d9ff01cf0180dae7f75d9753ba889d0caddad937
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775344"
---
W tej sekcji opisano sposób wykonywania następujących operacji modyfikacji dla komunikacji bezpośredniej komunikacji równorzędnej:

### <a name="add-direct-peering-connections"></a>Dodawanie bezpośrednich połączeń komunikacji równorzędnej
1. Kliknij przycisk **+ Dodaj połączenia** u góry i skonfiguruj nowe połączenie komunikacji równorzędnej.
    > [!div class="mx-imgBorder"]
    > ![Widok zasobów komunikacji równorzędnej](../media/setup-direct-modify-addconnection.png)
1. Wypełnij formularz **Bezpośrednie połączenie równorzędne** i kliknij przycisk **Zapisz**. Aby uzyskać pomoc dotyczącą konfigurowania połączenia równorzędnego, zapoznaj się z instrukcjami w sekcji "Tworzenie i inicjowanie obsługi administracyjnej" powyżej.
    > [!div class="mx-imgBorder"]
    > ![Widok zasobów komunikacji równorzędnej](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Usuwanie bezpośrednich połączeń komunikacji równorzędnej

Usuwanie połączenia nie jest obecnie obsługiwane w portalu. Skontaktuj się z [programem Microsoft w komunikacji równorzędnej](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Uaktualnianie lub obniżanie przepustowości połączeń aktywnych
1. Kliknij na połączenie komunikacji równorzędnej, które chcesz zmodyfikować, a następnie kliknij **na ...**  > Edytuj przycisk **połączenia.**
    > [!div class="mx-imgBorder"]
    > ![Edycja połączenia komunikacji równorzędnej](../media/setup-direct-modify-editconnection.png)
1. Zmodyfikuj przepustowość, jak pokazano poniżej, a następnie kliknij przycisk **Zapisz**.
    > [!div class="mx-imgBorder"]
    > ![Udostępnianie przepustowości połączenia równorzędnego](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Dodaj sesję IPv4/IPv6 w aktywnych połączeniach.
1. Kliknij na połączenie komunikacji równorzędnej, które chcesz zmodyfikować, a następnie kliknij **na ...**  > Edytuj przycisk **połączenia,** jak pokazano powyżej.
1. Dodaj **prefiks IPv4 sesji** lub informacje o **prefiksie IPv6 sesji** i kliknij przycisk **Zapisz**.

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Usuń sesję IPv4/IPv6 w aktywnych połączeniach.
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
