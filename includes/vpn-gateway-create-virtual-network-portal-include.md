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
ms.openlocfilehash: 19b8a73835e8ac5ecaac7b42793140325964d17c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523458"
---
Aby utworzyć sieć wirtualną w modelu wdrożenia usługi Resource Manager w witrynie Azure Portal, wykonaj poniższe kroki. Użyj **przykładowych wartości** , jeśli używasz tych kroków jako samouczka. Jeśli nie wykonujesz tych kroków w ramach samouczka, koniecznie zastąp te wartości własnymi. Aby uzyskać więcej informacji dotyczących pracy z sieciami wirtualnymi, zobacz temat [Omówienie sieci wirtualnych](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Aby ta sieć wirtualna nawiązywała połączenie z lokalizacją lokalną, musisz zapewnić koordynację z administratorem sieci lokalnej w celu wyznaczenia zakresu adresów IP, którego będzie można używać w szczególności dla tej sieci wirtualnej. Jeśli po obu stronach połączenia sieci VPN istnieje powielony zakres adresów, ruch nie będzie przekierowywany w oczekiwany sposób. Ponadto jeśli chcesz połączyć tę sieć wirtualną z inną siecią wirtualną, przestrzeń adresowa nie może pokrywać się z inną siecią wirtualną. Dlatego należy odpowiednio zaplanować konfigurację sieci.
>

1. Z menu [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób**. 

   ![Utwórz zasób w Azure Portal](./media/vpn-gateway-create-virtual-network-portal-include/azure-portal-create-resource.png)
2. W polu **Szukaj w witrynie Marketplace** wpisz „Sieć wirtualna”. Znajdź pozycję **Sieć wirtualna** na liście wyników i kliknij, aby otworzyć stronę **Sieć wirtualna**.
3. Kliknij pozycję **Utwórz**. Spowoduje to otwarcie strony **Tworzenie sieci wirtualnej** .
4. Na stronie **Tworzenie sieci wirtualnej** skonfiguruj ustawienia sieci wirtualnej. Po wypełnieniu pól czerwony wykrzyknik zmieni się na zielony znacznik wyboru, jeśli znaki wprowadzone w polu są prawidłowe. Wprowadź następujące wartości:

   - **Nazwa**: VNet1
   - **Przestrzeń adresowa**: 10.1.0.0/16
   - **Subskrypcja**: Sprawdź, czy na liście znajduje się subskrypcja, której chcesz użyć. Subskrypcje można zmieniać, korzystając z listy rozwijanej.
   - **Grupa zasobów**: TestRG1 (kliknij pozycję **Utwórz nową** , aby utworzyć nową grupę)
   - **Lokalizacja**: Wschodnie stany USA
   - **Podsieć**: fronton
   - **Zakres adresów**: 10.1.0.0/24

   ![Strona Tworzenie sieci wirtualnej](./media/vpn-gateway-create-virtual-network-portal-include/create-virtual-network1.png)
5. Pozostaw pozycję DDoS jako podstawową, punkty końcowe usługi jako wyłączone, a Zapora jako wyłączona.
6. Kliknij pozycję **Utwórz**, aby utworzyć sieć wirtualną.