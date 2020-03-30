---
title: Włączanie replikacji zaszyfrowanych maszyn wirtualnych platformy Azure w usłudze Azure Site Recovery
description: W tym artykule opisano sposób konfigurowania replikacji maszyn wirtualnych z dyskami z włączonym kluczem zarządzanym przez klienta (CMK) z jednego regionu platformy Azure do drugiego przy użyciu usługi Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/10/2020
ms.author: mayg
ms.openlocfilehash: 367f29237a3f2a634f209026df47b0cbd6ffc97c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75897964"
---
# <a name="replicate-machines-with-customer-managed-keys-cmk-enabled-disks"></a>Replikowanie maszyn z dyskami z obsługą kluczy zarządzanych przez klienta (CMK)

W tym artykule opisano sposób replikowania maszyn wirtualnych platformy Azure przy za pomocą dysków zarządzanych przez klienta (CMK), z jednego regionu platformy Azure do drugiego.

## <a name="prerequisite"></a>Wymagania wstępne
Przed włączeniem replikacji dla maszyn wirtualnych z włączonymi dyskami zarządzanymi z funkcją CMK należy utworzyć zestaw(y) szyfrowania dysku w regionie docelowym dla subskrypcji docelowej.

## <a name="enable-replication"></a>Włączanie replikacji

W tym przykładzie podstawowym regionem platformy Azure jest Azja Wschodnia, a region pomocniczy to Azja Południowo-Wschodnia.

1. W przechowalni wybierz **+Replikuj**.
2. Zwróć uwagę na następujące pola.
    - **Źródło**: Punkt pochodzenia maszyn wirtualnych, który w tym przypadku jest **Azure**.
    - **Lokalizacja źródła:** region platformy Azure, w którym chcesz chronić maszyny wirtualne. W tym przykładzie lokalizacją źródłą jest "Azja Wschodnia".
    - **Model wdrażania:** model wdrażania platformy Azure maszyn źródłowych.
    - **Subskrypcja źródłona:** subskrypcja, do której należą źródłowe maszyny wirtualne. Może to być dowolna subskrypcja, która znajduje się w tej samej dzierżawie usługi Azure Active Directory co magazyn usług odzyskiwania.
    - **Grupa zasobów:** Grupa zasobów, do której należą źródłowe maszyny wirtualne. Wszystkie maszyny wirtualne w wybranej grupie zasobów są wymienione w celu ochrony w następnym kroku.

3. W **maszynach wirtualnych** > **Wybierz maszyny wirtualne**wybierz każdą maszynę wirtualną, którą chcesz replikować. Możesz wybrać tylko te maszyny, dla których można włączyć replikację. Następnie wybierz przycisk **OK**.

4. W **ustawieniach**można skonfigurować następujące ustawienia witryny docelowej.

    - **Lokalizacja docelowa:** lokalizacja, w której będą replikowane dane źródłowej maszyny wirtualnej. Odzysk lokacji zawiera listę odpowiednich regionów docelowych na podstawie lokalizacji wybranego komputera. Zaleca się korzystanie z tej samej lokalizacji co lokalizacja magazynu usług odzyskiwania.
    - **Subskrypcja docelowa:** subskrypcja docelowa używana do odzyskiwania po awarii. Domyślnie subskrypcja docelowa jest taka sama jak subskrypcji źródłowej.
    - **Docelowa grupa zasobów:** grupa zasobów, do której należą wszystkie zreplikowane maszyny wirtualne. Domyślnie program Site Recovery tworzy nową grupę zasobów w regionie docelowym. Nazwa pobiera `asr` sufiks. Jeśli grupa zasobów już istnieje, który został utworzony przez usługę Azure Site Recovery, jest ponownie. Można również dostosować go, jak pokazano w poniższej sekcji. Lokalizacja docelowej grupy zasobów może być dowolny region platformy Azure z wyjątkiem regionu, w którym są hostowane źródłowe maszyny wirtualne.
    - **Docelowa sieć wirtualna:** Domyślnie usługa Site Recovery tworzy nową sieć wirtualną w regionie docelowym. Nazwa pobiera `asr` sufiks. Jest mapowany do sieci źródłowej i używany do wszelkiej przyszłej ochrony. [Dowiedz się więcej](site-recovery-network-mapping-azure-to-azure.md) o mapowaniu sieci.
    - **Docelowe konta magazynu (jeśli źródłowa maszyna wirtualna nie używa dysków zarządzanych)**: Domyślnie usługa Site Recovery tworzy nowe konto magazynu docelowego, naśladując konfigurację źródłowego magazynu maszyn wirtualnych. Jeśli konto magazynu już istnieje, jest ponownie używane.
    - **Replika dysków zarządzanych (jeśli źródłowa maszyna wirtualna używa dysków zarządzanych)**: Usługa Site Recovery tworzy nowe dyski zarządzane repliki w regionie docelowym w celu odzwierciedlenia dysków zarządzanych źródłowej maszyny Wirtualnej tego samego typu magazynu (standardowego lub premium) co dyski zarządzane źródłowej maszyny Wirtualnej.
    - **Konta magazynu pamięci podręcznej:** Usługa Site Recovery wymaga dodatkowego konta magazynu o nazwie *magazyn pamięci podręcznej* w regionie źródłowym. Wszystkie zmiany na źródłowych maszynach wirtualnych są śledzone i wysyłane do konta magazynu pamięci podręcznej. Są one następnie replikowane do lokalizacji docelowej.
    - **Zestaw dostępności:** Domyślnie usługa Site Recovery tworzy nowy zestaw dostępności w regionie docelowym. Nazwa ma `asr` sufiks. Jeśli zestaw dostępności utworzony przez odzysk witryny już istnieje, jest ponownie on akces.
    - **Zestawy szyfrowania dysków (DES)**: Usługa Site Recovery wymaga użycia zestawu szyfrowania dysku dla replik i docelowych dysków zarządzanych. Przed włączeniem replikacji należy wstępnie utworzyć des w subskrypcji docelowej i regionie docelowym. Domyślnie des nie jest zaznaczona. Musisz kliknąć na "Dostosuj", aby wybrać DES na dysku źródłowym.
    - **Zasady replikacji:** Definiuje ustawienia historii przechowywania punktów odzyskiwania i częstotliwości migawek spójnych z aplikacją. Domyślnie usługa Site Recovery tworzy nową zasadę replikacji z domyślnymi ustawieniami *24 godzin* przechowywania punktów odzyskiwania i *60 minut* dla częstotliwości migawek spójnych z aplikacją.

    ![Włącz replikację dla komputera z dyskami z obsługą cmk](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-enable-dr.png)

## <a name="customize-target-resources"></a>Dostosowywanie zasobów docelowych

Wykonaj następujące kroki, aby zmodyfikować domyślne ustawienia docelowe usługi Site Recovery.

1. Wybierz **pozycję Dostosuj** obok opcji "Subskrypcja docelowa", aby zmodyfikować domyślną subskrypcję docelową. Wybierz subskrypcję z listy subskrypcji, które są dostępne w dzierżawie usługi Azure AD.

2. Wybierz **pozycję Dostosuj** obok pozycji "Grupy zasobów, sieci, magazynu i dostępności", aby zmodyfikować następujące ustawienia domyślne:
    - W przypadku **docelowej grupy zasobów**wybierz grupę zasobów z listy grup zasobów w lokalizacji docelowej subskrypcji.
    - W przypadku **docelowej sieci wirtualnej**wybierz sieć z listy sieci wirtualnych w lokalizacji docelowej.
    - W przypadku **zestawu dostępności**można dodać ustawienia zestawu dostępności do maszyny Wirtualnej, jeśli są one częścią zestawu dostępności w regionie źródłowym.
    - W przypadku **kont magazynu docelowego**wybierz konto, którego chcesz użyć.

3. Wybierz **pozycję Dostosuj** obok pozycji "Ustawienia szyfrowania magazynu", aby wybrać docelową des dla każdego zarządzanego przez klienta dysku zarządzanego przez źródło (CMK). W momencie wyboru będzie można również zobaczyć, z którym docelowym magazynem kluczy jest skojarzony des.

4. Wybierz **pozycję Utwórz zasób docelowy** > **Włącz replikację**.
5. Po włączeniu maszyn wirtualnych do replikacji można sprawdzić stan kondycji maszyn wirtualnych w obszarze **Elementy replikowane**.

![Włącz replikację dla komputera z dyskami z obsługą cmk](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-customize-target-disk-properties.png)

>[!NOTE]
>Podczas replikacji początkowej stan może zająć trochę czasu, aby odświeżyć, bez widocznego postępu. Kliknij **przycisk Odśwież,** aby uzyskać najnowszy stan.

## <a name="faqs"></a>Często zadawane pytania

* Mam włączone CMK na istniejącym zreplikowanym elemencie, jak mogę zapewnić, że CMK jest stosowany w regionie docelowym, jak również?

    Możesz znaleźć nazwę dysku zarządzanego repliki (utworzonego przez usługę Azure Site Recovery w regionie docelowym) i dołączyć des do tego dysku repliki. Jednak nie będzie można zobaczyć szczegóły DES w disks bloku po dołączeniu go. Alternatywnie można wyłączyć replikację maszyny Wirtualnej i włączyć ją ponownie. Zapewni to, że zobaczysz des i szczegóły magazynu kluczy w bloku Dyski dla zreplikowanego elementu.

* Dodałem nowy dysk cmk włączony do zreplikowanego elementu. Jak replikować ten dysk za pomocą usługi Azure Site Recovery?

    Dodawanie nowego dysku włączonego cmk do istniejącego elementu replikowanego nie jest obsługiwane. Wyłącz replikację i ponownie włącz replikację maszyny wirtualnej.

