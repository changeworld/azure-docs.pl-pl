---
title: Funkcja natychmiastowego przywracania platformy Azure
description: Możliwości natychmiastowego przywracania platformy Azure i często zadawane pytania dotyczące stosu kopii zapasowych maszyn wirtualnych, modelu wdrażania Menedżera zasobów
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 7cf437c6b20ea6b688e8e93e401cf71ef0260888
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705432"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Uzyskaj lepszą wydajność tworzenia kopii zapasowych i przywracania dzięki funkcji natychmiastowego przywracania kopii zapasowej usługi Azure

> [!NOTE]
> Na podstawie opinii od użytkowników zmieniamy nazwę **stosu kopii zapasowych maszyny Wirtualnej v2** do **natychmiastowego przywracania,** aby zmniejszyć pomyłki z funkcją usługi Azure Stack.
> Wszyscy użytkownicy kopii zapasowej platformy Azure zostali teraz uaktualnieni do **funkcji Natychmiastowego przywracania**.

Nowy model przywracania błyskawicznego udostępnia następujące ulepszenia funkcji:

* Możliwość używania migawek wykonanych jako część zadania tworzenia kopii zapasowej, które jest dostępne do odzyskiwania bez oczekiwania na zakończenie transferu danych do magazynu. Skraca czas oczekiwania na migawki do skopiowania do magazynu przed wyzwoleniem przywracania.
* Skraca czas tworzenia kopii zapasowych i przywracania, zachowując migawki lokalnie, domyślnie przez dwa dni. Ta domyślna wartość przechowywania migawki można skonfigurować na dowolną wartość od 1 do 5 dni.
* Obsługuje dysk o rozmiarze do 32 TB. Zmiana rozmiaru dysków nie jest zalecane przez usługę Azure Backup.
* Obsługuje standardowe dyski SSD wraz ze standardowymi dyskami HDD i dyskami premium SSD.
* Możliwość używania nierządowanych kont magazynu oryginalnego maszyny Wirtualnej (na dysk) podczas przywracania. Ta możliwość istnieje nawet wtedy, gdy maszyna wirtualna ma dyski, które są rozproszone na kontach magazynu. Przyspiesza operacje przywracania dla szerokiej gamy konfiguracji maszyn wirtualnych.
* W przypadku tworzenia kopii zapasowych maszyn wirtualnych korzystających z magazynu w wersji premium za pomocą funkcji Natychmiastowe przywracanie zaleca się przydzielenie *50%* wolnego miejsca w całkowitej ilości przydzielonego miejsca do magazynowania, co jest wymagane **tylko** dla pierwszej kopii zapasowej. 50% wolnego miejsca nie jest wymagane dla kopii zapasowych po zakończeniu pierwszej kopii zapasowej.

## <a name="whats-new-in-this-feature"></a>Co nowego w tej funkcji

Obecnie zadanie tworzenia kopii zapasowej składa się z dwóch faz:

1. Robienie migawki maszyny Wirtualnej.
2. Przenoszenie migawki maszyny Wirtualnej do magazynu usług Azure Recovery Services.

Punkt odzyskiwania jest uważany za utworzony dopiero po zakończeniu fazy 1 i 2. W ramach tego uaktualnienia punkt odzyskiwania jest tworzony natychmiast po zakończeniu migawki i ten punkt odzyskiwania typu migawki może służyć do wykonywania przywracania przy użyciu tego samego przepływu przywracania. Ten punkt odzyskiwania można zidentyfikować w witrynie Azure portal przy użyciu "migawki" jako typ punktu odzyskiwania, a po przeniesieniu migawki do magazynu, typ punktu odzyskiwania zmienia się na "migawka i przechowalnia".

![Zadanie tworzenia kopii zapasowej w modelu wdrażania menedżera zasobów menedżera kopii zapasowych maszyny Wirtualnej — magazyn i magazyn](./media/backup-azure-vms/instant-rp-flow.png)

Domyślnie migawki są zachowywane przez dwa dni. Ta funkcja umożliwia przywracanie operacji z tych migawek tam przez wycinanie czasów przywracania. Skraca czas wymagany do przekształcania i kopiowania danych z magazynu.

## <a name="feature-considerations"></a>Zagadnienia dotyczące funkcji

* Migawki są przechowywane wraz z dyskami, aby zwiększyć tworzenie punktów odzyskiwania i przyspieszyć operacje przywracania. W rezultacie zobaczysz koszty magazynu, które odpowiadają migawek wykonanych w tym okresie.
* Migawki przyrostowe są przechowywane jako obiekty blob strony. Wszyscy użytkownicy korzystający z dysków niezarządzanych są obciążani migawkami przechowywanymi na ich koncie magazynu lokalnego. Ponieważ kolekcje punktów przywracania używane przez kopie zapasowe zarządzanych maszyn wirtualnych używają migawek obiektów blob na podstawowym poziomie magazynu, dla dysków zarządzanych zostaną wyświetlona koszty odpowiadające cennikowi migawki obiektów blob i są przyrostowe.
* W przypadku kont magazynu w wersji premium migawki wykonane dla punktów natychmiastowego odzyskiwania są wliczane do limitu 10 TB przydzielonego miejsca.
* Można uzyskać możliwość skonfigurowania przechowywania migawki na podstawie potrzeb przywracania. W zależności od wymagań można ustawić przechowywanie migawki na co najmniej jeden dzień w bloku zasad kopii zapasowej, jak wyjaśniono poniżej. Pomoże to zaoszczędzić koszty przechowywania migawki, jeśli nie wykonujesz często przywracanych.
* Jest to jednokierunkowe uaktualnienie, po uaktualnieniu do natychmiastowego przywracania, nie można wrócić.

>[!NOTE]
>Dzięki tej aktualizacji natychmiastowego przywracania czas przechowywania migawki wszystkich klientów **(nowych i istniejących w obu uwzględnionych)** zostanie ustawiony na domyślną wartość dwóch dni. Można jednak ustawić czas trwania zgodnie z wymaganiami na dowolną wartość od 1 do 5 dni.

## <a name="cost-impact"></a>Wpływ na koszty

Migawki przyrostowe są przechowywane na koncie magazynu maszyny Wirtualnej, który jest używany do natychmiastowego odzyskiwania. Migawka przyrostowa oznacza, że miejsce zajmowane przez migawkę jest równe przestrzeni zajmowanej przez strony, które są zapisywane po utworzeniu migawki. Rozliczenia są nadal dla za GB używane miejsce zajmowane przez migawkę, a cena za GB jest taka sama, jak wspomniano na [stronie cennika](https://azure.microsoft.com/pricing/details/managed-disks/). W przypadku maszyn wirtualnych, które używają dysków niezarządzanych, migawki można zobaczyć w menu dla pliku VHD każdego dysku. W przypadku dysków zarządzanych migawki są przechowywane w zasobie zbierania punktów przywracania w wyznaczonej grupie zasobów, a same migawki nie są bezpośrednio widoczne.

>[!NOTE]
> Przechowywanie migawki jest ustalona do 5 dni dla zasad tygodniowych.

## <a name="configure-snapshot-retention"></a>Konfigurowanie przechowywania migawek

### <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal

W witrynie Azure portal można wyświetlić pole dodane w bloku **zasady kopii zapasowej maszyny Wirtualnej** w sekcji **Przywracanie błyskawiczne.** Można zmienić czas przechowywania migawki z bloku **zasad kopii zapasowej maszyny Wirtualnej** dla wszystkich maszyn wirtualnych skojarzonych z określonymi zasadami tworzenia kopii zapasowych.

![Możliwość natychmiastowego przywracania](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>Korzystanie z programu PowerShell

>[!NOTE]
> Począwszy od programu Az PowerShell w wersji 1.6.0 można zaktualizować okres przechowywania migawki natychmiastowego przywracania w zasadach przy użyciu programu PowerShell

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

Domyślne przechowywanie migawki dla każdej zasady jest ustawiona na dwa dni. Użytkownik może zmienić wartość na co najmniej 1 i maksymalnie pięć dni. W przypadku zasad tygodniowych przechowywanie migawki jest stałe na pięć dni.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Jakie są skutki kosztowe natychmiastowego przywracania?

Migawki są przechowywane wraz z dyskami, aby przyspieszyć tworzenie i przywracanie operacji punktu odzyskiwania. W rezultacie zobaczysz koszty magazynowania, które odpowiadają przechowywania migawki wybrane jako część zasad tworzenia kopii zapasowych maszyny Wirtualnej.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Czy na kontach magazynu w u klasy premium migawki wykonane dla punktu natychmiastowego odzyskiwania zajmują limit migawek o pojemności 10 TB?

Tak, w przypadku kont magazynu w wersji premium migawki wykonane dla punktu natychmiastowego odzyskiwania zajmują 10 TB przydzielonego miejsca migawki.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Jak działa przechowywanie migawki w okresie pięciu dni?

Każdego dnia zostanie podjęta nowa migawka, a następnie istnieje pięć pojedynczych migawek przyrostowych. Rozmiar migawki zależy od zmian danych, które w większości przypadków wynosi około 2%-7%.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Czy migawka natychmiastowego przywracania jest migawką przyrostową lub pełną migawką?

Migawki wykonane w ramach funkcji natychmiastowego przywracania są migawkami przyrostowymi.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Jak obliczyć przybliżony wzrost kosztów z powodu funkcji natychmiastowego przywracania?

To zależy od zmian maszyny Wirtualnej. W stanie stacjonarnym można założyć, że wzrost kosztu jest = Okres przechowywania migawki dziennych zmian na koszt magazynu maszyny Wirtualnej na GB.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Jeśli typem odzyskiwania dla punktu przywracania jest "Migawka i przechowalnia" i wykonać operację przywracania, który typ odzyskiwania będzie używany?

Jeśli typem odzyskiwania jest "migawka i przechowalnia", przywracanie zostanie automatycznie wykonane z migawki lokalnej, która będzie znacznie szybsza w porównaniu do przywracania wykonanego z magazynu.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Co się stanie, jeśli wybiorę okres przechowywania punktu przywracania (warstwa 2) mniejszy niż okres przechowywania migawki (Tier1)?

Nowy model nie zezwala na usuwanie punktu przywracania (Tier2), chyba że migawka (Tier1) zostanie usunięta. Zaleca się planowanie punktu przywracania (Tier2) okres przechowywania większy niż okres przechowywania migawki.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Dlaczego moja migawka istnieje nawet po upływie ustawionego okresu przechowywania w zasadach tworzenia kopii zapasowych?

Jeśli punkt odzyskiwania ma migawkę i jest to najnowsza dostępna gra RP, jest zachowywana do czasu, gdy nastąpi następna pomyślna kopia zapasowa. Jest to zgodne z zaprojektowanymi zasadami "wyrzucania elementów bezużytecznych" (GC) dzisiaj, która nakazuje co najmniej jeden ostatni RP być zawsze obecny w przypadku, gdy wszystkie kopie zapasowe dalej na niepowodzenie z powodu problemu w maszynie wirtualnej. W normalnych scenariuszach punkty transakcji są czyszczone w ciągu maksymalnie 24 godzin po ich wygaśnięciu.

### <a name="i-dont-need-instant-restore-functionality-can-it-be-disabled"></a>Nie potrzebuję funkcji natychmiastowego przywracania. Czy można go wyłączyć?

Funkcja natychmiastowego przywracania jest włączona dla wszystkich i nie można jej wyłączyć. Można zmniejszyć przechowywanie migawki do minimum jednego dnia.

>[!NOTE]
> **Usługa Azure Backup obsługuje teraz selektywną kopię zapasową dysku i przywracanie przy użyciu rozwiązania do tworzenia kopii zapasowych maszyny wirtualnej platformy Azure.**
>
>Obecnie usługa Azure Backup obsługuje tworzenie kopii zapasowych wszystkich dysków (system operacyjny i dane) na maszynie wirtualnej przy użyciu rozwiązania do tworzenia kopii zapasowych maszyny wirtualnej. Dzięki funkcji wykluczeń dysku można uzyskać opcję tworzenia kopii zapasowej jednego lub kilku z wielu dysków z danymi na maszynie Wirtualnej. Zapewnia to wydajne i ekonomiczne rozwiązanie dla potrzeb związanych z tworzeniem kopii zapasowych i przywracania. Każdy punkt odzyskiwania zawiera dane dysków uwzględnionych w operacji tworzenia kopii zapasowej, co dodatkowo umożliwia przywrócenie podzbioru dysków z danego punktu odzyskiwania podczas operacji przywracania. Dotyczy to przywracania zarówno z migawki, jak i z magazynu.
>
>**Aby zapisać się do podglądu, napisz do nas naAskAzureBackupTeam@microsoft.com**
