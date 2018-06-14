---
title: Automatyczne skalowanie usługi w chmurze w portalu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować regułę automatycznego skalowania dla roli sieci web usługi w chmurze lub roli proces roboczy na platformie Azure za pomocą portalu.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: 701d4404-5cc0-454b-999c-feb94c1685c0
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: adegeo
ms.openlocfilehash: 0eea38cdb9827ab6e322025ff344ebbab0e83da3
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2018
ms.locfileid: "27992276"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Jak skonfigurować automatyczne skalowanie usługi w chmurze w portalu

Warunki można ustawić dla roli proces roboczy usługi chmury, wyzwalające skali przychodzący lub wychodzący operacji. Warunki dla roli może być oparta na Procesora, dysku lub obciążenia sieciowego w roli. Można również ustawić warunek na podstawie kolejki komunikatów lub Metryka innego zasobu platformy Azure skojarzonych z Twoją subskrypcją.

> [!NOTE]
> Ten artykuł dotyczy usługi w chmurze role sieci web i proces roboczy. Po utworzeniu maszyny wirtualnej (klasyczne) bezpośrednio, znajduje się w usłudze w chmurze. Standardowa maszyny wirtualnej można skalować przez skojarzenie jej z [zestawu dostępności](../virtual-machines/windows/classic/configure-availability-classic.md) i ręcznie włączyć je lub wyłączyć.

## <a name="considerations"></a>Zagadnienia do rozważenia
Przed skonfigurowaniem skalowania dla aplikacji, należy rozważyć następujące informacje:

* Skalowanie jest zagrożony użycia rdzeni.

    Większe wystąpień roli za pomocą większej liczby rdzeni. Można skalować aplikacji tylko w ramach limitu rdzeni dla Twojej subskrypcji. Na przykład załóżmy, że w subskrypcji ma limit liczby rdzeni (20). Po uruchomieniu aplikacji z dwie średnich chmury usługi (łącznie 4 rdzenie) można tylko zwiększać innych wdrożeń usługi w chmurze w ramach subskrypcji przez pozostałe 16 rdzeni. Aby uzyskać więcej informacji na temat rozmiarów, zobacz [rozmiary usługi w chmurze](cloud-services-sizes-specs.md).

* Możesz skalować oparte na próg komunikat kolejki. Aby uzyskać więcej informacji o tym, jak używać kolejek, zobacz [sposobu korzystania z usługi magazynowania kolejki](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Można również skalować innych zasobów skojarzonych z Twoją subskrypcją.

* Aby włączyć wysoką dostępność aplikacji, należy upewnić się, że jest wdrażany z dwóch lub więcej wystąpień roli. Aby uzyskać więcej informacji, zobacz [umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/).

* Automatyczne skalowanie tylko wtedy, gdy wszystkie role są w **gotowe** stanu.  


## <a name="where-scale-is-located"></a>Gdzie znajduje się skali
Po wybraniu usługi w chmurze powinny być widoczne bloku usługi chmury.

1. W bloku usługi w chmurze na **ról i wystąpień** kafelka, wybierz nazwę usługi w chmurze.   
   **WAŻNE**: Upewnij się, że zostało rolą usługi chmury nie znajduje się poniżej roli wystąpienia roli.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Wybierz **skali** kafelka.

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Automatyczne skalowanie
Można skonfigurować ustawienia skalowania dla roli, albo w dwóch trybach **ręczne** lub **automatyczne**. Ręczne zgodnie z regułami, wartość bezwzględną liczby wystąpień. Automatyczne umożliwia jednak należy do zestawu reguły, które kontrolują sposób oraz znacznie można skalować.

Ustaw **skalowanie przez** opcji w celu **reguły wydajności i harmonogram**.

![Ustawienia skalowania usługi chmury z profilu i reguł](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Istniejący profil.
2. Dodaj regułę dla profilu nadrzędnej.
3. Dodaj inny profil.

Wybierz **Dodaj profil**. Profil określa tryb, w którym ma być używany w skali: **zawsze**, **cyklu**, **Stała data**.

Po skonfigurowaniu profilu i reguł, wybierz **zapisać** w górnym.

#### <a name="profile"></a>Profil
Profil Ustawia minimalną i maksymalną wystąpień skali, a także, gdy ten zakres skali jest aktywny.

* **Zawsze**

    Zawsze Zachowuj ten zakres dostępnych wystąpień.  

    ![Usługi w chmurze, która zawsze skalowania](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Cyklu**

    Wybierz zestaw dni tygodnia do skalowania.

    ![Skali usługi chmury, za pomocą harmonogramu cyklu](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Daty stałej**

    Stały zakres dat można skalować roli.

    ![Skali usługi chmury z datą stałej](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Po skonfigurowaniu profilu wybierz **OK** przycisk w dolnej części bloku profilu.

#### <a name="rule"></a>Reguła
Reguły są dodawane do profilu i stanowią warunek wyzwalający skali.

Wyzwalacz reguła opiera się na metryki usługi w chmurze (użycie procesora CPU, aktywności dysku lub działanie sieci) do której można dodać wartość warunkowego. Ponadto można mieć wyzwalania na podstawie kolejki komunikatów lub Metryka innego zasobu platformy Azure skojarzonych z Twoją subskrypcją.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Po skonfigurowaniu reguły wybierz **OK** przycisk w dolnej części bloku reguły.

## <a name="back-to-manual-scale"></a>Powrót do ręcznego skali
Przejdź do [ustawień skalowania](#where-scale-is-located) i ustaw **skalowanie przez** opcji w celu **liczba wystąpień ustawiana ręcznie**.

![Ustawienia skalowania usługi chmury z profilu i reguł](./media/cloud-services-how-to-scale-portal/manual-basics.png)

To ustawienie powoduje usunięcie automatyczne skalowanie z roli i, można ustawić liczbę wystąpień bezpośrednio.

1. Opcja (ręczne lub automatyczne) skali.
2. Można ustawić wystąpienia można skalować do suwaka wystąpienia roli.
3. Wystąpienia roli, aby skalować do.

Po skonfigurowaniu ustawień skalowania, wybierz **zapisać** w górnym.
