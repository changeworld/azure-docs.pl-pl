---
title: Planowanie infrastruktury kopii zapasowych maszyn wirtualnych na platformie Azure | Dokumentacja firmy Microsoft
description: Ważne uwagi dotyczące planowania kopii zapasowych maszyn wirtualnych na platformie Azure
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: Wykonaj kopię zapasową maszyn wirtualnych, kopii zapasowych maszyn wirtualnych
ms.assetid: 19d2cf82-1f60-43e1-b089-9238042887a9
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/23/2018
ms.author: markgal;trinadhk
ms.openlocfilehash: 47d5da880f47831274fe05817ac9c488464d3096
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="plan-your-vm-backup-infrastructure-in-azure"></a>Planowanie infrastruktury kopii zapasowych maszyny wirtualnej na platformie Azure
Ten artykuł zawiera wydajności i sugestie zasobów ułatwiają planowanie infrastruktury kopii zapasowych maszyn wirtualnych. Definiuje również kluczowych aspektów usługi tworzenia kopii zapasowej; te aspekty może mieć decydujące znaczenie dla określenia architektury, planowanie pojemności i planowania. Jeśli znasz [przygotować środowisko](backup-azure-arm-vms-prepare.md), następnym krokiem planowania jest przed rozpoczęciem [do tworzenia kopii zapasowych maszyn wirtualnych](backup-azure-arm-vms.md). Aby uzyskać więcej informacji o maszynach wirtualnych platformy Azure, zobacz [dokumentacji maszyn wirtualnych](https://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="how-does-azure-back-up-virtual-machines"></a>Jak Azure wykonywanie kopii zapasowych maszyn wirtualnych?
Gdy usługa Azure Backup Inicjuje zadania tworzenia kopii zapasowej w zaplanowanym terminie, wyzwalaczy usługi zapasowy numer wewnętrzny do tworzenia migawki w chwili. Używa usługi Azure Backup _VMSnapshot_ rozszerzenia w systemie Windows i _VMSnapshotLinux_ rozszerzenia w systemie Linux. Rozszerzenie jest zainstalowany podczas pierwszego tworzenia kopii zapasowej maszyny Wirtualnej. Aby zainstalować to rozszerzenie, musi być uruchomiona maszyna wirtualna. Jeśli maszyna wirtualna nie jest uruchomiona, usługa Backup utworzy migawkę powiązanego magazynu (ponieważ gdy maszyna wirtualna jest zatrzymana, nie występują zapisy aplikacji).

Podczas wykonywania migawki maszyn wirtualnych systemu Windows, usługi Kopia zapasowa koordynuje z woluminów w tle kopii Service (VSS) do uzyskania migawki spójne z dysków maszyny wirtualnej. Jeśli tworzysz kopię zapasową maszyn wirtualnych systemu Linux, napisania własnych skryptów niestandardowych w celu zapewnienia spójności podczas wykonywania migawki maszyny Wirtualnej. Szczegółowe informacje na temat wywoływania tych skryptów znajdują się w dalszej części tego artykułu.

Po utworzeniu migawki w usłudze Azure Backup dane są przesyłane do magazynu. Aby zmaksymalizować wydajność, usługa rozpoznaje i przesyła jedynie te bloki danych, które uległy zmianie od czasu utworzenia poprzedniej kopii zapasowej.

![Architektura kopii zapasowych maszyny wirtualnej platformy Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

Po ukończeniu przesyłania danych migawka jest usuwana, a utworzony zostaje punkt odzyskiwania.

> [!NOTE]
> 1. Podczas procesu tworzenia kopii zapasowej Azure Backup nie uwzględnia dysku tymczasowym dołączony do maszyny wirtualnej. Aby uzyskać więcej informacji znajduje się we wpisie na [magazyn tymczasowy](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).
> 2. Azure ma kopii zapasowej poziom miejsca do magazynowania migawki i transferuje ją do magazynu, nie należy zmieniać klucze konta magazynu, zakończenie zadania tworzenia kopii zapasowej.
> 3. Dla maszyn wirtualnych w warstwie premium Azure Backup kopiuje migawki do konta magazynu. To upewnij się, że usługa kopii zapasowej używa IOPS wystarczające do przesyłania danych do magazynu. To dodatkowe kopię magazynu jest pobierana zgodnie z harmonogramem przydzielony rozmiar maszyny Wirtualnej. 
>

### <a name="data-consistency"></a>Spójność danych
Tworzenie kopii zapasowej i przywracanie danych o kluczowym znaczeniu skomplikowane jest fakt, że krytyczne dane biznesowe musi kopii zapasowej podczas aplikacje, które tworzą dane biznesowe są uruchomione. Aby rozwiązać ten problem, kopia zapasowa Azure obsługuje kopie zapasowe spójnych z aplikacją dla systemu Windows i maszyn wirtualnych systemu Linux
#### <a name="windows-vm"></a>Maszyna wirtualna z systemem Windows
Kopia zapasowa Azure przejmuje VSS pełnych kopii zapasowych maszyn wirtualnych systemu Windows (Dowiedz się więcej o [pełna kopia zapasowa VSS](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)). Aby włączyć usługi VSS skopiowanie kopii zapasowych, następujący klucz rejestru musi być ustawiona na maszynie Wirtualnej.

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
"USEVSSCOPYBACKUP"="TRUE"
```

#### <a name="linux-vms"></a>Maszyny wirtualne z systemem Linux
Kopia zapasowa Azure zapewnia platformę skryptów. Do zapewnienia spójności aplikacji, podczas tworzenia kopii zapasowej maszyn wirtualnych systemu Linux, Utwórz niestandardowe skrypty przed i po skrypty, które kontroli przepływu pracy tworzenia kopii zapasowej i środowiska. Kopia zapasowa Azure wywołuje skrypt wstępnego przed wykonaniem migawki maszyny Wirtualnej i wywołuje skrypt po po zakończeniu zadania migawki maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [aplikacji spójne wirtualna kopii zapasowych przy użyciu skryptów przed i po skrypt](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).
> [!NOTE]
> Kopia zapasowa Azure tylko wywołuje zapisywane klienta przed i po skryptów. Jeśli skrypt przed i po skryptów zostanie wykonane pomyślnie, kopia zapasowa Azure oznacza punkt odzyskiwania jako aplikacji spójne. Jednak klient jest odpowiedzialny spójności aplikacji, korzystając z niestandardowych skryptów.
>


W następującej tabeli opisano typy spójności i warunki, które występują w czasie maszyny Wirtualnej Azure kopie zapasowe i przywracanie procedur.

| Spójność | Na podstawie usługi VSS | Objaśnienie i szczegóły |
| --- | --- | --- |
| Spójność aplikacji |Tak dla systemu Windows|Spójność aplikacji jest idealne dla obciążeń, zapewnia, że:<ol><li> Maszyna wirtualna *rozruch*. <li>Brak *nie uszkodzenie*. <li>Brak *bez utraty danych*.<li> Dane są spójne do aplikacji, która korzysta z danych poprzez włączenie aplikacji w czasie wykonywania kopii zapasowej — za pomocą usługi VSS lub poprzedzającego utworzenie kopii zapasowej lub używanego po skryptu.</ol> <li>*Maszyny wirtualne systemu Windows*-obciążeń najbardziej firmy Microsoft ma składniki zapisywania usługi VSS, które wykonują akcje specyficznego dla obciążenia związane z spójności danych. Na przykład Microsoft SQL Server ma składnik zapisywania usługi VSS, które powodują, że zapis do pliku dziennika transakcji i baza danych są wykonywane prawidłowo. Kopii zapasowych maszyny Wirtualnej systemu Windows Azure Tworzenie punktu odzyskiwania zapewniających spójność aplikacji zapasowy numer wewnętrzny należy wywołać przepływu pracy usługi VSS i Zakończ ją przed wykonaniem migawki maszyny Wirtualnej. Dla migawki maszyny Wirtualnej platformy Azure jako dokładne należy wykonać również składniki zapisywania usługi VSS wszystkich aplikacji w maszynie Wirtualnej platformy Azure. (Dowiedz się [podstawy VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) i Poznaj głębokość szczegóły [jej działania](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx)). </li> <li> *Maszyny wirtualne systemu Linux*— klienci mogą wykonywać [niestandardowego skryptu przed i po skryptu w celu zapewnienia spójności aplikacji](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent). </li> |
| Spójności systemu plików |Tak — w przypadku komputerów z systemem Windows |Istnieją dwa scenariusze, w której punkt odzyskiwania może być *systemu plików spójne*:<ul><li>Tworzenie kopii zapasowych maszyn wirtualnych systemu Linux na platformie Azure, bez wstępnie przygotowany-script/po-script lub jeśli wstępnie przygotowany-script/po-script nie powiodło się. <li>Błąd VSS podczas tworzenia kopii zapasowej dla maszyn wirtualnych systemu Windows na platformie Azure.</li></ul> W obu przypadkach najlepiej, którą można wykonać jest upewnij się, że: <ol><li> Maszyna wirtualna *rozruch*. <li>Brak *nie uszkodzenie*.<li>Brak *bez utraty danych*.</ol> Aplikacje muszą implementować własnych "Napraw" mechanizm na przywróconych danych. |
| Spójność awarii |Nie |Taka sytuacja jest odpowiednikiem maszyny wirtualnej występuje "awarii" (za pośrednictwem zmiennego lub stałego reset). Spójność awarii zwykle odbywa się podczas zamykania maszyny wirtualnej platformy Azure w czasie wykonywania kopii zapasowej. Punkt odzyskiwania spójna w razie awarii zapewnia żadnych gwarancji wokół spójności danych na nośniku — albo z punktu widzenia systemu operacyjnego lub aplikacji. Tylko dane, które już istnieje na dysku w momencie tworzenia kopii zapasowej zostaną zebrane i kopii zapasowej. <br/> <br/> Gdy są zazwyczaj żadnych gwarancji, uruchomieniem systemu operacyjnego, następuje sprawdzenie dysku procedury, takich jak narzędzia chkdsk, aby naprawić błędy uszkodzenia. Wszystkie dane w pamięci lub operacji zapisu, które nie zostały przeniesione na dysku zostaną utracone. Aplikacji zazwyczaj wynika z mechanizm weryfikacji w przypadku wycofywania danych musi być przeprowadzane. <br><br>Na przykład jeśli dziennik transakcji ma wpisy nie znajduje się w bazie danych, bazy danych oprogramowania przedstawia ponownie, dopóki dane są spójne. W przypadku danych zostanie rozmieszczona na wielu dyskach wirtualnych (takich jak woluminy łączone), punkt odzyskiwania spójna w razie awarii zapewnia żadnych gwarancji pod kątem poprawności danych. |

## <a name="performance-and-resource-utilization"></a>Wydajności i użycia zasobów
Podobnie jak oprogramowanie kopii zapasowej jest wdrożony lokalnymi należy zaplanować pojemności i wykorzystania zasobów potrzeby podczas tworzenia kopii zapasowych maszyn wirtualnych na platformie Azure. [Magazyn Azure ogranicza](../azure-subscription-service-limits.md#storage-limits) definiowania struktury wdrażania maszyny Wirtualnej, aby uzyskać maksymalną wydajność z minimalny wpływ na obciążeniami.

Podczas planowania wydajności tworzenia kopii zapasowych, należy zwrócić uwagę na następujące limity magazynu Azure:

* Maksymalna liczba wyjście na konto magazynu
* Całkowita liczba żądań stawka za transakcje konta magazynu

### <a name="storage-account-limits"></a>Limity konta magazynu
Dane kopii zapasowej skopiowane z konta magazynu, dodaje do operacji wejścia/wyjścia na sekundę (IOPS) i transfer danych wychodzących (lub przepływności) metryki konta magazynu. W tym samym czasie maszyny wirtualne zużywają również IOPS i przepustowość. Celem jest zapewnienie, że ruch maszyny wirtualnej i tworzenia kopii zapasowej nie przekraczają limitów konta miejsca do magazynowania.

### <a name="number-of-disks"></a>Liczba dysków
Proces tworzenia kopii zapasowej podejmie próbę ukończenia zadania tworzenia kopii zapasowej tak szybko jak to możliwe. W ten sposób zużywa można dowolną liczbą zasobów. Jednak wszystkie operacje We/Wy są ograniczone przez *przepływność docelowy dla pojedynczego obiektu Blob*, która ma ograniczenie 60 MB na sekundę. W celu maksymalizacji szybkości jego, próbuje wykonać kopię zapasową wszystkich dysków maszyny Wirtualnej procesu tworzenia kopii zapasowej *równolegle*. Jeśli maszyna wirtualna ma cztery dyski, usługa próbuje wykonać kopię zapasową wszystkich dysków cztery równolegle. **Liczba dysków** tworzona kopia zapasowa, jest najważniejszym czynnikiem umożliwiającym określenie ruch kopii zapasowej konta magazynu.

### <a name="backup-schedule"></a>Harmonogram tworzenia kopii zapasowych
Dodatkowy czynnik, który ma wpływ na wydajność jest **harmonogram tworzenia kopii zapasowych**. Jeśli konfigurujesz zasady, wszystkie maszyny wirtualne kopie zapasowe są tworzone w tym samym czasie, zostanie zaplanowane zakleszczenie ruchu. Proces tworzenia kopii zapasowej próbuje wykonać kopię zapasową wszystkich dysków równolegle. Aby zmniejszyć ruch kopii zapasowej z konta magazynu, należy wykonać kopię zapasową różnych maszyn wirtualnych w innym czasie dnia, bez nakładania się.

## <a name="capacity-planning"></a>Planowanie pojemności
Zestawienie poprzedniej czynników, należy zaplanować na potrzeby użycia konta magazynu. Pobierz [pojemności kopii zapasowej maszyny Wirtualnej, arkusz kalkulacyjny programu Excel planowania](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) aby zobaczyć wpływ dysku i harmonogram tworzenia kopii zapasowych wyborów.

### <a name="backup-throughput"></a>Przepustowość kopii zapasowej
Dla każdego dysku tworzona kopia zapasowa Azure Backup odczytuje bloków na dysku i przechowuje tylko zmienione dane (przyrostowej kopii zapasowej). W poniższej tabeli przedstawiono wartości średniej przepływności usługi kopii zapasowej. Następujące dane można oszacować ilość czasu wymagane do wykonania kopii zapasowej dysku na dany rozmiar.

| Operacja tworzenia kopii zapasowej | Najlepszym przepływności |
| --- | --- |
| Początkowa kopia zapasowa |160 MB/s |
| Przyrostowej kopii zapasowej (DR) |640 MB/s <br><br> Przepływności porzuca znacznie, jeśli zmienione dane (wymagane do wykonania kopii zapasowej) są rozsyłane na dysku.|

## <a name="total-vm-backup-time"></a>Łączny czas tworzenia kopii zapasowej maszyny Wirtualnej
Podczas gdy większość czasu tworzenia kopii zapasowej jest poświęcony na odczytywanie i kopiowanie danych, innych operacji przyczyniają się do całkowity czas wymagane do wykonania kopii zapasowej maszyny Wirtualnej:

* Czas potrzebny do [Zainstaluj lub zaktualizuj zapasowy numer wewnętrzny](backup-azure-arm-vms.md).
* Czas migawki to czas potrzebny do wyzwolenia migawki. Migawki są wyzwalane bliski zaplanowanego czasu tworzenia kopii zapasowej.
* Czas oczekiwania w kolejce. Ponieważ usługa kopii zapasowej przetwarza kopii zapasowych z wielu klientów, kopiowanie danych kopii zapasowej z migawki kopii zapasowej lub magazyn usług odzyskiwania może nie uruchomić natychmiast. Czas szczytowego obciążenia, czas oczekiwania może rozciąganie ze względu na liczbę kopii zapasowych przetwarzanych maksymalnie ośmiu godzin. Łączny czas tworzenia kopii zapasowej maszyny Wirtualnej jest jednak mniej niż 24 godziny dla zasad tworzenia kopii zapasowych codziennie.
* Czas transferu danych, czasu potrzebnego dla usługi tworzenia kopii zapasowych na potrzeby obliczania zmiany przyrostowe z poprzedniej kopii zapasowej i przenieść te zmiany do magazynu magazynu.

### <a name="why-am-i-observing-longer12-hours-backup-time"></a>Dlaczego mam obserwowania longer(>12 hours) kopii zapasowej czas?
Kopia zapasowa składa się z dwóch faz: tworzenie migawek i przesyłania jej do magazynu. Optymalizuje usługi Kopia zapasowa dla magazynu. Podczas transferu danych migawki do magazynu, usługa tylko przesyła zmiany przyrostowe z wcześniejszej migawki.  Aby ustalić zmiany przyrostowe, usługa oblicza sumę kontrolną bloków. Zmiana bloku bloku jest identyfikowany jako blok do wysłania do magazynu. Następnie ćwiczenia usługi dodatkowych do każdego z określonych bloków, wyszukiwanie możliwości zminimalizować danych do przesłania. Po przeprowadzeniu oceny wszystkie zmienione bloki, usługa łączy zmiany i wysyła je do magazynu. W niektórych starszych aplikacji małe, pofragmentowane zapisy nie są optymalne dla magazynu. Jeśli migawka zawiera wiele małych, pofragmentowane zapisy, usługa spędza dodatkowy czas przetwarzania danych zapisanych przez aplikacje. Blok zapisu zalecane aplikacji z platformy Azure, dla aplikacji działających w ramach maszyny Wirtualnej, jest co najmniej 8 KB. Jeśli aplikacja korzysta z bloku mniej niż 8 KB, odbywa się wydajność tworzenia kopii zapasowej. Aby uzyskać pomoc dotyczącą Dostrajanie aplikacji w celu zwiększenia wydajności tworzenia kopii zapasowych, zobacz [Dostrajanie aplikacji, aby uzyskać optymalną wydajność z usługą Azure storage](../virtual-machines/windows/premium-storage-performance.md). Chociaż artykuł na temat wydajności tworzenia kopii zapasowych używa przykłady magazynu Premium, wskazówki dotyczy dyski magazynu w warstwie standardowa.

## <a name="total-restore-time"></a>Przywracanie całkowity czas
Operacja przywracania składa się z dwóch zadań main sub: kopiowanie danych z magazynu do konta magazynu wybranego klienta i tworzenie maszyny wirtualnej. Kopiowanie danych z magazynu kopii zależy od tego, gdzie przechowywane kopie zapasowe są wewnętrznie na platformie Azure i przechowywania konta magazynu klienta. Czas potrzebny do skopiowania danych zależy od:
* Czas oczekiwania w kolejce — ponieważ usługa przetwarza przywrócić zadania z wielu klientów w tym samym czasie, przywracanie żądania są umieszczone w kolejce.
* Czas — kopiowania danych dane są kopiowane z magazynu do konta magazynu klienta. Przywróć czas zależy od IOPS i pobiera przepływności usługi Kopia zapasowa Azure na koncie magazynu wybranego odbiorcy. W celu skrócenia czasu kopiowania podczas procesu przywracania, wybierz konto magazynu, nie załadowano z innych aplikacji zapisami i odczytami.

## <a name="best-practices"></a>Najlepsze praktyki
Sugerujemy następujące te wskazówki podczas konfigurowania kopii zapasowych maszyn wirtualnych:

* Nie należy planować więcej niż 10 klasycznych maszyn wirtualnych z tej samej usługi w chmurze do tworzenia kopii zapasowych, w tym samym czasie. Jeśli chcesz wykonać kopię zapasową wielu maszyn wirtualnych z tej samej usługi w chmurze, przesunąć godziny rozpoczęcia tworzenia kopii zapasowej o godzinę.
* Nie należy planować więcej niż 40 maszyn wirtualnych, aby utworzyć kopię zapasową w tym samym czasie.
* Planowanie kopii zapasowych maszyn wirtualnych podczas godziny poza szczytem. Dzięki temu usługa Kopia zapasowa używa IOPS do przesyłania danych z konta magazynu klienta do magazynu.
* Upewnij się, że zasady są stosowane na maszynach wirtualnych umieszczonych na różnych kont magazynu. Zalecamy nie więcej niż 20 całkowita liczba dysków z konta magazynu pojedynczego jest chroniona przez ten sam harmonogram tworzenia kopii zapasowej. Jeśli masz większą niż 20 dyski na koncie magazynu, rozłożyć tych maszyn wirtualnych na wiele zasad, aby uzyskać wymagany IOPS transfer w fazie procesu tworzenia kopii zapasowej.
* Nie należy przywracać maszyny Wirtualnej z systemem na magazyn w warstwie Premium do tego samego konta magazynu. Jeśli proces operacji przywracania pokrywa się z operacji tworzenia kopii zapasowej, zmniejsza dostępne IOPS dla kopii zapasowej.
* Dla kopii zapasowej maszyny Wirtualnej — wersja Premium upewnij się, że dysków premium hostów ma co najmniej 50% wolnego miejsca do przemieszczania migawki do pomyślnego utworzenia kopii zapasowej konto magazynu. 
* Upewnij się, że ta wersja języka python na maszynach wirtualnych systemu Linux włączone dla kopii zapasowej jest 2.7

## <a name="data-encryption"></a>Szyfrowanie danych
Kopia zapasowa Azure nie szyfruje danych jako część procesu tworzenia kopii zapasowej. Jednak szyfrowanie danych w ramach maszyny Wirtualnej i łatwe tworzenie kopii zapasowej chronionych danych (więcej informacji o [tworzenie kopii zapasowych danych zaszyfrowanych](backup-azure-vms-encryption.md)).

## <a name="calculating-the-cost-of-protected-instances"></a>Obliczanie kosztu wystąpienia chronione
Podlegają maszyn wirtualnych platformy Azure, których kopie zapasowe są tworzone za pomocą usługi Kopia zapasowa Azure [cennikiem usługi Kopia zapasowa Azure](https://azure.microsoft.com/pricing/details/backup/). Obliczenia chronione wystąpienia są oparte na *rzeczywiste* rozmiar maszyny wirtualnej, który jest sumą wszystkich danych w maszynę wirtualną — bez magazynu tymczasowego.

Cennik tworzenia kopii zapasowych maszyn wirtualnych nie jest oparty na maksymalny rozmiar obsługiwany dla każdego dysku danych dołączonych do maszyny wirtualnej. Cennik jest oparta na dane przechowywane na dysku danych. Podobnie rachunku magazynu kopii zapasowej jest oparta na ilość danych przechowywanych w kopia zapasowa Azure, który jest sumą rzeczywistymi danymi każdego punktu odzyskiwania.

Na przykład zająć A2 standardowy rozmiar maszyny wirtualnej, która ma dwa dyski dodatkowe dane o maksymalnym rozmiarze 1 TB. W poniższej tabeli przedstawiono rzeczywistymi danymi przechowywanymi na każdym z tych dysków:

| Typ dysku | Maks. rozmiar | Rzeczywiste dane. |
| --------- | -------- | ----------- |
| Dysk systemu operacyjnego |1023 GB |17 GB |
| Dysk lokalny / tymczasowego dysku |135 GB |5 GB (nie uwzględniony w kopii zapasowej) |
| Dysk danych 1 |1023 GB |30 GB |
| Dysk z danymi 2 |1023 GB |0 GB |

Rzeczywisty rozmiar maszyny wirtualnej jest w tym przypadku 17 GB + 30 GB + 0 GB = 47 GB. Ten rozmiar chronione wystąpienia (47 GB) staje się podstawę rachunek miesięczny. Wraz z rozwojem ilości danych na maszynie wirtualnej, rozmiar chronione wystąpienia odpowiednio używane przy zmianach rozliczeń.

Nie można uruchomić rozliczeń, aż zostanie ukończone pomyślnie pierwszej kopii zapasowej. W tym momencie rozpoczyna się rozliczeń dla magazynu i chronione wystąpienia. Karta będzie kontynuowane tak długo, jak dowolne dane kopii zapasowej są przechowywane w magazynie dla maszyny wirtualnej. Jeśli użytkownik zaprzestanie ochrony na maszynie wirtualnej, ale dane kopii zapasowej maszyny wirtualnej istnieje w magazynie, nadal rozliczeń.

Rozliczeń dla określonej maszyny wirtualnej zatrzymuje tylko wtedy, gdy ochrona jest zatrzymana i zostaną usunięte wszystkie dane kopii zapasowej. Po zatrzymaniu ochrony i nie istnieją aktywne zadania tworzenia kopii zapasowej, rozmiar chronione wystąpienia używany do rachunek miesięczny staje się rozmiar ostatniej pomyślnej kopii zapasowej maszyny Wirtualnej.

## <a name="questions"></a>Pytania?
Jeśli masz pytania lub jeśli brakuje Ci jakiejś funkcji, [prześlij nam opinię](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie kopii zapasowych maszyn wirtualnych](backup-azure-arm-vms.md)
* [Zarządzanie kopiami zapasowymi maszyny wirtualnej](backup-azure-manage-vms.md)
* [Przywracanie maszyn wirtualnych](backup-azure-arm-restore-vms.md)
* [Rozwiązywanie problemów kopii zapasowej maszyny Wirtualnej](backup-azure-vms-troubleshoot.md)
