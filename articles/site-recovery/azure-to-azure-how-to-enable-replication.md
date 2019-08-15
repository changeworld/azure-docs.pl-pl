---
title: Skonfiguruj replikację maszyn wirtualnych platformy Azure w Azure Site Recovery | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania replikacji dla maszyn wirtualnych platformy Azure z jednego regionu świadczenia usługi Azure do innego przy użyciu Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: asgang
ms.openlocfilehash: 7559bfd3d97f7b430b92578473501b519eb0a07f
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934559"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Replikowanie maszyn wirtualnych platformy Azure do innego regionu platformy Azure


W tym artykule opisano sposób włączania replikacji maszyn wirtualnych platformy Azure z jednego regionu świadczenia usługi Azure do innego.

## <a name="before-you-start"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że przygotowano do wdrożenia Site Recovery, zgodnie z opisem w [samouczku odzyskiwania po awarii z platformy Azure do platformy Azure](azure-to-azure-tutorial-enable-replication.md).

Wymagania wstępne powinny mieć miejsce i należy utworzyć magazyn Recovery Services.


## <a name="enable-replication"></a>Włączanie replikacji

Włącz replikację. W tej procedurze przyjęto założenie, że podstawowy region platformy Azure jest Azja Wschodnia, a region pomocniczy to Azja Wschodnia Południowe.

1. W magazynie kliknij pozycję **+ Replikuj**.
2. Zwróć uwagę na następujące pola:
   - **Źródło**: Punkt pochodzenia maszyn wirtualnych, który w tym przypadku jest **platformą Azure**.
   - **Lokalizacja źródłowa**: Region świadczenia usługi Azure, w którym chcesz chronić maszyny wirtualne. Na tej ilustracji lokalizacja źródłowa to "Azja Wschodnia"
   - **Model wdrażania**: Model wdrażania platformy Azure dla maszyn źródłowych.
   - **Subskrypcja źródłowa**: Subskrypcja, do której należą źródłowe maszyny wirtualne. Może to być dowolna subskrypcja w obrębie tej samej dzierżawy usługi Azure Active Directory, w której znajduje się magazyn usługi Recovery Services.
   - **Grupa zasobów**: Grupa zasobów, do której należą źródłowe maszyny wirtualne. W następnym kroku zostaną wyświetlone wszystkie maszyny wirtualne należące do wybranej grupy zasobów.

     ![Włączanie replikacji](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. W **Virtual Machines > wybierz pozycję Maszyny wirtualne**, kliknij i zaznacz każdą maszynę wirtualną, którą chcesz replikować. Możesz wybrać tylko te maszyny, dla których można włączyć replikację. Następnie kliknij przycisk **OK**.
    ![Włączanie replikacji](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. W obszarze **Ustawienia**można opcjonalnie skonfigurować ustawienia lokacji docelowej:

   - **Lokalizacja**docelowa: Lokalizacja, w której zostaną zreplikowane dane źródłowej maszyny wirtualnej. W zależności od lokalizacji wybranych maszyn Site Recovery będzie udostępnić listę odpowiednich regionów docelowych. Zalecamy, aby lokalizacja docelowa była taka sama, jak lokalizacja magazynu Recovery Services.
   - **Subskrypcja docelowa**: Subskrypcja docelowa używana na potrzeby odzyskiwania po awarii. Domyślnie subskrypcja docelowa będzie taka sama jak subskrypcja źródłowa.
   - **Docelowa grupa zasobów**: Grupa zasobów, do której należą wszystkie zreplikowane maszyny wirtualne.
       - Domyślnie Site Recovery tworzy nową grupę zasobów w regionie docelowym z sufiksem "ASR" w nazwie.
       - Jeśli grupa zasobów utworzona przez Site Recovery już istnieje, zostanie ponownie użyta.
       - Można dostosować ustawienia grupy zasobów.
       - Lokalizacją docelowej grupy zasobów może być dowolny region świadczenia usługi Azure, z wyjątkiem regionu, w którym są hostowane źródłowe maszyny wirtualne.
   - **Docelowa sieć wirtualna**: Domyślnie Site Recovery tworzy nową sieć wirtualną w regionie docelowym z sufiksem "ASR" w nazwie. Jest on mapowany do sieci źródłowej i używany do ochrony w przyszłości. [Dowiedz się więcej](site-recovery-network-mapping-azure-to-azure.md) na temat mapowania sieci.
   - **Docelowe konta magazynu (źródłowa maszyna wirtualna nie używa dysków zarządzanych)** : Domyślnie program Site Recovery tworzy nowe docelowe konto magazynu naśladując konfigurację magazynu źródłowej maszyny wirtualnej. W przypadku, gdy konto magazynu już istnieje, jest ponownie używane.
   - **Dyski zarządzane przez replikę (źródłowa maszyna wirtualna korzysta z dysków zarządzanych)** : Site Recovery tworzy nowe dyski zarządzane przez repliki w regionie docelowym, aby dublować dyski zarządzane źródłowej maszyny wirtualnej z tym samym typem magazynu (w warstwie Standardowa lub Premium) co dysk zarządzany źródłowej maszyny wirtualnej.
   - **Konta magazynu pamięci**podręcznej: Site Recovery wymaga dodatkowego konta magazynu o nazwie Magazyn pamięci podręcznej w regionie źródłowym. Wszystkie zmiany wykonywane na źródłowych maszynach wirtualnych są śledzone i wysyłane do konta magazynu pamięci podręcznej przed replikowaniem ich do lokalizacji docelowej. To konto magazynu powinno być standardowe.
   - **Docelowe zestawy dostępności**: Domyślnie Site Recovery tworzy nowy zestaw dostępności w regionie docelowym z sufiksem "ASR" w nazwie, dla maszyn wirtualnych, które są częścią zestawu dostępności w regionie źródłowym. Jeśli zestaw dostępności utworzony przez Site Recovery już istnieje, jest ponownie używany.
   - **Docelowe strefy dostępności**: Domyślnie usługa Site Recovery przypisuje w regionie docelowym ten sam numer strefy co regionu źródłowego, jeśli region docelowy obsługuje strefy dostępności.

     Jeśli region docelowy nie obsługuje stref dostępności, docelowe maszyny wirtualne są konfigurowane domyślnie jako pojedyncze wystąpienia. W razie potrzeby można skonfigurować takie maszyny wirtualne jako część zestawów dostępności w regionie docelowym, klikając pozycję „Dostosuj”.

     >[!NOTE]
     >Nie można zmienić typu dostępności — pojedyncze wystąpienie, zestaw dostępności, strefa dostępności — po włączeniu replikacji. W celu zmiany typu dostępności należy wyłączyć i włączyć replikację.
     >
    
   - **Zasady replikacji**: Definiuje on ustawienia dla historii przechowywania punktów odzyskiwania i częstotliwości migawek spójnej na poziomie aplikacji. Domyślnie Azure Site Recovery tworzy nowe zasady replikacji z ustawieniami domyślnymi "24 godziny" dla przechowywania punktów odzyskiwania i "4 godzin" dla częstotliwości migawek spójnej na poziomie aplikacji.

     ![Włączanie replikacji](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>Włącz replikację dla dodanych dysków

Po dodaniu dysków do maszyny wirtualnej platformy Azure, dla której włączono replikację, występują następujące sytuacje:
-   Kondycja replikacji maszyny wirtualnej zawiera ostrzeżenie, a Uwaga informuje o tym, że co najmniej jeden dysk jest dostępny do ochrony.
-   Jeśli włączysz ochronę dla dodanych dysków, ostrzeżenie zniknie po początkowej replikacji dysku.
-   Jeśli nie zdecydujesz się na włączenie replikacji dla dysku, możesz wybrać opcję odrzucania ostrzeżenia.

    
    ![Dodano nowy dysk](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Aby włączyć replikację dla dodanego dysku, wykonaj następujące czynności:

1.  W magazynie > **zreplikowane elementy**kliknij maszynę wirtualną, do której dodano dysk.
2.  Kliknij pozycję **dyski**, a następnie wybierz dysk danych, dla którego chcesz włączyć replikację (te dyski mają stan **niechroniony** ).
3.  W obszarze **szczegóły dysku**kliknij pozycję **Włącz replikację**.

    ![Włącz replikację dla dodanego dysku](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

Po uruchomieniu zadania włączania replikacji i zakończeniu początkowej replikacji zostanie usunięte ostrzeżenie o kondycji replikacji dla problemu z dyskiem.


  
## <a name="customize-target-resources"></a>Dostosowywanie zasobów docelowych

Można modyfikować domyślne ustawienia docelowe używane przez Site Recovery.

1. Kliknij przycisk **Dostosuj:** obok pozycji "subskrypcja docelowa", aby zmodyfikować domyślną subskrypcję docelową. Wybierz subskrypcję z listy wszystkich subskrypcji dostępnych w tej samej dzierżawie usługi Azure Active Directory (AAD).

2. Kliknij przycisk **Dostosuj:** aby zmodyfikować ustawienia domyślne:
    - W obszarze docelowa **Grupa zasobów**wybierz grupę zasobów z listy wszystkich grup zasobów w lokalizacji docelowej subskrypcji.
    - W obszarze docelowa **Sieć wirtualna**wybierz sieć z listy wszystkich sieci wirtualnych w lokalizacji docelowej.
    - W obszarze **zestaw dostępności**można dodać ustawienia zestawu dostępności do maszyny wirtualnej, jeśli są one częścią zestawu dostępności w regionie źródłowym.
    - W obszarze **docelowe konta magazynu**wybierz konto, którego chcesz użyć.

        ![Włączanie replikacji](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. Kliknij przycisk **Dostosuj:** aby zmodyfikować ustawienia replikacji.
4. W obszarze spójność z **obsługą wiele maszyn wirtualnych**Wybierz Maszyny wirtualne, które mają być replikowane ze sobą.
    - W przypadku przejścia w tryb failover wszystkie maszyny w grupie replikacji będą korzystać ze współdzielonych punktów odzyskiwania, spójnych na poziomie awarii i aplikacji.
    - Włączenie spójności wielu maszyn wirtualnych może wpłynąć na wydajność obciążeń (w miarę intensywnego użycia procesora). Należy ją włączyć tylko wtedy, gdy na maszynach działa to samo obciążenie, a na wielu maszynach jest wymagana spójność.
    - Na przykład jeśli aplikacja ma 2 SQL Server maszyny wirtualne i dwa serwery sieci Web, należy dodać do grupy replikacji tylko SQL Server maszyny wirtualne.
    - W grupie replikacji można wybrać maksymalnie 16 maszyn wirtualnych.
    - Jeśli włączono spójność między wieloma maszynami wirtualnymi, maszyny z grupy replikacji komunikują się między sobą przez port 20004.
    - Upewnij się, że urządzenie zapory nie blokuje komunikacji wewnętrznej między maszynami wirtualnymi przez port 20004.
    - Jeśli chcesz, aby maszyny wirtualne z systemem Linux były częścią grupy replikacji, upewnij się, że ruch wychodzący na porcie 20004 został ręcznie otwarty zgodnie ze wskazówkami dotyczącymi konkretnej wersji systemu Linux.
![Włączanie replikacji](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)
    
5. Kliknij pozycję **Utwórz zasób** > docelowy**Włącz replikację**.
6. Po włączeniu maszyn wirtualnych do replikacji można sprawdzić stan kondycji maszyny wirtualnej w obszarze **zreplikowane elementy**

>[!NOTE]
>Podczas replikacji początkowej stan może zająć trochę czasu, bez postępu. Kliknij przycisk **Odśwież** , aby uzyskać najnowszy stan.
>

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](site-recovery-test-failover-to-azure.md) o uruchamianiu testowej pracy w trybie failover.
