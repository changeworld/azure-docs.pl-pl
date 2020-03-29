---
title: Automatyczne skalowanie usługi w chmurze w portalu | Dokumenty firmy Microsoft
description: Dowiedz się, jak korzystać z portalu w celu skonfigurowania reguł skalowania automatycznego dla roli sieci web usługi w chmurze lub roli procesu roboczego na platformie Azure.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: tagore
ms.openlocfilehash: 5880544137855a2ea5bcd6d6e4bada46563564ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360841"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Jak skonfigurować skalowanie automatyczne dla usługi w chmurze w portalu

Warunki można ustawić dla roli procesu roboczego usługi w chmurze, które wyzwalają operację skalowania w lub w poziomie. Warunki roli mogą być oparte na obciążeniu procesora CPU, dysku lub sieci roli. Można również ustawić warunek na podstawie kolejki komunikatów lub metryki innego zasobu platformy Azure skojarzonego z subskrypcją.

> [!NOTE]
> W tym artykule skupiono się na usługach w sieci Web usługi w chmurze i rolach procesów procesowych. Podczas tworzenia maszyny wirtualnej (klasycznej) bezpośrednio, jest ona hostowana w usłudze w chmurze. Standardową maszynę wirtualną można skalować, kojarząc ją z [zestawem dostępności](../virtual-machines/windows/classic/configure-availability-classic.md) i ręcznie włączać lub wyłączać.

## <a name="considerations"></a>Zagadnienia do rozważenia
Przed skonfigurowaniem skalowania dla aplikacji należy wziąć pod uwagę następujące informacje:

* Użycie rdzenia ma wpływ na skalowanie.

    Większe wystąpienia roli używają więcej rdzeni. Aplikację można skalować tylko w granicach rdzeni dla subskrypcji. Załóżmy na przykład, że subskrypcja ma limit 20 rdzeni. Jeśli uruchomisz aplikację z dwoma średnimi usługami w chmurze (łącznie 4 rdzenie), możesz skalować inne wdrożenia usług w chmurze w ramach subskrypcji tylko przez pozostałe 16 rdzeni. Aby uzyskać więcej informacji o rozmiarach, zobacz [Rozmiary usług w chmurze](cloud-services-sizes-specs.md).

* Można skalować na podstawie progu komunikatu kolejki. Aby uzyskać więcej informacji na temat korzystania z kolejek, zobacz [Jak korzystać z usługi magazynowania kolejek](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Można również skalować inne zasoby skojarzone z subskrypcją.

* Aby włączyć wysoką dostępność aplikacji, należy upewnić się, że jest ona wdrażana z co najmniej dwoma wystąpieniami roli. Aby uzyskać więcej informacji, zobacz [Umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/).

* Automatyczne skalowanie dzieje się tylko wtedy, gdy wszystkie role są w stanie **Gotowy.**  


## <a name="where-scale-is-located"></a>Gdzie znajduje się skala
Po wybraniu usługi w chmurze powinien być widoczny blok usługi w chmurze.

1. W bloku usługi w chmurze na **kafelku Role i wystąpienia** wybierz nazwę usługi w chmurze.   
   **WAŻNE:** Upewnij się, że klikniesz rolę usługi w chmurze, a nie wystąpienie roli, które znajduje się poniżej roli.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Wybierz kafelek **skali.**

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Waga automatyczna
Ustawienia skali dla roli można skonfigurować za pomocą dwóch trybów **ręcznych** lub **automatycznych**. Podręcznik jest tak, jak można się spodziewać, można ustawić bezwzględną liczbę wystąpień. Automatyczne jednak pozwala ustawić reguły, które określają, jak i o ile należy skalować.

Ustaw opcję **Skala według** **reguł planowania i wydajności**.

![Ustawienia skalowania usług w chmurze za pomocą profilu i reguły](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Istniejący profil.
2. Dodaj regułę dla profilu nadrzędnego.
3. Dodaj inny profil.

Wybierz **pozycję Dodaj profil**. Profil określa, który tryb ma być używany dla skali: **zawsze**, **cykl**, **stała data**.

Po skonfigurowaniu profilu i reguł wybierz ikonę **Zapisz** u góry.

#### <a name="profile"></a>Profil
Profil ustawia minimalne i maksymalne wystąpienia skali, a także wtedy, gdy ten zakres skalowania jest aktywny.

* **Zawsze**

    Zawsze należy zachować ten zakres wystąpień dostępne.  

    ![Usługa w chmurze, która zawsze jest skalowana](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Cyklu**

    Wybierz zestaw dni tygodnia do skalowania.

    ![Skalowanie usługi w chmurze z harmonogramem cyklu](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Stała data**

    Stały zakres dat do skalowania roli.

    ![Skala usługi CLoud ze stałą datą](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Po skonfigurowaniu profilu wybierz przycisk **OK** u dołu bloku profilu.

#### <a name="rule"></a>Reguła
Reguły są dodawane do profilu i reprezentują warunek, który wyzwala skalę.

Wyzwalacz reguły jest oparty na metryce usługi w chmurze (użycie procesora CPU, aktywność dysku lub aktywność sieciowa), do której można dodać wartość warunkową. Ponadto można mieć wyzwalacz na podstawie kolejki komunikatów lub metryki innego zasobu platformy Azure skojarzone z subskrypcją.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Po skonfigurowaniu reguły wybierz przycisk **OK** u dołu bloku reguły.

## <a name="back-to-manual-scale"></a>Powrót do skalowania ręcznego
Przejdź do [ustawień skali](#where-scale-is-located) i ustaw opcję **Skaluj według** liczby **wystąpień, które wprowadzam ręcznie**.

![Ustawienia skalowania usług w chmurze za pomocą profilu i reguły](./media/cloud-services-how-to-scale-portal/manual-basics.png)

To ustawienie usuwa automatyczne skalowanie z roli, a następnie można ustawić liczbę wystąpień bezpośrednio.

1. Opcja skalowania (ręczna lub zautomatyzowana).
2. Suwak wystąpienia roli, aby ustawić wystąpienia do skalowania.
3. Wystąpienia roli do skalowania.

Po skonfigurowaniu ustawień skali wybierz ikonę **Zapisz** u góry.



