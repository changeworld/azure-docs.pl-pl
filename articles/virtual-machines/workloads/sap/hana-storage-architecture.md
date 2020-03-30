---
title: Architektura magazynu SAP HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Architektura magazynu, jak wdrożyć SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a12c454906d6c6ff702b7f635a91361bbe3994c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616891"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>Architektura magazynu SAP HANA (duże wystąpienia)

Układ magazynu dla sap HANA na platformie Azure (duże wystąpienia) jest skonfigurowany przez SAP HANA na klasyczny model wdrażania zgodnie z zalecanymi wytycznymi SAP. Wytyczne są udokumentowane w white paper [wymagania dotyczące pamięci masowej SAP HANA.](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)

Hana duże wystąpienie typu I klasy pochodzi z czterokrotnie wolumin pamięci jako wolumin magazynu. Dla typu II klasy jednostek dużych wystąpień HANA magazynu nie jest cztery razy więcej. Jednostki są wyposażone w wolumin, który jest przeznaczony do przechowywania kopii zapasowych dziennika transakcji HANA. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie sap HANA (duże wystąpienia) na platformie Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Zobacz poniższą tabelę pod względem alokacji magazynu. W tabeli wymieniono pojemność przybliżone dla różnych woluminów dostarczonych z różnymi jednostkami dużych wystąpień HANA.

| HANA Duże wystąpienie SKU | hana/dane | hana/log | hana/shared | hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 ( S72 ) | 1280 GB | 512 GB | 768 GB | 512 GB |
| S72m ( S72m ) | 3 328 GB | 768 GB |1280 GB | 768 GB |
| S96 | 1280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4608 GB | 1024 GB | 1 536 GB | 1024 GB |
| S192m | 11 520 GB | 1 536 GB | 1 792 GB | 1 536 GB |
| S192xm |  11 520 GB |  1 536 GB |  1 792 GB |  1 536 GB |
| S224 |  4 224 GB |  512 GB |  1024 GB |  512 GB |
| S224m |  8 448 GB |  512 GB |  1024 GB |  512 GB |
| S384 | 11 520 GB | 1 536 GB | 1 792 GB | 1 536 GB |
| S384m | 12 000 GB | 2050 GB | 2050 GB | 2040 GB |
| S384xm | 16 000 GB | 2050 GB | 2050 GB | 2040 GB |
| S384xxm |  20 000 GB | 3100 GB | 2050 GB | 3100 GB |
| S576m | 20 000 GB | 3100 GB | 2050 GB | 3100 GB |
| S576xm | 31 744 GB | 4 096 GB | 2048 GB | 4 096 GB |
| S768m | 28 000 GB | 3100 GB | 2050 GB | 3100 GB |
| S768xm | 40 960 GB | 6 144 GB | 4 096 GB | 6 144 GB |
| S960m | 36 000 GB | 4100 GB | 2050 GB | 4100 GB |


Rzeczywiste wdrożone woluminy mogą się różnić w zależności od wdrożenia i narzędzia używanego do pokazywalności woluminów.

Jeśli podzielisz jednostkę SKU dużych wystąpień HANA, może wyglądać kilka przykładów możliwych elementów podziału:

| Partycja pamięci w GB | hana/dane | hana/log | hana/shared | hana/log/kopia zapasowa |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1 792 GB | 640 GB | 1024 GB | 640 GB |
| 1536 | 3 328 GB | 768 GB | 1280 GB | 768 GB |


Te rozmiary są przybliżone numery woluminów, które mogą się nieznacznie różnić w zależności od wdrożenia i narzędzi używanych do patrzenia na woluminy. Istnieją również inne rozmiary partycji, takie jak 2,5 TB. Te rozmiary magazynu są obliczane za pomocą formuły podobnej do tej używanej dla poprzednich partycji. Termin "partycje" nie oznacza, że system operacyjny, pamięć lub zasoby procesora SĄ w jakikolwiek sposób podzielone na partycje. Wskazuje partycje magazynu dla różnych wystąpień HANA, które można wdrożyć na jednej jednostce dużych wystąpień HANA. 

Może być potrzebna większa ilość miejsca do magazynowania. Możesz dodać pamięć masową, kupując dodatkową pamięć masową w jednostkach o pojemności 1 TB. Ten dodatkowy magazyn można dodać jako dodatkowy wolumin. Może również służyć do rozszerzenia jednego lub więcej istniejących woluminów. Nie jest możliwe zmniejszenie rozmiarów woluminów, jak pierwotnie wdrożone i w większości udokumentowane przez poprzednie tabele. Nie można również zmienić nazw woluminów ani nazw instalacji. Woluminy magazynu opisane wcześniej są dołączone do jednostek dużych wystąpień HANA jako woluminy NFS4.

Migawek magazynu można używać do tworzenia kopii zapasowych i przywracania oraz odzyskiwania po awarii. Aby uzyskać więcej informacji, zobacz [WYSOKIEJ DOSTĘPNOŚCI I ODZYSKIWANIA PO AWARII SAP HANA (Duże wystąpienia) na platformie Azure.](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Zapoznaj się [z obsługiwanymi scenariuszami HLI,](hana-supported-scenario.md) aby uzyskać szczegółowe informacje o układzie magazynu dla twojego scenariusza.

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Uruchamianie wielu wystąpień SAP HANA na jednej jednostce dużych wystąpień HANA

Jest możliwe host więcej niż jedno aktywne wystąpienie SAP HANA na jednostek dużych wystąpień HANA. Aby zapewnić możliwości migawek magazynu i odzyskiwania po awarii, taka konfiguracja wymaga zestawu woluminów na wystąpienie. Obecnie jednostki dużych wystąpień HANA można podzielić w następujący sposób:

- **S72, S72m, S96, S144, S192**: W przyrostach 256 GB, z 256 GB najmniejszą jednostką wyjściową. Różne przyrosty, takie jak 256 GB i 512 GB, można łączyć z maksymalną pamięcią urządzenia.
- **S144m i S192m**: W przyrostach 256 GB, z 512 GB najmniejszą jednostką. Różne przyrosty, takie jak 512 GB i 768 GB, można łączyć z maksymalną pamięcią urządzenia.
- **Typ II klasa:** W przyrostach 512 GB, z najmniejszą jednostką wyjściową 2 TB. Różne przyrosty, takie jak 512 GB, 1 TB i 1,5 TB, można łączyć z maksymalną pamięcią urządzenia.

Kilka przykładów uruchamiania wielu wystąpień SAP HANA może wyglądać następująco.

| SKU | Rozmiar pamięci | Rozmiar magazynu | Rozmiary z wieloma bazami danych |
| --- | --- | --- | --- |
| S72 ( S72 ) | 768 GB | 3 TB | Wystąpienie HANA 1x768 GB<br /> lub wystąpienie 1x512 GB + wystąpienie 1x256 GB<br /> lub wystąpienia 3x256 GB | 
| S72m ( S72m ) | 1,5 TB | 6 TB | Wystąpienia HANA 3x512 GB<br />lub wystąpienie 1x512 GB + wystąpienie 1x1 TB<br />lub wystąpienia 6x256 GB<br />lub wystąpienie 1x1,5 TB | 
| S192m | 4 TB | 16 TB | Wystąpienia 8x512 GB<br />lub wystąpienia 4x1 TB<br />lub wystąpienia 4x512 GB + wystąpienia 2x1 TB<br />lub wystąpienia 4x768 GB + wystąpienia 2x512 GB<br />lub wystąpienie 1x4 TB |
| S384xm | 8 TB | 22 TB | Wystąpienia 4x2 TB<br />lub wystąpienia 2x4 TB<br />lub wystąpienia 2x3 TB + wystąpienia 1x2 TB<br />lub wystąpienia 2x2,5 TB + wystąpienia 1x3 TB<br />lub wystąpienie 1x8 TB |


Istnieją również inne odmiany. 

## <a name="encryption-of-data-at-rest"></a>Szyfrowanie danych w stanie spoczynku
Magazyn używany dla wystąpienia dużych HANA używa przezroczystego szyfrowania danych przechowywanych na dyskach od końca roku 2018. We wcześniejszych wdrożeniach można wybrać, aby uzyskać woluminy zaszyfrowane. Jeśli zdecydujesz się na tę opcję, możesz poprosić o zaszyfrowanie woluminów online. Przejście z woluminów niezaszyfrowanych do zaszyfrowanych jest przezroczyste i nie wymaga przestojów. 

W przypadku jednostek SKU klasy typu I wolumin, na który jest przechowywana jednostka LUN rozruchu, jest szyfrowany. W wersji 3 hana dużych wystąpień sygnatury, przy użyciu typu II klasy jednostek SKU HANA wystąpienie duże, należy zaszyfrować jednostki LUN rozruchu za pomocą metod systemu operacyjnego. W wersji 4 HANA duże wystąpienia sygnatury, przy użyciu jednostek typu II wolumin LUN rozruchu jest przechowywany i jest szyfrowany w spoczynku domyślnie, jak również. 

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>Wymagane ustawienia dla większych wystąpień HANA w dużych wystąpieniach HANA
Magazyn używany w dużych wystąpieniach HANA ma ograniczenie rozmiaru pliku. [Ograniczenie rozmiaru wynosi 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) na plik. W przeciwieństwie do ograniczeń rozmiaru pliku w systemach plików EXT3 HANA nie jest w sposób niejawny świadomy ograniczenia magazynu wymuszanego przez magazyn dużych wystąpień HANA. W rezultacie HANA nie utworzy automatycznie nowego pliku danych po osiągnięciu limitu rozmiaru pliku wynoszącego 16 TB. Jak HANA próbuje zwiększyć plik powyżej 16 TB, HANA będzie zgłaszać błędy i serwer indeksu upaść na końcu.

> [!IMPORTANT]
> Aby zapobiec próbie hana próbuje rozwijać pliki danych poza limit rozmiaru pliku 16 TB hana dużych wystąpień magazynu, należy ustawić następujące parametry w pliku konfiguracyjnym global.ini HANA
> 
> - datavolume_striping=prawda
> - datavolume_striping_size_gb = 15000
> - Zobacz też [#2400005](https://launchpad.support.sap.com/#/notes/2400005) notatki SAP
> - Należy pamiętać o [#2631285](https://launchpad.support.sap.com/#/notes/2631285) notatki SAP




**Następne kroki**
- Odwoływanie się [do obsługiwanych scenariuszy dla dużych wystąpień HANA](hana-supported-scenario.md)