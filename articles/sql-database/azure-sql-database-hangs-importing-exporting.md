---
title: Importowanie lub eksportowanie bazy danych za pomocą usługi Azure SQL Database Import/Export | Microsoft Docs
description: Importowanie lub eksportowanie bazy danych za pomocą usługi Azure SQL Database Import/Export
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: ed5f3d1cd505270eb91c9cfbd6fb5c38b908f33d
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974463"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Importowanie lub eksportowanie bazy danych za pomocą usługi Azure SQL Database Import/Export

W przypadku korzystania z usługi Azure SQL Database Import/Export można zauważyć, że czasami proces może zająć dużo czasu. Ten artykuł zawiera dodatkowe informacje na temat potencjalnych przyczyn opóźnień Thee i alternatywnych metod, których można użyć do obejścia tych problemów.

## <a name="azure-sql-database-importexport-service"></a>Azure SQL Database usługi Import/Export

Usługa Azure SQL Database Import/Export jest usługą sieci Web opartą na interfejsie REST, która działa w każdym Microsoft Azure centrum danych. Jest to usługa, która jest wywoływana w przypadku używania opcji [Importuj bazę danych](https://docs.microsoft.com/azure/sql-database/sql-database-import#import-from-a-bacpac-file-in-the-azure-portal) lub [eksport](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) w celu przeniesienia bazy danych SQL do Microsoft Azure Portal. Usługa udostępnia bezpłatną usługę kolejkowania żądań i bezpłatną usługę obliczeniową do wykonywania operacji importowania i eksportowania z Microsoft Azure bazy danych SQL w celu Microsoft Azure magazynu obiektów binarnych (BLOB).

Operacje importowania i eksportowania nie stanowią tradycyjnej fizycznej kopii zapasowej bazy danych, ale logicznej kopii zapasowej bazy danych, która używa specjalnego formatu BACPAC. Ten logiczny format BACPAC pozwala uniknąć konieczności używania fizycznego formatu, który może się różnić między wersjami SQL Server i SQL Database. Z tego względu można użyć go do bezpiecznego przywrócenia bazy danych do bazy danych SQL i bazy danych SQL Server.

## <a name="what-causes-the-process-to-take-a-long-time"></a>Co sprawia, że proces zajmuje dużo czasu

Usługa Import/Export Azure SQL Database udostępnia ograniczoną liczbę maszyn wirtualnych obliczeniowych (VM) na region do przetwarzania operacji importu i eksportu. Maszyna wirtualna obliczeniowa jest hostowana na region, aby upewnić się, że import lub eksport nie pozwala na opóźnienia przepustowości i opłaty między regionami. Tak więc, jeśli zbyt wiele żądań jest wykonywanych w tym samym czasie w tym samym regionie, znaczne opóźnienia występują podczas przetwarzania operacji. Czas wymagany do ukończenia żądań może się różnić od kilku sekund do wielu godzin.

> [!NOTE]
> Jeśli żądanie nie zostanie przetworzone w ciągu czterech dni, usługa automatycznie anuluje żądanie.

## <a name="recommended-solutions"></a>Zalecane rozwiązania

Jeśli eksporty bazy danych są używane tylko do odzyskiwania po przypadkowym usunięciu danych, wszystkie wersje usługi Azure SQL Server Database zapewniają możliwość przywracania samoobsługowego z kopii zapasowych generowanych przez system. Ale jeśli potrzebne są te eksporty z innych powodów, a w przypadku konieczności ciągłej szybszej lub bardziej przewidywalnej wydajności operacji importowania lub eksportowania należy wziąć pod uwagę następujące opcje:

* [Eksportowanie do pliku BACPAC przy użyciu narzędzia sqlpackage](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility)
* [Eksportowanie do pliku BACPAC przy użyciu programu SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms)
* Uruchom BACPAC zaimportuj lub wyeksportuj bezpośrednio w kodzie za pomocą interfejsu API programu Microsoft® SQL Server® DacFx. Aby uzyskać więcej informacji, zobacz
  * [Eksportowanie aplikacji warstwy danych](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Przestrzeń nazw Microsoft. SqlServer. DAC](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [Pobierz DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="considerations-when-exporting-or-importing-an-azure-sql-database"></a>Zagadnienia dotyczące eksportowania lub importowania bazy danych Azure SQL Database

* Wszystkie metody omówione w tym artykule korzystają z limitu przydziału jednostek DTU, co spowodowało ograniczenie przez usługę Azure SQLDB. Można [wyświetlić statystyki jednostek DTU dla bazy danych na Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#monitor-database-performance). Jeśli baza danych zwiększa limity zasobów, należy [uaktualnić warstwę usług](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) , aby dodać więcej zasobów.
* Aplikacje klienckie (takie jak narzędzie sqlpackage lub aplikacja niestandardowa DAC) powinny być najlepiej uruchamiane z maszyny wirtualnej w tym samym regionie, w którym znajduje się baza danych SQL, lub w przeciwnym razie mogą wystąpić problemy z wydajnością z powodu opóźnienia sieci.
* Eksportowanie dużych tabel bez indeksów klastrowanych może być bardzo wolne lub nawet powodować błędy. Wynika to z faktu, że nie można podzielić i wyeksportować tabeli w ramach jednej transakcji, która powoduje spowolnienie i potencjalne błędy podczas eksportowania, szczególnie w przypadku dużych tabel. 


## <a name="related-documents"></a>Powiązane dokumenty

[Zagadnienia dotyczące eksportowania bazy danych Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
