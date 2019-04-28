---
title: Automatyczne skalowanie usługi w chmurze w portalu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z portalu, aby skonfigurować reguły skalowania automatycznego dla rola sieci web usługi w chmurze lub roli procesu roboczego na platformie Azure.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 701d4404-5cc0-454b-999c-feb94c1685c0
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeconnoc
ms.openlocfilehash: f5597773b3127852481d5e14844bed889c4d6f83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61435320"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Jak skonfigurować automatyczne skalowanie usługi w chmurze w portalu

Warunki można ustawić dla roli procesu roboczego usługi chmury, wyzwalające wewnątrz lub na zewnątrz operacji skalowania. Warunki dla roli może opierać się na procesor CPU, dysku lub obciążenia sieciowego w roli. Można również ustawić warunek, w oparciu o kolejki komunikatów lub Metryka niektórych innych zasobów platformy Azure skojarzonych z subskrypcją.

> [!NOTE]
> Ten artykuł koncentruje się na role sieć web i procesu roboczego usługi w chmurze. Po utworzeniu maszyny wirtualnej (klasycznej) bezpośrednio znajduje się w usłudze w chmurze. Standardowa maszynę wirtualną można skalować przez skojarzenie jej z [zestaw dostępności](../virtual-machines/windows/classic/configure-availability-classic.md) i ręcznie je włączyć lub wyłączyć.

## <a name="considerations"></a>Zagadnienia do rozważenia
Przed skonfigurowaniem skalowania dla aplikacji, należy rozważyć następujące informacje:

* Skalowanie dotyczy użycia rdzeni.

    Większych wystąpień roli za pomocą rdzeni. Możesz skalować aplikację tylko w obrębie limit rdzeni dla Twojej subskrypcji. Na przykład załóżmy, że Twoja subskrypcja ma limit 20 rdzeni. Jeśli uruchamiasz aplikację za pomocą dwóch usług w chmurze o średniej wielkości, (łącznie 4 rdzenie), możesz tylko skalować w górę innych wdrożeń usługi w chmurze w Twojej subskrypcji przez pozostałe 16 rdzeni. Aby uzyskać więcej informacji na temat rozmiarów, zobacz [rozmiary usług w chmurze](cloud-services-sizes-specs.md).

* Możesz skalować na podstawie progu kolejki komunikatów. Aby uzyskać więcej informacji o tym, jak używać kolejek, zobacz [sposób używania usługi Queue Storage](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Możesz również skalować w innych zasobów skojarzonych z Twoją subskrypcją.

* Aby włączyć wysoką dostępność aplikacji, należy upewnić się, że jest wdrażany za pomocą co najmniej dwóch wystąpień roli. Aby uzyskać więcej informacji, zobacz [umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/).

* Automatyczne skalowanie odbywa się tylko, gdy wszystkie role są **gotowe** stanu.  


## <a name="where-scale-is-located"></a>Gdzie znajduje się skalowania
Po wybraniu usługi w chmurze, powinny być widoczne bloku usługi chmury.

1. W bloku usługi w chmurze na **rolami i wystąpieniami** kafelka, wybierz nazwę usługi w chmurze.   
   **WAŻNE**: Upewnij się, że rola usługi w chmurze, nie znajduje się poniżej rolę wystąpienie roli.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Wybierz **skalowania** kafelka.

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Automatyczne skalowanie
Można skonfigurować ustawienia skalowania dla roli, albo w dwóch trybach **ręczne** lub **automatyczne**. Ręcznego zgodnie z regułami, ustaw bezwzględną liczbę wystąpień. Automatyczne umożliwia jednak do zestawu reguły, które kontrolują sposób, jak również jak znacznie możesz powinny być skalowane.

Ustaw **skalowanie przez** opcję **reguły harmonogramu i wydajności**.

![Ustawienia skalowania usług chmury za pomocą profilu i reguł](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Istniejący profil.
2. Dodaj regułę dla profilu nadrzędnej.
3. Dodaj inny profil.

Wybierz **Dodaj profil**. Profil, który określa tryb, w którym chcesz użyć skali: **zawsze**, **cyklu**, **ustalona data**.

Po skonfigurowaniu profilu i reguł, wybierz **Zapisz** ikonę u góry.

#### <a name="profile"></a>Profil
Profil, który Ustawia minimalną i maksymalną wystąpień skali, a także, gdy aktywny jest zakresu tej skali.

* **zawsze**

    Zawsze Zachowuj tego zakresu wystąpień jest dostępna.  

    ![Usługa w chmurze, która zawsze skalowanie](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Cykl**

    Wybierz zestaw dni tygodnia do skalowania.

    ![Skalowanie usługi chmury za pomocą harmonogramu cyklu](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Ustalona data**

    Zakres dat stały, aby skalować roli.

    ![Skalowanie usługi chmury przy użyciu stałej dacie](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Po skonfigurowaniu profilu wybierz **OK** znajdujący się u dołu bloku profilu.

#### <a name="rule"></a>Reguła
Reguły są dodawane do profilu i reprezentują warunku, która powoduje uruchomienie skalowania.

Wyzwalacz reguła opiera się na metryki usługi w chmurze (użycie procesora CPU, operacji na dysku lub aktywności sieciowej) do którego można dodać warunkowych wartości. Ponadto może mieć wyzwalaczy na podstawie kolejki komunikatów lub metrykę niektórych innych zasobów platformy Azure skojarzonych z Twoją subskrypcją.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Po skonfigurowaniu reguły wybierz **OK** znajdujący się u dołu bloku reguły.

## <a name="back-to-manual-scale"></a>Powrót do skalowanie ręczne
Przejdź do [skalowanie ustawienia](#where-scale-is-located) i ustaw **skalowanie przez** opcję **liczba wystąpień ustawiana ręcznie**.

![Ustawienia skalowania usług chmury za pomocą profilu i reguł](./media/cloud-services-how-to-scale-portal/manual-basics.png)

To ustawienie powoduje usunięcie automatyczne skalowanie z roli, a następnie bezpośrednio możesz Ustaw liczbę wystąpień.

1. Opcja (ręczne lub automatyczne) skali.
2. Suwak wystąpienia roli, aby ustawić wystąpień, aby możliwe było skalowanie.
3. Wystąpienia roli, aby możliwe było skalowanie.

Po skonfigurowaniu ustawień skalowania, wybierz **Zapisz** ikonę u góry.
