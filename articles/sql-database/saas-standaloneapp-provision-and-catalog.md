---
title: Samouczek SaaS wielodostępne — baza danych SQL Azure | Dokumentacja firmy Microsoft
description: Dostarczanie i katalogu nowi dzierżawcy przy użyciu wzorca aplikacji autonomicznych
keywords: samouczek usługi sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: SaaS
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: billgib
ms.openlocfilehash: 0f2495ddc5d5053582d67bd44cdf80d018f79e42
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646157"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Dostarczanie i katalogu nowi dzierżawcy przy użyciu aplikacji dla poszczególnych dzierżawców wzorzec SaaS

Ten artykuł obejmuje Inicjowanie obsługi i skatalogowania nowe dzierżawców przy użyciu aplikacji autonomicznej dla każdego dzierżawcy wzorzec SaaS.
Ten artykuł zawiera dwie główne części:
* Omówienie pojęć inicjowania obsługi administracyjnej i skatalogowania nowi dzierżawcy
* Samouczek, który prezentuje przykładowy kod programu PowerShell, który wykonuje inicjowania obsługi administracyjnej i skatalogowania
    * W samouczku aplikacji SaaS biletów Wingtip próbki, dostosowane do aplikacji autonomicznej na wzorzec dzierżawy.

## <a name="standalone-application-per-tenant-pattern"></a>Aplikacja autonomiczna na wzorzec dzierżawy
Aplikacji autonomicznej na wzorzec dzierżawy jest jednym z kilku dla wielodostępnych aplikacji SaaS.  W tym wzorcu aplikacji autonomicznej zostanie zainicjowana dla każdego dzierżawcy. Aplikacja składa się z poziomu składniki aplikacji i bazy danych SQL.  Każdej dzierżawy aplikacji można wdrożyć w dostawcy subskrypcji.  Alternatywnie Azure oferuje [zarządzanych aplikacji program](https://docs.microsoft.com/azure/managed-applications/overview) aplikacji może być wdrożonych w subskrypcji dzierżawcy i zarządzane przez dostawcę w imieniu dzierżawcy. 

   ![wzorzec aplikacji na dzierżawy](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

W przypadku wdrażania aplikacji dla dzierżawy, aplikacji i bazy danych są udostępniane w nowej grupie zasobów utworzona dla dzierżawcy.  Przy użyciu oddzielnych grup zasobów każdej dzierżawy zasobów aplikacji i pozwala je, aby zarządzać w sposób niezależny. W każdej grupie zasobów każde wystąpienie aplikacji skonfigurowano bezpośrednio uzyskać dostępu do jego odpowiednia baza danych.  Ten model połączenia różni się znacząco z innymi wzorami korzystających z katalogu brokera połączeń między bazy danych i aplikacji.  I niezmieniona nie udostępniania zasobów każdej dzierżawy bazy danych muszą mieć aprowizowane wystarczające zasoby do obsługi obciążenia szczytowego. Ten wzorzec zwykle używanego do aplikacji SaaS przy użyciu mniejszej liczby dzierżawców w przypadku, gdy istnieje duży nacisk położono na dzierżawy, izolacji i mniej nacisk położono na kosztów zasobów.  

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Przy użyciu katalogu dzierżawcy z aplikacją na wzorzec dzierżawy
Podczas każdego dzierżawcy aplikacji i bazy danych są całkowicie odizolowane, różnych zarządzania i scenariusze analizy może działać przez dzierżawców.  Na przykład zastosowanie zmiany schematu w nowej wersji aplikacji wymaga zmiany schematu bazy danych każdego dzierżawcy. Scenariusze raportowanie i analiza może również wymagać dostępu do wszystkich dzierżawy bazy danych niezależnie od tego, w których są wdrożone.

   ![wzorzec aplikacji na dzierżawy](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

Katalog dzierżawy przechowuje mapowanie między identyfikator dzierżawy i dzierżawy bazy danych, umożliwiając identyfikator, aby rozpoznać serwera i nazwę bazy danych.  W aplikacji Wingtip SaaS identyfikator dzierżawy jest obliczana jako skrót nazwę dzierżawcy, mimo że można użyć innych systemów.  Gdy aplikacje autonomiczne nie ma potrzeby katalog do zarządzania połączeniami, katalogu może służyć do określania zakresu inne akcje na zestaw baz danych dzierżawy. Na przykład kwerendy elastycznej służy katalogu do określania zestawu baz danych, w których kwerendy są dystrybuowane do raportowania między dzierżawy.

## <a name="elastic-database-client-library"></a>Biblioteka kliencka Elastic Database
W przykładowej aplikacji Wingtip katalogu jest implementowany przez niezależnego fragmentu funkcji do zarządzania [elastycznej bazy danych klienta biblioteki](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library) (EDCL).  Biblioteka włącza aplikację do tworzenia, zarządzania i użyć mapy niezależnego fragmentu, który jest przechowywany w bazie danych. W przykładowym biletów Wingtip katalogu są przechowywane w *katalogu dzierżawcy* bazy danych.  Identyfikator niezależnego fragmentu mapuje klucz dzierżawy, aby niezależnego fragmentu (baza danych), w którym są przechowywane dane tej dzierżawy.  EDCL funkcje zarządzania *mapy globalne niezależnych* przechowywane w tabelach w *katalogu dzierżawcy* bazy danych i *mapy lokalnego niezależnych* przechowywanych w każdej niezależnego fragmentu.

Funkcje EDCL może zostać wywołana z aplikacji i skryptów programu PowerShell do tworzenia i zarządzania nimi wpisy w mapie niezależnego fragmentu. Inne funkcje EDCL może służyć do pobierania zestawu odłamków lub połączenia z poprawną bazą danych dla danego klucza dzierżawy. 
    
> [!IMPORTANT] 
> Nie należy edytować dane w bazie danych katalogu lub mapy lokalnego niezależnego fragmentu w bazach danych dzierżawy bezpośrednio. Bezpośrednie aktualizacje nie są obsługiwane ze względu na duże ryzyko uszkodzenia danych. Zamiast tego można edytować dane mapowania tylko przy użyciu interfejsów API EDCL.

## <a name="tenant-provisioning"></a>Inicjowanie obsługi administracyjnej dzierżawy 
Każdy dzierżawca wymaga nowej grupy zasobów platformy Azure, które należy utworzyć przed zasobów można udostępnić w niej. Po grupie zasobów istnieje, zarządzania zasobami Azure szablonu można wdrożyć składniki aplikacji i bazy danych, a następnie skonfiguruj połączenie z bazą danych. Aby zainicjować schemat bazy danych, szablon można zaimportować plik pliku bacpac.  Alternatywnie można utworzyć bazy danych jako kopię bazy danych "template".  Baza danych jest następnie dalsze zaktualizowany danymi miejscową początkowej i zarejestrowane w wykazie.

## <a name="tutorial"></a>Samouczek

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
* Zainicjuj obsługę wykazu
* Zarejestruj przykładowe bazy dzierżawy wdrożone wcześniej w katalogu
* Udostępnić dodatkowe dzierżawy i zarejestruj go w katalogu

Szablon usługi Azure Resource Manager służy do wdrażania i konfigurowania aplikacji, Utwórz bazę danych dzierżawy, a następnie zaimportuj pliku bacpac pliku można go zainicjować. Żądania importu mogą być kolejkowane na kilka minut, zanim zostanie których wykonano akcje.

Na końcu tego samouczka masz zestawu autonomicznego dzierżawy aplikacji, z każdej bazy danych zarejestrowane w wykazie.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych: 
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Trzy przykładowych aplikacji dzierżawy są wdrażane. Do wdrożenia tych aplikacji w mniej niż 5 minut, zobacz [Wdróż i eksplorowanie wzorzec aplikacji autonomicznej SaaS biletów Wingtip](https://docs.microsoft.com/azure/sql-database/saas-standaloneapp-get-started-deploy).

## <a name="provision-the-catalog"></a>Udostępnianie katalogu
W tym zadaniu Dowiedz się udostępnianie katalogu używane do rejestrowania wszystkich baz danych dzierżawy. Wykonasz następujące zadania: 
* **Udostępniania bazy danych katalogu** przy użyciu szablonu usługi zarządzania zasobów platformy Azure. Baza danych jest zainicjowana przez zaimportowanie pliku pliku bacpac.  
* **Zarejestruj przykładowych aplikacji dzierżawy** wdrożonej wcześniej.  Poszczególni dzierżawcy jest zarejestrowana przy użyciu klucza utworzone na podstawie wartości skrótu nazwę dzierżawcy.  Nazwa dzierżawcy jest również przechowywane w tabeli rozszerzenia w katalogu.

1. Otwórz w programie PowerShell ISE *...\Learning Modules\UserConfig.psm* i zaktualizuj **\<użytkownika\>** wartość używana w przypadku wdrażania trzy przykładowe aplikacje.  **Zapisz plik**.  
1. Otwórz w programie PowerShell ISE *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* i ustaw **$Scenario = 1**. Wdróż katalogu dzierżawcy i zarejestruj wstępnie zdefiniowane dzierżaw.

1. Dodawanie punktu przerwania w dowolnym umieszczając kursor w wierszu, który jest wyświetlany komunikat `& $PSScriptRoot\New-Catalog.ps1`, a następnie naciśnij klawisz **F9**.

    ![ustawienia punktu przerwania śledzenia](media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Uruchom skrypt, naciskając klawisz **F5**. 
1.  Po zatrzymaniu wykonywania skryptu na punkt przerwania, naciśnij klawisz **F11** do kroku do skryptu Catalog.ps1 nowy.
1.  Wykonywanie skryptu, korzystając z menu opcji debugowania, a F10 i F11 śledzenia, do kroku powyżej lub do wywoływać funkcje.
    *   Aby uzyskać więcej informacji na temat debugowania skryptów programu PowerShell, zobacz [porady dotyczące pracy z i debugowania skryptów programu PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

Po ukończeniu działania skryptu katalogu będzie istnieć i dzierżawcami próbki zostanie zarejestrowany. 

Teraz wyglądać na zasoby, które utworzono.

1. Otwórz [portalu Azure](https://portal.azure.com/) i grupy zasobów.  Otwórz **wingtip-sa-katalogu -\<użytkownika\>**  zasobów grupy i zanotuj katalogu serwera i bazy danych.
1. Otwórz w portalu i wybierz bazę danych *Eksploratora danych* z menu po lewej stronie.  Kliknij polecenie logowania, a następnie wprowadź hasło = **P@ssword1**.


1. Eksploruj schemat *tenantcatalog* bazy danych.  
   * Obiekty w `__ShardManagement` schematu są dostarczane przez biblioteki klienta elastycznej bazy danych.
   * `Tenants` Tabeli i `TenantsExtended` widoku są dodane w próbce rozszerzenia, które pokazują, jak można rozszerzyć katalog, aby podać dodatkowe wartości.
1. Uruchom zapytanie, `SELECT * FROM dbo.TenantsExtended`.          

   ![Eksplorator danych](media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Alternatywą wobec za pomocą Eksploratora danych mogą łączyć z bazą danych z programu SQL Server Management Studio. W tym celu należy połączyć się z serwerem wingtip- 

    
    Uwaga nie należy edytować dane bezpośrednio w katalogu — zawsze za pomocą zarządzania niezależnego fragmentu interfejsów API. 

## <a name="provision-a-new-tenant-application"></a>Udostępnianie nowej aplikacji dzierżawy

W tym zadaniu Dowiedz się udostępnianie aplikacji pojedynczej dzierżawy. Wykonasz następujące zadania:  
* **Utwórz nową grupę zasobów** dla dzierżawy. 
* **Udostępnianie aplikacji i baz danych** do nowej grupy zasobów przy użyciu szablonu usługi zarządzania zasobów platformy Azure.  Ta akcja obejmuje podczas inicjowania bazy danych o wspólny schemat i danych referencyjnych przez zaimportowanie pliku pliku bacpac. 
* **Zainicjuj bazę danych z informacjami o podstawowych dzierżawy**. Ta akcja obejmuje określenie typu miejsce, który określa fotografii, używany w tle w swojej witrynie zdarzenia. 
* **Zarejestruj bazy danych w bazie danych katalogu**. 

1. Otwórz w programie PowerShell ISE *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* i ustaw **$Scenario = 2**. Wdróż katalogu dzierżawcy i zarejestruj wstępnie zdefiniowane dzierżawcy

1. Dodaj punkt przerwania w skrypcie umieszczając kursor dowolne miejsce w wierszu 49, który jest wyświetlany komunikat `& $PSScriptRoot\New-TenantApp.ps1`, a następnie naciśnij klawisz **F9**.
1. Uruchom skrypt, naciskając klawisz **F5**. 
1.  Po zatrzymaniu wykonywania skryptu na punkt przerwania, naciśnij klawisz **F11** do kroku do skryptu Catalog.ps1 nowy.
1.  Wykonywanie skryptu, korzystając z menu opcji debugowania, a F10 i F11 śledzenia, do kroku powyżej lub do wywoływać funkcje.

Po zainicjowano dzierżawcy, zdarzenia dzierżawy nowej witryny sieci Web jest otwarty.

   ![klon czerwony wyścigowe](media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Następnie możesz sprawdzić nowe zasoby utworzone w portalu Azure. 

   ![klon czerwony wyścigowych zasobów](media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Aby zatrzymać, rozliczeń, usunąć grupy zasobów ##

Po zakończeniu eksploracji próbki, należy usunąć wszystkie grupy zasobów został utworzony w celu zatrzymania skojarzone rozliczeń.

## <a name="additional-resources"></a>Zasoby dodatkowe

- Aby dowiedzieć się więcej na temat wielodostępnej SaaS bazy danych aplikacji, zobacz [wzorce dla aplikacji SaaS wielodostępne projektowe](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Kolejne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Jak wdrożyć aplikacji autonomicznej Wingtip biletów SaaS.
> * Temat serwerów i baz danych, które tworzą aplikacji.
> * Jak usunąć przykładowych zasobów, aby zatrzymać rozliczeń powiązanego.

Można sprawdzić, jak katalog jest używany do obsługi różnych scenariuszy dzierżawy między za pomocą wersji bazy danych dla dzierżawy [aplikacji SaaS biletów Wingtip](https://docs.microsoft.com/azure/sql-database/saas-dbpertenant-wingtip-app-overview).  
