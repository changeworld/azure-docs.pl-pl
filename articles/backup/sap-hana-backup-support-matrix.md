---
title: Tabela obsługi kopii zapasowych platformy SAP HANA
description: W tym artykule dowiesz się o obsługiwanych scenariuszach i ograniczeniach podczas korzystania z kopii zapasowej platformy Azure do tworzenia kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 8496dc4996cac68535bfe9be30e4b5f72e2d5721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252443"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Macierz obsługi dla kopii zapasowych baz danych platformy SAP HANA na maszynach wirtualnych platformy Azure

Usługa Azure Backup obsługuje tworzenie kopii zapasowych baz danych SAP HANA na platformie Azure. W tym artykule podsumowano scenariusze obsługiwane i ograniczenia obecne podczas korzystania z usługi Azure Backup do tworzenia kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure.

> [!NOTE]
> Częstotliwość tworzenia kopii zapasowej dziennika można teraz ustawić na co najmniej 15 minut. Kopie zapasowe dziennika zaczynają przepływać dopiero po pomyślnym zakończeniu pełnej kopii zapasowej bazy danych.

## <a name="scenario-support"></a>Obsługa scenariuszy

| **Scenariusz**               | **Obsługiwane konfiguracje**                                | **Nieobsługiwały konfiguracje**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologia**               | SAP HANA uruchomiony tylko na maszynach wirtualnych z systemem Azure Linux                    | Duże wystąpienia HANA (HLI)                                   |
| **Geos**                   | **Ga:**<br> Ameryki – Środkowe stany USA, **Wschodnie** stany USA 2, Wschodnie stany USA, Północno-środkowe stany USA, Południowo-środkowe stany USA, Zachodnie stany USA 2, Zachodnie środkowe stany USA, Zachodnie stany USA, Kanada Środkowa, Kanada Wschodnia, Brazylia Południowa <br> **Azja i Pacyfik** – Australia Central, Australia Central 2, Australia Wschodnia, Australia Południowo-Wschodnia, Japonia Wschodnia, Japonia Zachodnia, Korea Środkowa, Korea Południowa, Azja Wschodnia, Azja Południowo-Wschodnia, Indie Środkowe, Indie Zachodnie, Chiny Wschodnie, Chiny Północne2, Chiny Północne 2 <br> **Europa** – Europa Zachodnia, Europa Północna, Francja Środkowa, Wielka Brytania Południowa, Wielka Brytania Zachodnia, Niemcy Północne, Niemcy Środkowo-Zachodnie, Szwajcaria Północna, Szwajcaria Zachodnia, Szwajcaria Środkowa Północ <br> **Afryka / ME** - Republika Południowej Afryki Północ, Republika Południowej Afryki Zachód, Zjednoczone Emiraty Zjednoczone Ameryki Północnej, Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone  <BR>  **Regiony Azure Government** | Francja Południowa, Niemcy Środkowe, Niemcy północno-wschodnie, US Gov IOWA |
| **Wersje systemu operacyjnego**            | SLES 12 z SP2, SP3 lub SP4; SLES 15 z SP1                              | RHEL                                                |
| **Wersje HANA**          | SDC na HANA 1.x, MDC na HANA 2.x <= SPS04 Rev 46       | -                                                            |
| **Wdrożenia HANA**       | SAP HANA na jednej maszynie wirtualnej platformy Azure — skalowanie tylko w górę. <br><br> W przypadku wdrożeń o wysokiej dostępności oba węzły na dwóch różnych komputerach są traktowane jako pojedyncze węzły z oddzielnymi łańcuchami danych.               | Skalowanie w poziomie <br><br> W wdrożeniach o wysokiej dostępności kopia zapasowa nie działa automatycznie w węźle pomocniczym. Konfigurowanie kopii zapasowej należy wykonać oddzielnie dla każdego węzła.                                           |
| **Wystąpienia HANA**         | Pojedyncze wystąpienie SAP HANA na jednej maszynie wirtualnej platformy Azure — skalowanie tylko w górę | Wiele wystąpień SAP HANA na jednej maszynie wirtualnej                  |
| **Typy baz danych HANA**    | Kontener pojedynczej bazy danych (SDC) ON 1.x, kontener wielodeksowy (MDC) na 2.x | MDC w HANA 1.x                                              |
| **Rozmiar bazy danych HANA**     | Pełny rozmiar kopii zapasowej o pojemności 2 TB zgłoszony przez HANA)                   |                                                              |
| **Typy kopii zapasowych**           | Pełne, różnicowe i dziennikowe kopie zapasowe                          | Przyrostowe, migawki                                       |
| **Typy przywracania**          | Zapoznaj się z notatką SAP HANA [1642148,](https://launchpad.support.sap.com/#/notes/1642148) aby dowiedzieć się więcej o obsługiwanych typach przywracania |                                                              |
| **Limity kopii zapasowych**          | Do 2 TB pełnego rozmiaru kopii zapasowej na wystąpienie SAP HANA         |                                                              |
| **Konfiguracje specjalne** |                                                              | SAP HANA + dynamiczne warstwowanie <br>  Klonowanie przez LaMa        |

------

> [!NOTE]
> Operacje tworzenia kopii zapasowych i przywracania z klientów natywnych SAP HANA (SAP HANA Studio / Cockpit / DBA Cockpit) nie są obecnie obsługiwane.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [wykonać kopię zapasową baz danych SAP HANA uruchomionych na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Dowiedz się, jak [przywrócić bazy danych SAP HANA uruchomione na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Dowiedz się, jak [zarządzać bazami danych SAP HANA, których kopia zapasowa jest archiwizna przy użyciu usługi Azure Backup](sap-hana-db-manage.md)
* Dowiedz się, jak [rozwiązywać typowe problemy podczas tworzenia kopii zapasowej baz danych SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
