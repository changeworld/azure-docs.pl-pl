---
title: Planowanie infrastruktury kopii zapasowej maszyny Wirtualnej na platformie Azure
description: Istotne zagadnienia dotyczące planowania, tworzenia kopii zapasowych maszyn wirtualnych na platformie Azure
services: backup
author: markgalioto
manager: carmonm
keywords: kopie zapasowe maszyn wirtualnych, kopie zapasowe maszyn wirtualnych
ms.service: backup
ms.topic: conceptual
ms.date: 7/26/2018
ms.author: markgal
ms.openlocfilehash: b6288cd51cbbe36297235a65fb55c0d9c92101b6
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283710"
---
# <a name="plan-your-vm-backup-infrastructure-in-azure"></a>Planowanie infrastruktury kopii zapasowych maszyny wirtualnej na platformie Azure
Ten artykuł zawiera wydajności i sugestii zasobu, aby ułatwić planowanie infrastruktury kopii zapasowej maszyny Wirtualnej. Definiuje również kluczowe aspekty w usłudze Kopia zapasowa; te aspekty mogą być szczególnie ważne w określaniu architektury, planowania pojemności i planowania. Jeśli masz [przygotować środowisko](backup-azure-arm-vms-prepare.md), planowania jest kolejnym krokiem przed rozpoczęciem [do tworzenia kopii zapasowych maszyn wirtualnych](backup-azure-arm-vms.md). Aby uzyskać więcej informacji na temat maszyn wirtualnych platformy Azure, zobacz [dokumentacja dotycząca maszyn wirtualnych](https://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="how-does-azure-back-up-virtual-machines"></a>Jakie platformy Azure kopia zapasowa maszyn wirtualnych?
Gdy usługa Azure Backup Inicjuje zadania tworzenia kopii zapasowej w zaplanowanym czasie, wyzwalaczy usługi zapasowy numer wewnętrzny, aby utworzyć migawkę punktu w czasie. Używa usługi Azure Backup _VMSnapshot_ rozszerzenie w Windows, a _VMSnapshotLinux_ rozszerzenia w systemie Linux. To rozszerzenie jest zainstalowane podczas pierwszego tworzenia kopii zapasowej maszyny Wirtualnej. Aby zainstalować rozszerzenie, musi być uruchomiona maszyna wirtualna. Jeśli maszyna wirtualna nie jest uruchomiona, usługa Backup utworzy migawkę powiązanego magazynu (ponieważ gdy maszyna wirtualna jest zatrzymana, nie występują zapisy aplikacji).

Podczas wykonywania migawki maszyn wirtualnych z systemem Windows usługa Backup koordynuje się z usługą kopiowania woluminów w tle (VSS, Volume Shadow Copy Service), aby uzyskać spójną migawkę dysków maszyny wirtualnej. Jeśli wykonujesz kopie zapasowe maszyn wirtualnych systemu Linux, można napisać własne niestandardowe skrypty do zapewnienie spójności podczas wykonywania migawki maszyny Wirtualnej. Szczegółowe informacje na temat wywoływania tych skryptów znajdują się w dalszej części tego artykułu.

Po utworzeniu migawki w usłudze Azure Backup dane są przesyłane do magazynu. Aby zmaksymalizować wydajność, usługa rozpoznaje i przesyła jedynie te bloki danych, które uległy zmianie od czasu utworzenia poprzedniej kopii zapasowej.

![Architektura kopii zapasowych maszyn wirtualnych platformy Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

Po ukończeniu przesyłania danych migawka jest usuwana, a utworzony zostaje punkt odzyskiwania.

> [!NOTE]
> 1. Podczas procesu tworzenia kopii zapasowej usługi Azure Backup nie uwzględnia dysku tymczasowego dołączonych do maszyny wirtualnej. Aby uzyskać więcej informacji, zapoznaj się z blogu na [magazynu tymczasowego](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).
> 2. Azure przyjmuje kopii zapasowej migawki i transferuje ją do magazynu, na poziomie magazynu nie należy zmieniać klucze konta magazynu, aż do zakończenia zadania tworzenia kopii zapasowej.
> 3. W przypadku maszyn wirtualnych — wersja premium usługi Azure Backup kopiuje migawkę do konta magazynu. To, aby upewnić się, że usługa Kopia zapasowa używa wystarczające operacje We/Wy do przesyłania danych do magazynu. Tej dodatkowej kopii magazynu jest rozliczana zgodnie z przydzielony rozmiar maszyny Wirtualnej. 
>

### <a name="data-consistency"></a>Spójność danych
Tworzenie kopii zapasowej i przywracanie kluczowych danych jest skomplikowane fakt, że krytyczne dane biznesowe, należy wykonać kopię zapasową podczas aplikacje, które generują dane biznesowe są uruchomione. Aby rozwiązać ten problem, usługa Azure Backup obsługuje spójnych z aplikacją kopii zapasowych dla maszyn wirtualnych systemu Linux i Windows
#### <a name="windows-vm"></a>Maszyna wirtualna z systemem Windows
Usługa Azure Backup ma pełne kopie zapasowe usługi VSS na maszynach wirtualnych Windows (Dowiedz się więcej o [pełnej kopii zapasowej VSS](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)). Aby włączyć kopie zapasowe kopii usługi VSS, następujący klucz rejestru musi być ustawiona na maszynie Wirtualnej.

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
"USEVSSCOPYBACKUP"="TRUE"
```

#### <a name="linux-vms"></a>Maszyny wirtualne z systemem Linux
Usługa Azure Backup udostępnia strukturę, skryptów. Zapewnienie spójności aplikacji podczas wykonywania kopii zapasowej maszyn wirtualnych systemu Linux, Utwórz niestandardowe skrypty przed i skryptu używanego po utworzeniu przepływu pracy tworzenia kopii zapasowych i środowiska. Usługa Azure Backup wywołuje skryptu poprzedzającego przed wykonaniem migawki maszyny Wirtualnej i wywołuje skrypt używany po utworzeniu, po ukończeniu zadania migawki maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [aplikacji spójne kopie zapasowe maszyn wirtualnych przy użyciu skryptów uruchamianych skryptu używanego po utworzeniu](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).
> [!NOTE]
> Usługa Azure Backup tylko wywołuje napisanymi odbiorców wstępnej i skryptu używanego po utworzeniu. Jeśli skryptu poprzedzającego i skryptu używanego po utworzeniu wykonane pomyślnie, kopia zapasowa Azure oznacza punkt odzyskiwania jako aplikacja spójne. Jednak klient jest ponoszą ostateczną odpowiedzialność za spójności aplikacji, gdy za pomocą skryptów niestandardowych.
>


W następującej tabeli opisano typy spójności i warunki, które one odbywały się w ramach maszyny Wirtualnej platformy Azure kopia zapasowa i przywracanie procedur.

| Spójność | Na podstawie usługi VSS | Wyjaśnienie, jak i szczegółów |
| --- | --- | --- |
| Spójność aplikacji |Tak dla Windows|Spójność aplikacji jest idealnym rozwiązaniem w przypadku obciążeń zapewnia, że:<ol><li> Maszyna wirtualna *jest uruchamiany*. <li>Brak *nie uszkodzenia*. <li>Brak *bez utraty danych*.<li> Dane są spójne do aplikacji, która korzysta z danych, poprzez włączenie aplikacji w czasie wykonywania kopii zapasowych — za pomocą skryptu usługi VSS lub pre/post.</ol> <li>*Maszyny wirtualne Windows*-obciążenia najbardziej firmy Microsoft mają składniki zapisywania usługi VSS, które wykonują akcje specyficznego dla obciążenia związane z wyjaśnienie pojęcia spójności danych. Na przykład Microsoft SQL Server ma składnik zapisywania usługi VSS, które gwarantuje, że prawidłowo wykonane operacje zapisu w pliku dziennika transakcji, jak i bazy danych. Dla kopii zapasowych maszyn wirtualnych Windows Azure Tworzenie punktu odzyskiwania spójnego z aplikacją rozszerzenie kopii zapasowej należy wywołać przepływ pracy usługi VSS i on zostać ukończona przed wykonaniem migawki maszyny Wirtualnej. Migawki maszyny Wirtualnej platformy Azure muszą być dokładne również należy wykonać składniki zapisywania usługi VSS wszystkich aplikacji na maszynie Wirtualnej platformy Azure. (Dowiedz się, [podstawy usługi VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) i detalach szczegóły [jak to działa](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx)). </li> <li> *Maszyny wirtualne systemu Linux*— klienci mogą wykonywać [niestandardowego skryptu poprzedzającego i skryptu używanego po utworzeniu w celu zapewnienia spójności aplikacji](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent). </li> |
| Spójności systemu plików |Tak — dla komputerów z systemem Windows |Istnieją dwa scenariusze, w których punkt odzyskiwania może być *pliku spójne na poziomie systemu*:<ul><li>Tworzenie kopii zapasowych maszyn wirtualnych systemu Linux na platformie Azure, bez konieczności wstępnej-script/odniesienie-script lub jeśli wstępnie przygotowany-script/odniesienie-script nie powiodło się. <li>Błąd VSS podczas tworzenia kopii zapasowej dla maszyn wirtualnych Windows na platformie Azure.</li></ul> W obu przypadkach upewnij się, że jest najlepsze, którą można wykonać: <ol><li> Maszyna wirtualna *jest uruchamiany*. <li>Brak *nie uszkodzenia*.<li>Brak *bez utraty danych*.</ol> Aplikacje muszą implementować ich własny mechanizm "poprawki" przywróconych danych. |
| Spójność awarii |Nie |Ta sytuacja jest równoznaczne z maszyny wirtualnej, u których występują "awarii" (za pośrednictwem i miękkich reset). Spójność awarii zazwyczaj ma miejsce, gdy maszyna wirtualna platformy Azure zostanie zamknięta w czasie wykonywania kopii zapasowej. Punktu odzyskiwania spójnego na poziomie awarii zawiera nie gwarancje w zakresie spójności danych na nośniku — z punktu widzenia systemu operacyjnego lub aplikacji. Tylko dane, które już istnieje na dysku w czasie wykonywania kopii zapasowej jest przechwytywane i kopii zapasowej. <br/> <br/> Gdy istnieją zazwyczaj żadnej gwarancji, uruchomieniem systemu operacyjnego, następuje sprawdzenie dysku procedury, takich jak narzędzia chkdsk, aby naprawić błędy uszkodzenia. Wszelkie dane w pamięci i operacji zapisu, które nie zostały przeniesione do dysku zostaną utracone. Aplikacja zazwyczaj następuje z własny mechanizm weryfikacji w przypadku wycofywania danych musi odbywać się. <br><br>Na przykład jeśli dziennik transakcji zawiera wpisy, które nie znajduje się w bazie danych, oprogramowanie bazy danych przedstawia ponownie, dopóki dane są spójne. Gdy data rozkłada się na wiele dysków wirtualnych (takich jak woluminy łączone), punktu odzyskiwania spójnego na poziomie awarii zapewnia żadnej gwarancji pod kątem poprawności danych. |

## <a name="performance-and-resource-utilization"></a>Wydajności i użycia zasobów
Podobnie jak oprogramowania kopii zapasowej, który jest wdrożony lokalnie należy również zaplanować pojemności i wykorzystania zasobów potrzeby podczas tworzenia kopii zapasowych maszyn wirtualnych na platformie Azure. [Limity usługi Azure Storage](../azure-subscription-service-limits.md#storage-limits) zdefiniowanie struktury wdrożeń maszyn wirtualnych, aby zapewnić maksymalną wydajność minimalne wpływu na działające zadania.

Podczas planowania wydajności tworzenia kopii zapasowych, należy zwrócić uwagę na następujące limity usługi Azure Storage:

* Maksymalna liczba wychodzących na konto magazynu
* Łączna liczba żądań zakończonych na konto magazynu

### <a name="storage-account-limits"></a>Limity konta magazynu
Dane kopii zapasowej kopiowane z konta magazynu, dodaje do operacji wejścia/wyjścia na sekundę (IOPS) oraz ruchu wychodzącego (lub przepływność) metryki konta magazynu. W tym samym czasie maszyny wirtualne są również korzystanie z operacji We/Wy i przepływność. Celem jest, aby upewnić się, że ruch maszyny wirtualnej i kopii zapasowych nie przekraczają limitów konta magazynu.

### <a name="number-of-disks"></a>Liczba dysków
Proces tworzenia kopii zapasowej podejmie próbę możliwie jak najszybciej ukończenia zadania tworzenia kopii zapasowej. W ten sposób zużywa tylu zasobów, ponieważ jest to możliwe. Jednak wszystkie operacje We/Wy są ograniczone dzięki *docelowa przepustowość dla pojedynczego obiektu Blob*, która ma limit równy 60 MB na sekundę. W celu zmaksymalizowania jej szybkość działania, proces tworzenia kopii zapasowej próbuje utworzyć kopię zapasową wszystkich dysków maszyny Wirtualnej *równolegle*. Jeśli maszyna wirtualna ma cztery dyski, usługa próbuje utworzyć kopię zapasową wszystkie cztery dyski równolegle. **Liczbę dysków** tworzona kopia zapasowa, jest najważniejszym czynnikiem umożliwiającym określenie ruchu kopii zapasowej konta magazynu.

### <a name="backup-schedule"></a>Harmonogram tworzenia kopii zapasowych
Dodatkowy czynnik, który ma wpływ na wydajność jest **harmonogram tworzenia kopii zapasowych**. Jeśli konfigurujesz zasady, dzięki czemu wszystkie maszyny wirtualne kopie zapasowe są tworzone w tym samym czasie, zostanie zaplanowane zakleszczenie ruchu. Proces tworzenia kopii zapasowej próbuje utworzyć kopię zapasową wszystkich dysków w sposób równoległy. Aby zmniejszyć ruch kopii zapasowej z konta magazynu, należy wykonać kopię zapasową różnych maszyn wirtualnych w różnych porę dnia, bez nakładania się.

## <a name="capacity-planning"></a>Planowanie pojemności
Zestawiania różnych czynników poprzedniej, należy zaplanować na potrzeby użycia konta magazynu. Pobierz [VM backup planowania pojemności na potrzeby arkusz kalkulacyjny programu Excel](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) pozwala określić wpływ dysku i harmonogram tworzenia kopii zapasowych wyborów.

### <a name="backup-throughput"></a>Przepustowość kopii zapasowej
Dla każdego dysku, w których powstaje kopia zapasowa kopia zapasowa Azure odczytuje bloków na dysku i przechowuje tylko zmienionych danych (przyrostową kopię zapasową). W poniższej tabeli przedstawiono wartości średnie przepływności usługi kopii zapasowej. Następujące dane można oszacować ilości czasu wymaganego do utworzenia kopii zapasowej dysku dany rozmiar.

| Operacja tworzenia kopii zapasowej | Najlepszego przypadku przepływności |
| --- | --- |
| Początkowa kopia zapasowa |160 MB/s |
| Przyrostowe kopie zapasowe (DR) |640 MB/s <br><br> Przepływność znacznie spada Jeśli zmienione dane, (które należy utworzyć kopię) są rozdzielane na dysku.|

## <a name="total-vm-backup-time"></a>Łączny czas wykonywania kopii zapasowych maszyn wirtualnych
Chociaż w większości przypadków tworzenia kopii zapasowych odbywa się do czytania i kopiowanie danych, inne operacje przyczyniają się do całkowity czas potrzebne do tworzenia kopii zapasowej maszyny Wirtualnej:

* Czas potrzebny do [Zainstaluj lub zaktualizuj rozszerzenie kopii zapasowej](backup-azure-arm-vms.md).
* Godzina migawki to czas potrzebny do wyzwolenia migawki. Migawki są wyzwalane blisko według harmonogramu wykonywania kopii zapasowej.
* Czas oczekiwania w kolejce. Ponieważ usługa kopii zapasowej przetwarza kopie zapasowe z wieloma klientami, kopiowanie danych kopii zapasowej z migawki do tworzenia kopii zapasowej lub magazynu usługi Recovery Services nie można uruchomić od razu. W czasie szczytowego obciążenia, czas oczekiwania rozciągnąć maksymalnie osiem godzin ze względu na liczbę kopii zapasowych przetwarzany. Jednak całkowity czas tworzenia kopii zapasowej maszyny Wirtualnej jest krótszy niż 24 godziny dla zasad tworzenia kopii zapasowej dzienny. <br>
**Przechowuje to prawidłowe tylko dla przyrostowych kopii zapasowych, a nie dla pierwszej kopii zapasowej. Po raz pierwszy kopii zapasowej jest proporcjonalna i może być większa niż 24 godziny, w zależności od rozmiaru danych, i jest wykonywana kopia zapasowa czasu.**
* Czas transferu danych, czas potrzebny do tworzenia kopii zapasowych usługi obliczeniowe przyrostowych zmian od poprzedniej kopii zapasowej i przenieść te zmiany do magazynu z magazynu.

### <a name="why-am-i-observing-longer12-hours-backup-time"></a>Dlaczego mam obserwowania longer(>12 hours) czas wykonania kopii zapasowej?
Kopia zapasowa obejmuje dwa etapy: tworzenie migawek i przekazywania migawek do magazynu. Usługa Backup optymalizuje dla magazynu. Podczas transferu danych migawki do magazynu, usługa tylko przesyła przyrostowe zmiany z poprzednią migawkę.  Aby określić zmiany przyrostowe, usługa oblicza sumę kontrolną bloków. Jeśli blok zostanie zmieniony, blok jest rozpoznawany jako bloku do wysłania do magazynu. Następnie awarii usługi bardziej szczegółowo w każdej z określonych bloków, szuka możliwości zminimalizować danych do przesłania. Po dokonaniu oceny wszystkie zmienione bloki, usługa łączy zmiany i wysyła je do magazynu. W niektórych starszych aplikacji zapisów niewielkich, podzielonej zawartości nie są optymalne dla magazynu. Jeśli migawka zawiera wiele zapisów niewielkich, fragmentacji, usługa spędza dodatkowy czas przetwarzania danych zapisanych przez aplikacje. Blok zapisu zalecanych aplikacji na platformie Azure, w przypadku aplikacji uruchomionych na maszynie Wirtualnej jest co najmniej 8 KB. Jeśli aplikacja korzysta z blokiem mniej niż 8 KB, odbywa się wydajność tworzenia kopii zapasowej. Aby uzyskać pomoc dotyczącą Dostrajanie aplikacji w taki sposób, aby zwiększyć wydajność tworzenia kopii zapasowych, zobacz [Dostrajanie aplikacji, aby uzyskać optymalną wydajność z usługą Azure storage](../virtual-machines/windows/premium-storage-performance.md). Chociaż artykuł w wydajności tworzenia kopii zapasowych używa przykładów magazynu Premium, wskazówki ma zastosowanie w przypadku dysków magazynu w warstwie standardowa.

## <a name="total-restore-time"></a>Czas całkowity przywracania
Operacja przywracania składa się z dwóch głównych podzadań: kopiowanie danych z magazynu do konta magazynu wybranego klienta i tworzenie maszyny wirtualnej. Kopiowanie danych z magazynu kopii zależy od tego, których kopie zapasowe są przechowywane wewnętrznie na platformie Azure i gdzie są przechowywane na koncie magazynu klienta. Czas poświęcony na kopiowanie danych zależy od:
* Czas oczekiwania w kolejce — ponieważ procesów informatycznych przywrócić zadania z wieloma klientami w tym samym czasie, przywracanie żądania są umieszczone w kolejce.
* Czas — kopiowania danych dane są kopiowane z magazynu do konta magazynu klienta. Przywróć czas zależy od operacji We/Wy i przepływność usługi Azure Backup pobiera na koncie magazynu wybranego klienta. W celu skrócenia czasu kopiowania podczas procesu przywracania, wybierz konto magazynu nie załadowano z innymi aplikacji zapisu i odczytu.

## <a name="best-practices"></a>Najlepsze praktyki
Sugerujemy następujące rozwiązania w zakresie podczas konfigurowania kopii zapasowych dla maszyn wirtualnych:

* Nie należy planować więcej niż 10 klasyczne maszyny wirtualne w tej samej usłudze w chmurze do tworzenia kopii zapasowych, w tym samym czasie. Jeśli chcesz tworzyć kopie zapasowe wielu maszyn wirtualnych z jednej usługi w chmurze, należy przesunąć godziny rozpoczęcia tworzenia kopii zapasowych o godzinę.
* Nie należy planować ponad 100 maszyn wirtualnych, aby utworzyć kopię zapasową w tym samym czasie w jednym magazynie. 
* Planowanie kopii zapasowych maszyn wirtualnych poza godzinami poza szczytem. Dzięki temu usługa Backup korzysta z operacji We/Wy do przesyłania danych z konta magazynu klienta do magazynu.
* Upewnij się, że zasady są stosowane na maszynach wirtualnych rozkładają się na różnych kont magazynu. Zalecamy nie więcej niż 20 łączna liczba dysków z jednego konta magazynu jest chronione przez ten sam harmonogram tworzenia kopii zapasowej. Jeśli masz większe niż 20 dyskami na koncie magazynu, rozkłada się na tych maszynach wirtualnych wiele zasad, aby uzyskać wymagane operacje We/Wy podczas fazy transferu procesu tworzenia kopii zapasowej.
* Nie należy przywracać maszynę Wirtualną z systemem w magazynie Premium storage do tego samego konta magazynu. Jeśli proces operacji przywracania pokrywa się z operacji tworzenia kopii zapasowej, zmniejsza dostępne operacje We/Wy do utworzenia kopii zapasowej.
* Dla kopii zapasowej maszyny Wirtualnej usługi Premium na stos kopii zapasowej maszyny Wirtualnej w wersji 1 zaleca się przydzielanie tylko 50% całkowitego konta miejsca do magazynowania, dzięki czemu usługa Azure Backup można skopiować migawki do magazynu konta i transfer danych z tej lokalizacji skopiowany na koncie magazynu w magazynie.
* Upewnij się, że włączono danej wersji języka python na maszynach wirtualnych z systemem Linux dla kopii zapasowej jest w wersji 2.7

## <a name="data-encryption"></a>Szyfrowanie danych
Usługa Azure Backup nie szyfruje danych jako część procesu tworzenia kopii zapasowej. Jednak szyfrowanie danych na maszynie wirtualnej i bezproblemowe tworzenie kopii zapasowej chronionych danych (Dowiedz się więcej o [kopii zapasowej zaszyfrowanych danych](backup-azure-vms-encryption.md)).

## <a name="calculating-the-cost-of-protected-instances"></a>Obliczanie kosztu chronionych wystąpień
Maszyn wirtualnych platformy Azure, których kopie zapasowe są tworzone za pomocą usługi Azure Backup są podlegają [cennika usługi Azure Backup](https://azure.microsoft.com/pricing/details/backup/). Chronione wystąpienia jest obliczany na *rzeczywiste* rozmiar maszyny wirtualnej, która jest sumą wszystkich danych na maszynie wirtualnej — z wyjątkiem magazynu tymczasowego.

Cennik tworzenia kopii zapasowych maszyn wirtualnych nie jest oparty na maksymalny obsługiwany rozmiar dla każdego dysku danych dołączonego do maszyny wirtualnej. Cennik zależy od rzeczywistych danych, które są przechowywane na dysku danych. Podobnie opłata za magazyn kopii zapasowych zależy od ilości danych, który jest przechowywany w usłudze Azure Backup, który jest sumą rzeczywiste dane w poszczególnych punktach odzyskiwania.

Za przykład niech posłuży A2 standardowy rozmiar maszyny wirtualnej, która ma dwa dyski dodatkowe dane o maksymalnym rozmiarze 1 TB. W poniższej tabeli przedstawiono rzeczywiste dane przechowywane na każdym z tych dysków:

| Typ dysku | Maks. rozmiar | Rzeczywiste dane |
| --------- | -------- | ----------- |
| Dysk systemu operacyjnego |1023 GB |17 GB |
| Dysk lokalny / tymczasowe dysku |135 GB |5 GB (nie dołączona do tworzenia kopii zapasowych) |
| Dysk danych 1 |1023 GB |30 GB |
| Dysk danych 2 |1023 GB |0 GB |

Rzeczywisty rozmiar maszyny wirtualnej jest w tym przypadku 17 GB + 30 GB + 0 GB = 47 GB. Ten rozmiar chronionego wystąpienia (47 GB) staje się podstawą miesięczny rachunek. Wzrostem ilości danych na maszynie wirtualnej rozmiar chronionego wystąpienia w związku z tym używany dla zmiany w rozliczeniach.

Nie można uruchomić rozliczeń, dopiero po zakończeniu pierwszego pomyślne tworzenie kopii zapasowych. W tym momencie rozpoczyna się okres rozliczeniowy magazynu i chronionych wystąpień. Rozliczeń będzie kontynuowane, dopóki ma żadnych kopii zapasowej danych przechowywanych w magazynie dla maszyny wirtualnej. Jeśli użytkownik zaprzestanie ochrony na maszynie wirtualnej, ale istnieją dane kopii zapasowej maszyny wirtualnej w magazynie, nadal rozliczeń.

Rozliczenia dla określonej maszyny wirtualnej zatrzymuje tylko wtedy, gdy ochrona jest zatrzymana, a wszystkie dane kopii zapasowej zostaną usunięte. Po zatrzymaniu ochrony i nie istnieją aktywne zadania tworzenia kopii zapasowej, rozmiar Ostatnia pomyślna kopia zapasowa maszyny Wirtualnej staje się rozmiar chronionego wystąpienia, umożliwiający miesięczny rachunek.

## <a name="questions"></a>Pytania?
Jeśli masz pytania lub jeśli brakuje Ci jakiejś funkcji, [prześlij nam opinię](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie kopii zapasowych maszyn wirtualnych](backup-azure-arm-vms.md)
* [Zarządzanie kopiami zapasowymi maszyny wirtualnej](backup-azure-manage-vms.md)
* [Przywracanie maszyn wirtualnych](backup-azure-arm-restore-vms.md)
* [Rozwiązywanie problemów dotyczących tworzenia kopii zapasowej maszyny Wirtualnej](backup-azure-vms-troubleshoot.md)
