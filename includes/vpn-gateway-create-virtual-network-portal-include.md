---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8b585a47d3950d232eb3e8047c12ee8949030c95
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780203"
---
Aby utworzyć sieć wirtualną w modelu wdrożenia usługi Resource Manager w witrynie Azure Portal, wykonaj poniższe kroki. Użyj **przykładowych wartości** , jeśli używasz tych kroków jako samouczka. Jeśli nie wykonujesz tych kroków w ramach samouczka, koniecznie zastąp te wartości własnymi. Aby uzyskać więcej informacji dotyczących pracy z sieciami wirtualnymi, zobacz temat [Omówienie sieci wirtualnych](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Aby ta sieć wirtualna nawiązywała połączenie z lokalizacją lokalną, musisz zapewnić koordynację z administratorem sieci lokalnej w celu wyznaczenia zakresu adresów IP, którego będzie można używać w szczególności dla tej sieci wirtualnej. Jeśli po obu stronach połączenia sieci VPN istnieje powielony zakres adresów, ruch nie będzie przekierowywany w oczekiwany sposób. Ponadto jeśli chcesz połączyć tę sieć wirtualną z inną siecią wirtualną, przestrzeń adresowa nie może pokrywać się z inną siecią wirtualną. Dlatego należy odpowiednio zaplanować konfigurację sieci.
>

1. Przejdź w przeglądarce do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.
2. Kliknij pozycję **Utwórz zasób**. W polu **Szukaj w witrynie Marketplace** wpisz „Sieć wirtualna”. Znajdź pozycję **Sieć wirtualna** na liście wyników i kliknij, aby otworzyć stronę **Sieć wirtualna**.
3. Kliknij przycisk **Utwórz**. Spowoduje to otwarcie strony **Tworzenie sieci wirtualnej** .
4. Na stronie **Tworzenie sieci wirtualnej** skonfiguruj ustawienia sieci wirtualnej. Po wypełnieniu pól czerwony wykrzyknik zmieni się na zielony znacznik wyboru, jeśli znaki wprowadzone w polu są prawidłowe. Użyj następujących wartości:

   - **Nazwa**: Sieć wirtualna 1
   - **Przestrzeń adresowa**: 10.1.0.0/16
   - **Subskrypcja**: Sprawdź, czy na liście znajduje się subskrypcja, której chcesz użyć. Subskrypcje można zmieniać, korzystając z listy rozwijanej.
   - **Grupa zasobów**: TestRG1 (kliknij przycisk **Utwórz nowy** , aby utworzyć nową grupę)
   - **Lokalizacja**: East US
   - **Podsieć**: Fronton
   - **Zakres adresów**: 10.1.0.0/24

   ![Strona Tworzenie sieci wirtualnej](./media/vpn-gateway-create-virtual-network-portal-include/create-virtual-network1.png)
5. Pozostaw pozycję DDoS jako podstawową, punkty końcowe usługi jako wyłączone, a Zapora jako wyłączona.
6. Kliknij pozycję **Utwórz**, aby utworzyć sieć wirtualną.