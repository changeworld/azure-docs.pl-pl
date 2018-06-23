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
ms.openlocfilehash: 5e7e365a73c9ba9cfd91d4f93759e7af55a0ad48
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "30929329"
---
Poniższej tabeli wymieniono wymagania dla bram PolicyBased i RouteBased sieci VPN. Ta tabela ma zastosowanie w obu modelach wdrażania — przy użyciu usługi Resource Manager i klasycznym. W przypadku modelu klasycznego bramy sieci VPN PolicyBased są takie same, jak bramy statyczne, a bramy oparte na trasach są takie same, jak bramy dynamiczne.

|  | **PolicyBased Basic bramy sieci VPN** | **RouteBased Basic bramy sieci VPN** | **RouteBased Standard bramy sieci VPN** | **Brama sieci VPN RouteBased wysokiej wydajności** |
| --- | --- | --- | --- | --- |
| **Połączenie lokacja-lokacja (S2S)** |Konfiguracja sieci VPN PolicyBased |Konfiguracja sieci VPN z siecią typu RouteBased |Konfiguracja sieci VPN z siecią typu RouteBased |Konfiguracja sieci VPN z siecią typu RouteBased |
| **Łączność typu „punkt do lokacji” (Point-to-Site, P2S**) |Nieobsługiwane |Obsługiwane (może współistnieć z S2S) |Obsługiwane (może współistnieć z S2S) |Obsługiwane (może współistnieć z S2S) |
| **Metoda uwierzytelniania** |Klucz wstępny |Klucz wstępny dla połączeń S2S, certyfikaty dla łączności P2S |Klucz wstępny dla połączeń S2S, certyfikaty dla łączności P2S |Klucz wstępny dla połączeń S2S, certyfikaty dla łączności P2S |
| **Maksymalna liczba połączeń S2S** |1 |10 |10 |30 |
| **Maksymalna liczba połączeń P2S** |Nieobsługiwane |128 |128 |128 |
| **Aktywna Obsługa routingu (BGP)** (*) |Nieobsługiwane |Nieobsługiwane |Obsługiwane |Obsługiwane |

  (*) Protokół BGP nie jest obsługiwany dla klasycznym modelu wdrażania.
