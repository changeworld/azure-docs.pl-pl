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
ms.openlocfilehash: 4ea97e2dbee87f7ab129c4295276c9024c0212c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117076"
---
Nową funkcją klienta sieci VPN systemu Windows 10, Always On, jest możliwość utrzymania połączenia VPN. Dzięki funkcji Zawsze włączone aktywny profil sieci VPN może łączyć się automatycznie i pozostawać połączony na podstawie wyzwalaczy, takich jak logowanie użytkownika, zmiana stanu sieci lub aktywny ekran urządzenia.

Bramy z systemem Windows 10 Always On umożliwiają ustanawianie tuneli użytkowników trwałych i tuneli urządzeń na platformie Azure. Ten artykuł ułatwia konfigurowanie tunelu użytkownika zawsze włączonej sieci VPN.

Zawsze na połączeniach sieci VPN obejmują jeden z dwóch typów tuneli:

* **Tunel urządzenia:** Łączy się z określonymi serwerami sieci VPN, zanim użytkownicy zalogują się do urządzenia. Scenariusze łączności wstępnej i zarządzanie urządzeniami używają tunelu urządzeń.

* **Tunel użytkownika**: Łączy się tylko wtedy, gdy użytkownicy zalogują się do urządzenia. Korzystając z tuneli użytkowników, można uzyskiwać dostęp do zasobów organizacji za pośrednictwem serwerów sieci VPN.

Tunele urządzeń i tunele użytkowników działają niezależnie od ich profili VPN. Można je łączyć w tym samym czasie i mogą używać różnych metod uwierzytelniania i innych ustawień konfiguracji sieci VPN, odpowiednio.
