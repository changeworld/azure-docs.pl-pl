---
title: Instalowanie zadania elastycznej bazy danych | Dokumentacja firmy Microsoft
description: Przeprowadzenie instalacji funkcji elastycznej zadania.
services: sql-database
manager: craigg
author: ddove
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 5760ca693f347068e03770b348d88b3b2adbf678
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645616"
---
# <a name="installing-elastic-database-jobs-overview"></a>Instalowanie omówienie zadania elastycznej bazy danych
[**Zadania elastyczne bazy danych** ](sql-database-elastic-jobs-overview.md) można zainstalować za pomocą programu PowerShell lub za pośrednictwem portalu Azure. Możliwość uzyskania dostępu do utworzenia zadania i zarządzać nimi przy użyciu interfejsu API środowiska PowerShell, tylko w przypadku instalowania pakietu programu PowerShell. Ponadto API środowiska PowerShell stanowią znacznie więcej funkcji niż portalu w tym momencie.

Jeśli użytkownik zainstalował już **zadania elastycznej bazy danych** za pośrednictwem portalu z istniejącego **puli elastycznej**, najnowszej wersji zapoznawczej programu Powershell obejmuje skrypty w celu uaktualnienia istniejącej instalacji. Zdecydowanie zaleca się uaktualnienia do najnowszej wersji instalacji **zadania elastycznej bazy danych** składników, aby korzystać z nowych funkcji udostępnianych za pośrednictwem interfejsów API środowiska PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure. Bezpłatnej wersji próbnej, zobacz [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* Azure PowerShell. Zainstaluj najnowszą wersję za pomocą [Instalatora platformy sieci Web](http://go.microsoft.com/fwlink/p/?linkid=320376). Aby uzyskać szczegółowe informacje, zobacz artykuł [How to install and configure Azure PowerShell](/powershell/azure/overview) (Instalowanie i konfigurowanie programu Azure PowerShell).
* [Narzędzie wiersza polecenia NuGet](https://nuget.org/nuget.exe) służy do zainstalowania pakietu zadania elastycznej bazy danych. Aby uzyskać więcej informacji, zobacz http://docs.nuget.org/docs/start-here/installing-nuget.

## <a name="download-and-import-the-elastic-database-jobs-powershell-package"></a>Pobieranie i importowanie pakietu programu PowerShell zadania elastycznej bazy danych
1. Uruchom okno poleceń programu PowerShell usługi Microsoft Azure i przejdź do katalogu, którego pobrano narzędzie wiersza polecenia NuGet (nuget.exe).
2. Pobieranie i importowanie **zadania elastycznej bazy danych** pakietu do bieżącego katalogu przy użyciu następującego polecenia:
   
        PS C:\>.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease
   
    **Zadania elastycznej bazy danych** pliki są umieszczane w katalogu lokalnym, w folderze o nazwie **Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x** gdzie *x.x.xxxx.x* odzwierciedla numer wersji. Polecenia cmdlet programu PowerShell (w tym wymagany klient biblioteki) znajdują się w **tools\ElasticDatabaseJobs** podkatalogu i skryptów programu PowerShell do instalowania, uaktualniania i odinstalowywania również znajdują się w **narzędzia** podkatalogu.
3. Przejdź do podkatalogu narzędzia w folderze Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x, wpisując polecenie cd narzędzi, na przykład:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

4. Uruchom skrypt.\InstallElasticDatabaseJobsCmdlets.ps1 można skopiować katalogu ElasticDatabaseJobs do $home\Documents\WindowsPowerShell\Modules. Spowoduje to również automatycznie zaimportowanie modułu do użycia, na przykład:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobsCmdlets.ps1

## <a name="install-the-elastic-database-jobs-components-using-powershell"></a>Zainstaluj składniki zadania elastycznej bazy danych przy użyciu programu PowerShell
1. Uruchom okno poleceń programu PowerShell usługi Microsoft Azure i przejdź do podkatalogu \tools w folderze Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x: wpisz \tools dysku cd
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2. Wykonaj.\InstallElasticDatabaseJobs.ps1 skrypt programu PowerShell i podaj wartości dla żądanej zmienne. Ten skrypt utworzy składników zawartych w [zadania elastycznej bazy danych, składników i cenach](sql-database-elastic-jobs-overview.md#components-and-pricing) oraz konfigurowanie usługi w chmurze Azure umożliwia odpowiednio składników zależnych.

        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobs.ps1

Po uruchomieniu tego polecenia okna otwiera pytania o **nazwy użytkownika** i **hasło**. Nie jest to Twoje poświadczenia platformy Azure, wprowadź nazwę użytkownika i hasło, które będą poświadczeniami administratora, którą chcesz utworzyć dla nowego serwera.

Parametry podane na tej próbki wywołania można modyfikować dla żądane ustawienia. Poniżej zamieszczono więcej informacji dotyczących zachowania każdego parametru:

<table style="width:100%">
  <tr>
    <th>Parametr</th>
    <th>Opis</th>
  </tr>

<tr>
    <td>ResourceGroupName</td>
    <td>Zawiera nazwę grupy zasobów platformy Azure zawiera nowo utworzony składniki platformy Azure. Domyślnie ten parametr "__ElasticDatabaseJob". Nie zaleca się zmienić tę wartość.</td>
    </tr>

</tr>

    <tr>
    <td>ResourceGroupLocation</td>
    <td>Określa lokalizację Azure do zastosowania w przypadku nowo utworzonego składniki platformy Azure. Ten parametr domyślnie lokalizacji środkowe stany USA.</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>Zawiera liczbę procesów roboczych usługi do zainstalowania. Ten parametr ma domyślnie wartość 1. Większa liczba procesów roboczych może służyć do skalowania w poziomie usługi i zapewnienie wysokiej dostępności. Zalecane jest, aby użyć "2" dla wdrożenia, które wymagają wysokiej dostępności usługi.</td>
    </tr>

</tr>
    <tr>
    <td>ServiceVmSize</td>
    <td>Udostępnia rozmiar maszyny Wirtualnej do użycia w ramach usługi w chmurze. Ten parametr domyślnie A0. Wartości parametrów A0/A1/A2/a3 są akceptowane powodujących rolę procesu roboczego Użyj odpowiednio rozmiar ExtraSmall/małych/średnich/duży. Zobacz więcej informacji na temat rozmiarów roli procesu roboczego, FO [zadania elastycznej bazy danych, składników i cenach](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</tr>
    <tr>
    <td>SqlServerDatabaseSlo</td>
    <td>Standard edition zapewnia cel poziomu usługi. Ten parametr domyślnie S0. Wartości parametrów S0/S1/S2/S3/S4/S6 w warstwie/S9/S12 są akceptowane powodujących bazę danych SQL Azure do używania odpowiednich SLO. Aby uzyskać więcej informacji na cele slo bazy danych SQL, zobacz [zadania elastycznej bazy danych, składników i cenach](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorUserName</td>
    <td>Zawiera nazwę użytkownika administratora dla nowo utworzonego serwera bazy danych SQL Azure. Jeśli nie zostanie określony, okno poświadczenia programu PowerShell zostanie otwarty z monitem o poświadczenia.</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorPassword</td>
    <td>Udostępnia hasło administratora dla nowo utworzonego serwera bazy danych SQL Azure. Jeśli nie podano okno poświadczenia programu PowerShell zostanie otwarty z monitem o poświadczenia.</td>
</tr>
</table>

Dla systemów przeznaczonych o dużej liczby zadań uruchamianych jednocześnie na dużej liczbie baz danych, zaleca się określić parametry: - ServiceWorkerCount 2 - ServiceVmSize A2 - SqlServerDatabaseSlo S2.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\InstallElasticDatabaseJobs.ps1 -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2

## <a name="update-an-existing-elastic-database-jobs-components-installation-using-powershell"></a>Aktualizowanie instalacji składników istniejącego zadania elastycznej bazy danych przy użyciu programu PowerShell
**Zadania elastyczne bazy danych** może zostać zaktualizowana w ramach istniejącej instalacji zapewnienia skalowalności i wysokiej dostępności. Dzięki temu w ramach przyszłych uaktualnień kodu usługi bez usunięcia i ponownego utworzenia bazy danych kontroli. Ten proces można również w ramach tej samej wersji do modyfikowania rozmiaru maszyny Wirtualnej usługi lub liczby serwerów procesu roboczego.

Aby zaktualizować rozmiaru maszyny Wirtualnej instalacji, uruchom następujący skrypt z parametrami zaktualizowany do wartości wybranych przez użytkownika.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\UpdateElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\UpdateElasticDatabaseJobs.ps1 -ServiceVmSize A1 -ServiceWorkerCount 2

<table style="width:100%">
  <tr>
  <th>Parametr</th>
  <th>Opis</th>
</tr>

  <tr>
    <td>ResourceGroupName</td>
    <td>Określa nazwę grupy zasobów platformy Azure używana podczas początkowo zostały zainstalowane składniki zadania elastycznej bazy danych. Domyślnie ten parametr "__ElasticDatabaseJob". Ponieważ nie jest to zalecane, aby zmienić tę wartość, nie powinien zawierać określenie tego parametru.</td>
    </tr>
</tr>

</tr>

  <tr>
    <td>ServiceWorkerCount</td>
    <td>Zawiera liczbę procesów roboczych usługi do zainstalowania.  Ten parametr ma domyślnie wartość 1.  Większa liczba procesów roboczych może służyć do skalowania w poziomie usługi i zapewnienie wysokiej dostępności.  Zalecane jest, aby użyć "2" dla wdrożenia, które wymagają wysokiej dostępności usługi.</td>
</tr>

</tr>

    <tr>
    <td>ServiceVmSize</td>
    <td>Udostępnia rozmiar maszyny Wirtualnej do użycia w ramach usługi w chmurze. Ten parametr domyślnie A0. Wartości parametrów A0/A1/A2/a3 są akceptowane powodujących rolę procesu roboczego Użyj odpowiednio rozmiar ExtraSmall/małych/średnich/duży. Zobacz więcej informacji na temat rozmiarów roli procesu roboczego, FO [zadania elastycznej bazy danych, składników i cenach](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</table>

## <a name="install-the-elastic-database-jobs-components-using-the-portal"></a>Zainstaluj składniki zadania elastycznej bazy danych przy użyciu portalu
Po utworzeniu [utworzyć puli elastycznej](sql-database-elastic-pool-manage-portal.md), można zainstalować **zadania elastycznej bazy danych** składniki umożliwiające wykonywanie zadań administracyjnych dla każdej bazy danych w puli elastycznej. Inaczej niż w przypadku, gdy za pomocą **zadania elastycznej bazy danych** interfejsów API środowiska PowerShell, interfejsu portalu jest obecnie ograniczone do wykonywania tylko dla istniejącej puli.

**Szacowany czas trwania:** 10 minut.

1. W widoku pulpitu nawigacyjnego puli elastycznej za pośrednictwem [portalu Azure](https://portal.azure.com/#) , kliknij przycisk **Utwórz zadanie**.
2. W przypadku tworzenia zadania po raz pierwszy, musisz zainstalować **zadania elastycznej bazy danych** klikając **warunki dotyczące**.
3. Klikając pole wyboru, należy zaakceptować postanowienia.
4. W widoku "Zainstalować usługi" kliknij **POŚWIADCZENIA zadania**.
   
    ![Instalowanie usług][1]
5. Wpisz nazwę użytkownika i hasło administratora bazy danych. W ramach instalacji jest tworzony nowy serwer bazy danych SQL Azure. W ramach tego nowego serwera nowej bazy danych, nazywane bazie danych kontroli jest tworzone i używane do przechowywania metadanych dla zadania elastycznej bazy danych. Nazwa użytkownika i hasło utworzone w tym miejscu są używane na potrzeby logowania do bazy danych kontroli. Oddzielne poświadczeń jest używany do wykonania skryptu baz danych w puli.
   
    ![Utwórz nazwę użytkownika i hasło][2]
6. Kliknij przycisk OK. Składniki są tworzone automatycznie w ciągu kilku minut w nowym [grupy zasobów](../azure-resource-manager/resource-group-overview.md). Nową grupę zasobów jest przypięty do tablicy rozpoczęcia, jak pokazano poniżej. Zadania po utworzeniu elastycznej bazy danych (usługi w chmurze, bazę danych SQL, magistrali usług i magazyn) są tworzone w grupie.
   
    ![grupy zasobów w tablicy start][3]
7. Jeśli spróbujesz tworzyć ani nimi zarządzać zadania podczas instalowania zadania elastycznej bazy danych podczas tworzenia **poświadczenia** zostanie wyświetlony następujący komunikat.
   
    ![Wdrożenie w toku][4]

Jeśli wymagana jest dezinstalację, należy usunąć grupę zasobów. Zobacz [jak odinstalować składniki zadania elastycznej bazy danych](sql-database-elastic-jobs-uninstall.md).

## <a name="next-steps"></a>Kolejne kroki
Upewnij się, poświadczenie o odpowiednie prawa do wykonania skryptu jest utworzony w każdej bazie danych w grupie, aby uzyskać więcej informacji, zobacz [Zabezpieczanie bazy danych SQL](sql-database-manage-logins.md).
Zobacz [Tworzenie zadania i zarządzać nimi elastycznej bazy danych](sql-database-elastic-jobs-create-and-manage.md) rozpocząć pracę.

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/not-done.png
