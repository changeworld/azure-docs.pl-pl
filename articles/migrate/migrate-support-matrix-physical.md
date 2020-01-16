---
title: Obsługa oceny serwera fizycznego przy użyciu Azure Migrate
description: Dowiedz się więcej o obsłudze oceny serwera fizycznego za pomocą Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 32080605217cde78bd648ca6192f73d1025dea4c
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028766"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Macierz obsługi dla oceny serwera fizycznego 

Za pomocą [usługi Azure Migrate](migrate-overview.md) można oceniać i migrować maszyny do chmury Microsoft Azure. Ten artykuł podsumowuje ustawienia i ograniczenia dotyczące obsługi oceniania i migrowania lokalnych serwerów fizycznych.


## <a name="overview"></a>Przegląd

Aby ocenić maszyny lokalne na potrzeby migracji na platformę Azure z tego artykułu, należy dodać Azure Migrate: Narzędzie do oceny serwera do projektu Azure Migrate. Należy wdrożyć [urządzenie Azure Migrate](migrate-appliance.md). Urządzenie stale odnajduje maszyny lokalne i wysyła dane dotyczące konfiguracji i wydajności na platformę Azure. Po odnajdywaniu maszyn można zebrać odnalezione maszyny do grup i uruchomić ocenę dla grupy

## <a name="limitations"></a>Ograniczenia

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Limity oceny**| Odnajdź i Oceń maksymalnie 35 000 serwerów fizycznych w jednym [projekcie](migrate-support-matrix.md#azure-migrate-projects).
**Limity projektu** | Możesz utworzyć wiele projektów w ramach subskrypcji platformy Azure. Projekt może obejmować maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V i serwery fizyczne oraz limity oceny.
**Discovery** (Odnajdywanie) | Urządzenie Azure Migrate może odnajdywać maksymalnie 250 serwerów fizycznych.
**Ocena** | Można dodać do 35 000 maszyn w jednej grupie.<br/><br/> W ramach jednej oceny można ocenić do 35 000 maszyn.

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat ocen.




## <a name="physical-server-requirements"></a>Wymagania dotyczące serwera fizycznego

| **Pomoc techniczna**                | **Szczegóły**               
| :-------------------       | :------------------- |
| **Wdrożenie serwera fizycznego**       | Serwer fizyczny może być autonomiczny lub wdrożony w klastrze. |
| **Uprawnienia**           | **System Windows:** Skonfiguruj konto użytkownika lokalnego na wszystkich serwerach z systemem Windows, które mają zostać uwzględnione w odnajdywaniu. Należy dodać konto użytkownika do tych grup — Użytkownicy Pulpit zdalny, użytkownicy i Dzienniki wydajności. <br/> System **Linux:** Na serwerach z systemem Linux, które mają zostać odnajdywane, jest potrzebne konto główne. |
| **System operacyjny** | Wszystkie systemy operacyjne [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) i [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) są obsługiwane z wyjątkiem następujących:<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="azure-migrate-appliance-requirements"></a>Wymagania dotyczące urządzenia Azure Migrate

Azure Migrate używa [urządzenia Azure Migrate](migrate-appliance.md) do odnajdowania i oceny. Urządzenie dla serwerów fizycznych można uruchomić na maszynie wirtualnej lub na komputerze fizycznym. Skonfigurujesz go przy użyciu skryptu programu PowerShell pobranego z Azure Portal.

- Dowiedz się więcej o [wymaganiach dotyczących urządzeń](migrate-appliance.md#appliance---physical) dla serwerów fizycznych.
- Informacje o [adresach URL](migrate-appliance.md#url-access) , do których urządzenie musi uzyskać dostęp.

## <a name="port-access"></a>Dostęp do portu

Poniższa tabela zawiera podsumowanie wymagań dotyczących portów dla oceny.

**urządzenia** | **Połączenie**
--- | ---
**Wprowadzony** | Połączenia przychodzące na porcie TCP 3389, aby zezwolić na połączenia pulpitu zdalnego z urządzeniem.<br/> Połączenia przychodzące na porcie 44368 do zdalnego dostępu do aplikacji do zarządzania urządzeniami przy użyciu adresu URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Połączenia wychodzące na portach 443, 5671 i 5672 do wysyłania metadanych odnajdywania i wydajności do Azure Migrate.
**Serwery fizyczne** | **System Windows:** Połączenia przychodzące na porcie 443, WinRM Ports 5985 (HTTP) i 5986 (HTTPS) do ściągania metadanych konfiguracji i wydajności z serwerów z systemem Windows. <br/> System **Linux:**  Połączenia przychodzące na porcie 22 (UDP) do ściągania metadanych konfiguracji i wydajności z serwerów z systemem Linux. |


## <a name="next-steps"></a>Następne kroki

[Przygotuj się do oceny serwera fizycznego](tutorial-prepare-physical.md).
