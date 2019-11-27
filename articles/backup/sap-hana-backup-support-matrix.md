---
title: Tabela obsługi kopii zapasowych platformy SAP HANA
description: W tym artykule omówiono obsługiwane scenariusze i ograniczenia dotyczące tworzenia kopii zapasowych SAP HANA baz danych na maszynach wirtualnych platformy Azure przy użyciu usługi Azure Backup.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 0d847d2131a9a9c21fde14cae40a184de4195223
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74288232"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Macierz obsługi dla tworzenia kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure

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
| **Georegiony**                   | Australia Południowa Południowo-Wschodnia, Australia Południowo-Wschodnia, Południowe stany USA, Kanada Wschodnia, Południowe stany USA Azja Wschodnia, Azja Wschodnia Wschodnie stany USA, wschód US 2, zachodnio-środkowe Stany Zjednoczone, Europa Zachodnia, zachodnie stany USA 2, Północno-środkowe stany USA, środkowe stany USA, Południowo-środkowe stany USA, Indie Południowo-Wschodnia, Japonia Korea środkowa, Korea Południowa Europa Północna, Europa Zachodnia Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo | Australia Środkowa, Australia Środkowa 2 Chiny Wschodnie, Chiny Północne, Chiny 2, Chiny Północne 2 zachodnie Indie Francja środkowa, Francja Południowa Niemcy Północne, Niemcy Środkowo-Zachodnie Szwajcaria Północna, Szwajcaria Zachodnia Północna Republika Południowej Afryki, Zachodnia Republika Południowej Afryki, Zjednoczone Emiraty Arabskie, Zjednoczone Emiraty Arabskie Central regiony l Azure Government |
| **Wersje systemu operacyjnego**            | SLES 12 z dodatkiem SP2, SP3 lub SP4           | SLES 15, RHEL                                                |
| **Wersje platformy HANA**          | SDC na platformie HANA 1. x, MDC w systemie HANA 2. x < = SPS04 Rev 44           | -                                                            |
| **Wdrożenia HANA**       | SAP HANA pojedynczej maszynie wirtualnej platformy Azure — skalowanie w górę               | Skalowanie w poziomie                                                    |
| **Wystąpienia HANA**         | Pojedyncze wystąpienie SAP HANA na pojedynczej maszynie wirtualnej platformy Azure — skalowanie w górę | Wiele wystąpień SAP HANA na jednej maszynie wirtualnej                  |
| **Typy baz danych HANA**    | Kontener pojedyncza baza danych (SDC) na 1. x, kontener wielobaza danych (MDC) w 2. x | MDC w HANA 1. x                                              |
| **Rozmiar bazy danych HANA**     | 2 TB rozmiaru pełnej kopii zapasowej po kompresji (Seria M serii 2 TB, 4 TB pamięci RAM) |                                                              |
| **Typy kopii zapasowych**           | Pełne, różnicowe i dzienniki kopii zapasowych                           | Przyrostowe, migawki                                       |
| **Przywróć typy**          | Zapoznaj się z SAP HANA Uwaga [1642148](https://launchpad.support.sap.com/#/notes/1642148) , aby dowiedzieć się więcej o obsługiwanych typach przywracania |                                                              |
| **Limity kopii zapasowych**          | Do 2 TB rozmiaru pełnej kopii zapasowej na wystąpienie SAP HANA  |                                                              |
| **Konfiguracje specjalne** |                                                              | SAP HANA + warstwa dynamiczna <br>  Klonowanie za poorednictwem LaMa            |

------

> [!NOTE]
> Operacje tworzenia kopii zapasowych i przywracania z SAP HANA natywnych klientów (SAP HANA Studio/Panel sterowania/DBA) nie są obecnie obsługiwane.



## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [tworzyć kopie zapasowe baz danych SAP HANA na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Dowiedz się, jak [przywrócić bazy danych SAP HANA uruchomione na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Dowiedz się [, jak zarządzać bazami danych SAP HANA, których kopia zapasowa została utworzona przy użyciu Azure Backup](sap-hana-db-manage.md)
* Dowiedz się, jak [rozwiązywać typowe problemy podczas tworzenia kopii zapasowych baz danych SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
