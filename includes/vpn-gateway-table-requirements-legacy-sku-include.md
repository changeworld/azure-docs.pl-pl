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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183120"
---
W poniższej tabeli wymieniono wymagania dotyczące bram sieci VPN opartych na zasadach i routingu. Ta tabela ma zastosowanie w obu modelach wdrażania — przy użyciu usługi Resource Manager i klasycznym. W przypadku modelu klasycznego bramy sieci VPN oparte na zasadach są takie same jak bramy statyczne, a bramy oparte na marszsłce są takie same jak bramy dynamiczne.

|  | **Podstawowa brama sieci VPN oparta na zasadach** | **Podstawowa brama sieci VPN oparta na routinie** | **Standardowa brama sieci VPN oparta na routinie** | **Brama sieci VPN o wysokiej wydajności o wysokiej wydajności opartej na routinie** |
| --- | --- | --- | --- | --- |
| **Łączność typu „lokacja do lokacji” (Site-to-Site, S2S)** |Konfiguracja sieci VPN oparta na zasadach |Konfiguracja sieci VPN opartej na routinie |Konfiguracja sieci VPN opartej na routinie |Konfiguracja sieci VPN opartej na routinie |
| **Łączność typu „punkt do lokacji” (Point-to-Site, P2S**) |Nieobsługiwane |Obsługiwane (może współistnieć z S2S) |Obsługiwane (może współistnieć z S2S) |Obsługiwane (może współistnieć z S2S) |
| **Metoda uwierzytelniania** |Klucz wstępny |Klucz wstępny dla połączeń S2S, certyfikaty dla łączności P2S |Klucz wstępny dla połączeń S2S, certyfikaty dla łączności P2S |Klucz wstępny dla połączeń S2S, certyfikaty dla łączności P2S |
| **Maksymalna liczba połączeń S2S** |1 |10 |10 |30 |
| **Maksymalna liczba połączeń P2S** |Nieobsługiwane |128 |128 |128 |
| **Aktywna obsługa routingu (BGP)** |Nieobsługiwane |Nieobsługiwane |Obsługiwane |Obsługiwane |
