---
title: 'Wirtualna sieć WAN: tworzenie tabeli tras centrum wirtualnego do sieci Wus: Witryna Azure portal'
description: Tabela tras wirtualnego koncentratora sieci WAN w celu kierowania ruchu do wirtualnego urządzenia sieciowego za pomocą portalu.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 0807b535adc45093b439dba5ab8a0ea26b2a0721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402931"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Tworzenie tabeli tras centrum wirtualnej sieci WAN dla obiektów w sieciowych: witryna Azure portal

W tym artykule pokazano, jak sterować ruchem z gałęzi (lokacji lokalnej) połączonej z koncentratorem wirtualnej sieci WAN z siecią wirtualną Szprychy za pośrednictwem sieci wirtualnej sieciowej (NVA).

![Diagram usługi Virtual WAN](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Przed rozpoczęciem

Sprawdź, czy spełniasz następujące kryteria:

*  Masz wirtualne urządzenie sieciowe (NVA). Wirtualne urządzenie sieciowe to wybrane oprogramowanie innej firmy, które jest zazwyczaj aprowiowane z portalu Azure Marketplace w sieci wirtualnej.

    * Prywatny adres IP musi być przypisany do interfejsu sieciowego urządzenia wus.

    * Urządzenie WUS nie jest wdrażane w centrum wirtualnym. Musi być wdrożony w oddzielnej sieci wirtualnej.

    *  Sieć wirtualna urządzenia WUS może mieć jedną lub wiele sieci wirtualnych podłączonych do niej. W tym artykule odnosimy się do sieci wirtualnej urządzenia WUS jako "pośredniej sieci wirtualnej szprychy". Te sieci wirtualne mogą być połączone z siecią wirtualną NVA przy użyciu komunikacji równorzędnej sieci wirtualnej. Łącza komunikacji równorzędnej sieci wirtualnej są przedstawione przez czarne strzałki na powyższym rysunku między siecią wirtualną 1, siecią wirtualną 2 i siecią wirtualną NVA.
*  Utworzono dwie sieci wirtualne. Będą one używane jako szprychowe sieci wirtualne.

    * Przestrzenie adresowe szprychy sieci wirtualnej to: VNet1: 10.0.2.0/24 i VNet2: 10.0.3.0/24. Jeśli potrzebujesz informacji na temat tworzenia sieci wirtualnej, zobacz [Tworzenie sieci wirtualnej](../virtual-network/quick-create-portal.md).

    * Upewnij się, że w żadnej z sieci wirtualnych nie ma żadnych bram sieci wirtualnej.

    * Sieci wirtualne nie wymagają podsieci bramy.

## <a name="1-sign-in"></a><a name="signin"></a>1. Zaloguj się

Przejdź w przeglądarce do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

## <a name="2-create-a-virtual-wan"></a><a name="vwan"></a>2. Tworzenie wirtualnej sieci WAN

Utwórz wirtualną sieć WAN. Użyj następujących przykładowych wartości:

* **Nazwa wirtualnej sieci WAN:** myVirtualWAN
* **Grupa zasobów:** testRG
* **Lokalizacja:** Zachodnie Stany Zjednoczone

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="3-create-a-hub"></a><a name="hub"></a>3. Tworzenie koncentratora

Utwórz koncentrator. Użyj następujących przykładowych wartości:

* **Lokalizacja:** Zachodnie Stany Zjednoczone
* **Nazwa:** westushub
* **Prywatna przestrzeń adresowa centrum:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="4-create-and-apply-a-hub-route-table"></a><a name="route"></a>4. Tworzenie i stosowanie tabeli tras koncentratora

Aktualizowanie koncentratora za pomocą tabeli tras koncentratora. Użyj następujących przykładowych wartości:

* **Szprychowe przestrzenie adresowe sieci wirtualnej:** (VNet1 i VNet2) 10.0.2.0/24 i 10.0.3.0/24
* **Prywatny adres IP interfejsu sieciowego DMZ NVA:** 10.0.4.5

1. Przejdź do wirtualnej sieci WAN.
2. Kliknij koncentrator, dla którego chcesz utworzyć tabelę tras.
3. Kliknij **...**, a następnie kliknij pozycję **Edytuj koncentrator wirtualny**.
4. Na stronie **Edytowanie koncentratora wirtualnego** przewiń w dół i zaznacz pole wyboru **Użyj tabeli do routingu**.
5. W **prefiksie Jeśli miejsce docelowe jest** kolumna, dodaj spacje adresowe. W kolumnie **Wyślij do następnego przeskoku** dodaj prywatny adres IP interfejsu sieciowego DMZ NVA.
6. Kliknij **przycisk Potwierdź,** aby zaktualizować zasób koncentratora z ustawieniami tabeli tras.

## <a name="5-create-the-vnet-connections"></a><a name="connections"></a>5. Tworzenie połączeń sieci wirtualnej

Utwórz połączenie sieci wirtualnej z każdej sieci wirtualnej z szprychą pośrednią (sieć wirtualną 1 i VNet2) do koncentratora. Te połączenia sieci wirtualnej są przedstawione przez niebieskie strzałki na powyższym rysunku. Następnie utwórz połączenie sieci wirtualnej z sieci wirtualnej NVA do koncentratora (czarna strzałka na rysunku).

 W tym kroku można użyć następujących wartości:

| Nazwa sieci wirtualnej| Nazwa połączenia|
| --- | --- |
| Sieć wirtualna 1 | połączenie testowe1 |
| Sieć wirtualna 2 | połączenia testowe2 |
| NVAVNet (sieciowe NVAVNet) | połączenia testowe3 |

Powtórz poniższą procedurę dla każdej sieci wirtualnej, którą chcesz połączyć.

1. Na stronie sieci wirtualnej WAN kliknij pozycję **Połączenia sieci wirtualnych**.
2. Na stronie połączenia sieci wirtualnej kliknij polecenie **+ Dodaj połączenie**.
3. Na stronie **Dodaj połączenie** wypełnij następujące pola:

    * **Nazwa połączenia** — nazwij połączenie.
    * **Koncentratory** — wybierz koncentrator, z którym chcesz skojarzyć to połączenie.
    * **Subskrypcja** — sprawdź, czy wybrano właściwą subskrypcję.
    * **Sieć wirtualna** — wybierz sieć wirtualną, którą chcesz połączyć z tym koncentratorem. Sieć wirtualna nie może mieć istniejącej bramy sieci wirtualnej.
4. Kliknij **przycisk OK,** aby utworzyć połączenie.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).
