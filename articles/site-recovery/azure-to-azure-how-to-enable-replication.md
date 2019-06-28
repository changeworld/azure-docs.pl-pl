---
title: Konfigurowanie replikacji dla maszyn wirtualnych platformy Azure w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania replikacji dla maszyn wirtualnych platformy Azure między regionami platformy Azure do innego za pomocą Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: asgang
ms.openlocfilehash: 0dec8179cc9b69eeea679f517693e4debb9fb56e
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340703"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Replikowanie maszyn wirtualnych platformy Azure do innego regionu platformy Azure


W tym artykule opisano sposób włączania replikacji maszyn wirtualnych platformy Azure między regionami platformy Azure do innego.

## <a name="before-you-start"></a>Przed rozpoczęciem

W tym artykule założono, że przygotowanej dla wdrożenia usługi Site Recovery, zgodnie z opisem w [samouczek odzyskiwania po awarii Azure – Azure](azure-to-azure-tutorial-enable-replication.md).

Wymagania wstępne powinny zostać zastosowane, i należy utworzyć magazyn usługi Recovery Services.


## <a name="enable-replication"></a>Włączanie replikacji

Włącz replikację. Ta procedura zakłada, że Azure region podstawowy jest Azja Wschodnia, a w regionie pomocniczym Azja południowo-wschodnia.

1. W magazynie kliknij **+ Replikuj**.
2. Należy zwrócić uwagę następujących pól:
   - **Źródło**: Punkt początkowy maszyn wirtualnych, czyli w tym przypadku **Azure**.
   - **Lokalizacja źródłowa**: Region platformy Azure, z których chcesz chronić maszyn wirtualnych. Na tej ilustracji lokalizacja źródłowa jest "Azja Wschodnia"
   - **Model wdrażania**: Model wdrażania platformy Azure maszyna źródłowa.
   - **Źródło subskrypcji**: Subskrypcja, do której należą źródłowe maszyny wirtualne. Może to być dowolna subskrypcja w obrębie tej samej dzierżawy usługi Azure Active Directory, w której znajduje się magazyn usługi Recovery Services.
   - **Grupa zasobów**: Grupa zasobów, do której należą źródłowych maszyn wirtualnych. Wszystkie maszyny wirtualne w ramach wybranej grupy zasobów są wyświetlane do ochrony w następnym kroku.

     ![Włączanie replikacji](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. W **maszyny wirtualne > Wybierz maszyny wirtualne**, kliknij i zaznacz każdej maszyny Wirtualnej, którą chcesz replikować. Możesz wybrać tylko te maszyny, dla których można włączyć replikację. Następnie kliknij przycisk **OK**.
    ![Włączanie replikacji](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. W **ustawienia**, można opcjonalnie skonfigurować ustawienia lokacji docelowej:

   - **Lokalizacja docelowa**: Lokalizacja, w którym będą replikowane dane źródłowe maszyny wirtualnej. W zależności od lokalizacji wybranych maszyn, Usługa Site Recovery udostępnia listę regionów udało się. Firma Microsoft zaleca zachowywanie lokalizacji docelowej taka sama jako lokalizacja magazynu usługi Recovery Services.
   - **Subskrypcja docelowa**: Subskrypcja docelowa używana na potrzeby odzyskiwania po awarii. Domyślnie subskrypcja docelowa będzie taka sama jak subskrypcja źródłowa.
   - **Docelowa grupa zasobów**: Grupa zasobów, w którym wszystkie replikowane maszyny wirtualne należą.
       - Domyślnie usługa Site Recovery tworzy nową grupę zasobów w regionie docelowym sufiks "asr" w nazwie.
       - Jeśli grupa zasobów utworzonych przez usługę Site Recovery jest już istnieje, zostanie on użyty ponownie.
       - Można dostosować ustawienia grupy zasobów.
       - Lokalizacja docelowa grupa zasobów może być dowolnego regionu systemu Azure, z wyjątkiem regionu, w którym znajdują się źródłowe maszyny wirtualne.
   - **Docelowa sieć wirtualna**: Domyślnie usługa Site Recovery tworzy nową sieć wirtualną w regionie docelowym sufiks "asr" w nazwie. To jest mapowane na sieci źródłowej i używane do wszelkich przyszłych ochrony. [Dowiedz się więcej](site-recovery-network-mapping-azure-to-azure.md) o mapowaniu sieci.
   - **Docelowe konta magazynu (źródłowa maszyna wirtualna nie używa dysków zarządzanych)** : Domyślnie usługa Site Recovery tworzy nowe konto magazynu docelowego naśladując konfigurację magazynu źródłowej maszyny Wirtualnej. W przypadku, gdy konto magazynu już istnieje, zostanie on użyty ponownie.
   - **Dyski zarządzane repliki, (źródłowa maszyna wirtualna używa dysków zarządzanych)** : Usługa Site Recovery tworzy nowe dyski zarządzane repliki w regionie docelowym w celu zdublowania dysków zarządzanych źródłowej maszyny Wirtualnej przy użyciu tego samego typu magazynu (standardowa / premium), jak dysku zarządzanego źródłowej maszyny Wirtualnej.
   - **Konta magazynu pamięci podręcznej**: Usługa Site Recovery wymaga konta dodatkowego magazynu o nazwie magazynu pamięci podręcznej w regionie źródłowym. Wszystkie zmiany, które są wykonywane na źródłowe maszyny wirtualne są śledzone i wysyłane do konta magazynu pamięci podręcznej przed zreplikowaniem tych do lokalizacji docelowej.
   - **Docelowe zestawy dostępności**: Domyślnie usługa Site Recovery tworzy nowy zestaw dostępności w regionie docelowym sufiks "asr" w nazwie maszyny wirtualne, które są częścią zestaw dostępności w regionie źródłowym. Jeśli zestaw dostępności, utworzonych przez usługę Site Recovery jest już istnieje, zostanie on użyty ponownie.
   - **Docelowe strefy dostępności**: Domyślnie usługa Site Recovery przypisuje w regionie docelowym ten sam numer strefy co regionu źródłowego, jeśli region docelowy obsługuje strefy dostępności.

     Jeśli region docelowy nie obsługuje stref dostępności, docelowe maszyny wirtualne są konfigurowane domyślnie jako pojedyncze wystąpienia. W razie potrzeby można skonfigurować takie maszyny wirtualne jako część zestawów dostępności w regionie docelowym, klikając pozycję „Dostosuj”.

     >[!NOTE]
     >Nie można zmienić typu dostępności — pojedyncze wystąpienie, zestaw dostępności, strefa dostępności — po włączeniu replikacji. W celu zmiany typu dostępności należy wyłączyć i włączyć replikację.
     >
    
   - **Zasady replikacji**: Definiuje ustawienia odzyskiwania punktu przechowywania historii i aplikacji częstotliwość wykonywania migawek. Domyślnie usługa Azure Site Recovery tworzy nowe zasady replikacji z ustawieniami domyślnymi 24 godzin czas przechowywania punktu odzyskiwania i częstotliwość migawek "w ciągu 4 godzin" spójna na poziomie aplikacji.

     ![Włączanie replikacji](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>Włączanie replikacji na potrzeby dodane dyski

Jeśli dodasz dyski na maszynie Wirtualnej platformy Azure, dla których replikacja jest włączona, mają miejsce następujące zdarzenia:
-   Kondycja replikacji maszyny wirtualnej, wyświetla ostrzeżenie i notatkę informuje informujący, że jeden lub więcej dysków są dostępne do ochrony.
-   Jeśli włączysz ochronę dodane dyski, ostrzeżenie znikną po początkowej replikacji dysku.
-   Jeśli wybierzesz nie włączyć replikację dla dysku, możesz wybrać, aby zignorować to ostrzeżenie.

    
    ![Nowy dysk, dodane](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Aby włączyć replikację dla dodany dysk, wykonaj następujące czynności:

1.  W magazynie > **zreplikowane elementy**, kliknij maszynę Wirtualną, do którego został dodany dysk.
2.  Kliknij przycisk **dysków**, a następnie wybierz dysk danych, dla którego chcesz włączyć replikację (dyski te mają **nie są chronione** stanu).
3.  W **szczegóły dysku**, kliknij przycisk **włączyć replikację**.

    ![Włączanie replikacji na potrzeby dodany dysk](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

Po wykonaniu uruchomionych zadań włączenie replikacji i zakończeniu początkowej replikacji ostrzeżenie kondycji replikacji dla problemu z dyskiem zostaną usunięte.


  
## <a name="customize-target-resources"></a>Dostosuj zasoby docelowe

Można zmodyfikować domyślne ustawienia regionu docelowego używane przez usługę Site Recovery.

1. Kliknij przycisk **Dostosuj:** obok "Subskrypcji docelowej" Aby zmodyfikować domyślne subskrypcji docelowej. Wybierz subskrypcję z listy wszystkich subskrypcji dostępnych w tej samej dzierżawie usługi Azure Active Directory (AAD).

2. Kliknij przycisk **Dostosuj:** Aby zmodyfikować domyślne ustawienia:
    - W **docelowa grupa zasobów**, wybierz grupę zasobów z listy wszystkich grup zasobów w lokalizacji docelowej subskrypcji.
    - W **docelowa sieć wirtualna**, wybierz sieć, z listy sieci wirtualnej w lokalizacji docelowej.
    - W **zestawu dostępności**, możesz dodać ustawienia zestawu dostępności maszyny wirtualnej, jeśli są one częścią zestaw dostępności w regionie źródłowym.
    - W **kont magazynu docelowego**, wybierz konto, którego chcesz użyć.

        ![Włączanie replikacji](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. Kliknij przycisk **Dostosuj:** Aby zmodyfikować ustawienia replikacji.
4. W **spójność wielu maszyn wirtualnych**, wybierz maszyny wirtualne, które mają być replikowane wspólnie.
    - W przypadku przejścia w tryb failover wszystkie maszyny w grupie replikacji będą korzystać ze współdzielonych punktów odzyskiwania, spójnych na poziomie awarii i aplikacji.
    - Włączenie spójności wielu maszyn wirtualnych może wpłynąć na wydajność obciążeń (ponieważ jest intensywnie z Procesora). Powinna być włączona, tylko jeśli maszyn są to samo obciążenie i jest wymagana spójność między wieloma maszynami.
    - Na przykład jeśli aplikacja ma 2 maszyny wirtualne programu SQL Server i dwa serwery sieci web, następnie należy dodać tylko maszyny wirtualne SQL Server do grupy replikacji.
    - Można mieć maksymalnie 16 maszyn wirtualnych w grupie replikacji.
    - Jeśli włączono spójność między wieloma maszynami wirtualnymi, maszyny z grupy replikacji komunikują się między sobą przez port 20004.
    - Upewnij się, istnieje żadne urządzenie zapory nie blokuje wewnętrznej komunikacji między maszynami wirtualnymi za pośrednictwem portu 20004.
    - Jeśli chcesz, aby maszyny wirtualne systemu Linux jako część grupy replikacji, upewnij się, że ruch wychodzący na porcie 20004 musisz ręcznie otworzyć zgodnie ze wskazówkami dla określonych wersji systemu Linux.
![Włączanie replikacji](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)
    
5. Kliknij przycisk **Utwórz zasób docelowy** > **włączania replikacji**.
6. Po włączeniu replikacji maszyn wirtualnych można sprawdzić stan kondycji maszyny Wirtualnej w obszarze **zreplikowane elementy**

>[!NOTE]
>Podczas replikacji początkowej stanu może potrwać pewien czas, aby odświeżyć bez postępu. Kliknij przycisk **Odśwież** przycisk, aby uzyskać najnowszy stan.
>

# <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej](site-recovery-test-failover-to-azure.md) o uruchamianiu testowy tryb failover.
