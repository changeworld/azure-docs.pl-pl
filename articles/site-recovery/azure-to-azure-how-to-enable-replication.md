---
title: Konfigurowanie replikacji dla maszyn wirtualnych platformy Azure w usłudze Azure Site Recovery
description: Dowiedz się, jak skonfigurować replikację do innego regionu dla maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2018
ms.openlocfilehash: 1c6b7cfbf193f02598052b6922efec17fb16ec83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973686"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Replikowanie maszyn wirtualnych platformy Azure do innego regionu platformy Azure


W tym artykule opisano sposób włączania replikacji maszyn wirtualnych platformy Azure z jednego regionu platformy Azure do drugiego.

## <a name="before-you-start"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że zostało przygotowane do wdrożenia usługi Site Recovery, zgodnie z opisem w [samouczku odzyskiwania po awarii platformy Azure na platformie Azure.](azure-to-azure-tutorial-enable-replication.md)

Wymagania wstępne powinny być w miejscu i należy utworzyć magazyn usług odzyskiwania.


## <a name="enable-replication"></a>Włączanie replikacji

Włącz replikację. W tej procedurze przyjęto założenie, że podstawowym regionem platformy Azure jest Azja Wschodnia, a region pomocniczy to Azja Południowo-Wschodnia.

1. W przechowalni kliknij przycisk **+Replikuj**.
2. Zwróć uwagę na następujące pola:
   - **Źródło**: Punkt pochodzenia maszyn wirtualnych, który w tym przypadku jest **Azure**.
   - **Lokalizacja źródła:** region platformy Azure, z którego chcesz chronić maszyny wirtualne. Na tej ilustracji lokalizacją źródłową jest "Azja Wschodnia"
   - **Model wdrażania:** model wdrażania platformy Azure maszyn źródłowych.
   - **Subskrypcja źródłona:** subskrypcja, do której należą źródłowe maszyny wirtualne. Może to być dowolna subskrypcja w obrębie tej samej dzierżawy usługi Azure Active Directory, w której znajduje się magazyn usługi Recovery Services.
   - **Grupa zasobów:** Grupa zasobów, do której należą źródłowe maszyny wirtualne. Wszystkie maszyny wirtualne w ramach wybranej grupy zasobów są wymienione w celu ochrony w następnym kroku.

     ![Włączanie replikacji](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. W **maszynach wirtualnych > Wybierz maszyny wirtualne**kliknij i wybierz każdą maszynę wirtualną, którą chcesz replikować. Możesz wybrać tylko te maszyny, dla których można włączyć replikację. Następnie kliknij przycisk **OK**.
    ![Włączanie replikacji](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. W **ustawieniach**można opcjonalnie skonfigurować docelowe ustawienia witryny:

   - **Lokalizacja docelowa:** lokalizacja, w której będą replikowane dane źródłowej maszyny wirtualnej. W zależności od wybranej lokalizacji maszyn, Site Recovery zapewni Ci listę odpowiednich regionów docelowych. Zaleca się, aby lokalizacja docelowa była taka sama jak lokalizacja magazynu usług odzyskiwania.
   - **Subskrypcja docelowa**: subskrypcja docelowa używana na potrzeby odzyskiwania po awarii. Domyślnie subskrypcja docelowa będzie taka sama jak subskrypcja źródłowa.
   - **Docelowa grupa zasobów:** grupa zasobów, do której należą wszystkie zreplikowane maszyny wirtualne.
       - Domyślnie Program Site Recovery tworzy nową grupę zasobów w regionie docelowym z sufiksem "asr" w nazwie.
       - Jeśli grupa zasobów utworzona przez odzysk witryny już istnieje, jest ponownie używana.
       - Ustawienia grupy zasobów można dostosować.
       - Lokalizacja docelowej grupy zasobów może być dowolny region platformy Azure, z wyjątkiem regionu, w którym są hostowane źródłowe maszyny wirtualne.
   - **Docelowa sieć wirtualna:** Domyślnie usługa Site Recovery tworzy nową sieć wirtualną w regionie docelowym z sufiksem "asr" w nazwie. Jest to mapowane do sieci źródłowej i używane do wszelkiej przyszłej ochrony. [Dowiedz się więcej](site-recovery-network-mapping-azure-to-azure.md) o mapowaniu sieci.
   - **Docelowe konta magazynu (źródłowa maszyna wirtualna nie używa dysków zarządzanych):** Domyślnie usługa Site Recovery tworzy nowe konto magazynu docelowego naśladujące źródłową konfigurację magazynu maszyn wirtualnych. W przypadku, gdy konto magazynu już istnieje, jest ponownie używane.
   - **Dyski zarządzane repliką (źródłowa maszyna wirtualna używa dysków zarządzanych):** Usługa Site Recovery tworzy nowe dyski zarządzane repliką w regionie docelowym w celu odzwierciedlenia dysków zarządzanych źródłowej maszyny Wirtualnej o tym samym typie magazynu (standard lub premium) co dysk zarządzany źródłowej maszyny Wirtualnej.
   - **Konta magazynu pamięci podręcznej:** Usługa Site Recovery wymaga dodatkowego konta magazynu o nazwie magazyn pamięci podręcznej w regionie źródłowym. Wszystkie zmiany zachodzące na źródłowych maszynach wirtualnych są śledzone i wysyłane do konta magazynu pamięci podręcznej przed replikowaniem ich do lokalizacji docelowej. To konto magazynu powinno być standardowe.
   - **Zestawy dostępności docelowej:** Domyślnie usługa Site Recovery tworzy nowy zestaw dostępności w regionie docelowym z sufiksem "asr" w nazwie dla maszyn wirtualnych, które są częścią zestawu dostępności w regionie źródłowym. Jeśli zestaw dostępności utworzony przez odzysk lokacji już istnieje, jest ponownie odtwarzany.
   - **Docelowe strefy dostępności**: domyślnie usługa Site Recovery przypisuje ten sam numer strefy, co region źródłowy, w regionie docelowym, jeśli region docelowy obsługuje strefy dostępności.

     Jeśli region docelowy nie obsługuje stref dostępności, docelowe maszyny wirtualne są konfigurowane domyślnie jako pojedyncze wystąpienia. W razie potrzeby można skonfigurować takie maszyny wirtualne jako część zestawów dostępności w regionie docelowym, klikając pozycję „Dostosuj”.

     >[!NOTE]
     >Nie można zmienić typu dostępności — pojedyncze wystąpienie, zestaw dostępności, strefa dostępności — po włączeniu replikacji. W celu zmiany typu dostępności należy wyłączyć i włączyć replikację.
     >

   - **Zasady replikacji:** Definiuje ustawienia historii przechowywania punktów odzyskiwania i częstotliwości migawek spójnych aplikacji. Domyślnie usługa Azure Site Recovery tworzy nową zasadę replikacji z domyślnymi ustawieniami "24 godzin" dla przechowywania punktów odzyskiwania i "4 godziny" dla częstotliwości migawek spójnej aplikacji.

     ![Włączanie replikacji](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>Włączanie replikacji dla dodanych dysków

Jeśli dodasz dyski do maszyny Wirtualnej platformy Azure, dla której replikacja jest włączona, wystąpią następujące zdarzenia:
-   Kondycja replikacji maszyny Wirtualnej wyświetla ostrzeżenie, a notatka informuje, że jeden lub więcej dysków są dostępne do ochrony.
-   Jeśli włączysz ochronę dodanych dysków, ostrzeżenie zniknie po początkowej replikacji dysku.
-   Jeśli nie chcesz włączać replikacji dysku, możesz wybrać, aby odrzucić ostrzeżenie.


    ![Dodano nowy dysk](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Aby włączyć replikację dodanego dysku, wykonaj następujące czynności:

1.  W przechowalni > **elementy replikowane**kliknij maszynę wirtualną, do której dodano dysk.
2.  Kliknij pozycję **Dyski**, a następnie wybierz dysk danych, dla którego chcesz włączyć replikację (dyski te mają stan **Niechroniony).**
3.  W **opcji Szczegóły dysku**kliknij pozycję Włącz **replikację**.

    ![Włączanie replikacji dla dodanego dysku](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

Po uruchomieniu zadania replikacji włączania i zakończeniu replikacji początkowej ostrzeżenie o kondycji replikacji dla problemu z dyskiem zostanie usunięte.



## <a name="customize-target-resources"></a>Dostosowywanie zasobów docelowych

Można zmodyfikować domyślne ustawienia docelowe używane przez odzyskiwanie witryny.

1. Kliknij **przycisk Dostosuj:** obok pozycji "Subskrypcja docelowa", aby zmodyfikować domyślną subskrypcję docelową. Wybierz subskrypcję z listy wszystkich subskrypcji dostępnych w tej samej dzierżawie usługi Azure Active Directory (AAD).

2. Kliknij **przycisk Dostosuj:** aby zmodyfikować ustawienia domyślne:
    - W **grupie zasobów docelowych**wybierz grupę zasobów z listy wszystkich grup zasobów w lokalizacji docelowej subskrypcji.
    - W **docelowej sieci wirtualnej**wybierz sieć z listy całej sieci wirtualnej w lokalizacji docelowej.
    - W **zestawie dostępności**można dodać ustawienia zestawu dostępności do maszyny Wirtualnej, jeśli są one częścią zestawu dostępności w regionie źródłowym.
    - Na **kontach magazynu docelowego**wybierz konto, którego chcesz użyć.

        ![Włączanie replikacji](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. Kliknij **przycisk Dostosuj:** aby zmodyfikować ustawienia replikacji.
4. W **obszarze Spójność wielu maszyn wirtualnych**wybierz maszyny wirtualne, które chcesz replikować razem.
    - W przypadku przejścia w tryb failover wszystkie maszyny w grupie replikacji będą korzystać ze współdzielonych punktów odzyskiwania, spójnych na poziomie awarii i aplikacji.
    - Włączenie spójności wielu maszyn wirtualnych może mieć wpływ na wydajność obciążenia (ponieważ jest to procesor intensywnie korzystający z procesora CPU). Należy włączyć tylko wtedy, gdy na komputerach jest uruchomione to samo obciążenie i potrzebujesz spójności na wielu komputerach.
    - Na przykład jeśli aplikacja ma 2 maszyny wirtualne programu SQL Server i dwa serwery sieci web, należy dodać tylko maszyny wirtualne programu SQL Server do grupy replikacji.
    - W grupie replikacji można wybrać maksymalnie 16 maszyn wirtualnych.
    - Jeśli włączono spójność między wieloma maszynami wirtualnymi, maszyny z grupy replikacji komunikują się między sobą przez port 20004.
    - Upewnij się, że nie ma urządzenia zapory blokującej komunikację wewnętrzną między maszynami wirtualnymi za pomocą portu 20004.
    - Jeśli chcesz, aby maszyny wirtualne z systemem Linux były częścią grupy replikacji, upewnij się, że ruch wychodzący na porcie 20004 jest otwierany ręcznie zgodnie ze wskazówkami dotyczącymi określonej wersji systemu Linux.
![Włączanie replikacji](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)

5. Kliknij **pozycję Utwórz zasób docelowy** > **Włącz replikację**.
6. Po włączeniu maszyn wirtualnych dla replikacji można sprawdzić stan kondycji maszyny wirtualnej w obszarze **Elementy replikowane**

>[!NOTE]
>Podczas replikacji początkowej stan może zająć trochę czasu, aby odświeżyć, bez postępu. Kliknij przycisk **Odśwież,** aby uzyskać najnowszy stan.
>

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](site-recovery-test-failover-to-azure.md) o uruchamianiu pracy awaryjnej testu.
