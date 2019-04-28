---
title: Wielodostępne SaaS samouczek — Azure SQL Database | Dokumentacja firmy Microsoft
description: Aprowizacji i wykazu nowych dzierżaw za pomocą wzorca aplikacji autonomicznych
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 09/24/2018
ms.openlocfilehash: 28deb9b7ba15744b9bd3d273d02db4398d2b2ef3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61484594"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Za pomocą aplikacji w dzierżawie wzorzec SaaS aprowizacji i wykazu nowych dzierżaw

W tym artykule opisano aprowizowanie i katalogowanie nowych dzierżaw przy użyciu aplikacji autonomicznej na dzierżawę wzorzec SaaS.
W tym artykule znajdują się dwie główne części:
* Omówienie koncepcyjne katalogowanie nowych dzierżaw i udostępniania
* Samouczek, w którym wyróżnia przykładowego kodu programu PowerShell w ramach inicjowania obsługi administracyjnej i katalogowanie
    * W tym samouczku użyto aplikacji Wingtip Tickets Przykładowa aplikacja SaaS, dostosowane do aplikacji autonomicznej na wzorzec dzierżawy.

## <a name="standalone-application-per-tenant-pattern"></a>Aplikacja autonomiczna na wzorzec dzierżawy

Aplikacji autonomicznej na wzorzec dzierżawy jest jednym z kilku wzorce dla wielodostępnych aplikacji SaaS.  W tym wzorcu autonomiczna aplikacja jest przeznaczona dla każdego dzierżawcy. Aplikacja składa się z poziomu składniki aplikacji i bazy danych SQL.  Każda aplikacja dzierżawy można wdrożyć w ramach subskrypcji dostawcy.  Alternatywnie, platforma Azure oferuje [programów zarządzanych aplikacji](https://docs.microsoft.com/azure/managed-applications/overview) w którym aplikację można wdrożyć w ramach subskrypcji dzierżawy i zarządzane przez dostawcę w imieniu dzierżawcy. 

   ![wzorzec aplikacji dla dzierżawcy](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

W przypadku wdrażania aplikacji dla dzierżawy, aplikacji i bazy danych są aprowizowane w nowej grupie zasobów utworzonych dla dzierżawy.  Przy użyciu oddzielnych grup zasobów każdego dzierżawcy zasobów aplikacji i pozwala im zarządzać w sposób niezależny. W każdej grupie zasobów każde wystąpienie aplikacji jest skonfigurowana do bezpośredni dostęp do odpowiedniej bazy danych.  Ten model połączenia różni się znacząco od innych wzorców, korzystających z katalogu do brokera połączeń między aplikacją a bazą danych.  A ponieważ nie ma żadnych zasobów, udostępnianie, każda baza danych dzierżawy muszą mieć przydzielone wystarczające zasoby do obsługi obciążenia szczytowego. Ten wzorzec jest zwykle ma być używany dla aplikacji SaaS z mniejszą liczbą dzierżawami, gdy istnieje naciskiem na dzierżawę, izolacji i w mniejszym stopniu na koszty zasobów.  

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Za pomocą katalogu dzierżawy z aplikacją na wzorzec dzierżawy

Każda dzierżawa aplikacji i bazy danych są całkowicie odizolowane, różnych zarządzania i scenariusze analizy może działać dla dzierżaw.  Na przykład zastosowanie zmiany schematu, istnieje nowa wersja aplikacji wymaga wprowadzenia zmian schematu każda baza danych dzierżawy. Scenariusze raportowanie i analiza może również wymagać dostępu do wszystkich baz danych dzierżawy niezależnie od tego, gdzie są one wdrażane.

   ![wzorzec aplikacji dla dzierżawcy](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

Wykaz dzierżaw zawiera mapowanie między identyfikator dzierżawy i dzierżawy bazy danych, umożliwiając identyfikator, aby zostać rozpoznane serwera i nazwę bazy danych.  W aplikacji SaaS o nazwie Wingtip identyfikator dzierżawy jest obliczana jako skrót nazwy dzierżawy, mimo że można użyć innych systemów.  Natomiast aplikacje autonomiczne nie są potrzebne wykaz aby można było zarządzać połączeniami, wykazu może służyć do określania zakresu innych akcji na zestaw baz danych dzierżaw. Na przykład zapytanie elastyczne można użyć katalogu do określania zestawu baz danych, w których kwerendy są dystrybuowane do raportowania w wielu dzierżawach.

## <a name="elastic-database-client-library"></a>Biblioteka kliencka Elastic Database

W przykładowej aplikacji Wingtip wykazu jest implementowany przez funkcje zarządzania fragmentami [Biblioteka kliencka Elastic Database](sql-database-elastic-database-client-library.md) (EDCL).  Biblioteka umożliwia aplikacji do tworzenia, zarządzania i używania mapowania fragmentów, która jest przechowywana w bazie danych. W tym przykładzie aplikacji Wingtip Tickets katalogu są przechowywane w *wykaz dzierżaw* bazy danych.  Dzielenie map na fragmenty z klucza dzierżawy do fragmentów (baza danych), w którym są przechowywane dane tej dzierżawy.  Biblioteki EDCL funkcje zarządzania *mapowania fragmentów globalnego* przechowywane w tabelach w *wykaz dzierżaw* bazy danych i *mapowanie fragmentów w postaci lokalnej* przechowywanych w poszczególnych fragmentach.

Funkcje biblioteki EDCL można wywołać z aplikacji lub skryptów programu PowerShell do tworzenia i zarządzania wpisów w ramach mapowania fragmentów. Inne funkcje biblioteki EDCL można pobrać zestaw fragmentów, lub połącz się z odpowiednią bazą danych, dla danego klucza dzierżawy. 

> [!IMPORTANT]
> Nie należy edytować dane w bazie danych wykazu lub lokalnego podzielonej na fragmenty mapy w bazach danych dzierżaw bezpośrednio. Bezpośrednie aktualizacje nie są obsługiwane ze względu na duże ryzyko uszkodzenia danych. Zamiast tego dane mapowania można edytować tylko przy użyciu biblioteki EDCL interfejsów API.

## <a name="tenant-provisioning"></a>Inicjowania obsługi dzierżawy 

Każda dzierżawa wymaga nową grupę zasobów platformy Azure, która musi zostać utworzona przed zasoby mogą być udostępniane w nim. Gdy grupa zasobów istnieje, szablonu usługi Azure Resource Management może służyć do wdrożenia składników aplikacji i bazy danych, a następnie skonfiguruj połączenie z bazą danych. Aby zainicjować schemat bazy danych, szablon można zaimportować plik bacpac.  Alternatywnie można utworzyć bazy danych jako kopię bazy danych "template".  Bazy danych jest następnie dalsze aktualizowane przy użyciu miejsc początkowa data i zarejestrowanych w wykazie.

## <a name="tutorial"></a>Samouczek

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

* Aprowizowanie wykazu
* Zarejestruj baz danych dzierżaw próbki, wdrożone wcześniej w katalogu
* Aprowizacja dodatkowych dzierżawy i zarejestruj go w katalogu

Szablon usługi Azure Resource Manager umożliwia wdrażanie i konfigurowanie aplikacji, Utwórz bazę danych dzierżawy i zaimportowanie pliku bacpac, aby go zainicjować. Żądanie importowania może być umieszczona w kolejce przez kilka minut, zanim zostanie actioned.

Na końcu tego samouczka masz zestaw autonomicznych dzierżawy aplikacji, z każdą bazą danych zarejestrowanych w wykazie.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych: 

* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Trzy przykładowe dzierżawy aplikacje są wdrażane. Aby wdrożyć te aplikacje w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie wzorca aplikacji Wingtip Tickets SaaS autonomiczną aplikacją](saas-standaloneapp-get-started-deploy.md).

## <a name="provision-the-catalog"></a>Aprowizowanie wykazu

W tym zadaniu dowiesz się, jak wykonać aprowizację katalogu, używany do rejestrowania wszystkich baz danych dzierżaw. Wykonasz następujące zadania: 

* **Aprowizowanie bazy danych wykazów** przy użyciu szablonu usługi Azure resource management. Baza danych jest inicjowany przez zaimportowanie pliku bacpac.  
* **Rejestrowanie przykładowej aplikacji dzierżawy** wdrożonego wcześniej.  Każda dzierżawa jest zarejestrowana przy użyciu klucza zbudowane na podstawie funkcji skrótu nazwy dzierżawy.  Nazwa dzierżawy także są przechowywane w tabeli rozszerzenia w katalogu.

1. Otwórz w środowisku PowerShell ISE *...\Learning Modules\UserConfig.psm* i zaktualizuj **\<użytkownika\>** wartość do wartości zostały użyte podczas wdrażania trzy przykładowe aplikacje.  **Zapisz plik**.  
1. Otwórz w środowisku PowerShell ISE *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* i ustaw **$Scenario = 1**. Wdrażanie wykaz dzierżaw i rejestrowanie wstępnie zdefiniowanych dzierżaw.

1. Dodaj punkt przerwania, umieszczając kursor w dowolnym miejscu w wierszu, który jest wyświetlany komunikat, `& $PSScriptRoot\New-Catalog.ps1`, a następnie naciśnij klawisz **F9**.

    ![ustawienie punktu przerwania śledzenia](media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Uruchom skrypt, naciskając klawisz **F5**. 
1.  Po zatrzymaniu w punkcie przerwania wykonywania skryptu naciśnij **F11** do skryptu Catalog.ps1 nowy krok po kroku.
1.  Śledź wykonywanie skryptu przy użyciu opcji menu debugowania: F10 i F11, aby wychodzisz nad lub do funkcji o nazwie.
    *   Aby uzyskać więcej informacji na temat debugowania skryptów programu PowerShell, zobacz [porady dotyczące pracy z i debugowania skryptów programu PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

Po zakończeniu działania skryptu katalogu będzie istnieć i przykładowymi dzierżawami będą rejestrowane. 

Teraz sprawdźmy zasobów, który został utworzony.

1. Otwórz [witryny Azure portal](https://portal.azure.com/) oraz przeglądanie grup zasobów.  Otwórz **wingtip-sa-catalog -\<użytkownika\>**  zasobów grupy i zanotuj serwer wykazu i bazy danych.
1. Otwórz bazę danych w portalu i wybierz *Eksplorator danych* z menu po lewej stronie.  Kliknij polecenie logowania, a następnie wprowadź hasło = **P\@ssword1**.


1. Zapoznaj się ze schematem *tenantcatalog* bazy danych.  
   * Obiekty w `__ShardManagement` schematu są dostarczane przez bibliotece klienckiej Elastic Database.
   * `Tenants` Tabeli i `TenantsExtended` widoku są rozszerzenia dodane w przykładzie, które pokazują, jak można je rozszerzyć wykaz aby zapewnić dodatkową wartość.
1. Uruchom zapytanie, `SELECT * FROM dbo.TenantsExtended`.          

   ![Eksplorator danych](media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Jako alternatywa za pomocą Eksploratora danych będzie można połączyć z bazą danych z programu SQL Server Management Studio. W tym celu należy połączyć się z serwerem o nazwie wingtip- 

    
    Należy pamiętać, nie należy edytować dane bezpośrednio w wykazie — zawsze używać interfejsów API zarządzania fragmentami. 

## <a name="provision-a-new-tenant-application"></a>Aprowizacja nowej dzierżawy aplikacji

W tym zadaniu dowiesz się, jak inicjować obsługę aplikacji pojedynczej dzierżawy. Wykonasz następujące zadania:  

* **Utwórz nową grupę zasobów** dla dzierżawy. 
* **Aprowizowanie aplikacji i baz danych** do nowej grupy zasobów przy użyciu szablonu usługi Azure resource management.  Ta czynność obejmuje inicjowanie bazy danych za pomocą wspólnego schematu i danych referencyjnych przez zaimportowanie pliku bacpac. 
* **Inicjowanie bazy danych przy użyciu informacji o dzierżawie podstawowe**. Ta czynność obejmuje określenie typu właściwości, który określa fotografii, używany w tle w swojej witrynie sieci web zdarzenia. 
* **Zarejestruj bazy danych w bazie danych wykazów**. 

1. Otwórz w środowisku PowerShell ISE *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* i ustaw **$Scenario = 2**. Wdrażanie wykaz dzierżaw i rejestrowanie wstępnie zdefiniowanych dzierżaw

1. Dodaj punkt przerwania w skrypcie, umieszczając kursor w dowolnym miejscu w wierszu 49, który jest wyświetlany komunikat, `& $PSScriptRoot\New-TenantApp.ps1`, a następnie naciśnij klawisz **F9**.
1. Uruchom skrypt, naciskając klawisz **F5**. 
1.  Po zatrzymaniu w punkcie przerwania wykonywania skryptu naciśnij **F11** do skryptu Catalog.ps1 nowy krok po kroku.
1.  Śledź wykonywanie skryptu przy użyciu opcji menu debugowania: F10 i F11, aby wychodzisz nad lub do funkcji o nazwie.

Po zainicjowaniu obsługi dzierżawy nowej dzierżawy zdarzenia witryny sieci Web jest otwarty.

   ![Red maple racing](media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Następnie można sprawdzić nowe zasoby utworzone w witrynie Azure portal. 

   ![Red maple ścigają zasobów](media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Aby zatrzymać Naliczanie opłaty, należy usunąć grupy zasobów

Po zakończeniu eksplorowania próbki, należy usunąć wszystkie grupy zasobów został utworzony, aby zatrzymać Naliczanie opłat za skojarzone.

## <a name="additional-resources"></a>Dodatkowe zasoby

- Aby dowiedzieć się więcej o wielodostępnych aplikacji SaaS w bazie danych, zobacz [wzorce projektowe dla wielodostępnych aplikacji SaaS](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Kolejne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Jak wdrożyć aplikację Wingtip Tickets SaaS autonomicznych.
> * Temat serwerów i baz danych, które składają się na aplikację.
> * Jak usunąć przykładowych zasobów w celu zakończenia ich rozliczania.

Możesz eksplorować, jak katalog jest używany do obsługi różnych scenariuszy międzydzierżawowa, za pomocą wersji bazy danych dla dzierżawcy [aplikacji SaaS o nazwie Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md).  
