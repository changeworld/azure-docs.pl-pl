---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 96cba4e077be8b7658c270b09b177a845e16c8b0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183757"
---
W tym artykule założono, że

1. Ustanowiono już połączenie **VPN lokacja-lokacja** lub **Express Route** między siecią lokalną a usługą Azure Virtual Network.
2. Twoje konto użytkownika ma uprawnienia do tworzenia nowej maszyny wirtualnej w ramach subskrypcji platformy Azure, do której maszyny wirtualne zostały awaryjnie przełączone.
3. W Twojej subskrypcji są dostępne co najmniej 4 rdzenie do uruchomienia nowej maszyny wirtualnej serwera przetwarzania.
4. Masz dostępne **hasło serwera konfiguracji**.

> [!TIP]
> Upewnij się, że możesz połączyć się z portem 443 serwera konfiguracji (działającego lokalnie) z usługi Azure Virtual Network, do której maszyny wirtualne zostały awaryjnie przełączone.
