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
ms.openlocfilehash: e3e87e59f5b3c95051d9ee53e4b8d87afe9d9ba8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774564"
---
W tej sekcji opisano sposób wykonywania następujących operacji modyfikacji dla bezpośredniej komunikacji równorzędnej:

### <a name="add-exchange-peering-connections"></a>Dodawanie połączeń komunikacji równorzędnej programu Exchange

1. Kliknij przycisk **+ Dodaj połączenia** u góry i skonfiguruj nowe połączenie komunikacji równorzędnej.
    > [!div class="mx-imgBorder"]
    > ![widoku zasobów komunikacji równorzędnej](../media/setup-exchange-modify-addconnection.png)
1. Wypełnij formularz **połączenie komunikacji równorzędnej programu Exchange** , a następnie kliknij przycisk **Zapisz**. Aby uzyskać pomoc dotyczącą konfigurowania połączenia komunikacji równorzędnej, zapoznaj się z instrukcjami w sekcji "Tworzenie i udostępnianie bezpośredniej komunikacji równorzędnej" powyżej.
    > [!div class="mx-imgBorder"]
    > ![widoku zasobów komunikacji równorzędnej](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Usuń połączenia komunikacji równorzędnej programu Exchange

1. Kliknij połączenie komunikacji równorzędnej, które chcesz usunąć, a następnie kliknij przycisk **...**  > **Usuń połączenie** .
    > [!div class="mx-imgBorder"]
    > ![usuwania połączenia komunikacji równorzędnej](../media/setup-exchange-modify-deleteconnection.png)
1. Wprowadź identyfikator zasobu w polu **Potwierdź usunięcie** , jak pokazano w wyróżnionych polach, a następnie kliknij przycisk **Usuń**.
    > [!div class="mx-imgBorder"]
    > Połączenie komunikacji równorzędnej ![DeleteConfirm](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Dodaj sesję IPv4/IPv6 dla aktywnych połączeń

1. Kliknij połączenie komunikacji równorzędnej, które chcesz zmodyfikować, a następnie kliknij przycisk **...**  > **Edytuj połączenie** .
    > [!div class="mx-imgBorder"]
    > ![edytowanie połączenia komunikacji równorzędnej](../media/setup-exchange-modify-editconnection.png)
1. Dodaj **adres IPv4** lub informacje o **adresie IPv6** , a następnie kliknij przycisk **Zapisz**.
    > [!div class="mx-imgBorder"]
    > ![modyfikowanie połączenia komunikacji równorzędnej](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Usuń sesję protokołu IPv4/IPv6 dla aktywnych połączeń

Usuwanie sesji IPv4/IPv6 z istniejącego połączenia nie jest obecnie obsługiwane w portalu. Skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peeringexperience@microsoft.com).