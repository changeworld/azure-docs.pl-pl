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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775344"
---
W tej sekcji opisano sposób wykonywania następujących operacji modyfikacji dla bezpośredniej komunikacji równorzędnej:

### <a name="add-direct-peering-connections"></a>Dodaj bezpośrednie połączenia komunikacji równorzędnej
1. Kliknij przycisk **+ Dodaj połączenia** u góry i skonfiguruj nowe połączenie komunikacji równorzędnej.
    > [!div class="mx-imgBorder"]
    > ![widoku zasobów komunikacji równorzędnej](../media/setup-direct-modify-addconnection.png)
1. Wypełnij formularz **połączenia bezpośredniego komunikacji równorzędnej** i kliknij przycisk **Zapisz**. Aby uzyskać pomoc dotyczącą konfigurowania połączenia komunikacji równorzędnej, zapoznaj się z instrukcjami w sekcji "Tworzenie i udostępnianie bezpośredniej komunikacji równorzędnej" powyżej.
    > [!div class="mx-imgBorder"]
    > ![widoku zasobów komunikacji równorzędnej](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Usuń bezpośrednie połączenia komunikacji równorzędnej

Usuwanie połączenia nie jest obecnie obsługiwane w portalu. Skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Przepustowość uaktualnienia lub obniżenia poziomu aktywnych połączeń
1. Kliknij połączenie komunikacji równorzędnej, które chcesz zmodyfikować, a następnie kliknij przycisk **...**  > **Edytuj połączenie** .
    > [!div class="mx-imgBorder"]
    > ![edytowanie połączenia komunikacji równorzędnej](../media/setup-direct-modify-editconnection.png)
1. Zmodyfikuj przepustowość, jak pokazano poniżej, a następnie kliknij przycisk **Zapisz**.
    > [!div class="mx-imgBorder"]
    > ![zmodyfikować połączenia komunikacji równorzędnej](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Dodaj sesję IPv4/IPv6 dla aktywnych połączeń.
1. Kliknij połączenie komunikacji równorzędnej, które chcesz zmodyfikować, a następnie kliknij przycisk **...**  > **Edytuj połączenie** , jak pokazano powyżej.
1. Dodaj **prefiks IPv4 sesji** lub informacje **prefiksu IPv6 sesji** , a następnie kliknij przycisk **Zapisz**.

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Usuń sesję IPv4/IPv6 dla aktywnych połączeń.
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
