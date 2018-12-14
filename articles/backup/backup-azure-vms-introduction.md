---
title: Planowanie infrastruktury kopii zapasowej maszyny Wirtualnej na platformie Azure
description: Istotne zagadnienia dotyczące planowania, tworzenia kopii zapasowych maszyn wirtualnych na platformie Azure
services: backup
author: rayne-wiselman
manager: carmonm
keywords: kopie zapasowe maszyn wirtualnych, kopie zapasowe maszyn wirtualnych
ms.service: backup
ms.topic: conceptual
ms.date: 8/29/2018
ms.author: raynew
ms.openlocfilehash: e38f245197f2b1bdb22a2866028ad10f4ec39ec1
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2018
ms.locfileid: "53343501"
---
# <a name="plan-your-vm-backup-infrastructure-in-azure"></a>Planowanie infrastruktury kopii zapasowych maszyny wirtualnej na platformie Azure
Ten artykuł zawiera wydajności i sugestii zasobu, aby ułatwić planowanie infrastruktury kopii zapasowej maszyny Wirtualnej. Definiuje również kluczowe aspekty w usłudze Kopia zapasowa; te aspekty mogą być szczególnie ważne w określaniu architektury, planowania pojemności i planowania. Jeśli masz [przygotować środowisko](backup-azure-arm-vms-prepare.md), planowania jest kolejnym krokiem przed rozpoczęciem [do tworzenia kopii zapasowych maszyn wirtualnych](backup-azure-arm-vms.md). Aby uzyskać więcej informacji na temat maszyn wirtualnych platformy Azure, zobacz [dokumentacja dotycząca maszyn wirtualnych](https://azure.microsoft.com/documentation/services/virtual-machines/).

> [!NOTE]
> Ten artykuł jest przeznaczony dla dysków zarządzanych i niezarządzanych. Jeśli używasz dysków niezarządzanych, istnieją zalecenia dotyczące konta magazynu. Jeśli używasz [usługi Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md), nie trzeba martwić się o problemy dotyczące użycia wydajności lub zasobu. Platforma Azure optymalizuje wykorzystanie magazynu dla Ciebie.
>

## <a name="how-does-azure-back-up-virtual-machines"></a>Jakie platformy Azure kopia zapasowa maszyn wirtualnych?
Gdy usługa Azure Backup Inicjuje zadania tworzenia kopii zapasowej w zaplanowanym czasie, wyzwalaczy usługi zapasowy numer wewnętrzny, aby utworzyć migawkę punktu w czasie. Używa usługi Azure Backup _VMSnapshot_ rozszerzenie w Windows, a _VMSnapshotLinux_ rozszerzenia w systemie Linux. To rozszerzenie jest zainstalowane podczas pierwszego tworzenia kopii zapasowej maszyny Wirtualnej. Aby zainstalować rozszerzenie, musi być uruchomiona maszyna wirtualna. Jeśli maszyna wirtualna nie jest uruchomiona, usługa Backup utworzy migawkę powiązanego magazynu (ponieważ gdy maszyna wirtualna jest zatrzymana, nie występują zapisy aplikacji).

Podczas wykonywania migawki maszyn wirtualnych z systemem Windows usługa Backup koordynuje się z usługą kopiowania woluminów w tle (VSS, Volume Shadow Copy Service), aby uzyskać spójną migawkę dysków maszyny wirtualnej. Jeśli wykonujesz kopie zapasowe maszyn wirtualnych systemu Linux, można napisać własne niestandardowe skrypty do zapewnienie spójności podczas wykonywania migawki maszyny Wirtualnej. Szczegółowe informacje na temat wywoływania tych skryptów znajdują się w dalszej części tego artykułu.

Po utworzeniu migawki w usłudze Azure Backup dane są przesyłane do magazynu. Aby zmaksymalizować wydajność, usługa rozpoznaje i przesyła jedynie te bloki danych, które uległy zmianie od czasu utworzenia poprzedniej kopii zapasowej.

![Architektura kopii zapasowych maszyn wirtualnych platformy Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

Po ukończeniu przesyłania danych migawka jest usuwana, a utworzony zostaje punkt odzyskiwania.

> [!NOTE]
> 1. Podczas procesu tworzenia kopii zapasowej usługi Azure Backup nie uwzględnia dysku tymczasowego dołączonych do maszyny wirtualnej. Aby uzyskać więcej informacji, zapoznaj się z blogu na [magazynu tymczasowego](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).
> 2. Usługa Azure Backup tworzy migawkę poziom miejsca do magazynowania i transferuje ją do magazynu. Nie należy zmieniać klucze konta magazynu, aż do zakończenia zadania tworzenia kopii zapasowej.
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

Usługa Azure Backup zapewnia platformę skryptów do kontrolowania przepływu pracy tworzenia kopii zapasowych i środowiska. W celu zapewnienia spójnych z aplikacją kopii zapasowych maszyny Wirtualnej systemu Linux, należy użyć skryptów framework do tworzenia niestandardowych skryptów przed i skryptu używanego po utworzeniu. Wywoływanie skryptu poprzedzającego przed wykonaniem migawki maszyny Wirtualnej, a następnie wywołaj skrypt używany po utworzeniu, po ukończeniu zadania migawki maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz artykuł [kopie zapasowe maszyn wirtualnych z systemem Linux spójne aplikacji](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

> [!NOTE]
> Usługa Azure Backup tylko wywołuje napisanymi odbiorców wstępnej i skryptu używanego po utworzeniu. Jeśli skryptu poprzedzającego i skryptu używanego po utworzeniu wykonane pomyślnie, kopia zapasowa Azure oznacza punkt odzyskiwania jako aplikacja spójne. Jednak klient jest ponoszą ostateczną odpowiedzialność za spójności aplikacji, gdy za pomocą skryptów niestandardowych.
>

W poniższej tabeli opisano typy spójności i warunki, kiedy się pojawią.

| Spójność | Na podstawie usługi VSS | Wyjaśnienie, jak i szczegółów |
| --- | --- | --- |
| Spójność aplikacji |Tak dla Windows|Spójność aplikacji jest idealnym rozwiązaniem w przypadku obciążeń zapewnia, że:<ol><li> Maszyna wirtualna *jest uruchamiany*. <li>Brak *nie uszkodzenia*. <li>Brak *bez utraty danych*.<li> Dane są spójne do aplikacji, która korzysta z danych, poprzez włączenie aplikacji w czasie wykonywania kopii zapasowych — za pomocą skryptu usługi VSS lub pre/post.</ol> <li>*Maszyny wirtualne Windows*-obciążenia najbardziej firmy Microsoft mają składniki zapisywania usługi VSS, które są wykonywane działania specyficznego dla obciążenia związane z wyjaśnienie pojęcia spójności danych. Na przykład składnik zapisywania usługi VSS programu SQL Server zapewnia zapisuje plik dziennika transakcji, jak i bazy danych, są wykonywane prawidłowo. Dla kopii zapasowych maszyn wirtualnych Windows IaaS Tworzenie punktu odzyskiwania spójnego z aplikacją rozszerzenie kopii zapasowej należy wywołania przepływu pracy usługi VSS i oznacz go jako ukończony przed wykonaniem migawki maszyny Wirtualnej. Migawki maszyny Wirtualnej platformy Azure muszą być dokładne również należy wykonać składniki zapisywania usługi VSS wszystkich aplikacji na maszynie Wirtualnej platformy Azure. (Dowiedz się, [podstawy usługi VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) i detalach szczegóły [jak to działa](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx)). </li> <li> *Maszyny wirtualne systemu Linux*— klienci mogą wykonywać [niestandardowego skryptu poprzedzającego i skryptu używanego po utworzeniu w celu zapewnienia spójności aplikacji](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent). </li> |
| Spójności systemu plików |Tak — dla komputerów z systemem Windows |Istnieją dwa scenariusze, w których punkt odzyskiwania może być *pliku spójne na poziomie systemu*:<ul><li>Tworzenie kopii zapasowych maszyn wirtualnych systemu Linux na platformie Azure, bez konieczności wstępnej-script/odniesienie-script lub jeśli wstępnie przygotowany-script/odniesienie-script nie powiodło się. <li>Błąd VSS podczas tworzenia kopii zapasowej dla maszyn wirtualnych Windows na platformie Azure.</li></ul> W obu przypadkach upewnij się, że jest najlepsze, którą można wykonać: <ol><li> Maszyna wirtualna *jest uruchamiany*. <li>Brak *nie uszkodzenia*.<li>Brak *bez utraty danych*.</ol> Aplikacje muszą implementować ich własny mechanizm "poprawki" przywróconych danych. |
| Spójność awarii |Nie |Ta sytuacja jest równoznaczne z maszyny wirtualnej, u których występują "awarii" (za pośrednictwem i miękkich reset). Spójność awarii zazwyczaj ma miejsce, gdy maszyna wirtualna platformy Azure zostanie zamknięta w czasie wykonywania kopii zapasowej. Punktu odzyskiwania spójnego na poziomie awarii zawiera nie gwarancje w zakresie spójności danych na nośniku — z punktu widzenia systemu operacyjnego lub aplikacji. Tylko dane, które już istnieje na dysku w czasie wykonywania kopii zapasowej jest przechwytywane i kopii zapasowej. <br/> <br/> Gdy istnieją zazwyczaj żadnej gwarancji, uruchomieniem systemu operacyjnego, następuje sprawdzenie dysku procedury, takich jak narzędzia chkdsk, aby naprawić błędy uszkodzenia. Wszelkie dane w pamięci i operacji zapisu, które nie zostały przeniesione do dysku zostaną utracone. Aplikacja zazwyczaj następuje z własny mechanizm weryfikacji w przypadku wycofywania danych musi odbywać się. <br><br>Na przykład jeśli dziennik transakcji zawiera wpisy, które nie znajduje się w bazie danych, oprogramowanie bazy danych przedstawia ponownie, dopóki dane są spójne. Gdy data rozkłada się na wiele dysków wirtualnych (takich jak woluminy łączone), punktu odzyskiwania spójnego na poziomie awarii zapewnia żadnej gwarancji pod kątem poprawności danych. |

## <a name="performance-and-resource-utilization"></a>Wydajności i użycia zasobów
Podobnie jak oprogramowania kopii zapasowej, który jest wdrożony lokalnie należy również zaplanować pojemności i wykorzystania zasobów potrzeby podczas tworzenia kopii zapasowych maszyn wirtualnych na platformie Azure. [Limity usługi Azure Storage](../azure-subscription-service-limits.md#storage-limits) zdefiniowanie struktury wdrożeń maszyn wirtualnych, aby zapewnić maksymalną wydajność minimalne wpływu na działające zadania.

### <a name="number-of-disks"></a>Liczba dysków
Proces tworzenia kopii zapasowej podejmie próbę możliwie jak najszybciej ukończenia zadania tworzenia kopii zapasowej. W ten sposób zużywa tylu zasobów, ponieważ jest to możliwe. Jednak wszystkie operacje We/Wy są ograniczone dzięki *docelowa przepustowość dla pojedynczego obiektu Blob*, która ma limit równy 60 MB na sekundę. W celu zmaksymalizowania jej szybkość działania, proces tworzenia kopii zapasowej próbuje utworzyć kopię zapasową wszystkich dysków maszyny Wirtualnej *równolegle*. Jeśli maszyna wirtualna ma cztery dyski, usługa próbuje utworzyć kopię zapasową wszystkie cztery dyski równolegle. **Liczbę dysków** tworzona kopia zapasowa, jest najważniejszym czynnikiem umożliwiającym określenie ruchu kopii zapasowej konta magazynu.

### <a name="backup-schedule"></a>Harmonogram tworzenia kopii zapasowych
Dodatkowy czynnik, który ma wpływ na wydajność jest **harmonogram tworzenia kopii zapasowych**. Jeśli konfigurujesz zasady, dzięki czemu wszystkie maszyny wirtualne kopie zapasowe są tworzone w tym samym czasie, zostanie zaplanowane zakleszczenie ruchu. Proces tworzenia kopii zapasowej próbuje utworzyć kopię zapasową wszystkich dysków w sposób równoległy. Aby zmniejszyć ruch kopii zapasowej, tworzenie kopii zapasowych różnych maszyn wirtualnych w różnych porę dnia, bez nakładania się.

## <a name="capacity-planning"></a>Planowanie pojemności
Pobierz [VM backup planowania pojemności na potrzeby arkusz kalkulacyjny programu Excel](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) pozwala określić wpływ dysku i harmonogram tworzenia kopii zapasowych wyborów.

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
* Czas oczekiwania w kolejce. Od zadania tworzenia kopii zapasowej usługi procesów z wieloma klientami w tym samym czasie dane migawki, nie mogą być od razu kopiowane do magazynu usługi Recovery Services. W godzinach szczytu obciążenia może potrwać do ośmiu godzin przed kopie zapasowe są przetwarzane. Jednak całkowity czas tworzenia kopii zapasowej maszyny Wirtualnej jest krótszy niż 24 godziny dla zasad tworzenia kopii zapasowej dzienny.
Łączny czas tworzenia kopii zapasowych w mniej niż 24 godziny jest prawidłowa dla przyrostowych kopii zapasowych, ale nie może być dla pierwszej kopii zapasowej. Po raz pierwszy kopii zapasowej jest proporcjonalna i może być większa niż 24 godziny, w zależności od rozmiaru danych i tworzenia kopii zapasowej.
* Czas transferu danych, czas potrzebny do tworzenia kopii zapasowych usługi obliczeniowe przyrostowych zmian od poprzedniej kopii zapasowej i przenieść te zmiany do magazynu z magazynu.

### <a name="why-are-backup-times-longer-than-12-hours"></a>Dlaczego są dłużej niż 12 godzin godziny tworzenia kopii zapasowej?

Kopia zapasowa obejmuje dwa etapy: tworzenie migawek i przekazywania migawek do magazynu. Usługa Backup optymalizuje dla magazynu. Podczas transferu danych migawki do magazynu, usługa tylko przesyła przyrostowe zmiany z poprzednią migawkę.  Aby określić zmiany przyrostowe, usługa oblicza sumę kontrolną bloków. Jeśli blok zostanie zmieniony, blok jest rozpoznawany jako bloku do wysłania do magazynu. Następnie awarii usługi bardziej szczegółowo w każdej z określonych bloków, szuka możliwości zminimalizować danych do przesłania. Po dokonaniu oceny wszystkie zmienione bloki, usługa łączy zmiany i wysyła je do magazynu. W niektórych starszych aplikacji zapisów niewielkich, podzielonej zawartości nie są optymalne dla magazynu. Jeśli migawka zawiera wiele zapisów niewielkich, fragmentacji, usługa spędza dodatkowy czas przetwarzania danych zapisanych przez aplikacje. W przypadku aplikacji uruchomionych na maszynie Wirtualnej bloku zapisy aplikacji zalecane minimalne wynosząca 8 KB. Jeśli aplikacja korzysta z blokiem mniej niż 8 KB, odbywa się wydajność tworzenia kopii zapasowej. Aby uzyskać pomoc dotyczącą Dostrajanie aplikacji w taki sposób, aby zwiększyć wydajność tworzenia kopii zapasowych, zobacz [Dostrajanie aplikacji, aby uzyskać optymalną wydajność z usługą Azure storage](../virtual-machines/windows/premium-storage-performance.md). Chociaż artykuł w wydajności tworzenia kopii zapasowych używa przykładów magazynu Premium, wskazówki ma zastosowanie w przypadku dysków magazynu w warstwie standardowa.<br>
Może to mieć kilka przyczyn godzina długa kopii zapasowej:
  1. **Pierwsza kopia zapasowa dla nowo dodany dysk do maszyny Wirtualnej już chronione** <br>
    Jeśli maszyny Wirtualnej zostało zakończone tworzenie początkowej kopii zapasowej i wykonuje przyrostowej kopii zapasowej. Dodawanie nowych dysków pominąć SLA 1 dzień, w zależności od rozmiaru nowego dysku.
  2. **Fragmentacji** <br>
    Jeśli obciążenia (aplikacja) na maszynie Wirtualnej wykonuje małe zapisy pofragmentowane następnie go może niekorzystnie wpłynąć na wydajność tworzenia kopii zapasowej. <br>
  3. **Konto magazynu przeciążone** <br>
      a. Jeśli zaplanowano tworzenie kopii zapasowej podczas korzystania z podglądu aplikacji.  
      b. Jeśli więcej niż 5 – 10 dysków znajdują się w tym samym koncie magazynu.<br>
  4. **Tryb Check(CC) spójności** <br>
      Na > 1TB dysków, jeśli tworzenie kopii zapasowej odbywa się w trybie DW z przyczyn wymienionych poniżej:<br>
        a. Przenosi dysku zarządzanego jako część ponowne uruchomienie maszyny Wirtualnej.<br>
        b. Podwyższ poziom migawki do podstawowego obiektu blob.<br>

## <a name="total-restore-time"></a>Czas całkowity przywracania

Operacja przywracania obejmuje dwa główne zadania: kopiowanie danych z magazynu do konta magazynu wybranego klienta i tworzenie maszyny wirtualnej. Czas potrzebny do skopiowania danych z magazynu zależy od tego, których kopie zapasowe są przechowywane na platformie Azure i lokalizacji konta magazynu klienta. Czas poświęcony na kopiowanie danych zależy od:
* Czas oczekiwania w kolejce — ponieważ procesów informatycznych przywrócić zadania z wieloma klientami w tym samym czasie, przywracanie żądania są umieszczone w kolejce.
* Czas — kopiowania danych dane są kopiowane z magazynu do konta magazynu klienta. Przywróć czas zależy od operacji We/Wy i przepływność usługi Azure Backup pobiera na koncie magazynu wybranego klienta. W celu skrócenia czasu kopiowania podczas procesu przywracania, wybierz konto magazynu nie załadowano z innymi aplikacji zapisu i odczytu.

## <a name="best-practices"></a>Najlepsze praktyki

Sugerujemy następujące rozwiązania w zakresie podczas konfigurowania kopii zapasowych dla wszystkich maszyn wirtualnych:

* Nie Planowanie tworzenia kopii zapasowych dla więcej niż 10 klasycznych maszyn wirtualnych w tej samej usłudze w chmurze, w tym samym czasie. Jeśli chcesz utworzyć kopię zapasową wielu maszyn wirtualnych w tej samej usłudze w chmurze, należy przesunąć godziny rozpoczęcia tworzenia kopii zapasowych o godzinę.
* Nie Planowanie tworzenia kopii zapasowych dla ponad 100 maszyn wirtualnych z jednego magazynu, w tym samym czasie.
* Planowanie kopii zapasowych maszyn wirtualnych poza godzinami poza szczytem. Dzięki temu usługa Backup korzysta z operacji We/Wy do przesyłania danych z konta magazynu klienta do magazynu.
* Upewnij się, że włączone do tworzenia kopii zapasowych maszyn wirtualnych systemu Linux, język Python w wersji 2.7 lub nowszego.

### <a name="best-practices-for-vms-with-unmanaged-disks"></a>Najlepsze rozwiązania dotyczące maszyn wirtualnych z dyskami niezarządzanymi

Poniższe zalecenia dotyczą tylko maszyn wirtualnych przy użyciu dysków niezarządzanych. Jeśli Twoje maszyny wirtualne korzystają z dysków zarządzanych, usługa Backup obsługuje wszystkie działania związane z zarządzaniem magazynu.

* Upewnij się, że dotyczą zasady tworzenia kopii zapasowych maszyn wirtualnych, rozkładają się na wielu kontach magazynu. Nie więcej niż 20 łączna liczba dysków z jednego konta magazynu powinna być chroniona przez ten sam harmonogram tworzenia kopii zapasowej. Jeśli masz większe niż 20 dyskami na koncie magazynu, rozkłada się na tych maszynach wirtualnych wiele zasad, aby uzyskać wymagane operacje We/Wy podczas fazy transferu procesu tworzenia kopii zapasowej.
* Nie należy przywracać maszynę Wirtualną z systemem w magazynie Premium storage do tego samego konta magazynu. Jeśli proces operacji przywracania pokrywa się z operacji tworzenia kopii zapasowej, zmniejsza dostępne operacje We/Wy do utworzenia kopii zapasowej.
* Dla kopii zapasowej maszyny Wirtualnej usługi Premium na stos kopii zapasowej maszyny Wirtualnej w wersji 1 należy przydzielić tylko 50% całkowitego konta miejsca do magazynowania, dzięki czemu usługa Backup może kopiowania migawki do konta magazynu i transferu danych z konta magazynu do magazynu.


## <a name="data-encryption"></a>Szyfrowanie danych
Usługa Azure Backup nie szyfruje danych jako część procesu tworzenia kopii zapasowej. Jednak szyfrowanie danych na maszynie wirtualnej i bezproblemowe tworzenie kopii zapasowej chronionych danych (Dowiedz się więcej o [kopii zapasowej zaszyfrowanych danych](backup-azure-vms-encryption.md)).

## <a name="calculating-the-cost-of-protected-instances"></a>Obliczanie kosztu chronionych wystąpień
Maszyn wirtualnych platformy Azure, których kopie zapasowe są tworzone za pomocą usługi Azure Backup są podlegają [cennika usługi Azure Backup](https://azure.microsoft.com/pricing/details/backup/). Chronione wystąpienia jest obliczany na *rzeczywiste* rozmiar maszyny wirtualnej, która jest sumą wszystkich danych na maszynie wirtualnej — z wyjątkiem magazynu tymczasowego.

Cennik tworzenia kopii zapasowych maszyn wirtualnych nie jest oparty na maksymalny obsługiwany rozmiar dla każdego dysku danych dołączonego do maszyny wirtualnej. Cennik zależy od rzeczywistych danych, które są przechowywane na dysku danych. Podobnie opłata za magazyn kopii zapasowych zależy od ilości danych, który jest przechowywany w usłudze Azure Backup, który jest sumą rzeczywiste dane w poszczególnych punktach odzyskiwania.

Za przykład niech posłuży A2 standardowy rozmiar maszyny wirtualnej, która ma dwa dyski dodatkowe dane o maksymalnym rozmiarze 4 TB. W poniższej tabeli przedstawiono rzeczywiste dane przechowywane na każdym z tych dysków:

| Typ dysku | Maks. rozmiar | Rzeczywiste dane |
| --------- | -------- | ----------- |
| Dysk systemu operacyjnego |4095 GB |17 GB |
| Dysk lokalny / tymczasowe dysku |135 GB |5 GB (nie dołączona do tworzenia kopii zapasowych) |
| Dysk danych 1 |4095 GB |30 GB |
| Dysk danych 2 |4095 GB |0 GB |

Rzeczywisty rozmiar maszyny wirtualnej jest w tym przypadku 17 GB + 30 GB + 0 GB = 47 GB. Ten rozmiar chronionego wystąpienia (47 GB) staje się podstawą miesięczny rachunek. Wzrostem ilości danych na maszynie wirtualnej rozmiar chronionego wystąpienia w związku z tym używany dla zmiany w rozliczeniach.

Nie można uruchomić rozliczeń, dopiero po zakończeniu pierwszego pomyślne tworzenie kopii zapasowych. W tym momencie rozpoczyna się okres rozliczeniowy magazynu i chronionych wystąpień. Rozliczeń będzie kontynuowane, dopóki ma żadnych kopii zapasowej danych przechowywanych w magazynie dla maszyny wirtualnej. Jeśli użytkownik zaprzestanie ochrony na maszynie wirtualnej, ale istnieją dane kopii zapasowej maszyny wirtualnej w magazynie, nadal rozliczeń.

Rozliczenia dla określonej maszyny wirtualnej zatrzymuje tylko wtedy, gdy ochrona jest zatrzymana, a wszystkie dane kopii zapasowej zostaną usunięte. Po zatrzymaniu ochrony i nie istnieją aktywne zadania tworzenia kopii zapasowej, rozmiar Ostatnia pomyślna kopia zapasowa maszyny Wirtualnej staje się rozmiar chronionego wystąpienia, umożliwiający miesięczny rachunek.

## <a name="questions"></a>Pytania?
Jeśli masz pytania lub jeśli brakuje Ci jakiejś funkcji, [prześlij nam opinię](https://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie kopii zapasowych maszyn wirtualnych](backup-azure-arm-vms.md)
* [Zarządzanie kopiami zapasowymi maszyny wirtualnej](backup-azure-manage-vms.md)
* [Przywracanie maszyn wirtualnych](backup-azure-arm-restore-vms.md)
* [Rozwiązywanie problemów dotyczących tworzenia kopii zapasowej maszyny Wirtualnej](backup-azure-vms-troubleshoot.md)
