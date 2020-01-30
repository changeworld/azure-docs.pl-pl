---
title: Tabela obsługi kopii zapasowych platformy SAP HANA
description: W tym artykule omówiono obsługiwane scenariusze i ograniczenia dotyczące tworzenia kopii zapasowych SAP HANA baz danych na maszynach wirtualnych platformy Azure przy użyciu usługi Azure Backup.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 82d844385290ab0dc2953537c1f9a3387dd7b2b2
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842635"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Macierz obsługi dla kopii zapasowych baz danych platformy SAP HANA na maszynach wirtualnych platformy Azure

Azure Backup obsługuje tworzenie kopii zapasowych baz danych SAP HANA na platformie Azure. W tym artykule przedstawiono podsumowanie obsługiwanych scenariuszy i ograniczeń występujących podczas tworzenia kopii zapasowych SAP HANA baz danych na maszynach wirtualnych platformy Azure za pomocą Azure Backup.

## <a name="onboard-to-the-public-preview"></a>Dołączanie do publicznej wersji zapoznawczej

Dołączanie do publicznej wersji zapoznawczej w następujący sposób:

* W portalu Zarejestruj swój identyfikator subskrypcji dla dostawcy usług Recovery Services, wykonując [ten artykuł](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal).
* Dla programu PowerShell uruchom to polecenie cmdlet. Powinna zostać zakończona jako "zarejestrowane".

```PowerShell
Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
```

> [!NOTE]
> Częstotliwość wykonywania kopii zapasowych dziennika może teraz wynosić co najmniej 15 minut. Kopie zapasowe dzienników zaczynają przepływać dopiero po pomyślnym wykonaniu pełnej kopii zapasowej bazy danych.

## <a name="scenario-support"></a>Obsługa scenariuszy

| **Scenariusz**               | **Obsługiwane konfiguracje**                                | **Nieobsługiwane konfiguracje**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologia**               | SAP HANA działa tylko na maszynach wirtualnych z systemem Linux Azure                    | Duże wystąpienia HANA (HLI)                                   |
| **Georegiony**                   | **POWSZECHNE**<br />**Europa** — Europa Zachodnia, Europa Północna, Francja środkowa, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo, Niemcy Północne, Niemcy Środkowo-Zachodnie, Szwajcaria Północna, Szwajcaria Zachodnia<br />**Azja i Pacyfik** — Australia Środkowa, Australia Środkowa 2, Australia Wschodnia, Australia Wschodnia, Japonia Wschodnia, Japonia Zachodnia, Korea środkowa, Korea Południowa<br /><br>**Przeglądania**<br />**Ameryki** — środkowe stany USA, Wschodnie stany USA 2, Wschodnie stany USA, Północno-środkowe stany USA, Południowo-środkowe stany USA, zachodnie stany USA 2, zachodnie stany USA, Europa Zachodnia, Kanada środkowa, Kanada Wschodnia <br />**Azja i Pacyfik** — Azja Wschodnia, Azja Południowo-Wschodnia, Indie Środkowe, Indie Południowe | Chiny Wschodnie, Chiny Północne, Chiny 2, Chiny Północne 2, Indie Zachodnie, środkowe Szwajcaria Północna, Północna Republika Południowej Afryki, Zachodnia Republika Południowej Afryki, Zjednoczone Emiraty Arabskie, środkowe Zjednoczone Emiraty Arabskie, regiony Azure Government, Francja Południowa, Brazylia Południowa |
| **Wersje systemu operacyjnego**            | SLES 12 z dodatkiem SP2, SP3 lub SP4                                | SLES 15, RHEL                                                |
| **Wersje platformy HANA**          | SDC na platformie HANA 1. x, MDC w systemie HANA 2. x < = SPS04 Rev 44            | -                                                            |
| **Wdrożenia HANA**       | SAP HANA pojedynczej maszynie wirtualnej platformy Azure — skalowanie w górę               | Skalowanie w poziomie                                                    |
| **Wystąpienia HANA**         | Pojedyncze wystąpienie SAP HANA na pojedynczej maszynie wirtualnej platformy Azure — skalowanie w górę | Wiele wystąpień SAP HANA na jednej maszynie wirtualnej                  |
| **Typy baz danych HANA**    | Kontener pojedyncza baza danych (SDC) na 1. x, kontener wielobaza danych (MDC) w 2. x | MDC w HANA 1. x                                              |
| **Rozmiar bazy danych HANA**     | 2 TB rozmiaru pełnej kopii zapasowej zgodnie z informacjami raportowanymi przez platformę HANA)                   |                                                              |
| **Typy kopii zapasowych**           | Pełne, różnicowe i dzienniki kopii zapasowych                          | Przyrostowe, migawki                                       |
| **Przywróć typy**          | Zapoznaj się z SAP HANA Uwaga [1642148](https://launchpad.support.sap.com/#/notes/1642148) , aby dowiedzieć się więcej o obsługiwanych typach przywracania |                                                              |
| **Limity kopii zapasowych**          | Do 2 TB rozmiaru pełnej kopii zapasowej na wystąpienie SAP HANA         |                                                              |
| **Konfiguracje specjalne** |                                                              | SAP HANA + warstwa dynamiczna <br>  Klonowanie za poorednictwem LaMa        |

------

> [!NOTE]
> Operacje tworzenia kopii zapasowych i przywracania z SAP HANA natywnych klientów (SAP HANA Studio/Panel sterowania/DBA) nie są obecnie obsługiwane.



## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [tworzyć kopie zapasowe baz danych SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Dowiedz się, jak [przywrócić bazy danych SAP HANA uruchomione na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Dowiedz się [, jak zarządzać bazami danych SAP HANA, których kopia zapasowa została utworzona przy użyciu Azure Backup](sap-hana-db-manage.md)
* Dowiedz się, jak [rozwiązywać typowe problemy podczas tworzenia kopii zapasowych baz danych SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
