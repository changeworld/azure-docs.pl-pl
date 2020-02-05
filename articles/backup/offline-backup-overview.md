---
title: Przegląd kopii zapasowej offline
description: Poznaj różne składniki kopii zapasowej offline, w tym oparte na Azure Data Box kopia zapasowa offline i kopia zapasowa offline usługi Azure Import/Export.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 35bc15488624f3648bdc765a36d10607b4ca2de9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027019"
---
# <a name="overview-of-offline-backup"></a>Przegląd kopii zapasowej offline

Ten artykuł zawiera omówienie kopii zapasowej offline.

Początkowe pełne kopie zapasowe na platformie Azure zazwyczaj przesyłają duże ilości danych w trybie online i wymagają większej przepustowości sieci w porównaniu z kolejnymi kopiami zapasowymi, które przesyłają tylko przyrostowe zmiany. Biura zdalne lub centra danych w niektórych lokalizacje geograficzne nie zawsze mają wystarczającą przepustowość sieci. W związku z tym te początkowe kopie zapasowe pobierają kilka dni, a w tym czasie stale używają tej samej sieci, która została zainicjowana dla aplikacji działających w lokalnym centrum danych.

Azure Backup obsługuje "kopia zapasowa offline", która umożliwia Transferowanie danych początkowej kopii zapasowej w trybie offline bez użycia przepustowości sieci. Zapewnia mechanizm kopiowania danych kopii zapasowej na magazyn fizyczny — urządzenia, które następnie są dostarczane do pobliskiego centrum danych platformy Azure i przekazywane do magazynu Recovery Services. Ten proces zapewnia niezawodny transfer danych kopii zapasowej bez użycia przepustowości sieci.

## <a name="offline-backup-options"></a>Opcje kopii zapasowej offline

Offline — kopia zapasowa jest oferowana w dwóch trybach na podstawie własności urządzeń magazynujących.

1. Oparta na Azure Data Box kopia zapasowa offline (wersja zapoznawcza)
2. Kopia zapasowa offline usługi Azure Import/Export

## <a name="azure-data-box-based-offline-backup-preview"></a>Oparta na Azure Data Box kopia zapasowa offline (wersja zapoznawcza)

Ten tryb jest obecnie obsługiwany przez agenta MARS w wersji zapoznawczej. Ta opcja korzysta z [usługi Azure Data Box](https://azure.microsoft.com/services/databox/) do dostarczania urządzeń do transferów firmy Microsoft, bezpiecznych i odpornych na manipulacje z użyciem łączników USB do centrum danych lub zdalnego pakietu, a dane kopii zapasowej są bezpośrednio zapisywane na tych urządzeniach. **Ta opcja umożliwia zaoszczędzenie nakładu pracy wymaganego do pozyskiwania własnych dysków i łączników zgodnych z platformą Azure lub aprowizacji magazynu tymczasowego jako lokalizacji tymczasowej.** Ponadto firma Microsoft obsługuje kompleksową logistykę transferu, którą można śledzić za pomocą Azure Portal. Poniżej przedstawiono architekturę opisującą przenoszenie danych kopii zapasowej z tą opcją.

![Architektura urządzenie Data Box Azure Backup](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Oto podsumowanie architektury:

1. Azure Backup bezpośrednio kopiuje dane kopii zapasowej do tych wstępnie skonfigurowanych urządzeń.
2. Następnie można wysłać te urządzenia z powrotem do centrum danych platformy Azure.
3. Usługa Azure Data Box kopiuje dane na konto magazynu należące do klienta.
4. Azure Backup automatycznie kopiuje dane kopii zapasowej z konta magazynu do wyznaczonego magazynu Recovery Services i planowane są przyrostowe kopie zapasowe w trybie online.

Aby użyć Azure Data Box kopii zapasowej w trybie offline, zapoznaj się z [tym artykułem](offline-backup-azure-data-box.md).

## <a name="azure-importexport-based-offline-backup"></a>Kopia zapasowa offline usługi Azure Import/Export

Ta opcja jest obsługiwana przez Azure Backup Server (serwera usługi MAB)/DPM-A/MARS Agent. Korzysta ona z [usługi Azure Import/Export](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) , która umożliwia transfer danych początkowej kopii zapasowej na platformę Azure przy użyciu własnych dysków i łączników zgodnych z platformą Azure. Takie podejście wymaga aprowizacji tymczasowego magazynu znanego jako **Lokalizacja tymczasowa** i użycie wstępnie utworzonych narzędzi do formatowania i kopiowania danych kopii zapasowej na dyski należące do klienta. Poniżej przedstawiono architekturę opisującą przenoszenie danych kopii zapasowej za pomocą tej opcji:

![Azure Backup architekturę importu/eksportu](./media/offline-backup-overview/azure-backup-import-export.png)

Oto podsumowanie architektury:

1. Zamiast wysyłać dane kopii zapasowej za pośrednictwem sieci, Azure Backup zapisuje dane kopii zapasowej w lokalizacji tymczasowej.
2. Dane w lokalizacji tymczasowej są zapisywane na jednym lub większej liczbie dysków SATA przy użyciu niestandardowego narzędzia.
3. W ramach pracy przygotowawczej narzędzie tworzy zadanie importu platformy Azure. Dyski SATA są dostarczane do najbliższego centrum danych platformy Azure i odwołują się do zadania importowania w celu połączenia działań.
4. W centrum danych Azure dane na dyskach są kopiowane do konta usługi Azure Storage.
5. Azure Backup kopiuje dane kopii zapasowej z konta magazynu do magazynu Recovery Services, a przyrostowe kopie zapasowe są zaplanowane.

Aby użyć usługi Azure Import/Export z kopii zapasowej w trybie offline za pomocą agenta MARS, zapoznaj się z [tym artykułem](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

Aby użyć tego samego programu wraz z serwera usługi MAB/DPM-A, zapoznaj się z [tym artykułem](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).

## <a name="offline-backup-support-summary"></a>Podsumowanie obsługi kopii zapasowych w trybie offline

W poniższej tabeli porównano dwie dostępne opcje, aby można było wybrać odpowiedni wybór w oparciu o twój scenariusz.

| **Zagadnienie**                                            | **Kopia zapasowa w trybie offline oparta na Azure Data Box**                     | **Kopia zapasowa offline usługi Azure Import/Export**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure Backup modele wdrażania                              | Agent MARS (wersja zapoznawcza)                                              | Agent MARS, Azure Backup Server (serwera usługi MAB), DPM-A                                           |
| Maksymalna ilość danych kopii zapasowej na serwer (MARS) lub na grupę ochrony (serwera usługi MAB, DPM-A) | [Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) – 7,2 TB <br> [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) – 80 TB       | 80 TB (do 10 dysków o pojemności 8 TB)                          |
| Zabezpieczenia (dane, usługa & urządzeń)                           | Szyfrowanie [danych](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) -AES-256 bitowego <br> Przypadek niezależny od [urządzenia](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) , własnościowy interfejs oparty na poświadczeniach do kopiowania danych <br> [Usługa](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) chroniona przez funkcje zabezpieczeń platformy Azure | Dane — szyfrowanie funkcją BitLocker                                 |
| Tymczasowa lokalizacja tymczasowa aprowizacji                     | Niewymagane                                                | Większy niż lub równy szacowanemu rozmiarowi danych kopii zapasowej        |
| Obsługiwane regiony                                           | [Azure Data Box Disk regiony](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Azure Data Box regiony](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Importowanie/Eksportowanie regionów platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| Wysyłka w różnych krajach *                                     | **Nieobsługiwane**  <br>    *Adres źródłowy & docelowego centrum danych platformy Azure musi znajdować się w tym samym kraju* | Obsługiwane                                                    |
| Przenoszenie logistyki (dostarczanie, transport, pobieranie)           | W pełni zarządzane firmy Microsoft                                     | Zarządzane przez klienta                                            |
| Cennik                                                      | [Cennik Azure Data Box](https://azure.microsoft.com/pricing/details/databox/) <br> [Cennik Azure Data Box Disk](https://azure.microsoft.com/pricing/details/databox/disk/) | [Cennik usługi Azure Import/Export](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* *Jeśli kraj nie ma centrum danych platformy Azure, musisz dostarczyć dyski do centrum danych platformy Azure w innym kraju.*

## <a name="next-steps"></a>Następne kroki

* [Tworzenie kopii zapasowych w trybie offline na podstawie Azure Data Box dla agenta MARS](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [Kopia zapasowa offline usługi Azure Import/Export oparta na usłudze MARS Agent](backup-azure-backup-import-export.md)  
* [Usługa Azure Import/Export oparta na usłudze Kopia zapasowa offline dla serwera usługi MAB/DPM-A](backup-azure-backup-server-import-export-.md)
