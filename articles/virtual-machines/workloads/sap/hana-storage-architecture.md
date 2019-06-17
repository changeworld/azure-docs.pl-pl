---
title: Architektura magazynu oprogramowania SAP Hana na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Architektura magazynu sposób wdrażania oprogramowania SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 02272ee16cf3303890a8ba6d35d38676e98c788c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60478654"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>Architektura magazynu oprogramowania SAP HANA (duże wystąpienia)

Układ magazynu dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia) jest skonfigurowany przez platformę SAP HANA w klasycznym modelu wdrażania na SAP zalecane wytyczne. Wytyczne są udokumentowane w artykule [wymagania dotyczące magazynu oprogramowania SAP HANA](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) oficjalny dokument.

Duże wystąpienie typu klasy I HANA zawiera cztery razy woluminie pamięci jako woluminu magazynu. W klasie typu II jednostek dużych wystąpień HANA magazynu nie jest cztery razy więcej. Jednostki są dostarczane z woluminu, który jest przeznaczony do przechowywania kopii zapasowych dziennika transakcji platformy HANA. Aby uzyskać więcej informacji, zobacz [zainstalować i skonfigurować oprogramowanie SAP HANA (duże wystąpienia) na platformie Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Zobacz poniższą tabelę pod względem przydziału magazynu. W tabeli wymieniono nierównej pojemności dla różnych woluminach, które są dostarczane z różnych jednostek dużych wystąpień HANA.

| Duże wystąpienie oprogramowania HANA jednostki SKU | hana/data | Hana/log | Hana/udostępnione | Hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328 GB | 768 GB |1,280 GB | 768 GB |
| S96 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4,608 GB | 1024 GB | 1,536 GB | 1024 GB |
| S192m | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S192xm |  11,520 GB |  1,536 GB |  1,792 GB |  1,536 GB |
| S384 | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S384m | 12 000 GB | 2050 GB | 2050 GB | 2040 GB |
| S384xm | 16 000 GB | 2050 GB | 2050 GB | 2040 GB |
| S384xxm |  20,000 GB | 3,100 GB | 2050 GB | 3,100 GB |
| S576m | 20,000 GB | 3,100 GB | 2050 GB | 3,100 GB |
| S576xm | 31,744 GB | 4096 GB | 2048 GB | 4096 GB |
| S768m | UŻYWANE 28 000 GB | 3,100 GB | 2050 GB | 3,100 GB |
| S768xm | 40,960 GB | 6,144 GB | 4096 GB | 6,144 GB |
| S960m | 36,000 GB | 4,100 GB | 2050 GB | 4,100 GB |


Rzeczywistej objętości wdrożonej może się różnić w zależności od wdrożenia i narzędzie, które służy do pokazywania rozmiary woluminów.

Jeśli dzielisz jednostki SKU HANA duże wystąpienie może wyglądać kilka przykładów dzielenia możliwych elementów:

| Partycja pamięci w GB | hana/data | Hana/log | Hana/udostępnione | Hana / / kopia zapasowa dziennika |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792 GB | 640 GB | 1024 GB | 640 GB |
| 1536 | 3,328 GB | 768 GB | 1,280 GB | 768 GB |


Te rozmiary są liczbami nierównej woluminów, które może różnić się nieco w zależności od wdrażania i narzędzia służące do wzięcia pod woluminów. Istnieją także inne rozmiary partycji, takie jak 2,5 TB. Te rozmiary pamięci masowej są obliczane przy użyciu formuły podobny do poprzedniego partycje. Termin "partycje" nie oznacza, że system operacyjny, pamięć lub zasobów procesora CPU są w żaden sposób na partycje. Oznacza to, partycji magazynu dla różnych wystąpieniach HANA, które można wdrożyć w jednej jednostce dużych wystąpień HANA. 

Możesz potrzebować więcej pamięci masowej. Magazyn można dodać przy zakupie dodatkowego magazynu w jednostkach 1 TB. Ten dodatkowy magazyn może być dodany jako dodatkowe woluminu. Również może służyć do rozszerzenia co najmniej jedną z istniejących woluminów. Nie można zmniejszyć rozmiaru woluminu pierwotnie wdrożone, a przede wszystkim opisane w poprzednich tabelach. Również nie można zmienić nazwy woluminów lub zainstalować nazwy. Woluminy magazynu opisany wcześniej są dołączone do jednostek dużych wystąpień HANA jako woluminy NFS4.

Migawek magazynu służy do celów odzyskiwania kopii zapasowej i przywracanie i odzyskiwanie po awarii. Aby uzyskać więcej informacji, zobacz [platformy SAP HANA (duże wystąpienia) o wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Zapoznaj się [HLI obsługiwane scenariusze](hana-supported-scenario.md) szczegóły układu magazynu dla danego scenariusza.

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Uruchamianie wielu wystąpień oprogramowania SAP HANA w jednej jednostce dużych wystąpień HANA

Istnieje możliwość hostowania więcej niż jednego aktywnego wystąpienia platformy SAP HANA w jednostkach dużych wystąpień HANA. Zapewnienie możliwości migawki magazynu i odzyskiwania po awarii, taka konfiguracja wymaga woluminu, który został ustawiony na wystąpienie. Obecnie dużych wystąpień HANA jednostki można podzielić w następujący sposób:

- **S72, S72m, S96, S144, S192**: Przyrostem wynoszącym 256 GB z 256 GB najmniejszy początkowa jednostki. Można łączyć wielokrotności np. 256 GB do 512 GB do maksymalnej pamięci jednostki.
- **S144m i platformie S192m**: Przyrostem wynoszącym 256 GB i 512 GB najmniejsza jednostka. Można łączyć wielokrotności np. 512 GB i 768 GB do maksymalnej pamięci jednostki.
- **Typ klasy II**: W przyrostach 512 GB do najmniejszego początkowy jednostki 2 TB. Wielokrotności np. 512 GB, 1 TB i 1,5 TB, można połączyć do maksymalnej pamięci jednostki.

Kilka przykładów uruchamianie wielu wystąpień oprogramowania SAP HANA może wyglądać następująco.

| SKU | Rozmiar pamięci | Rozmiar magazynu | Rozmiary z wieloma bazami danych |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | wystąpienie oprogramowania HANA 1 x 768 GB<br /> lub wystąpienia 1 x 512 GB + 1 x 256 GB wystąpienia<br /> lub wystąpień 3 x 256 GB | 
| S72m | 1,5 TB | 6 TB | 3x512GB wystąpień HANA<br />lub wystąpienia 1 x 512 GB + 1 x 1 TB wystąpienia<br />lub wystąpień 6 x 256 GB<br />lub wystąpienie 1x1.5 TB | 
| S192m | 4 TB | 16 TB | 8 x 512 GB wystąpień<br />lub wystąpień 4 x 1 TB<br />lub wystąpień 4 x 512 GB + 2 x 1 TB wystąpień<br />lub wystąpień 4 x 768 GB + 2 x 512 GB wystąpień<br />lub wystąpienie 1 x 4 TB |
| S384xm | 8 TB | 22 TB | 4 x 2 TB wystąpień<br />lub wystąpień 2 x 4 TB<br />lub wystąpień 2 x 3 TB + 1 x 2 TB wystąpień<br />lub wystąpień 2x2.5 TB + 1 x 3 TB wystąpień<br />lub wystąpienie 1 x 8 TB |


Istnieją także inne różnice. 

## <a name="encryption-of-data-at-rest"></a>Szyfrowanie nieużywanych danych
Magazyn używany dla dużych wystąpień HANA umożliwia przezroczyste szyfrowanie danych, ponieważ są przechowywane na dyskach. Po wdrożeniu jednostki dużych wystąpień HANA można włączyć ten rodzaj szyfrowania. Można także zmienić zaszyfrowanych woluminów po wdrożeniu. Przejście z protokołem szyfrowania zaszyfrowanych woluminach jest niewidoczna i bez przerywania. 

O typie I klasy jednostek SKU, woluminu rozruchowego jednostki LUN są przechowywane, są szyfrowane. Dla klasy typu II jednostki SKU z duże wystąpienie HANA należy zaszyfrować rozruchu jednostki LUN z metodami systemu operacyjnego. Aby uzyskać więcej informacji skontaktuj się z zespołu zarządzania usługami firmy Microsoft.

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>Wymagane ustawienia dla większych wystąpień HANA w dużych wystąpieniach HANA
Magazyn używany w dużych wystąpień HANA ma ograniczenie rozmiaru pliku. [Ograniczenie rozmiaru jest 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) każdego pliku. W przeciwieństwie do ograniczenia rozmiaru plików w systemach plików EXT3 HANA nie jest świadomość niejawnie ograniczenia pamięci masowej, wymuszane przez Magazyn dużych wystąpień HANA. w rezultacie HANA nie automatycznie utworzy nowy plik danych po osiągnięciu limitu rozmiaru pliku o rozmiarze 16TB. Jako HANA próby rośnie pliku ponad 16 TB, platformy HANA będzie raportów błędów i serwerze indeksowania ulegnie awarii na końcu.

> [!IMPORTANT]
> Aby zapobiec próby rozwój pliki danych przekracza limit rozmiaru pliku 16 TB magazynu oprogramowania HANA, duże wystąpienie oprogramowania HANA, należy ustawić następujące parametry w pliku konfiguracyjnym global.ini Hana
> 
> - datavolume_striping=true
> - datavolume_striping_size_gb = 15000
> - Zobacz też SAP Uwaga [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Należy pamiętać o Uwaga SAP [#2631285](https://launchpad.support.sap.com/#/notes/2631285)




**Następne kroki**
- Zapoznaj się [obsługiwane scenariusze dla dużych wystąpień HANA](hana-supported-scenario.md)