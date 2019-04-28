---
title: Instalowanie zadań elastycznych baz danych | Dokumentacja firmy Microsoft
description: Przeprowadzenie instalacji funkcji elastycznych zadań.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 32df3e7ac6dc22e247bd4aecea4f39bf6d3a8017
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475766"
---
# <a name="installing-elastic-database-jobs-overview"></a>Instalowanie Przegląd zadań elastycznej bazy danych

[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]



[**Zadania elastic Database** ](sql-database-elastic-jobs-overview.md) można zainstalować za pomocą programu PowerShell lub za pośrednictwem witryny Azure portal. Można uzyskać dostęp do tworzenia zadań i zarządzanie nimi przy użyciu interfejsu API programu PowerShell, tylko wtedy, gdy zainstalowanie pakietu programu PowerShell. Ponadto interfejsów API programu PowerShell zapewniają one znacznie więcej funkcji niż portalu w tym momencie.

Jeśli zainstalowano już **zadania Elastic Database** za pośrednictwem portalu z istniejącego **puli elastycznej**, najnowszą wersję zapoznawczą programu PowerShell obejmuje skrypty, aby uaktualnić istniejącą instalację. Zalecane jest aby uaktualnić instalację do najnowszej wersji **zadania Elastic Database** składników, aby wykorzystać nowe funkcje udostępniane za pośrednictwem interfejsów API programu PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure. Bezpłatnej wersji próbnej, zobacz [bezpłatna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).
* Azure PowerShell. Zainstaluj najnowszą wersję przy użyciu polecenia [Instalatora platformy sieci Web](https://go.microsoft.com/fwlink/p/?linkid=320376). Aby uzyskać szczegółowe informacje, zobacz artykuł [How to install and configure Azure PowerShell](/powershell/azure/overview) (Instalowanie i konfigurowanie programu Azure PowerShell).
* [Narzędzie wiersza polecenia NuGet](https://nuget.org/nuget.exe) służy do zainstalowania pakietu zadań elastycznej bazy danych. Aby uzyskać więcej informacji, zobacz https://docs.nuget.org/docs/start-here/installing-nuget.

## <a name="download-and-import-the-elastic-database-jobs-powershell-package"></a>Pobierz i zaimportuj pakiet PowerShell zadań elastycznej bazy danych
1. Uruchom okno poleceń programu Microsoft Azure PowerShell i przejdź do katalogu, do którego pobrano narzędzie wiersza polecenia NuGet (nuget.exe).
2. Pobierz i zaimportuj **zadania Elastic Database** pakietu do bieżącego katalogu za pomocą następującego polecenia:
   
        PS C:\>.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease
   
    **Zadania Elastic Database** pliki są umieszczane w katalogu lokalnym w folderze o nazwie **Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x** gdzie *x.x.xxxx.x* odzwierciedla numer wersji. Polecenia cmdlet programu PowerShell (w tym wymagany klient biblioteki) znajdują się w **tools\ElasticDatabaseJobs** podkatalogu i skryptów programu PowerShell do instalowania, uaktualniania i odinstalowywania również znajdują się w **narzędzia** podkatalogu.
3. Przejdź do narzędzia podkatalogu w folderze Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x, wpisując narzędzia ciągłego wdrażania, na przykład:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

4. Uruchom skrypt.\InstallElasticDatabaseJobsCmdlets.ps1 można skopiować katalogu ElasticDatabaseJobs do $home\Documents\WindowsPowerShell\Modules. To będzie również automatycznie zaimportować moduł do użycia, na przykład:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobsCmdlets.ps1

## <a name="install-the-elastic-database-jobs-components-using-powershell"></a>Zainstaluj składniki zadań elastycznych baz danych przy użyciu programu PowerShell
1. Uruchom okno poleceń programu Microsoft Azure PowerShell i przejdź do podkatalogu \tools w folderze Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x: Typ dysku cd \tools
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2. Wykonaj.\InstallElasticDatabaseJobs.ps1 skrypt programu PowerShell i podaj wartości dla żądanego zmienne. Ten skrypt spowoduje utworzenie składników zawartych w [zadań elastycznych baz danych, składniki i ceny](sql-database-elastic-jobs-overview.md#components-and-pricing) oraz konfigurowania usługi w chmurze Azure odpowiednio korzystania zależnych składników.

        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobs.ps1

Po uruchomieniu tego polecenia oknie zostanie otwarta, pytanie o **nazwa_użytkownika** i **hasło**. Nie jest to poświadczenia platformy Azure, wprowadź nazwę użytkownika i hasło, które będą poświadczenia administratora, którą chcesz utworzyć dla nowego serwera.

Parametry podane na to wywołanie przykładowe mógł zostać zmodyfikowany do żądane ustawienia. Poniżej przedstawiono więcej informacji na zachowaniu każdego parametru:

<table style="width:100%">
  <tr>
    <th>Parametr</th>
    <th>Opis</th>
  </tr>

<tr>
    <td>ResourceGroupName</td>
    <td>Zawiera nazwę grupy zasobów platformy Azure zawiera nowo utworzony składników platformy Azure. Ten parametr "__ElasticDatabaseJob". Nie zaleca się zmiany tej wartości.</td>
    </tr>

<tr>
    <td>ResourceGroupLocation</td>
    <td>Zapewnia lokalizacji platformy Azure, które ma być używany dla nowo utworzonego składników platformy Azure. Ten parametr do lokalizacji, środkowe stany USA.</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>Zawiera liczbę procesów roboczych usługi, aby zainstalować. Ten parametr ma domyślnie wartość 1. Większa liczba procesów roboczych może służyć do skalowania w poziomie usługi i umożliwiające uzyskanie wysokiej dostępności. Zaleca się użyć "2" na potrzeby wdrożeń, które wymagają wysokiej dostępności usługi.</td>
</tr>

<tr>
    <td>ServiceVmSize</td>
    <td>Zapewnia rozmiar maszyny Wirtualnej do użycia w ramach usługi w chmurze. Ten parametr A0. Wartości parametrów... /.. / A3 są akceptowane spowodować roli procesu roboczego użyć rozmiaru ExtraSmall/małych/średni/duża, odpowiednio. Aby uzyskać więcej informacji na temat rozmiarów ról procesów roboczych, zobacz <a href="https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview#components-and-pricing">zadań elastycznych baz danych, składniki i ceny</a>.</td>
</tr>

<tr>
    <td>SqlServerDatabaseSlo</td>
    <td>Udostępnia rozmiaru obliczeń dla wersji standardowa. Ten parametr S0. Wartości parametrów... /.. /.. /.. / S9/S12 — są akceptowane spowodować użycie rozmiaru odpowiednich obliczeniowych usługi Azure SQL Database. Aby uzyskać więcej informacji na temat rozmiarów wystąpień obliczeniowych bazy danych SQL, zobacz <a href="https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview#components-and-pricing">zadań elastycznych baz danych, składniki i ceny</a>.</td>
</tr>

<tr>
    <td>SqlServerAdministratorUserName</td>
    <td>Zawiera nazwę użytkownika administratora dla nowo utworzony serwer Azure SQL Database. Jeśli nie zostanie określony, monit o podanie poświadczeń otworzy się okno poświadczenia programu PowerShell.</td>
</tr>

<tr>
    <td>SqlServerAdministratorPassword</td>
    <td>Podaje hasło administratora dla nowo utworzony serwer Azure SQL Database. Jeśli nie podano okno poświadczenia programu PowerShell otworzy się monit o podanie poświadczeń.</td>
</tr>
</table>

Dla systemów, których platformą docelową o dużej liczby zadań uruchamianych równolegle w odniesieniu do dużej liczby baz danych, zaleca się określić parametry, takie jak: - ServiceWorkerCount 2 - ServiceVmSize A2 — SqlServerDatabaseSlo S2.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\InstallElasticDatabaseJobs.ps1 -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2

## <a name="update-an-existing-elastic-database-jobs-components-installation-using-powershell"></a>Aktualizowanie instalacji składników istniejących zadań elastycznych baz danych przy użyciu programu PowerShell
**Zadania elastic Database** mogą być aktualizowane w ramach istniejącej instalacji w celu zapewnienia skalowalności i wysokiej dostępności. Ten proces umożliwia w ramach przyszłych uaktualnień kodu usługi bez konieczności Usuń i Utwórz ponownie bazy danych kontroli. Ten proces może również w ramach tej samej wersji do modyfikowania rozmiaru maszyny Wirtualnej usługi lub liczba procesów roboczych na serwerze.

Aby zaktualizować rozmiar maszyny Wirtualnej, instalacji, uruchom następujący skrypt z parametrami zaktualizowany do wartości wybranych.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\UpdateElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\UpdateElasticDatabaseJobs.ps1 -ServiceVmSize A1 -ServiceWorkerCount 2

<table style="width:100%">
  <tr>
  <th>Parametr</th>
  <th>Opis</th>
</tr>

<tr>
    <td>ResourceGroupName</td>
    <td>Określa nazwę grupy zasobów platformy Azure używana podczas początkowo zostały zainstalowane składniki zadania elastycznych baz danych. Ten parametr "__ElasticDatabaseJob". Ponieważ nie jest to zalecane, aby zmienić tę wartość, nie powinny mieć do określenia tego parametru.</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>Zawiera liczbę procesów roboczych usługi, aby zainstalować.  Ten parametr ma domyślnie wartość 1.  Większa liczba procesów roboczych może służyć do skalowania w poziomie usługi i umożliwiające uzyskanie wysokiej dostępności.  Zaleca się użyć "2" na potrzeby wdrożeń, które wymagają wysokiej dostępności usługi.</td>
</tr>

<tr>
    <td>ServiceVmSize</td>
    <td>Zapewnia rozmiar maszyny Wirtualnej do użycia w ramach usługi w chmurze. Ten parametr A0. Wartości parametrów... /.. / A3 są akceptowane spowodować roli procesu roboczego użyć rozmiaru ExtraSmall/małych/średni/duża, odpowiednio. Aby uzyskać więcej informacji na temat rozmiarów ról procesów roboczych, zobacz <a href="https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview#components-and-pricing">zadań elastycznych baz danych, składniki i ceny</a>.</td>
</tr>

</table>

## <a name="install-the-elastic-database-jobs-components-using-the-portal"></a>Zainstaluj składniki zadań elastycznych baz danych przy użyciu portalu
Po utworzeniu [utworzona pula elastyczna](sql-database-elastic-pool-manage-portal.md), możesz zainstalować **zadania Elastic Database** składniki umożliwiające wykonywanie zadań administracyjnych w odniesieniu do każdej bazy danych w puli elastycznej. Inaczej niż w przypadku, gdy za pomocą **zadania Elastic Database** interfejsów API programu PowerShell, portal interfejs jest obecnie ograniczone do wykonywania tylko w odniesieniu istniejącej puli.

**Szacowany czas trwania:** 10 minut.

1. W widoku pulpitu nawigacyjnego puli elastycznej za pośrednictwem [witryny Azure portal](https://portal.azure.com/#) , kliknij przycisk **zadania Utwórz**.
2. Jeśli zadanie jest tworzony po raz pierwszy, musisz zainstalować **zadania Elastic Database** , klikając **dodatkowym POSTANOWIENIOM dotyczącym wersji**.
3. Zaakceptuj warunki, klikając pole wyboru.
4. W widoku "Zainstalować usługi" kliknij **POŚWIADCZENIA zadania**.
   
    ![Instalowanie usług][1]
5. Wpisz nazwę użytkownika i hasło administratora bazy danych. W ramach instalacji nowy serwer usługi Azure SQL Database jest tworzony. W ramach tego nowego serwera nowej bazy danych, nazywane w bazie danych kontroli jest utworzony i używany do zawiera metadanych dla zadania elastycznych baz danych. Nazwa użytkownika i hasło utworzone w tym miejscu są używane na potrzeby logowania się do bazy danych kontroli. Oddzielne poświadczenie jest używane do wykonania skryptu baz danych w puli.
   
    ![Utwórz nazwę użytkownika i hasło][2]
6. Kliknij przycisk OK. Składniki są tworzone w ciągu kilku minut w nowym [grupy zasobów](../azure-resource-manager/resource-group-overview.md). Nową grupę zasobów jest przypięty do tablicy rozpoczęcia, jak pokazano poniżej. Zadania po utworzeniu elastycznej bazy danych (usługi w chmurze, SQL Database, usługi Service Bus i magazyn) są tworzone w grupie.
   
    ![Grupa zasobów na początku tablicy][3]
7. Jeśli spróbujesz utworzyć lub zarządzać zadaniem, gdy instalowanie zadań elastycznych baz danych podczas podawania **poświadczenia** zostanie wyświetlony następujący komunikat.
   
    ![Wdrażanie jest nadal w toku][4]

Jeśli wymagana jest dezinstalacji, należy usunąć grupę zasobów. Zobacz [jak odinstalować składniki zadania Elastic Database](sql-database-elastic-jobs-uninstall.md).

## <a name="next-steps"></a>Kolejne kroki
Upewnij się, poświadczenia z odpowiednich uprawnień Wykonywanie skryptu jest utworzony w każdej bazie danych w grupie, aby uzyskać więcej informacji, zobacz [Zabezpieczanie bazy danych SQL](sql-database-manage-logins.md).
Zobacz [tworzenie i zarządzanie nimi zadania Elastic Database](sql-database-elastic-jobs-create-and-manage.md) na rozpoczęcie pracy.

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/not-done.png
