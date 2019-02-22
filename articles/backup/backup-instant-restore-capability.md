---
title: Możliwości platformy Azure, natychmiastowe Przywracanie
description: Błyskawiczne możliwości przywracania i często zadawane pytania dotyczące maszyn wirtualnych Azure utworzyć kopię zapasową stosu i modelu wdrażania usługi Resource Manager
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: sogup
ms.openlocfilehash: 566faaef4e91237bd0a0f915ea39f4bd389120d8
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593084"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Uzyskaj ulepszone kopii zapasowej i przywracanie wydajności za pomocą funkcji Azure kopii zapasowej natychmiastowe Przywracanie

> [!NOTE]
> Na podstawie opinii użytkowników, zmieniamy **stosu kopii zapasowej maszyny Wirtualnej V2** do **natychmiastowe Przywracanie** aby wyeliminować problemy z funkcjami usługi Azure Stack.

Nowy model dla przywracania błyskawiczne zapewnia następujące ulepszenia funkcji:

* Możliwość stosowania migawek wykonanych w ramach zadania tworzenia kopii zapasowej, który jest dostępny dla odzyskiwania bez oczekiwania na przesyłanie danych do magazynu, aby zakończyć. Zmniejsza to czas oczekiwania dla migawek skopiować do magazynu przed wyzwoleniem przywracania.
* Skraca okresy i przywracania kopii zapasowych, zachowując lokalnie, migawki przez dwa dni domyślnie. Ten magazyn domyślny jest konfigurowany na wartość z zakresu od 1 do 5 dni.
* Obsługa dysków o rozmiarach do 4 TB.
* Obsługuje dyski SSD w warstwie standardowa.
*   Możliwość używania niezarządzanej maszyny Wirtualnej w oryginalnych kont magazynu (na dysku), podczas przywracania. Ta możliwość istnieje, nawet wtedy, gdy maszyna wirtualna ma dyski, które są dystrybuowane na kontach magazynu. Jego przyspiesza operacje przywracania dla różnych konfiguracji maszyny Wirtualnej



## <a name="whats-new-in-this-feature"></a>What's new in tej funkcji

Obecnie zadanie tworzenia kopii zapasowej obejmuje dwa etapy:

1.  Wykonanie migawki maszyny Wirtualnej.
2.  Przenoszenie migawki maszyny Wirtualnej do magazynu usługi Azure Recovery Services.

Punkt odzyskiwania jest uważany za utworzony po zakończeniu fazy 1 i 2. W ramach tego uaktualnienia punkt odzyskiwania jest tworzony, natychmiast po zakończeniu migawki i tego punktu odzyskiwania typu Migawka może służyć do przywracania, przy użyciu tego samego przepływu przywracania. Tego punktu odzyskiwania w witrynie Azure portal można zidentyfikować za pomocą "snapshot" jako typ punktu odzyskiwania, a po przekazaniu migawki do magazynu typ punktu odzyskiwania zmienia się na "migawka i Magazyn".

![Zadanie tworzenia kopii zapasowej w maszynę Wirtualną stosu kopii zapasowej modelu Resource Manager deployment — magazynu i Magazyn](./media/backup-azure-vms/instant-rp-flow.png)

Domyślnie migawki są przechowywane przez dwa dni. Ta funkcja umożliwia operacji przywracania istnieje migawek, skracając czas przywracania w dół. Zmniejsza to czas, który jest wymagany do przekształcania, a następnie skopiować dane z magazynu do konta magazynu użytkownika dla scenariuszy dysk niezarządzany, natomiast w przypadku dysków zarządzanych użytkowników, tworzy dysków zarządzanych z danych usługi Recovery Services.

## <a name="feature-considerations"></a>Zagadnienia związane z funkcji

* Migawki są przechowywane wraz z dysków, aby zwiększyć możliwości tworzenia punktu odzyskiwania i przyspieszyć operacje przywracania. W rezultacie zobaczysz koszty magazynowania, odpowiadające migawek wykonanych w tym okresie.
* Przyrostowe migawki są przechowywane jako stronicowe obiekty BLOB. Wszyscy użytkownicy korzystają z dysków niezarządzanych są naliczane za migawek przechowywanych na jego koncie magazynu lokalnego. Ponieważ kolekcje punktów przywracania używana przez kopie zapasowe zarządzanych maszyn wirtualnych używa migawek obiektów blob na poziomie magazynu podstawowego, za dyski zarządzane zobaczysz koszty odpowiadający blob snapshot ceny i są przyrostowe.
* Dla kont usługi premium storage miejsca przydzielonego do migawek wykonywanych liczby punktów natychmiastowe odzyskiwanie kierunku limit 10 TB.
* Otrzymasz możliwość konfigurowania przechowywania migawki, w zależności od potrzeb przywracania. W zależności od wymagań można ustawić okres przechowywania migawek co najmniej jeden dzień w bloku zasad tworzenia kopii zapasowych, co zostało opisane poniżej. Może to pomóc Ci zaoszczędzić koszty przechowywania migawek, jeśli nie wykonasz często przywracania.


>[!NOTE]
>Z tym natychmiastowe Przywracanie uaktualnienia, czas trwania przechowywania migawek wszystkich klientów (**nowych i istniejących zarówno dołączona**) zostanie ustawiona na wartość domyślną w ciągu dwóch dni. Można jednak ustawić czas trwania, zgodnie z wymaganiami dowolną wartość z zakresu od 1 do 5 dni.


## <a name="cost-impact"></a>Wpływ na koszt

Migawek przyrostowych znajdują się na koncie magazynu maszyny Wirtualnej, które są używane na potrzeby natychmiastowe odzyskiwanie. Migawek przyrostowych oznacza, że miejsce zajmowane przez migawki jest równy miejsce zajmowane przez strony, które są zapisywane po utworzeniu migawki. Opłaty są nadal dla za GB zajęte miejsce zajmowane przez migawki i opłaty za GB danych jest taka sama, jak wspomniano w [stronę z cennikiem](https://azure.microsoft.com/pricing/details/managed-disks/).


## <a name="upgrading-to-instant-restore"></a>Uaktualnianie do natychmiastowe Przywracanie

Jeśli używasz witryny Azure portal, zostanie wyświetlone powiadomienie na pulpicie nawigacyjnym magazynu. To powiadomienie odnosi się do obsługi dużych dysków oraz ulepszenia dotyczące prędkości i przywracania kopii zapasowych.
Aby otworzyć ekran dla uaktualnienie do przywrócenia błyskawiczne, wybierz baner.

![Zadanie tworzenia kopii zapasowej w modelu wdrażania usługi Resource Manager stosu kopii zapasowej maszyny Wirtualnej — zgłoszenia pomocy technicznej](./media/backup-azure-vms/instant-rp-banner.png)

Kliknij pozycję **uaktualnienia** jak pokazano na poniższym zrzucie ekranu:

![Zadanie tworzenia kopii zapasowej w stos kopii zapasowej maszyny Wirtualnej modelu wdrażania usługi Resource Manager — uaktualnianie](./media/backup-azure-vms/instant-rp.png)

Alternatywnie, możesz przejść do **właściwości** strona magazynu można pobrać **uaktualnienia** opcji w obszarze **stosu kopii zapasowej maszyny Wirtualnej**.

![Zadanie tworzenia kopii zapasowej w stos kopii zapasowej maszyny Wirtualnej — strona właściwości](./media/backup-azure-vms/instant-restore-capability-properties.png)


## <a name="configure-snapshot-retention-using-azure-portal"></a>Konfigurowanie przechowywania migawki za pomocą witryny Azure portal
Ta opcja jest obecnie dostępna w zachodnio-środkowe stany USA, Indie Południowe i Australia Wschodnia.

Dla uaktualnionego użytkowników w witrynie Azure portal można wyświetlić pola dodane w **zasad tworzenia kopii zapasowej maszyny Wirtualnej** bloku w obszarze **natychmiastowe Przywracanie** sekcji. Można zmienić czasu trwania przechowywania migawek z **zasad tworzenia kopii zapasowej maszyny Wirtualnej** bloku dla wszystkich maszyn wirtualnych skojarzonych z określonych zasad tworzenia kopii zapasowej.

![Możliwość natychmiastowego przywracania](./media/backup-azure-vms/instant-restore-capability.png)

## <a name="upgrade-to-instant-restore-using-powershell"></a>Uaktualnij, aby przywrócić błyskawiczne przy użyciu programu PowerShell

Jeśli chcesz Samoobsługa i Uaktualnij, aby przywrócić błyskawiczne, uruchom następujące polecenia cmdlet z terminala PowerShell z podwyższonym poziomem uprawnień:

1.  Zaloguj się do konta platformy Azure:

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  Wybierz subskrypcję, której chcesz zarejestrować:

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  Zarejestruj tej subskrypcji:

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="upgrade-to-instant-restore-using-cli"></a>Uaktualnij, aby przywrócić błyskawiczne przy użyciu interfejsu wiersza polecenia

Uruchom następujące polecenia z poziomu powłoki:

1.  Zaloguj się do konta platformy Azure:

    ```
    az login
    ```

2.  Wybierz subskrypcję, której chcesz zarejestrować:

    ```
    az account set --subscription "Subscription Name"
    ```

3.  Zarejestruj tej subskrypcji:

    ```
    az feature register --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
    ```

## <a name="verify-that-the-upgrade-is-successful"></a>Sprawdź, że uaktualnienie powiodło się

### <a name="powershell"></a>PowerShell
Z poziomu terminalu programu PowerShell z podwyższonym poziomem uprawnień uruchom następujące polecenie cmdlet:

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" -ProviderNamespace Microsoft.RecoveryServices
```

### <a name="cli"></a>Interfejs wiersza polecenia
Z poziomu powłoki uruchom następujące polecenie:

```
az feature show --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
```

Jeśli wyświetlany jest tekst "Zarejestrowane", Twoja subskrypcja jest uaktualniany do natychmiastowe przywracanie.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Jakie są skutki koszt natychmiastowe Przywracanie?
Migawki są przechowywane wraz z dysków, aby przyspieszyć tworzenie punktu odzyskiwania i operacji przywracania. W rezultacie zobaczysz koszty magazynowania, które odnoszą się do przechowywania migawek wybrany jako część zasad tworzenia kopii zapasowej maszyny Wirtualnej.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>W ramach kont usługi Premium Storage migawki wykonane dla punktu odzyskiwania błyskawiczne zajmować limit migawek 10 TB?
Tak, dla kont usługi premium storage migawki wykonane dla punktu odzyskiwania błyskawiczne zajmują 10 TB miejsca przydzielonego migawki.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Jak działa przechowywania migawek w okresie pięciu dni
Każdego dnia nowe migawki, a następnie istnieje pięć poszczególnych migawek przyrostowych. Rozmiar migawki, zależy od współczynnika zmian danych, który znajduje się w większości przypadków około 2 – 7%.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>To natychmiastowe Przywracanie migawki, przyrostową migawką lub Pełna migawka?
Migawki podjęte w ramach natychmiastowe Przywracanie możliwości są migawek przyrostowych.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Jak można obliczyć wzrost przybliżony koszt ze względu na natychmiastowe Przywracanie funkcji
To zależy od zmian maszyny Wirtualnej. W stanie stabilnym można założyć, wzrost kosztów jest = migawki przechowywania okresu Dzienny współczynnik zmian koszt magazynu maszyny Wirtualnej za GB.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Jeśli typ odzyskiwania dla punktu przywracania jest "Migawka i Magazyn", a następnie wykonać operacji przywracania, jakiego typu odzyskiwania, który będzie używany?
W przypadku typu odzyskiwania "migawka i Magazyn", będzie można wykonać przywracania z automatycznie z migawka lokalna, która będzie znacznie szybciej porównywana przywracania z magazynu.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Co się stanie, jeśli wybiorę opcję okres przechowywania punkt przywracania (Warstwa 2) mniejszą niż okres przechowywania migawek (1)?
Nowy model nie zezwala na usuwanie punkt przywracania (w warstwie 2), chyba że migawka (1) zostanie usunięta. Zaleca się planowanie okres przechowywania (w warstwie 2) punktu przywracania większy niż okres przechowywania migawki.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Dlaczego moja migawki istnieje nawet po zakończeniu przechowywania zestaw kropki w zasadach kopii zapasowych?
Jeśli punkt odzyskiwania ma migawki, a to najnowsze dostępne jednostki Uzależnionej, wartość jest zachowywana do czasu, znajduje się dalej pomyślne tworzenie kopii zapasowych. Jest to zgodnie z zaprojektowane GC zasadami już dziś zezwalający na użycie co najmniej jeden RP najnowsze zawsze znajdować się w przypadku, gdy wszystkie kopie zapasowe dalsze na zakończyć się niepowodzeniem z powodu problemu na maszynie wirtualnej. W normalnym scenariuszy RPs są czyszczone w maksymalnie 24 godziny po ich wygaśnięciu.
