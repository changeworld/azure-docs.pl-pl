---
title: Tworzenie sieci platformy Azure z wirtualnych WAN tabeli tras koncentratora wirtualnego — witryna Azure portal | Dokumentacja firmy Microsoft
description: Wirtualne sieci WAN koncentrator wirtualny tabelę tras w celu kierowania ruchu do wirtualnego urządzenia sieciowego za pomocą portalu.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 2c8b3b4671fd14f9b10b8491861ae2c652f0188b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60461658"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Tworzenie wirtualnej sieci WAN tabeli tras Centrum dla urządzeń WUS: Azure Portal

W tym artykule przedstawiono sposób kierowania ruchu z poziomu Centrum do wirtualnego urządzenia sieciowego (WUS).

![Diagram usługi Virtual WAN](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Przed rozpoczęciem

Sprawdź, czy są spełnione następujące kryteria:

*  Masz wirtualnego urządzenia sieciowego (WUS). Wirtualne urządzenie sieciowe jest oprogramowanie innych firm wybranych przez użytkownika jest zazwyczaj zainicjowanym do obsługi administracyjnej z witryny Azure Marketplace w sieci wirtualnej.

    * Prywatny adres IP muszą być przypisane do interfejsu sieciowego urządzenia WUS.

    * Urządzenie WUS nie została wdrożona w koncentrator wirtualny. Musi zostać wdrożony w oddzielnych sieci wirtualnej.

    *  Sieć wirtualna urządzenia WUS może zawierać jeden lub wiele sieci wirtualnych połączonych z nim. W tym artykule nazywamy sieci wirtualnej urządzenie WUS "pośrednich sieć wirtualną będącą szprychą". Te sieci wirtualne można połączyć w sieci wirtualnej urządzenie WUS za pomocą komunikacji równorzędnej sieci wirtualnych.
*  Utworzono sieci wirtualne 2. Będą one służyć jako sieci wirtualne będące szprychami.

    * Na potrzeby tego ćwiczenia przestrzenie adresów sieci wirtualnej szprychy są: VNet1: 10.0.2.0/24 i VNet2: 10.0.3.0/24. Jeśli potrzebujesz informacji na temat tworzenia sieci wirtualnej, zobacz [tworzenie sieci wirtualnej](../virtual-network/quick-create-portal.md).

    * Upewnij się, że nie ma żadnych bram sieci wirtualnej w żadnym z sieciami wirtualnymi.
    * W przypadku tej konfiguracji tych sieciach wirtualnych nie wymagają podsieć bramy.

## <a name="signin"></a>1. Logowanie

Przejdź w przeglądarce do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

## <a name="vwan"></a>2. Tworzenie wirtualnej sieci WAN

Tworzenie wirtualnej sieci WAN. Na potrzeby tego ćwiczenia można użyć następujących wartości:

* **Nazwa wirtualnej sieci WAN:** myVirtualWAN
* **Grupa zasobów:** testRG
* **Lokalizacja:** Zachodnie stany USA

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. Tworzenie koncentratora

Utwórz koncentrator. Na potrzeby tego ćwiczenia można użyć następujących wartości:

* **Lokalizacja:** Zachodnie stany USA
* **Nazwa:** westushub
* **Centrum prywatną przestrzeń adresową:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. Tworzenie i stosowanie tabelę tras Centrum

Zaktualizuj Centrum z tabelą tras koncentratora. Na potrzeby tego ćwiczenia można użyć następujących wartości:

* **Pośrednie szprychy przestrzenie adresów sieci wirtualnej:** 10.0.2.0/24 (VNet1 i VNet2) i 10.0.3.0/24
* **Urządzenie WUS w strefie DMZ sieci interfejs prywatny adres IP:** 10.0.4.5

1. Przejdź do swojej wirtualna sieć WAN.
2. Kliknij Centrum, dla której chcesz utworzyć tabelę tras.
3. Kliknij przycisk **...** , a następnie kliknij przycisk **koncentrator wirtualny edycji**.
4. Na **koncentrator wirtualny edycji** strony, przewiń w dół i wybierz pole wyboru **użycia tabeli routingu**.
5. W **jeśli prefiks lokalizacji docelowej** kolumny, Dodaj przestrzenie adresowe. W **wysłać do miejsca docelowego następnego skoku** kolumny, Dodaj urządzenie WUS w strefie DMZ sieci interfejs prywatny adres IP.
6. Kliknij przycisk **Potwierdź** aktualizowania zasobów Centrum przy użyciu ustawienia tabeli tras.

## <a name="connections"></a>5. Tworzenie połączenia sieci wirtualnej

Utwórz połączenie z każdej szprysze pośrednich sieci wirtualnej (sieć VNet1 i VNet2) do koncentratora. Następnie utwórz połączenie z sieci wirtualnej urządzenie WUS z koncentratorem.

 W tym kroku można użyć następujących wartości:

| Nazwa sieci wirtualnej| Nazwa połączenia|
| --- | --- |
| VNet1 | testconnection1 |
| Sieć VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Powtórz procedurę dla każdej sieci wirtualnej, który chcesz się połączyć.

1. Na stronie sieci wirtualnej WAN kliknij pozycję **Połączenia sieci wirtualnych**.
2. Na stronie połączenia sieci wirtualnej kliknij polecenie **+ Dodaj połączenie**.
3. Na stronie **Dodaj połączenie** wypełnij następujące pola:

    * **Nazwa połączenia** — nazwij połączenie.
    * **Koncentratory** — wybierz koncentrator, z którym chcesz skojarzyć to połączenie.
    * **Subskrypcja** — sprawdź, czy wybrano właściwą subskrypcję.
    * **Sieć wirtualna** — wybierz sieć wirtualną, którą chcesz połączyć z tym koncentratorem. Sieć wirtualna nie może mieć istniejącej bramy sieci wirtualnej.
4. Kliknij przycisk **OK** do utworzenia połączenia.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).