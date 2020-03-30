---
title: Usługa importu/eksportu zajmuje dużo czasu
description: Usługa importu/eksportowania bazy danych SQL sql zajmuje dużo czasu, aby zaimportować lub wyeksportować bazę danych
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
manager: dcscontentpm
ms.openlocfilehash: cf2d9b218fe63414af2446b8562d3ba187b2d395
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535769"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Usługa importu/eksportowania bazy danych SQL sql zajmuje dużo czasu, aby zaimportować lub wyeksportować bazę danych

Korzystając z usługi import/eksport bazy danych SQL azure, proces może trwać dłużej niż oczekiwano. W tym artykule opisano potencjalne przyczyny tego opóźnienia i alternatywnych metod obejścia.

## <a name="azure-sql-database-importexport-service"></a>Usługa importowania/eksportowania bazy danych SQL azure

Usługa importowania/eksportowania bazy danych SQL azure to usługa sieci web oparta na rest, która działa w każdym centrum danych platformy Azure. Ta usługa jest wywoływana, gdy używasz [opcji Importuj bazę danych](sql-database-import.md#using-azure-portal) lub [Eksportuj,](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) aby przenieść bazę danych SQL w witrynie Azure portal. Usługa zapewnia bezpłatne usługi kolejkowania żądań i usługi obliczeniowe do wykonywania importu i eksportu między bazą danych SQL platformy Azure a magazynem obiektów Blob platformy Azure.

Operacje importowania i eksportowania nie reprezentują tradycyjnej fizycznej kopii zapasowej bazy danych, ale zamiast tego logiczną kopię zapasową bazy danych, która używa specjalnego formatu BACPAC. Format BACPAC pozwala uniknąć konieczności używania formatu fizycznego, który może się różnić między wersjami programu Microsoft SQL Server i usługi Azure SQL Database. W związku z tym można go użyć do bezpiecznego przywracania bazy danych do bazy danych programu SQL Server i do bazy danych SQL.

## <a name="what-causes-delays-in-the-process"></a>Co powoduje opóźnienia w tym procesie?

Usługa importu/eksportu bazy danych SQL azure zapewnia ograniczoną liczbę maszyn wirtualnych obliczeniowych (VM) na region do przetwarzania operacji importowania i eksportowania. Maszyny wirtualne obliczeniowe są hostowane na region, aby upewnić się, że importowanie lub eksport pozwala uniknąć opóźnień przepustowości między regionami i opłat. Jeśli zbyt wiele żądań są dokonywane w tym samym czasie w tym samym regionie, mogą wystąpić znaczne opóźnienia w przetwarzaniu operacji. Czas wymagany do wykonania żądań może się różnić od kilku sekund do wielu godzin.

> [!NOTE]
> Jeśli żądanie nie zostanie przetworzone w ciągu czterech dni, usługa automatycznie anuluje żądanie.

## <a name="recommended-solutions"></a>Zalecane rozwiązania

Jeśli eksport bazy danych są używane tylko do odzyskiwania po przypadkowym usunięciu danych, wszystkie wersje usługi Azure SQL Database zapewniają możliwość przywracania samoobsługowego z kopii zapasowych generowanych przez system. Ale jeśli potrzebujesz tych eksportu z innych powodów, a jeśli potrzebujesz konsekwentnie szybsze lub bardziej przewidywalne wyniki importu /eksportu, należy wziąć pod uwagę następujące opcje:

* [Eksportowanie do pliku BACPAC za pomocą narzędzia SQLPackage](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility).
* [Eksportowanie do pliku BACPAC przy użyciu programu SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms).
* Uruchom import lub eksport BACPAC bezpośrednio w kodzie przy użyciu interfejsu API programu Microsoft SQL Server Data-Tier Application Framework (DacFx). Aby uzyskać dodatkowe informacje, zobacz:
  * [Eksportowanie aplikacji warstwy danych](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Obszar nazw microsoft.sqlserver.dac](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [Pobierz DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-an-azure-sql-database"></a>Należy wziąć pod uwagę podczas eksportowania lub importowania bazy danych SQL platformy Azure

* Wszystkie metody omówione w tym artykule używają przydziału jednostki DTU (Database Transaction Unit), co powoduje ograniczanie przepustowości przez usługę Azure SQL Database. Statystyki [DTU bazy danych można wyświetlić w witrynie Azure portal](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#sql-database-resource-monitoring). Jeśli baza danych osiągnęła swoje limity zasobów, [uaktualnić warstwę usług,](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) aby dodać więcej zasobów.
* W idealnym przypadku należy uruchomić aplikacje klienckie (takie jak narzędzie sqlpackage lub niestandardowa aplikacja DAC) z maszyny Wirtualnej w tym samym regionie co baza danych SQL. W przeciwnym razie mogą wystąpić problemy z wydajnością związane z opóźnieniem sieci.
* Eksportowanie dużych tabel bez indeksów klastrowanych może być bardzo powolne, a nawet powodować awarię. To zachowanie występuje, ponieważ tabela nie może być podzielona i eksportowane równolegle. Zamiast tego musi być eksportowany w jednej transakcji, a to powoduje powolną wydajność i potencjalne niepowodzenie podczas eksportowania, szczególnie dla dużych tabel.


## <a name="related-documents"></a>Powiązane dokumenty

[Zagadnienia dotyczące eksportowania bazy danych SQL platformy Azure](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
