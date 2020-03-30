---
title: Przewodnik po kopiach zapasowych dla systemu SAP HANA na maszynach wirtualnych platformy Azure | Dokumenty firmy Microsoft
description: Przewodnik po kopiach zapasowych dla systemu SAP HANA zapewnia dwie główne możliwości tworzenia kopii zapasowych dla systemu SAP HANA na maszynach wirtualnych platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: bb32350597059209e5baf01d53b0c59fdc2344f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255206"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Przewodnik po kopiach zapasowych dla systemu SAP HANA na maszynach wirtualnych platformy Azure

## <a name="getting-started"></a>Wprowadzenie

Przewodnik po kopii zapasowej dla systemu SAP HANA uruchomiony na maszynach wirtualnych platformy Azure będzie opisywał tylko tematy specyficzne dla platformy Azure. W przypadku ogólnych elementów związanych z tworzeniem kopii zapasowych SAP HANA należy zapoznać się z dokumentacją sap hana. Oczekujemy, że będziesz zaznajomiony z strategiami tworzenia kopii zapasowych bazy danych, przyczynami i motywacjami, aby mieć solidną i prawidłową strategię tworzenia kopii zapasowych i są świadomi wymagań twojej firmy dotyczące procedury tworzenia kopii zapasowych, okresu przechowywania kopii zapasowych i przywracania Procedura.

SAP HANA jest oficjalnie obsługiwany na różnych typach maszyn wirtualnych platformy Azure, takich jak Azure M-Series. Aby uzyskać pełną listę maszyn wirtualnych platformy Azure z certyfikatem SAP HANA i jednostek dużych wystąpień HANA, zapoznaj się z [programem Znajdź certyfikowane platformy IaaS.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Platforma Microsoft Azure oferuje szereg jednostek, w których sap HANA działa niezwirtualizowane na serwerach fizycznych. Ta usługa nosi nazwę [HANA Dużych Wystąpień](hana-overview-architecture.md). Ten przewodnik nie obejmuje procesów tworzenia kopii zapasowych i narzędzi dla dużych wystąpień HANA. Ale będzie ograniczona do maszyn wirtualnych platformy Azure. Aby uzyskać szczegółowe informacje na temat procesów tworzenia kopii zapasowych/przywracania za pomocą dużych wystąpień HANA, przeczytaj artykuł [HLI Backup and Restore](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore).

W tym artykule skupiono się na trzech możliwościach tworzenia kopii zapasowych dla systemu SAP HANA na maszynach wirtualnych platformy Azure:

- Hana kopii zapasowej za pośrednictwem [usług azure kopii zapasowych](https://docs.microsoft.com/azure/backup/backup-overview) 
- Hana kopii zapasowej do systemu plików w maszynie wirtualnej systemu Azure Linux (patrz [SAP HANA Azure Backup na poziomie pliku)](sap-hana-backup-file-level.md)
- Kopia zapasowa HANA oparta na migawkach magazynu przy użyciu funkcji migawki obiektów blob usługi Azure storage ręcznie lub usługi Azure Backup


SAP HANA oferuje interfejs API kopii zapasowej, który umożliwia narzędzia do tworzenia kopii zapasowych innych firm do integracji bezpośrednio z SAP HANA. Produkty, takie jak usługa Azure Backup lub [Commvault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/) używają tego zastrzeżonego interfejsu do wyzwalania bazy danych SAP HANA lub ponownego wykonywania kopii zapasowych dziennika. 


Informacje na temat tego, jak można znaleźć oprogramowanie SAP obsługiwane na platformie Azure, można znaleźć w artykule [Jakie oprogramowanie SAP jest obsługiwane dla wdrożeń platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)

## <a name="azure-backup-service"></a>Usługa tworzenia kopii zapasowych platformy Azure

Pierwszy pokazany scenariusz to scenariusz, w którym usługa Azure `backint` Backup Service używa interfejsu SAP HANA do wykonywania kopii zapasowej przesyłania strumieniowego z bazy danych SAP HANA. Możesz też użyć bardziej ogólnej funkcji usługi Azure Backup, aby utworzyć migawkę dysku spójną aplikacji i przenieść ją do usługi Azure Backup.

Usługa Azure Backup integruje się i jest certyfikowana jako rozwiązanie do tworzenia kopii zapasowych dla sap HANA przy użyciu zastrzeżonego interfejsu SAP HANA o nazwie [backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5). Aby uzyskać więcej informacji na temat rozwiązania, jego możliwości i regionów platformy Azure, gdzie jest on dostępny, przeczytaj [artykuł Macierzy pomocy technicznej do tworzenia kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure.](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support) Aby uzyskać szczegółowe informacje i zasady dotyczące usługi Azure Backup dla hana, przeczytaj artykuł [o kopii zapasowej bazy danych SAP HANA na maszynach wirtualnych platformy Azure.](https://docs.microsoft.com/azure/backup/sap-hana-db-about) 

Drugą możliwością skorzystania z usługi Azure Backup jest utworzenie spójnej kopii zapasowej aplikacji przy użyciu migawek dysków usługi Azure Premium Storage. Inne magazyny platformy Azure z certyfikatem HANA, takie jak [dysk Azure Ultra](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd) i usługi Azure [NetApp Files,](https://azure.microsoft.com/services/netapp/) nie obsługują tego rodzaju migawek za pośrednictwem usługi Azure Backup. Czytanie tych artykułów:

- [Planowanie infrastruktury kopii zapasowych maszyny wirtualnej na platformie Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Tworzenie kopii zapasowych maszyn wirtualnych z systemem Linux w celu spójnego zastosowania](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent) 

ta sekwencja aktywności wyłania się:

- Usługa Azure Backup musi wykonać skrypt wstępnej migawki, który stawia aplikację, w tym przypadku SAP HANA, w spójnym stanie
- Ponieważ ten spójny stan zostanie potwierdzony, usługa Azure Backup wykona migawki dysku
- Po zakończeniu migawek usługa Azure Backup cofnie działanie, które wykonała w skrypcie wstępnej migawki
- Po pomyślnym wykonaniu usługa Azure Backup będzie przesyłać dane strumieniowo do magazynu kopii zapasowych

W przypadku SAP HANA większość klientów używa akceleratora zapisu azure dla woluminów, które zawierają dziennik ponawienia SAP HANA. Usługa Azure Backup automatycznie wykluczy te woluminy z migawek. To wykluczenie nie szkodzi zdolności HANA do przywrócenia. Chociaż to zablokować możliwość przywracania z prawie wszystkich innych SAP obsługiwane DBMS.

Wadą tej możliwości jest fakt, że trzeba opracować własny skrypt przed i po migawce. Skrypt wstępnej migawki musi utworzyć migawkę HANA i obsługiwać ewentualne przypadki wyjątków. Natomiast skrypt po migawce musi ponownie usunąć migawkę HANA. Aby uzyskać więcej informacji na temat logiki wymagane, należy rozpocząć od [notatki pomocy technicznej SAP #2039883](https://launchpad.support.sap.com/#/notes/2039883). Zagadnienia dotyczące sekcji "Spójność danych SAP HANA podczas wykonywania migawek magazynu" w tym artykule w pełni mają zastosowanie do tego rodzaju kopii zapasowej.

> [!NOTE]
> Kopie zapasowe oparte na migawkach dysku dla systemu SAP HANA we wdrożeniach, w których jest używanych wiele kontenerów bazy danych, wymagają minimalnego zwolnienia dodatku SP04 dla systemu HANA 2.0
> 

Zobacz szczegółowe informacje o migawek magazynu w dalszej części tego dokumentu.

![Na rysunku przedstawiono dwie możliwości zapisania bieżącego stanu maszyny Wirtualnej](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>Inne metody tworzenia kopii zapasowych HANA
Istnieją trzy inne metody tworzenia kopii zapasowych lub ścieżki, które można wziąć pod uwagę:

- Tworzenie kopii zapasowej względem udziału NFS, który jest oparty na usłudze Azure NetApp Files (ANF). Anf ponownie ma możliwość tworzenia migawek tych woluminów, na których przechowujesz kopie zapasowe. Biorąc pod uwagę przepływność, która ostatecznie wymaga do zapisu kopii zapasowych, to rozwiązanie może stać się kosztowną metodą. Chociaż łatwe do ustalenia, ponieważ HANA może zapisywać kopie zapasowe bezpośrednio w udziale nfs platformy Azure
- Wykonywanie kopii zapasowej HANA na dyskach dołączonych do maszyny Wirtualnej standardowych dysków SSD lub usługi Azure Premium Storage. W następnym kroku można skopiować te pliki kopii zapasowej do magazynu obiektów Blob platformy Azure. Strategia ta może być atrakcyjna cenowo
- Wykonywanie kopii zapasowej HANA na dyskach dołączonych do maszyny Wirtualnej standardowych dysków SSD lub usługi Azure Premium Storage. W następnym kroku dysk pobiera migawki na bieżąco. Po pierwszej migawce można użyć migawek przyrostowych w celu zmniejszenia kosztów

![Na rysunku przedstawiono opcje wykonywania kopii zapasowej pliku SAP HANA wewnątrz maszyny Wirtualnej](media/sap-hana-backup-guide/other-hana-backup-paths.png)

Na rysunku przedstawiono opcje wykonywania kopii zapasowej pliku SAP HANA wewnątrz maszyny Wirtualnej, a następnie przechowywania plików kopii zapasowych HANA w innym miejscu przy użyciu różnych narzędzi. Jednak wszystkie rozwiązania nie obejmujące usługi tworzenia kopii zapasowych innej firmy lub usługi Azure Backup mają kilka przeszkód wspólnych. Niektóre z nich mogą być wymienione, takie jak administrowanie przechowywania, automatyczny proces przywracania i zapewnienie automatycznego odzyskiwania w czasie, jak usługa Azure Backup lub inne wyspecjalizowane pakiety i usługi kopii zapasowych innych firm. Wiele z tych usług innych firm jest w stanie uruchomić na platformie Azure. 


## <a name="sap-resources-for-hana-backup"></a>Zasoby SAP dla kopii zapasowej HANA

### <a name="sap-hana-backup-documentation"></a>Dokumentacja tworzenia kopii zapasowych SAP HANA

- [Wprowadzenie do administracji SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Planowanie strategii tworzenia kopii zapasowych i odzyskiwania danych](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Zaplanuj hana backup przy użyciu ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Planowanie kopii zapasowych danych (kokpit SAP HANA)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Często zadawane pytania dotyczące kopii zapasowej SAP HANA w [SAP Note 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Często zadawane pytania dotyczące migawek bazy danych i magazynu SAP HANA w [sap Note 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Nieodpowiednie sieciowe systemy plików do tworzenia kopii zapasowych i odzyskiwania w [SAP Note 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Jak sprawdzić poprawność kopii zapasowej SAP HANA
Niezależnie od metody tworzenia kopii zapasowej, uruchamianie przywracania testu względem innego systemu jest absolutną koniecznością. Takie podejście zapewnia sposób, aby upewnić się, że kopia zapasowa jest poprawna i wewnętrzne procesy tworzenia kopii zapasowych i przywracania pracy zgodnie z oczekiwaniami. Przywracanie kopii zapasowych może być przeszkodą lokalną ze względu na wymagania dotyczące infrastruktury, jest znacznie łatwiejsze do wykonania w chmurze, zapewniając niezbędne zasoby tymczasowo w tym celu. Prawdą jest, że istnieją narzędzia dostarczone z HANA, które mogą sprawdzać pliki kopii zapasowych na możliwość przywracania. Jednak celem częstych ćwiczeń przywracania jest przetestowanie procesu przywracania bazy danych i przeszkolenie tego procesu z personelem operacyjnym.

Należy pamiętać, że wykonanie prostego przywracania i sprawdzanie, czy hana jest uruchomiony, nie jest wystarczające. Należy uruchomić sprawdzanie spójności tabeli, aby upewnić się, że przywrócona baza danych jest w porządku. SAP HANA oferuje kilka rodzajów kontroli spójności opisanych w [SAP Note 1977584](https://launchpad.support.sap.com/#/notes/1977584).

Informacje na temat sprawdzania spójności tabel można również znaleźć w witrynie SAP w [witrynie Kontroli spójności tabeli i katalogu](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Plusy i minusy tworzenia kopii zapasowych HANA w porównaniu z migawką pamięci masowej

SAP&#39;nie daje pierwszeństwa albo hana kopii zapasowej w porównaniu migawki magazynu. Wymienia ich plusy i minusy, więc można określić, którego używać w zależności od sytuacji i dostępnej technologii pamięci masowej (patrz [Planowanie strategii tworzenia kopii zapasowych i odzyskiwania).](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)

Na platformie Azure należy pamiętać o tym, że funkcja migawki obiektów blob platformy Azure&#39;nie zapewnia spójności systemu plików na wielu dyskach (zobacz [Używanie migawek obiektów blob z programem PowerShell).](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) 

Ponadto należy zrozumieć implikacje rozliczeń podczas częstej pracy z migawkami obiektów blob, jak opisano w tym artykule: [Opis jak migawki naliczane opłaty](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)— jest&#39;t tak oczywiste, jak przy użyciu dysków wirtualnych platformy Azure.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>Spójność danych SAP HANA podczas robienia migawek magazynu

Jak wcześniej udokumentowano, opisujące możliwości tworzenia kopii zapasowych migawki usługi Azure Backup, spójność systemu plików i aplikacji jest obowiązkowe podczas wykonywania migawek magazynu. Najprostszym sposobem uniknięcia problemów byłoby zamknięcie sap HANA, a może nawet całej maszyny wirtualnej. Coś, co nie jest możliwe dla wystąpienia produkcyjnego.

> [!NOTE]
> Kopie zapasowe oparte na migawkach dysku dla systemu SAP HANA we wdrożeniach, w których jest używanych wiele kontenerów bazy danych, wymagają minimalnego zwolnienia dodatku SP04 dla systemu HANA 2.0
> 

Usługa Azure storage, nie zapewnia spójności systemu plików na wielu dyskach lub woluminach, które są dołączone do maszyny Wirtualnej podczas procesu migawki. Oznacza to, że spójność aplikacji podczas migawki musi być dostarczana przez aplikację, w tym przypadku sam SAP HANA. [Uwaga SAP 2039883](https://launchpad.support.sap.com/#/notes/2039883) ma ważne informacje na temat kopii zapasowych SAP HANA według migawek magazynu. Na przykład w systemach plików XFS konieczne jest uruchomienie **zamrożenia xfs\_** przed uruchomieniem migawki pamięci masowej w celu zapewnienia spójności aplikacji (patrz [\_xfs freeze(8) - Strona man systemu Linux,](https://linux.die.net/man/8/xfs_freeze) aby uzyskać szczegółowe informacje na temat **zamrożenia xfs\_**).

Zakładając, że istnieje system plików XFS obejmujący cztery dyski wirtualne platformy Azure, poniższe kroki zapewniają spójną migawkę reprezentującą obszar danych HANA:

1. Tworzenie przygotowania migawki danych HANA
1. Zamrozić systemy plików wszystkich dysków /woluminów (na przykład użyj **xfs\_freeze)**
1. Tworzenie wszystkich niezbędnych migawek obiektów blob na platformie Azure
1. Odblogodowy system plików
1. Potwierdź migawkę danych HANA (usunie migawkę)

Podczas korzystania z możliwości usługi Azure Backup do wykonywania kopii zapasowych spójnych migawek aplikacji, kroki #1 muszą być kodowane/skryptowane przez Ciebie w dla skryptu wstępnej migawki. Usługa Azure Backup wykona kroki #2 i #3. Kroki #4 i #5 muszą być ponownie dostarczone przez kod w skrypcie po migawce. Jeśli nie używasz usługi tworzenia kopii zapasowych platformy Azure, należy również kod/skrypt krok #2 i #3 na własną rękę.
Więcej informacji na temat tworzenia migawek danych HANA można znaleźć w następujących artykułach:

- [Migawki danych HANA] (https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- Więcej szczegółów do wykonania kroku #1 można znaleźć w artykule [Tworzenie migawki danych (Native SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 
- Szczegóły, aby potwierdzić/usunąć migawki danych HANA jako potrzeba w kroku #5 można znaleźć w artykule [Tworzenie migawki danych (Native SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 

Ważne jest, aby potwierdzić migawkę HANA. Ze względu &quot;na kopiowanie&quot; na zapis, SAP HANA może nie wymagać dodatkowego miejsca na dysku w tym trybie przygotowania migawki. Nie&#39;również można uruchomić nowych kopii zapasowych, dopóki migawka SAP HANA nie zostanie potwierdzona.


### <a name="sap-hana-backup-scheduling-strategy"></a>Strategia planowania kopii zapasowych SAP HANA

Artykuł SAP HANA [Planowanie strategii tworzenia kopii zapasowych i odzyskiwania](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) określa podstawowy plan wykonywania kopii zapasowych. Polegaj na dokumentacji SAP wokół HANA i środowiskach z innymi systemami dbms podczas definiowania strategii tworzenia kopii zapasowych/przywracania i procesu dla sap HANA. Sekwencja różnych typów kopii zapasowych i okres przechowywania są w dużym stopniu zależne od instalów SLA, które należy podać.


### <a name="sap-hana-backup-encryption"></a>Szyfrowanie kopii zapasowych SAP HANA

SAP HANA oferuje szyfrowanie danych i dzienników. Jeśli dane i dziennik SAP HANA nie są szyfrowane, kopie zapasowe nie są domyślnie szyfrowane. Jednak SAP HANA oferuje oddzielne szyfrowanie kopii zapasowych, zgodnie z dokumentami w [SAP HANA Backup Encryption.](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html) Jeśli są uruchomione starsze wersje SAP HANA, może być konieczne sprawdzenie, czy szyfrowanie kopii zapasowej było częścią funkcji już podanych.  


## <a name="next-steps"></a>Następne kroki
* [Sap HANA Azure Backup na poziomie pliku](sap-hana-backup-file-level.md) opisuje opcję tworzenia kopii zapasowej opartej na plikach.
* Aby dowiedzieć się, jak ustalić wysoką dostępność i zaplanować odzyskiwanie po awarii sap HANA na platformie Azure (duże wystąpienia), zobacz [SAP HANA (duże wystąpienia) wysoka dostępność i odzyskiwanie po awarii na platformie Azure.](hana-overview-high-availability-disaster-recovery.md)
