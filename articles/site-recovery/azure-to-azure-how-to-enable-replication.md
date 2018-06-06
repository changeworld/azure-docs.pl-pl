---
title: Skonfigurować replikację dla maszyn wirtualnych platformy Azure w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania replikację dla maszyn wirtualnych platformy Azure z jednego regionu Azure, do drugiego za pomocą usługi Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/31/2018
ms.author: asgang
ms.openlocfilehash: 175b71488b429b3fd69d23db6cc9318d8db20568
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715976"
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Replikowanie maszyn wirtualnych platformy Azure do innego regionu systemu Azure



W tym artykule opisano sposób włączania replikacji maszyn wirtualnych platformy Azure z jednego regionu Azure do innego.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz już skonfigurowaną Site Recovery w tym scenariuszu zgodnie z opisem w [samouczek Azure do platformy Azure](azure-to-azure-tutorial-enable-replication.md). Upewnij się, że zostały przygotowane wymagań wstępnych i utworzyć magazyn usług odzyskiwania.



## <a name="enable-replication"></a>Włączanie replikacji

Włącz replikację. Tej procedurze przyjęto założenie, że podstawowy region platformy Azure to Azja Wschodnia, a region pomocniczy południowo Azja Wschodnia.

1. W magazynie, kliknij przycisk **+ Replikuj**.
2. Należy uwzględnić następujące pola:
    - **Źródło**: punkt początkowy maszyn wirtualnych, w tym przypadku jest **Azure**.
    - **Lokalizacja źródłowa**: regionu Azure, z której mają być chronione maszyny wirtualne. Na tej ilustracji lokalizacja źródłowa jest "Azja Wschodnia"
    - **Model wdrażania**: model wdrożenia usługi Azure maszyny źródłowej.
    - **Grupa zasobów**: grupy zasobów, do której należy źródło maszyn wirtualnych. Wszystkie maszyny wirtualne w wybranej grupy zasobów są wyświetlane w celu ochrony w następnym kroku.

    ![Włączanie replikacji](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. W **maszyny wirtualnej > Wybierz maszyny wirtualne**, kliknij i zaznacz każdej maszyny Wirtualnej, które mają być replikowane. Możesz wybrać tylko te maszyny, dla których można włączyć replikację. Następnie kliknij przycisk **OK**.
    ![Włączanie replikacji](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. W **ustawienia**, można opcjonalnie skonfigurować ustawienia lokacji docelowej:

    - **Lokalizacja docelowa**: lokalizacja, gdzie będą replikowane dane źródłowe maszyny wirtualnej. W zależności od lokalizacji wybranych maszyn Site Recovery zapewnia listę regionów odpowiedniego elementu docelowego. Firma Microsoft zaleca zachowanie lokalizacji docelowej takie same jak lokalizacja magazynu usług odzyskiwania.
    - **Docelowa grupa zasobów**: grupy zasobów, aby wszystkie zreplikowanej maszyny wirtualne należą. Domyślnie usługi Azure Site Recovery tworzy nową grupę zasobów w regionie docelowych z nazwą składającą się z sufiksem "asr". W przypadku grupy zasobów utworzonej za pomocą usługi Azure Site Recovery już istnieje, zostanie on użyty ponownie. Można również dostosować go, jak pokazano w poniższej sekcji. Lokalizacja docelowa grupa zasobów może być dowolny region platformy Azure z wyjątkiem regionu, w którym znajdują się maszyny wirtualne źródła.
    - **Docelowa sieci wirtualnej**: domyślnie Site Recovery tworzy nową sieć wirtualną w regionie docelowych z nazwą składającą się z sufiksem "asr". To jest zamapowana na sieć źródła, a następnie używane do wszelkich przyszłych ochrony. [Dowiedz się więcej](site-recovery-network-mapping-azure-to-azure.md) o mapowania sieci.
    - **Docelowa kont magazynu (Jeśli źródło maszyny Wirtualnej nie korzysta z zarządzanego dysków)**: Domyślnie, Usługa Site Recovery tworzy nowe konto magazynu docelowego mimicking konfigurację magazynu maszyny Wirtualnej źródłowego. W przypadku, gdy konto magazynu już istnieje, zostanie on użyty ponownie.
    - **Repliki dyskach zarządzanych (Jeśli źródło maszyny Wirtualnej używa dysków zarządzanych)**: Usługa Site Recovery tworzy nowe dyski replik zarządzanych w region docelowy dublowanego zarządzanych dysków maszyny Wirtualnej źródłowego tego samego typu magazynu (standardowa lub premium) jako źródłowej maszyny Wirtualnej na zarządzany dysku.
    - **Buforowanie kont magazynu**: Usługa Site Recovery wymaga konta dodatkowego magazynu o nazwie magazynu pamięci podręcznej w regionie źródła. Wszystkie zmiany, które są wykonywane na maszynach wirtualnych źródła są śledzone i wysyłane do konta magazynu pamięci podręcznej przed replikowanie tych do lokalizacji docelowej.
    - **Zestaw dostępności**: Domyślnie program Azure Site Recovery tworzy dostępność nowych ustawiony w regionie docelowych z nazwą składającą się z sufiksem "asr". W przypadku, gdy zestaw dostępności utworzone przez usługę Azure Site Recovery już istnieje, zostanie on użyty ponownie.
    - **Zasady replikacji**: definiuje ustawienia odzyskiwania punktu przechowywania historii i aplikacji migawki dotyczącej spójności częstotliwości. Domyślnie program Azure Site Recovery tworzy nowe zasady replikacji z ustawieniami domyślnymi 24 godzin przechowywania punktu odzyskiwania i "60 minut częstotliwości migawki dotyczącej spójności aplikacji.

    ![Włączanie replikacji](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>Dostosowywanie zasobami obiektów docelowych

Można zmodyfikować ustawienia docelowego domyślne używane przez usługę Site Recovery.

1. Kliknij przycisk **Dostosuj:** Aby zmodyfikować domyślne ustawienia:
    - W **docelowa grupa zasobów**, wybierz grupę zasobów z listy wszystkich grup zasobów w lokalizacji docelowej subskrypcji.
    - W **sieci wirtualnej docelowego**, wybierz sieć, z listy sieci wirtualnej w lokalizacji docelowej.
    - W **zestawu dostępności**, można dodać ustawienia zestawu dostępności maszyny wirtualnej, jeśli są one częścią zestawem dostępności w regionie źródłowym.
    - W **kont magazynu docelowego**, wybierz konto, którego chcesz użyć.

        ![Włączanie replikacji](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. Kliknij przycisk **tworzenie zasobu docelowego** > **włączania replikacji**.
3. Po włączeniu replikacji maszyn wirtualnych można sprawdzić stan kondycji maszyny Wirtualnej w obszarze **zreplikowane elementy**

>[!NOTE]
>Podczas replikacji początkowej stanu może potrwać pewien czas, aby odświeżyć bez postępu. Kliknij przycisk **Odśwież** przycisk, aby pobrać najnowszy stan.
>

# <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej](site-recovery-test-failover-to-azure.md) uruchomiony test trybu failover.
