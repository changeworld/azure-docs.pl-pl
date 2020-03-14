---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2783c828f96eeb3539e2266eaf1d6d590a6af4c4
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370999"
---
Nową funkcją klienta sieci VPN systemu Windows 10, która jest zawsze włączona, jest możliwość utrzymania połączenia sieci VPN. Zawsze, gdy aktywny profil sieci VPN może nawiązywać połączenie automatycznie i pozostaje połączony na podstawie wyzwalaczy, takich jak logowanie użytkownika, zmiana stanu sieci lub aktywny ekran urządzenia.

Za pomocą bram sieci wirtualnej platformy Azure z systemem Windows 10, które są zawsze włączone, można nawiązywać stałe tunele użytkowników i tunele urządzeń na platformie Azure. W tym artykule opisano sposób konfigurowania tunelu użytkownika o zawsze włączonym sieci VPN.

Zawsze włączone połączenia sieci VPN obejmują jeden z dwóch typów tuneli:

* **Tunel urządzenia**: nawiązuje połączenie z określonymi serwerami sieci VPN przed zalogowaniem się użytkowników na urządzeniu. Scenariusze łączności przed logowaniem i zarządzaniem urządzeniami używają tunelu urządzenia.

* **Tunel użytkownika**: nawiązuje połączenie tylko po zalogowaniu się użytkowników na urządzeniu. Korzystając z tuneli użytkowników, można uzyskać dostęp do zasobów organizacji za pośrednictwem serwerów sieci VPN.

Tunele urządzeń i tunele użytkownika działają niezależnie od profilów sieci VPN. Mogą być połączone w tym samym czasie i mogą korzystać z innych metod uwierzytelniania i innych ustawień konfiguracji sieci VPN, zgodnie z potrzebami.
