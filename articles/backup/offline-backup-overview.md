---
title: Omówienie tworzenia kopii zapasowych w trybie offline
description: Dowiedz się więcej o składnikach kopii zapasowej w trybie offline. Obejmują one tworzenie kopii zapasowych w trybie offline na podstawie usługi Azure Data Box i tworzenie kopii zapasowych w trybie offline na podstawie usługi Azure Import/Export.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 4dae68c46e0072a726bba13139e405b44be0f008
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196172"
---
# <a name="overview-of-offline-backup"></a>Omówienie tworzenia kopii zapasowych w trybie offline

Ten artykuł zawiera omówienie kopii zapasowej w trybie offline.

Początkowe pełne kopie zapasowe na platformę Azure zazwyczaj przesyłają duże ilości danych w trybie online i wymagają większej przepustowości sieci w porównaniu z kolejnymi kopiami zapasowymi, które przenoszą tylko przyrostowe zmiany. Zdalne biura lub centra danych w niektórych regionach geograficznych nie zawsze mają wystarczającą przepustowość sieci. Z tego powodu te początkowe kopie zapasowe trwać kilka dni. W tym czasie kopie zapasowe stale używają tej samej sieci, która została zainicjowana dla aplikacji uruchomionych w lokalnym centrum danych.

Usługa Azure Backup obsługuje tworzenie kopii zapasowych w trybie offline, które przesyła początkowe dane kopii zapasowej w trybie offline, bez użycia przepustowości sieci. Zapewnia mechanizm kopiowania kopii zapasowych danych na fizyczne urządzenia pamięci masowej. Urządzenia są następnie wysyłane do pobliskiego centrum danych platformy Azure i przekazywane do magazynu usług odzyskiwania. Proces ten zapewnia niezawodny transfer kopii zapasowych danych bez użycia przepustowości sieci.

## <a name="offline-backup-options"></a>Opcje tworzenia kopii zapasowych w trybie offline

Kopia zapasowa w trybie offline jest oferowana w dwóch trybach opartych na własności urządzeń magazynujących:

- Kopia zapasowa w trybie offline oparta na usłudze Azure Data Box (wersja zapoznawcza)
- Kopia zapasowa w trybie offline oparta na usłudze importu/eksportowania platformy Azure

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Kopia zapasowa w trybie offline oparta na usłudze Azure Data Box (wersja zapoznawcza)

Ten tryb jest obecnie obsługiwany przez agenta usług odzyskiwania platformy Microsoft Azure (MARS) w wersji zapoznawczej. Ta opcja korzysta z [usługi Azure Data Box](https://azure.microsoft.com/services/databox/) do przesyłania zastrzeżonych, bezpiecznych i odpornych na manipulacje urządzeń transferowych firmy Microsoft ze złączami USB do centrum danych lub zdalnego biura. Dane kopii zapasowej są zapisywane bezpośrednio na tych urządzeniach. Ta opcja pozwala zaoszczędzić nakład pracy wymagany do zakupu własnych dysków i łączników zgodnych z platformą Azure lub do aprowizowania magazynu tymczasowego jako lokalizacji tymczasowej. Firma Microsoft obsługuje również logistykę transferu end-to-end, którą można śledzić za pośrednictwem witryny Azure portal. 

W tym miejscu przedstawiono architekturę opisującą przepływ danych kopii zapasowej za pomocą tej opcji.

![Architektura skrzynki danych kopii zapasowej platformy Azure](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Oto podsumowanie architektury:

1. Usługa Azure Backup bezpośrednio kopiuje dane kopii zapasowej do tych wstępnie skonfigurowanych urządzeń.
2. Następnie można wysłać te urządzenia z powrotem do centrum danych platformy Azure.
3. Usługa Azure Data Box kopiuje dane na konto magazynu należące do klienta.
4. Usługa Azure Backup automatycznie kopiuje dane kopii zapasowej z konta magazynu do wyznaczonego magazynu usług odzyskiwania. Planowane są przyrostowe kopie zapasowe online.

Aby użyć kopii zapasowej w trybie offline na podstawie usługi Azure Data Box, zobacz [Tworzenie kopii zapasowej w trybie offline przy użyciu usługi Azure Data Box](offline-backup-azure-data-box.md).

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Kopia zapasowa w trybie offline oparta na usłudze importu/eksportowania platformy Azure

Ta opcja jest obsługiwana przez program Microsoft Azure Backup Server (MABS), Program DPM-A (System Center Data Protection Manager) i agenta MARS. Korzysta z [usługi Azure Import/Export .](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) Wstępne dane kopii zapasowej można przenieść na platformę Azure przy użyciu własnych dysków i łączników zgodnych z platformą Azure. Takie podejście wymaga, aby aprowizować magazyn tymczasowy znany jako lokalizacji tymczasowej i używać wstępnie utworzonych narzędzi do formatowania i kopiowania danych kopii zapasowej na dyski należące do klienta. 

W tym miejscu przedstawiono architekturę opisującą przepływ danych kopii zapasowej za pomocą tej opcji.

![Architektura usługi importu/eksportowania kopii zapasowych platformy Azure](./media/offline-backup-overview/azure-backup-import-export.png)

Oto podsumowanie architektury:

1. Zamiast wysyłać dane kopii zapasowej za pośrednictwem sieci, usługa Azure Backup zapisuje dane kopii zapasowej w lokalizacji przejściowej.
2. Dane w lokalizacji przemieszczania są zapisywane na co najmniej jednym dysku SATA przy użyciu narzędzia niestandardowego.
3. W ramach prac przygotowawczych narzędzie tworzy zadanie importowania platformy Azure. Dyski SATA są wysyłane do najbliższego centrum danych platformy Azure i odwołać się do zadania importu, aby połączyć działania.
4. W centrum danych platformy Azure dane na dyskach są kopiowane do konta magazynu platformy Azure.
5. Usługa Azure Backup kopiuje dane kopii zapasowej z konta magazynu do magazynu usług odzyskiwania. Planowane są przyrostowe kopie zapasowe.

Aby użyć kopii zapasowej w trybie offline na podstawie usługi Azure Import/Export service z agentem MARS, zobacz [Przepływ pracy tworzenia kopii zapasowych w trybie offline w usłudze Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

Aby używać tego samego razem z mabs lub programem DPM-A, zobacz [Przepływ pracy tworzenia kopii zapasowych w trybie offline dla programu DPM i serwera kopii zapasowych platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).

## <a name="offline-backup-support-summary"></a>Podsumowanie obsługi kopii zapasowych w trybie offline

W poniższej tabeli porównano dwie dostępne opcje, dzięki czemu można dokonywać odpowiednich wyborów na podstawie scenariusza.

| **Zagadnienie**                                            | **Tworzenie kopii zapasowej w trybie offline na podstawie usługi Azure Data Box**                     | **Kopia zapasowa w trybie offline oparta na usłudze importu/eksportowania platformy Azure**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Modele wdrażania kopii zapasowej platformy Azure                              | Mars Agent (wersja zapoznawcza)                                              | Agent MARS, MABS, DPM-A                                           |
| Maksymalna ilość danych kopii zapasowej na serwer (MARS) lub na grupę ochrony (MABS, DPM-A) | [Dysk usługi Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview) — 7,2 TB <br> [Skrzynka danych platformy Azure](https://docs.microsoft.com/azure/databox/data-box-overview) — 80 TB       | 80 TB (do 10 dysków po 8 TB każdy)                          |
| Zabezpieczenia (dane, urządzenie i usługa)                           | [Dane](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) — 256-bitowe zaszyfrowane AES <br> [Urządzenie](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) — wytrzymała obudowa, zastrzeżony interfejs oparty na poświadczeniach do kopiowania danych <br> [Usługa](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) — chroniona przez funkcje zabezpieczeń platformy Azure | Dane — szyfrowane funkcji BitLocker                                 |
| Tymczasowe inicjowanie obsługi administracyjnej lokalizacji tymczasowej                     | Niewymagane                                                | Większy lub równy szacowanemu rozmiarowi danych kopii zapasowej        |
| Obsługiwane regiony                                           | [Regiony dysków skrzynki danych platformy Azure](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Regiony skrzynki danych platformy Azure](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Regiony usługi importu/eksportu platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| Wysyłka biegowa                                     | Nieobsługiwane  <br>    Adres źródłowy i docelowe centrum danych platformy Azure musi znajdować się w tym samym kraju* | Obsługiwane                                                    |
| Logistyka transferu (dostawa, transport, odbiór)           | W pełni zarządzane przez firmę Microsoft                                     | Zarządzane przez klienta                                            |
| Cennik                                                      | [Cennik usługi Azure Data Box](https://azure.microsoft.com/pricing/details/databox/) <br> [Cennik dysku usługi Azure Data Box](https://azure.microsoft.com/pricing/details/databox/disk/) | [Ceny usług Azure Import/Eksportowanie usługi](https://azure.microsoft.com/pricing/details/storage-import-export/) |

*Jeśli twój kraj nie ma centrum danych platformy Azure, musisz wysłać dyski do centrum danych platformy Azure w innym kraju.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie kopii zapasowej w trybie online usługi Azure przy użyciu usługi Azure Data Box](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [Przepływ pracy tworzenia kopii zapasowych w trybie offline w kopii zapasowej platformy Azure](backup-azure-backup-import-export.md) 
* [Przepływ pracy tworzenia kopii zapasowych w trybie offline dla programu DPM i serwera kopii zapasowych platformy Azure](backup-azure-backup-server-import-export-.md)
