---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 11/28/2019
ms.author: raynew
ms.openlocfilehash: de15e3028cf22cdd03ce29385278fc5e2babaa9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74566328"
---
W tym artykule założono, że

1. Ustanowiono już połączenie **VPN lokacja-lokacja** lub **Express Route** między siecią lokalną a usługą Azure Virtual Network.
2. Twoje konto użytkownika ma uprawnienia do tworzenia nowej maszyny wirtualnej w ramach subskrypcji platformy Azure, do której maszyny wirtualne zostały awaryjnie przełączone.
3. Subskrypcja ma co najmniej 8 rdzeni dostępnych do rozkręcenia nowej maszyny wirtualnej serwera przetwarzania.
4. Masz dostępne **hasło serwera konfiguracji**.

> [!TIP]
> Upewnij się, że możesz połączyć się z portem 443 serwera konfiguracji (działającego lokalnie) z usługi Azure Virtual Network, do której maszyny wirtualne zostały awaryjnie przełączone.
