---
title: Projektowania i implementacji bazy danych programu Oracle na platformie Azure | Dokumentacja firmy Microsoft
description: Projektowania i implementacji bazy danych programu Oracle w środowisku platformy Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/22/2017
ms.author: rclaus
ms.openlocfilehash: b291066137df004625029aef12c3db4d5441a89c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Projektowania i implementacji bazy danych programu Oracle na platformie Azure

## <a name="assumptions"></a>Wartości domyślne

- Planujesz przeprowadzić migrację bazy danych Oracle z lokalnej na platformie Azure.
- Masz opis różnych metryk w raportach Oracle AWR.
- Możesz wiedzę na temat linii bazowej wydajności aplikacji oraz wykorzystania platformy.

## <a name="goals"></a>Cele

- Zrozumienie, jak zoptymalizować wdrożenia programu Oracle na platformie Azure.
- Poznaj opcje dla bazy danych Oracle w środowisku Azure dostrajania wydajności.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Różnice między lokalnymi i Azure implementacji 

Poniżej przedstawiono kilka ważnych rzeczy, które należy wziąć pod uwagę podczas migrowania aplikacji lokalnych do platformy Azure. 

Jedną istotną różnicą jest to, że w implementacji platformy Azure, zasobów, takich jak maszyn wirtualnych, dysków i sieci wirtualne są współużytkowane przez innych klientów. Ponadto zasobów może być ograniczony na podstawie wymagań. Zamiast koncentrujących się na temat niepowodzenia (MTBF), Azure więcej koncentruje się na pozostałych awarii (MTTR).

W poniższej tabeli wymieniono niektóre różnice między implementacją lokalną i Azure implementacji bazy danych programu Oracle.

> 
> |  | **Implementacja lokalna** | **Implementacja Azure** |
> | --- | --- | --- |
> | **Sieć** |LAN/WAN  |SDN (technologia Sdn)|
> | **Grupy zabezpieczeń** |Ograniczenia adresu IP i portu narzędzi |[Grupy zabezpieczeń sieci (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **Odporność** |MTBF (Średni czas między awariami) |MTTR (Średni czas do odzyskiwania)|
> | **Planowana konserwacja** |Stosowanie poprawek do uaktualnienia|[Zestawy dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (poprawki/uaktualniania zarządzane przez usługę Azure) |
> | **Zasób** |Dedykowany  |Współużytkowane z innymi klientami|
> | **Regiony** |Centra danych |[Pary regionu](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability)|
> | **Storage** |Dyski fizyczne/w sieci SAN |[Zarządzane Azure magazynu](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Skalowanie** |Skalowanie w pionie |Skalowanie w poziomie|


### <a name="requirements"></a>Wymagania

- Określ częstotliwość rozmiaru i wzrostu bazy danych.
- Określenie wymagań w zakresie IOPS, które można oszacować na podstawie raportów Oracle AWR lub innych narzędzi do monitorowania sieci.

## <a name="configuration-options"></a>Opcje konfiguracji

Istnieją cztery potencjalnych obszarów, które można dostosować w celu zwiększenia wydajności w środowisku platformy Azure:

- Rozmiar maszyny wirtualnej
- Przepustowość sieci
- Typy dysków i konfiguracji
- Ustawienia pamięci podręcznej dysku

### <a name="generate-an-awr-report"></a>Generowanie raportu AWR

Jeśli korzystasz z istniejącej bazy danych programu Oracle i jest planowana migracja do platformy Azure, masz kilka opcji. Można uruchomić raport Oracle AWR można pobrać metryki (IOPS, MB/s, GiBs i tak dalej). Następnie wybierz maszynę Wirtualną na podstawie zebranych metryk. Lub można skontaktuj się z zespołem infrastruktury, aby uzyskać podobne informacje.

Należy rozważyć działania raportu AWR podczas obciążeń zarówno regularne i szczytu, więc możesz porównać. Na podstawie tych raportów, można rozmiar maszyn wirtualnych na podstawie obciążenia średnia lub maksymalne obciążenie.

Poniżej przedstawiono przykładowy sposób wygenerować raport AWR:

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Kluczowych metryk

Metryki, który można uzyskać z raportu AWR są następujące:

- Całkowita liczba rdzeni
- Szybkość zegara Procesora
- Całkowitej ilości pamięci w GB
- Użycie procesora CPU
- Szczytowa szybkość transferu danych
- Liczba operacji We/Wy zmiany (odczyt/zapis)
- Wykonaj ponownie szybkość dziennika (MB/s)
- Przepustowość sieci
- Szybkość opóźnienia sieci (niski/wysoka)
- Rozmiar bazy danych w GB
- Bajty odebrane za pomocą programu SQL * Net od i do klienta

### <a name="virtual-machine-size"></a>Rozmiar maszyny wirtualnej

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. Szacowanie rozmiaru maszyny Wirtualnej na podstawie użycia procesora CPU, pamięci i we/wy z raportu AWR

Jedyną operacją, której może wyglądać na jest najwyższym pięć zdarzenia czasu pierwszego planu, które wskazują skutkującej wąskich gardeł systemu.

Na przykład na poniższym diagramie synchronizacji plików dziennika jest u góry. Wskazuje liczbę czeka przed LGWR zapisuje buforu dziennika Powtórz pliku dziennika. Te wyniki wskazują, że lepiej wykonuje magazynu lub dyski są wymagane. Ponadto na diagramie przedstawiono również liczbę procesora CPU (rdzenie) i ilość pamięci.

![Zrzut ekranu przedstawiający stronę raportu AWR](./media/oracle-design/cpu_memory_info.png)

Na poniższym diagramie przedstawiono całkowita liczba operacji We/Wy odczytu i zapisu. Znaleziono 59 GB do odczytu i 247.3 zapisane w czasie raportu.

![Zrzut ekranu przedstawiający stronę raportu AWR](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Wybierz Maszynę wirtualną

Na podstawie informacji zebranych z raportu AWR, następnym krokiem jest wybranie maszyny Wirtualnej o rozmiarze podobne, który spełnia wymagania. Listę dostępnych maszyn wirtualnych można znaleźć w artykule [zoptymalizowanych pod kątem pamięci](../../linux/sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Dostosowywanie rozmiaru maszyny Wirtualnej z podobnych seria maszyn wirtualnych w oparciu ACU

Po wybraniu maszyny Wirtualnej, należy zwrócić uwagę na ACU dla maszyny Wirtualnej. Możesz wybrać inną maszynę Wirtualną na podstawie wartości ACU dostosowany do potrzeb. Aby uzyskać więcej informacji, zobacz [jednostki rozwiązań usługi obliczenia Azure](https://docs.microsoft.com/azure/virtual-machines/windows/acu).

![Zrzut ekranu przedstawiający stronę jednostki ACU](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Przepustowość sieci

Na poniższym diagramie przedstawiono relację między przepływności i IOPS:

![Zrzut ekranu przedstawiający przepływność](./media/oracle-design/throughput.png)

Przepustowość sieci całkowita jest szacowana na podstawie następujących informacji:
- SQL * Net ruchu
- MB/s x wielu serwerów (strumienia wychodzącego takich jak Oracle Data Guard)
- Innych czynników, takich jak replikacja aplikacji

![Zrzut ekranu przedstawiający SQL * przepustowość sieci](./media/oracle-design/sqlnet_info.png)

Zgodnie z wymaganiami przepustowości sieci, istnieją różne typy bramy można wybrać. Obejmują one basic, VpnGw i usługa Azure ExpressRoute. Aby uzyskać więcej informacji, zobacz [bramy sieci VPN, na stronie dotyczącej cen](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Zalecenia**

- Opóźnienie sieci wyższy jest porównywany z lokalnego wdrożenia. Zmniejszenie sieci round rund może znacznie poprawić wydajność.
- Aby ograniczyć liczbę operacji, konsolidację aplikacji, które mają wysokie transakcji lub "chatty" aplikacji na tej samej maszyny wirtualnej.

### <a name="disk-types-and-configurations"></a>Typy dysków i konfiguracji

- *Domyślna dysków systemu operacyjnego*: tych typów dysków oferują trwałych danych i buforowania. Są zoptymalizowane pod kątem dostępu do systemu operacyjnego podczas uruchamiania, a nie są przeznaczone dla jednej transakcyjnej lub magazynu danych obciążenia (analitycznych).

- *Niezarządzane dysków*: Z tych typów dysku zarządzasz kont magazynu, w których są przechowywane pliki wirtualnego dysku twardego (VHD), które odpowiadają dysków maszyny Wirtualnej. Pliki VHD są przechowywane jako stronicowe obiekty BLOB na kontach magazynu Azure.

- *Dyski zarządzane*: Azure zarządza kontami magazynu, których używasz dla dysków maszyny Wirtualnej. Należy określić typ dysku (standardowy lub premium) oraz rozmiar dysku, które są potrzebne. Azure tworzy i którymi zarządza dysku dla Ciebie.

- *Dyski magazynu Premium*: tych typów dysków są najbardziej odpowiednie dla obciążeń produkcyjnych. Magazyn w warstwie Premium obsługuje dyski maszyn wirtualnych, które można dołączyć do określonego rozmiaru serii maszyn wirtualnych, takie jak seria DS, DSv2 i GS oraz F maszyn wirtualnych. Dysku premium jest dostarczany z różnych rozmiarach i można wybrać dyski od 32 GB do 4096 GB. Rozmiar każdego dysku ma specyfikacjami wydajności. W zależności od wymagań aplikacji można dołączyć jeden lub więcej dysków do maszyny Wirtualnej.

Podczas tworzenia nowego dysku zarządzanego z portalu, można wybrać **typ konta** dla typu dysku, którego chcesz użyć. Należy pamiętać, że nie wszystkie dostępne dyski są wyświetlane w menu rozwijanym. Po wybraniu określonego rozmiaru maszyny Wirtualnej, menu pokazuje tylko magazynu premium dostępne jednostki SKU, które są oparte na rozmiar tej maszyny Wirtualnej.

![Zrzut ekranu przedstawiający stronę dysków zarządzanych](./media/oracle-design/premium_disk01.png)

Aby uzyskać więcej informacji, zobacz [zarządzanych dysków dla maszyny wirtualne i Magazyn w warstwie Premium wysokiej wydajności](https://docs.microsoft.com/azure/storage/storage-premium-storage).

Po skonfigurowaniu magazynu na maszynie Wirtualnej można załadować testu z dysków przed utworzeniem bazy danych. Znajomość szybkości operacji We/Wy pod względem zarówno opóźnienia i przepływności może pomóc ustalić, czy oczekiwany przepływności z obiektami docelowymi opóźnienia obsługi maszyn wirtualnych.

Istnieje wiele narzędzi dla aplikacji testów obciążenia, takich jak Oracle Orion, Sysbench i Fio.

Po wdrożeniu bazy danych programu Oracle ponownie uruchomić test obciążenia. Uruchom obciążeń regularne i szczytu, a wyniki wskazują linii bazowej środowiska.

Może być większe znaczenie dla rozmiaru magazynu oparte na szybkość IOPS, a nie rozmiaru magazynu. Na przykład jeśli 5000 IOPS wymagane, ale wymagane jest tylko 200 GB, może nadal otrzymasz dysku premium klasy P30 nawet, jeśli zawiera więcej niż 200 GB przestrzeni dyskowej.

Szybkość IOPS można uzyskać z AWR raportu. Określono dziennika Powtórz, fizyczne odczyty i zapisy szybkości.

![Zrzut ekranu przedstawiający stronę raportu AWR](./media/oracle-design/awr_report.png)

Na przykład rozmiar Powtórz jest 12,200,000 bajtów na sekundę, która jest równa 11.63 MB/s.
IOPS jest 12,200,000 / 2,358 = 5,174.

Po umieszczeniu danych wymagań dotyczących operacji We/Wy, można wybrać kombinację dysków, które są najbardziej odpowiednie do spełnienia tych wymagań.

**Zalecenia**

- Dla tabel danych rozłożyć obciążenie We/Wy na liczbę dysków za pomocą funkcji ASM Oracle lub zarządzanego magazynu.
- Jak zwiększa rozmiar bloku we/wy dla operacji intensywnego odczytu i zapisu intensywnie, Dodaj więcej dysków danych.
- Zwiększ rozmiar bloku dla dużych sekwencyjnych procesów.
- Umożliwia kompresję danych Zmniejsz we/wy (dla danych i indeksów).
- Oddzielne Powtórz dzienniki systemu i temps, a cofnąć usług terminalowych na dyskach danych.
- Nie należy umieszczać plików aplikacji na domyślne dysków systemu operacyjnego (/ dev/sda). Te dyski nie są zoptymalizowane dla maszyny Wirtualnej szybkiego czas uruchamiania i nie może zawierać dobrą wydajność aplikacji.

### <a name="disk-cache-settings"></a>Ustawienia pamięci podręcznej dysku

Istnieją trzy opcje buforowania na hoście:

- *Tylko do odczytu*: wszystkie żądania są buforowane dla przyszłych operacji odczytu. Zapisuje wszystkie są zachowywane bezpośrednio do magazynu obiektów Blob Azure.

- *Odczyt i zapis*: jest to "odczytu z wyprzedzeniem" algorytmu. Odczyty i zapisy są buforowane dla przyszłych operacji odczytu. Zapisy non-write-through są zachowywane najpierw do lokalnej pamięci podręcznej. Dla programu SQL Server zapisy są zachowywane do magazynu Azure, ponieważ używa go do zapisu. Zapewnia także uzyskać najmniejsze opóźnienia dysku światła obciążeń.

- *Brak* (wyłączone): za pomocą tej opcji, można pominąć pamięci podręcznej. Wszystkie dane są przekazywane na dysku i utrwalone w magazynie Azure. Ta metoda zapewnia najwyższy szybkości operacji We/Wy dla intensywnych obciążeń we/wy. Należy również wziąć pod uwagę "kosztów transakcji".

**Zalecenia**

Aby zmaksymalizować przepustowość, zaleca się uruchamiania z **Brak** dla buforowania na hoście. Dla usługi Premium Storage należy pamiętać o tym, czy należy wyłączyć "bariery" w przypadku zainstalowania systemu plików z **tylko do odczytu** lub **Brak** opcje. Zaktualizuj plik /etc/fstab z identyfikatorem UUID na dyskach.

Aby uzyskać więcej informacji, zobacz [magazyn w warstwie Premium dla maszyn wirtualnych systemu Linux](https://docs.microsoft.com/azure/storage/storage-premium-storage#premium-storage-for-linux-vms).

![Zrzut ekranu przedstawiający stronę dysków zarządzanych](./media/oracle-design/premium_disk02.png)

- W przypadku dysków systemu operacyjnego, użyj domyślnej **odczytu/zapisu** buforowania.
- Dla systemu, TEMP i cofania użyj **Brak** do buforowania.
- W przypadku danych, użyj **Brak** do buforowania. Ale jeśli bazy danych jest tylko do odczytu lub intensywnego odczytu, użyj **tylko do odczytu** buforowania.

Po zapisaniu ustawień dysku danych, chyba że Odinstaluj dysku na poziomie systemu operacyjnego i ponownie zainstaluj po wprowadzeniu zmiany nie można zmienić ustawienia pamięci podręcznej hosta.


## <a name="security"></a>Bezpieczeństwo

Po Konfigurowanie i skonfigurowania środowiska platformy Azure, następnym krokiem jest zabezpieczenia sieci. Poniżej przedstawiono kilka zaleceń:

- *Zasady grupy NSG*: grupy NSG można zdefiniować przy podsieci lub kartę sieciową. Jest łatwiejsze w celu kontroli dostępu na poziomie podsieci zarówno zabezpieczeń i Wymuś routingu dla elementów, jak zapór aplikacji.

- *Jumpbox*: bezpieczniejszego dostępu administratorów nie należy bezpośrednio łączyć usługi aplikacji lub bazę danych. Jumpbox jest używane jako nośniki między administratora komputera i zasobów platformy Azure.
![Zrzut ekranu przedstawiający stronę topologii Jumpbox](./media/oracle-design/jumpbox.png)

    Administrator komputera powinien oferować ograniczonej IP dostęp do jumpbox tylko. Jumpbox powinien mieć dostęp do aplikacji i baz danych.

- *Sieć prywatna* (podsieci): zaleca się, że masz aplikacji usługi i bazy danych w różnych podsieciach, lepszą kontrolę można ustawić przez zasady grupy NSG.


## <a name="additional-reading"></a>Dodatkowe materiały

- [Configure Oracle ASM (Konfigurowanie programu Oracle ASM)](configure-oracle-asm.md)
- [Skonfiguruj Oracle Data Guard](configure-oracle-dataguard.md)
- [Konfigurowanie bramy Golden Oracle](configure-oracle-golden-gate.md)
- [Oracle kopii zapasowych i odzyskiwania](oracle-backup-recovery.md)

## <a name="next-steps"></a>Kolejne kroki

- [Samouczek: Tworzenie maszyn wirtualnych wysokiej dostępności](../../linux/create-cli-complete.md)
- [Eksploruj przykłady Azure CLI wdrożenia maszyny Wirtualnej](../../linux/cli-samples.md)
