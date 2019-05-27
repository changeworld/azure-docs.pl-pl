---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5b9e036816aa532d32b1b4305ef6ae646ae05bae
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159263"
---
Poniższa lista zawiera wymagania dotyczące bramy oparte na zasadach i sieci VPN typu RouteBased. Ta tabela ma zastosowanie w obu modelach wdrażania — przy użyciu usługi Resource Manager i klasycznym. W przypadku modelu klasycznego bramy sieci VPN oparte na zasadach są takie same, jak bramy statyczne, a bramy oparte na trasach są takie same, jak bramy dynamiczne.

|  | **PolicyBased Basic VPN Gateway** | **RouteBased Basic VPN Gateway** | **RouteBased Standard VPN Gateway** | **Bramy sieci VPN usługi RouteBased o wysokiej wydajności** |
| --- | --- | --- | --- | --- |
| **Połączenie lokacja-lokacja (S2S)** |Konfiguracja sieci VPN typu PolicyBased |Konfiguracja sieci VPN typu RouteBased |Konfiguracja sieci VPN typu RouteBased |Konfiguracja sieci VPN typu RouteBased |
| **Łączność typu „punkt do lokacji” (Point-to-Site, P2S**) |Nieobsługiwane |Obsługiwane (może współistnieć z S2S) |Obsługiwane (może współistnieć z S2S) |Obsługiwane (może współistnieć z S2S) |
| **Metoda uwierzytelniania** |Klucz wstępny |Klucz wstępny dla połączeń S2S, certyfikaty dla łączności P2S |Klucz wstępny dla połączeń S2S, certyfikaty dla łączności P2S |Klucz wstępny dla połączeń S2S, certyfikaty dla łączności P2S |
| **Maksymalna liczba połączeń S2S** |1 |10 |10 |30 |
| **Maksymalna liczba połączeń P2S** |Nieobsługiwane |128 |128 |128 |
| **Aktywna Obsługa routingu (BGP)** |Nieobsługiwane |Nieobsługiwane |Obsługiwane |Obsługiwane |
