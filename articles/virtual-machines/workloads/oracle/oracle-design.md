---
title: Projektowanie i implementację z bazą danych Oracle na platformie Azure | Dokumentacja firmy Microsoft
description: Projektowanie i implementowanie bazy danych Oracle w środowisku platformy Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: c5a76b9cee8fd6eb09ee4d24c1380202fd17cc6d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60836308"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Projektowanie i implementacja bazy danych Oracle na platformie Azure

## <a name="assumptions"></a>Założenia

- Zamierzasz migrować bazy danych Oracle ze środowiska lokalnego do platformy Azure.
- Masz zrozumienie różnych metryk w raportach Oracle AWR.
- Możesz wiedzę na temat punktu odniesienia wydajności aplikacji i wykorzystanie platformy.

## <a name="goals"></a>Cele

- Dowiedz się, jak zoptymalizować wdrożenia oprogramowania Oracle na platformie Azure.
- Poznaj opcje dla bazy danych Oracle w środowisku platformy Azure dostrajania wydajności.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Różnice między lokalnego i wdrażanie platformy Azure 

Poniżej przedstawiono niektóre ważne kwestie, należy pamiętać podczas migrowania aplikacji lokalnych do platformy Azure. 

Jedną istotną różnicą jest to, że w implementacji platformy Azure, zasoby, takie jak maszyny wirtualne, dyski i wirtualne są współużytkowane przez innych klientów. Ponadto zasoby mogą być ograniczone w oparciu o wymagania. Zamiast koncentrowania się na unikania awarii (MTBF), Azure bardziej koncentruje się na pozostałych awarii (MTTR).

Poniższa lista zawiera niektóre różnice między implementację w środowisku lokalnym i implementację platformy Azure z bazą danych Oracle.

> 
> |  | **Wdrożenia w środowisku lokalnym** | **Wdrożenia platformy Azure** |
> | --- | --- | --- |
> | **Sieć** |LAN/WAN  |SDN (Sdn)|
> | **Grupy zabezpieczeń** |Narzędzia ograniczenia adresu IP i portu |[Sieciowa grupa zabezpieczeń (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **Odporność na błędy** |MTBF (średniego czasu między awariami) |MTTR (średniego czasu odzyskiwania)|
> | **Planowana konserwacja** |Stosowanie poprawek bądź uaktualnień|[Zestawy dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (poprawki bądź uaktualnień zarządzane przez platformę Azure) |
> | **Zasób** |Dedykowany  |Współużytkowane z innymi klientami|
> | **Regiony** |Centra danych |[Pary regionów](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability)|
> | **Storage** |Dyski fizyczne/w sieci SAN |[Zarządzane usługi Azure storage](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Skalowanie** |Skalowanie w pionie |Skalowanie w poziomie|


### <a name="requirements"></a>Wymagania

- Określ częstotliwość rozmiar i wzrostu bazy danych.
- Ustal wymagania dotyczące operacji We/Wy, które można oszacować, na podstawie raportów Oracle AWR lub innych narzędzi do monitorowania sieci.

## <a name="configuration-options"></a>Opcje konfiguracji

Istnieją cztery potencjalne obszary, które można dostosować do zwiększenia wydajności w środowisku platformy Azure:

- Rozmiar maszyny wirtualnej
- Przepływność sieci
- Typy dysków i konfiguracji
- Ustawienia pamięci podręcznej dysku

### <a name="generate-an-awr-report"></a>Generowanie raportu AWR

Jeśli korzystasz z istniejącej bazy danych Oracle i jest planowana migracja na platformę Azure, masz kilka opcji. Możesz uruchomić raport Oracle AWR, aby uzyskać metryki (operacje We/Wy, MB/s, GiBs i tak dalej). Następnie wybierz maszynę Wirtualną na podstawie metryk, które zostały zebrane. Lub możesz skontaktować się ze zespołowi infrastruktury można pobrać informacji o podobnych.

Istnieje możliwość uruchomienia raportu AWR podczas regularnego i szczytowe wzrosty obciążeń, dzięki czemu można porównać. Na podstawie tych raportów, można rozmiar maszyn wirtualnych na podstawie średniego obciążenia lub przy maksymalnym obciążeniu.

Poniżej przedstawiono przykładowy sposób wygenerować raport AWR:

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Kluczowe metryki

Poniżej przedstawiono metryki, które można uzyskać z raportu AWR:

- Łączna liczba rdzeni
- Szybkość zegara Procesora
- Całkowitej ilości pamięci w GB
- Użycie procesora CPU
- Szczytowa szybkość transferu danych
- Częstotliwości zmian we/wy (odczyt/zapis)
- Wykonaj ponownie dziennika szybkość (MB/s)
- Przepływność sieci
- Szybkość opóźnienie sieci (niski/wysoki)
- Rozmiar bazy danych w GB
- Liczba bajtów odebranych za pośrednictwem programu SQL * Net od i do klienta

### <a name="virtual-machine-size"></a>Rozmiar maszyny wirtualnej

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. Szacowany rozmiar maszyny Wirtualnej na podstawie użycia procesora CPU, pamięci i operacji We/Wy z raportu AWR

Jedyną operacją, której może wyglądać na jest najważniejsze pięć zdarzenia czasowe pierwszego planu, które wskazują, gdzie znajdują się wąskie gardła systemu.

Na przykład na poniższym diagramie synchronizacji plików dziennika jest u góry. Jego wskazuje liczbę w tym czasie czeka, które są wymagane, zanim LGWR zapisuje Bufor dziennika Powtórz pliku dziennika. Te wyniki wskazują, że lepiej zachowującego magazynu lub dyski są wymagane. Ponadto diagram pokazuje również liczbę procesora CPU (rdzenie) i ilość pamięci.

![Zrzut ekranu przedstawiający stronę z raportem AWR](./media/oracle-design/cpu_memory_info.png)

Na poniższym diagramie przedstawiono łączna liczba operacji We/Wy odczytu i zapisu. Wystąpiły 59 GB, Odczyt i 247.3 zapisywane w chwili tworzenia raportu.

![Zrzut ekranu przedstawiający stronę z raportem AWR](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Wybierz Maszynę wirtualną

Na podstawie informacji zebranych z raportu AWR, następnym krokiem jest wybranie maszyny Wirtualnej o podobnej wielkości, która spełnia Twoje wymagania. Lista dostępnych maszyn wirtualnych można znaleźć w artykule [zoptymalizowanych pod kątem pamięci](../../linux/sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Dostosowywanie rozmiaru maszyny Wirtualnej przy użyciu podobnych serię maszyn wirtualnych, w oparciu o wartość ACU

Po wybraniu maszyny Wirtualnej, należy zwrócić uwagę na wartość ACU dla maszyny Wirtualnej. Możesz wybrać inną maszynę Wirtualną na podstawie wartości ACU, która lepiej odpowiada Twoim wymaganiom. Aby uzyskać więcej informacji, zobacz [Jednostka obliczeniowa platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/acu).

![Zrzut ekranu przedstawiający stronę jednostek ACU](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Przepływność sieci

Na poniższym diagramie przedstawiono relację między przepływność i operacje We/Wy:

![Zrzut ekranu przedstawiający przepływność](./media/oracle-design/throughput.png)

Przepływność sieci całkowita jest szacowany na podstawie następujących informacji:
- SQL*Net traffic
- MB/s x liczba serwerów (wychodzące strumień takich jak środowiska Oracle Data Guard)
- Inne czynniki, takie jak replikacji aplikacji

![Zrzut ekranu przedstawiający SQL * Net przepływności](./media/oracle-design/sqlnet_info.png)

Oparte na Twoje wymagania dotyczące przepustowości sieci, istnieją różne typy bram służących do wyboru. Obejmują one basic, VpnGw i usługi Azure ExpressRoute. Aby uzyskać więcej informacji, zobacz [bramy sieci VPN stronę z cennikiem](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Zalecenia**

- Opóźnienie sieci wyższej jest porównywany z wdrożeniem w środowisku lokalnym. Zmniejszenie sieci round podróży może znacznie zwiększyć wydajność.
- Aby zmniejszyć rund, skonsolidować aplikacji, które mają "duża liczba" aplikacji lub transakcji o wysokiej na tej samej maszyny wirtualnej.

### <a name="disk-types-and-configurations"></a>Typy dysków i konfiguracji

- *Domyślne dyski systemu operacyjnego*: Te typy dysków oferują trwałych danych i buforowania. Są zoptymalizowane pod kątem dostępu systemu operacyjnego podczas uruchamiania i nie są przeznaczone dla jednej transakcji lub magazynu danych obciążenia (analityczne).

- *Niezarządzane dyski*: W przypadku tych typów dysku możesz zarządzać kont magazynu, które przechowują pliki wirtualnego dysku twardego (VHD), które odpowiadają dysków maszyn wirtualnych. Pliki VHD są przechowywane jako stronicowe obiekty BLOB na kontach magazynu Azure.

- *Usługa Managed disks*: Platforma Azure zarządza kont magazynu, których używasz dla dysków maszyny Wirtualnej. Należy określić typ dysku (premium lub standardowa) i rozmiar dysku, które są potrzebne. Platforma Azure tworzy i zarządza dysku.

- *Dysków magazynu Premium storage*: Te typy dysków są najbardziej odpowiednie dla obciążeń produkcyjnych. Usługa Premium storage obsługuje dyski maszyn wirtualnych, które mogą być dołączane do określonego rozmiaru maszyny wirtualne z serii, takie jak maszyny wirtualne z serii DS, DSv2, GS i F. Dysku w warstwie premium jest dostarczany z różnych rozmiarów i możesz wybrać między dyskami w zakresie od 32 GB do 4096 GB. Rozmiar każdego dysku ma swój własny specyfikacje wydajności. W zależności od wymagań aplikacji możesz dołączyć co najmniej jeden dysk do maszyny Wirtualnej.

Po utworzeniu nowego dysku zarządzanego z poziomu portalu można wybrać **typ konta** dla typu dysku, którego chcesz użyć. Należy pamiętać, że nie wszystkie dostępne dyski są wyświetlane w menu rozwijanym. Po wybraniu określonego rozmiaru maszyny Wirtualnej, w menu pojawia się tylko magazyn premium dostępne jednostki SKU, które są oparte na tego rozmiaru maszyny Wirtualnej.

![Zrzut ekranu przedstawiający stronę dysku zarządzanego](./media/oracle-design/premium_disk01.png)

Po skonfigurowaniu magazynu na maszynie Wirtualnej, można załadować testu z dysków przed utworzeniem bazy danych. Znajomość szybkości operacji We/Wy pod kątem opóźnienia i przepływności może pomóc ustalić, czy maszyny wirtualne obsługuje oczekiwanej przepływności o wartości docelowe opóźnienia.

Istnieje szereg narzędzi do testowania obciążenia aplikacji, takich jak Oracle Orion Sysbench i Fio.

Ponownie uruchom test obciążenia po wdrożeniu bazy danych Oracle. Uruchom regularne i wartości szczytowych obciążeń, a wyniki wskazują linii bazowej środowiska.

Może być większe znaczenie dla rozmiaru magazynu, w oparciu o szybkość operacji We/Wy niż rozmiar magazynu. Na przykład jeśli wymagane operacje We/Wy jest 5000, ale wystarczy tylko 200 GB, możesz nadal otrzymać dysku P30 w warstwie premium klasy nawet, jeśli chodzi o więcej niż 200 GB miejsca.

Szybkość operacji We/Wy można uzyskać z raportu AWR. Zostanie ono określone dziennika ponawiania, odczyty fizyczne i szybkość zapisu.

![Zrzut ekranu przedstawiający stronę z raportem AWR](./media/oracle-design/awr_report.png)

Na przykład rozmiar ponownego wykonywania jest 12,200,000 bajtów na sekundę, która jest równa 11.63 MB/s.
Operacje We/Wy jest 12,200,000 / 2,358 = 5,174.

Po utworzeniu jasny obraz wymagań dotyczących operacji We/Wy, możesz wybrać kombinację dysków, które są najbardziej odpowiednie w celu spełnienia tych wymagań.

**Zalecenia**

- Dla danych tabel, rozkładają się obciążenie We/Wy na liczbę dysków przy użyciu magazynu zarządzanego lub Oracle ASM.
- W miarę zwiększania rozmiaru bloku operacji We/Wy operacji intensywnego odczytu i zapisu intensywnie, Dodaj więcej dysków danych.
- Zwiększ rozmiar bloku dla dużych sekwencyjnych procesów.
- Użyj kompresji danych, aby zredukować operacji We/Wy (dla danych i indeksów).
- Oddzielne dzienniki ponawiania, systemu i warunki i Cofnij usług terminalowych w osobne dyski z danymi.
- Nie należy umieszczać żadnych plików aplikacji na domyślne dyski systemu operacyjnego (/ dev/sda). Te dyski nie są zoptymalizowane pod kątem szybkiego maszyny wirtualnej czas uruchamiania, dlatego nie może zapewniają dobrą wydajność aplikacji.

### <a name="disk-cache-settings"></a>Ustawienia pamięci podręcznej dysku

Istnieją trzy opcje buforowania hosta:

- *Tylko do odczytu*: Wszystkie żądania są buforowane dla przyszłych operacji odczytu. Wszystkie operacje zapisu są zachowywane bezpośrednio do usługi Azure Blob storage.

- *Odczyt zapis*: Jest to algorytmów "odczytu z wyprzedzeniem". Odczyty i zapisy są buforowane dla przyszłych operacji odczytu. Non-write-through zapisy są zachowywane najpierw do lokalnej pamięci podręcznej. Dla programu SQL Server zapisy są zachowywane do usługi Azure Storage, ponieważ używa ona write-through. Umożliwia także najmniejszego opóźnienia dysku dla niewielkich obciążeń.

- *Brak* (wyłączone): Za pomocą tej opcji, można pominąć pamięci podręcznej. Wszystkie dane są przesyłane na dysku i utrwalone w usłudze Azure Storage. Ta metoda zapewnia najwyższej szybkości operacji We/Wy dla obciążeń intensywnie korzystających z operacji We/Wy. Należy również wziąć pod uwagę "Koszt transakcji".

**Zalecenia**

Aby zmaksymalizować przepływność, zalecamy rozpoczęcie od **Brak** dla buforowania hosta. Dla usługi Premium Storage, należy pamiętać o tym, czy należy wyłączyć "bariery" podczas instalacji systemu plików za pomocą **tylko do odczytu** lub **Brak** opcje. O identyfikatorze UUID na dyskach, należy zaktualizować plik/etc/fstab.

![Zrzut ekranu przedstawiający stronę dysku zarządzanego](./media/oracle-design/premium_disk02.png)

- W przypadku dysków systemu operacyjnego Użyj domyślnej **odczytu/zapisu** buforowania.
- SYSTEM, TEMP i cofania użytku **Brak** do buforowania.
- W przypadku danych, użyj **Brak** do buforowania. Ale jeśli bazy danych jest tylko do odczytu lub intensywnego odczytu, użyj **tylko do odczytu** buforowania.

Po zapisaniu ustawień dysku danych nie można zmienić ustawienie pamięci podręcznej hosta, chyba że Odinstaluj dysk na poziomie systemu operacyjnego i ponownie zainstaluj go tak, po dokonaniu zmiany.


## <a name="security"></a>Bezpieczeństwo

Po skonfigurowaniu i skonfigurować środowisko platformy Azure, następnym krokiem jest, aby zabezpieczyć swoją sieć. Poniżej przedstawiono kilka zaleceń:

- *Zasady sieciowej grupy zabezpieczeń*: Sieciowa grupa zabezpieczeń, można zdefiniować, podsieci lub karty sieciowej. Jest łatwiejsze w celu kontroli dostępu na poziomie podsieci, zarówno dla zabezpieczeń i wymuszanie routingu dla elementów, takich jak zapory aplikacji.

- *Serwer Przesiadkowy*: Na potrzeby bardziej bezpiecznego dostępu administratorów nie należy bezpośrednio łączyć do aplikacji usługi lub bazy danych. Serwer przesiadkowy jest używany jako nośniki między komputerem administratora i zasobów platformy Azure.
![Zrzut ekranu przedstawiający stronę topologia serwera Przesiadkowego](./media/oracle-design/jumpbox.png)

    Adresów IP z ograniczeniami dostępu do serwera przesiadkowego tylko powinno oferować się w komputerze administratora. Serwer przesiadkowy powinien mieć dostęp do aplikacji i baz danych.

- *Sieć prywatna* (podsieci): Zaleca się, że masz usługi aplikacji i bazy danych w różnych podsieciach, więc lepszą kontrolę, można ustawić przez zasady sieciowej grupy zabezpieczeń.


## <a name="additional-reading"></a>Materiały uzupełniające

- [Configure Oracle ASM (Konfigurowanie programu Oracle ASM)](configure-oracle-asm.md)
- [Konfigurowanie środowiska Oracle Data Guard](configure-oracle-dataguard.md)
- [Konfigurowanie bramy Golden Oracle](configure-oracle-golden-gate.md)
- [Oracle kopii zapasowych i odzyskiwania](oracle-backup-recovery.md)

## <a name="next-steps"></a>Kolejne kroki

- [Samouczek: Tworzenie maszyn wirtualnych o wysokiej dostępności](../../linux/create-cli-complete.md)
- [Poznaj przykłady interfejsu wiersza polecenia Azure wdrażania maszyn wirtualnych](../../linux/cli-samples.md)
