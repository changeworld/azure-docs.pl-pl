---
title: 'Wirtualna sieć WAN: Tworzenie tabeli tras koncentratora wirtualnego do urządzenie WUS: Azure Portal'
description: Tabela tras wirtualnego koncentratora sieci WAN do kierowania ruchu do sieciowego urządzenia wirtualnego przy użyciu portalu.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: c0681024b60827cf589906041c264d912ab209bb
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75612364"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Tworzenie tabeli tras wirtualnego centrum sieci WAN dla urządzeń WUS: Azure Portal

W tym artykule pokazano, jak przetworzyć ruch z gałęzi (lokacji lokalnej) połączonej z koncentratorem wirtualnej sieci WAN za pośrednictwem sieciowego urządzenia wirtualnego (urządzenie WUS).

![Diagram usługi Virtual WAN](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Przed rozpoczęciem

Sprawdź, czy zostały spełnione następujące kryteria:

*  Masz wirtualne urządzenie sieciowe (urządzenie WUS). Sieciowe urządzenie wirtualne to oferowane przez siebie oprogramowanie innych firm, które jest zazwyczaj inicjowane z portalu Azure Marketplace w sieci wirtualnej.

    * Prywatny adres IP musi być przypisany do interfejsu sieciowego urządzenie WUS.

    * URZĄDZENIE WUS nie jest wdrożona w koncentratorze wirtualnym. Należy ją wdrożyć w oddzielnej sieci wirtualnej.

    *  Do sieci wirtualnej urządzenie WUS może być połączona co najmniej jedna sieć wirtualna. W tym artykule odwołujemy się do sieci wirtualnej urządzenie WUS jako "pośrednia Sieć wirtualna". Te sieci wirtualnych mogą być połączone z siecią wirtualną urządzenie WUS przy użyciu komunikacji równorzędnej sieci wirtualnych. Łącza komunikacji równorzędnej sieci wirtualnych są przedstawiane za pomocą czarnych strzałek na powyższym rysunku między siecią wirtualną 1, Sieć wirtualna 2 i urządzenie WUS sieci wirtualnej.
*  Utworzono 2 sieci wirtualnych. Będą one używane jako szprychy sieci wirtualnych.

    * W tym ćwiczeniu przestrzenie adresowe sieci wirtualnej szprych są następujące: VNet1:10.0.2.0/24 i VNet2:10.0.3.0/24. Jeśli potrzebujesz informacji na temat sposobu tworzenia sieci wirtualnej, zobacz [Create a Virtual Network](../virtual-network/quick-create-portal.md).

    * Upewnij się, że w żadnym z sieci wirtualnych nie ma żadnych bram sieci wirtualnej.
    * W przypadku tej konfiguracji te sieci wirtualnych nie wymagają podsieci bramy.

## <a name="signin"></a>1. Zaloguj się

Przejdź w przeglądarce do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

## <a name="vwan"></a>2. Tworzenie wirtualnej sieci WAN

Utwórz wirtualną sieć WAN. Na potrzeby tego ćwiczenia można użyć następujących wartości:

* **Nazwa wirtualnej sieci WAN:** myVirtualWAN
* **Grupa zasobów:** testRG
* **Lokalizacja:** Zachodnie stany USA

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. Tworzenie centrum

Utwórz centrum. Na potrzeby tego ćwiczenia można użyć następujących wartości:

* **Lokalizacja:** Zachodnie stany USA
* **Nazwa:** westushub
* **Prywatna przestrzeń adresowa centrum:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. Tworzenie i stosowanie tabeli tras centrum

Zaktualizuj centrum za pomocą tabeli tras centrum. Na potrzeby tego ćwiczenia można użyć następujących wartości:

* **Przestrzenie adresowe sieci wirtualnej szprych:** (VNet1 i VNet2) 10.0.2.0/24 i 10.0.3.0/24
* **Prywatny adres IP interfejsu sieciowego urządzenie WUS DMZ:** 10.0.4.5

1. Przejdź do wirtualnej sieci WAN.
2. Kliknij centrum, dla którego chcesz utworzyć tabelę tras.
3. Kliknij przycisk **...** , a następnie kliknij pozycję **Edytuj centrum wirtualne**.
4. Na stronie **Edytowanie wirtualnego centrum** przewiń w dół i zaznacz pole wyboru **Użyj tabeli do routingu**.
5. W kolumnie **jeśli prefiks lokalizacji docelowej jest** kolumną Dodaj przestrzenie adresowe. W kolumnie **Wyślij do następnego przeskoku** Dodaj prywatny adres IP urządzenie WUS strefy DMZ.
6. Kliknij przycisk **Potwierdź** , aby zaktualizować zasób centrum przy użyciu ustawień tabeli tras.

## <a name="connections"></a>5. Tworzenie połączeń sieci wirtualnej

Utwórz połączenie sieci wirtualnej z każdej pośredniej sieci wirtualnej szprychy (VNet1 i VNet2) do centrum. Te połączenia sieci wirtualnej są przedstawiane przez niebieskie strzałki na powyższym rysunku. Następnie utwórz połączenie VNET z sieci wirtualnej urządzenie WUS do koncentratora (czarna strzałka na rysunku). 

 W tym kroku można użyć następujących wartości:

| Nazwa sieci wirtualnej| Nazwa połączenia|
| --- | --- |
| Sieć wirtualna 1 | testconnection1 |
| Sieć wirtualna 2 | testconnection2 |
| NVAVNet | testconnection3 |

Powtórz poniższą procedurę dla każdej sieci wirtualnej, która ma zostać nawiązane połączenie.

1. Na stronie sieci wirtualnej WAN kliknij pozycję **Połączenia sieci wirtualnych**.
2. Na stronie połączenia sieci wirtualnej kliknij polecenie **+ Dodaj połączenie**.
3. Na stronie **Dodaj połączenie** wypełnij następujące pola:

    * **Nazwa połączenia** — nazwij połączenie.
    * **Koncentratory** — wybierz koncentrator, z którym chcesz skojarzyć to połączenie.
    * **Subskrypcja** — sprawdź, czy wybrano właściwą subskrypcję.
    * **Sieć wirtualna** — wybierz sieć wirtualną, którą chcesz połączyć z tym koncentratorem. Sieć wirtualna nie może mieć istniejącej bramy sieci wirtualnej.
4. Kliknij przycisk **OK** , aby utworzyć połączenie.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).
