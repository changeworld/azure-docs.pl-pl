---
title: Wielodostępny samouczek SaaS
description: Aprowizowanie i katalog nowych dzierżaw przy użyciu autonomicznego wzorca aplikacji
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 09/24/2018
ms.openlocfilehash: 02682a18f14e7ecbf5b42783ab84a1b55a4bb77b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133128"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Aprowizowanie i katalog nowych dzierżaw przy użyciu aplikacji na wzorzec SaaS dzierżawcy

W tym artykule opisano inicjowanie obsługi administracyjnej i katalogowanie nowych dzierżaw przy użyciu autonomicznej aplikacji na wzorzec SaaS dzierżawy.
Ten artykuł ma dwie główne części:
* Koncepcyjne omówienie inicjowania obsługi administracyjnej i katalogowania nowych najemców
* Samouczek, który wyróżnia przykładowy kod programu PowerShell, który wykonuje inicjowanie obsługi administracyjnej i katalogowanie
    * Samouczek używa Wingtip Bilety przykładu aplikacji SaaS, dostosowane do autonomicznej aplikacji na wzorzec dzierżawy.

## <a name="standalone-application-per-tenant-pattern"></a>Autonomiczna aplikacja na wzorzec dzierżawy

Autonomiczna aplikacja na wzorzec dzierżawy jest jednym z kilku wzorców dla aplikacji SaaS z wieloma dzierżawcami.  W tym wzorzec autonomiczna aplikacja jest aprowizowana dla każdej dzierżawy. Aplikacja składa się ze składników poziomu aplikacji i bazy danych SQL.  Każdą aplikację dzierżawy można wdrożyć w subskrypcji dostawcy.  Alternatywnie platforma Azure oferuje [program zarządzanych aplikacji,](https://docs.microsoft.com/azure/managed-applications/overview) w którym aplikacja może być wdrażana w subskrypcji dzierżawy i zarządzana przez dostawcę w imieniu dzierżawy.

   ![wzorzec aplikacji na dzierżawę](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

Podczas wdrażania aplikacji dla dzierżawcy, aplikacja i baza danych są aprowizowani w nowej grupie zasobów utworzonej dla dzierżawcy.  Przy użyciu oddzielnych grup zasobów izoluje zasoby aplikacji każdej dzierżawy i umożliwia im zarządzanie niezależnie. W ramach każdej grupy zasobów każde wystąpienie aplikacji jest skonfigurowane tak, aby bezpośrednio uzyskiwało dostęp do odpowiedniej bazy danych.  Ten model połączenia kontrastuje z innymi wzorami, które używają katalogu do brokera połączeń między aplikacją a bazą danych.  A ponieważ nie ma udostępniania zasobów, każda baza danych dzierżawy musi być aprowizowana z wystarczającymi zasobami do obsługi jego szczytowego obciążenia. Ten wzorzec ma tendencję do użycia w aplikacjach SaaS z mniejszą liczbą dzierżawców, gdzie duży nacisk kładzie się na izolację dzierżawy i mniejszy nacisk na koszty zasobów.

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Korzystanie z katalogu dzierżawy z aplikacją na wzorzec dzierżawy

Podczas gdy każda aplikacja i baza danych każdej dzierżawy są w pełni odizolowane, różne scenariusze zarządzania i analizy mogą działać między dzierżawami.  Na przykład zastosowanie zmiany schematu dla nowej wersji aplikacji wymaga zmian w schemacie każdej bazy danych dzierżawy. Scenariusze raportowania i analizy mogą również wymagać dostępu do wszystkich baz danych dzierżawy, niezależnie od tego, gdzie są wdrażane.

   ![wzorzec aplikacji na dzierżawę](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

Katalog dzierżawy przechowuje mapowanie między identyfikatorem dzierżawy a bazą danych dzierżawy, umożliwiając rozpoznawanie identyfikatora na serwerze i nazwę bazy danych.  W aplikacji SaaS Wingtip identyfikator dzierżawy jest obliczany jako skrót nazwy dzierżawy, chociaż można użyć innych schematów.  Podczas gdy autonomiczne aplikacje nie potrzebują katalogu do zarządzania połączeniami, katalog może służyć do zakresu innych akcji do zestawu baz danych dzierżawy. Na przykład elastyczna kwerenda może użyć katalogu do określenia zestawu baz danych, w których kwerendy są dystrybuowane do raportowania między dzierżawami.

## <a name="elastic-database-client-library"></a>Biblioteka klienta elastycznej bazy danych

W aplikacji próbki Wingtip katalog jest implementowany przez funkcje zarządzania niezależnego fragmentu [biblioteki klienta elastycznej bazy danych](sql-database-elastic-database-client-library.md) (EDCL).  Biblioteka umożliwia aplikacji tworzenie, zarządzanie i używanie mapy niezależnego fragmentu, która jest przechowywana w bazie danych. W przykładzie Bilety Wingtip katalog jest przechowywany w bazie danych *katalogu dzierżawy.*  Fragment jest mapowany klucz dzierżawy do niezależnego fragmentu (bazy danych), w którym są przechowywane dane tej dzierżawy.  Funkcje EDCL zarządzają *mapą globalnego niezależnego fragmentu* przechowywaną w tabelach w bazie danych *katalogu dzierżawy* i *mapą lokalnego niezależnego fragmentu* przechowywanej w każdym niezależnuzyku.

Funkcje EDCL mogą być wywoływane z aplikacji lub skryptów programu PowerShell w celu tworzenia wpisów na mapie niezależnego fragmentu i zarządzania nimi. Inne funkcje EDCL może służyć do pobierania zestawu fragmentów lub połączyć się z poprawną bazą danych dla danego klucza dzierżawy.

> [!IMPORTANT]
> Nie należy edytować danych w bazie danych wykazu lub lokalnej mapy niezależnego fragmentu w bazach danych dzierżawy bezpośrednio. Aktualizacje bezpośrednie nie są obsługiwane ze względu na wysokie ryzyko uszkodzenia danych. Zamiast tego należy edytować dane mapowania przy użyciu tylko interfejsów API EDCL.

## <a name="tenant-provisioning"></a>Inicjowanie obsługi administracyjnej dzierżawy

Każda dzierżawa wymaga nowej grupy zasobów platformy Azure, która musi zostać utworzona, zanim zasoby będą mogły być aprowizacji w nim. Gdy grupa zasobów istnieje, szablon usługi Azure Resource Management może służyć do wdrażania składników aplikacji i bazy danych, a następnie skonfigurować połączenie z bazą danych. Aby zainicjować schemat bazy danych, szablon może zaimportować plik bacpac.  Alternatywnie baza danych może być utworzona jako kopia bazy danych "szablonu".  Baza danych jest następnie dalej aktualizowana o początkowe dane o miejscu i zarejestrowana w katalogu.

## <a name="tutorial"></a>Samouczek

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

* Aprowizuj katalog
* Rejestrowanie przykładowych baz danych dzierżawy wdrożonych wcześniej w katalogu
* Udostępnij dodatkową dzierżawę i zarejestruj ją w katalogu

Szablon usługi Azure Resource Manager służy do wdrażania i konfigurowania aplikacji, tworzenia bazy danych dzierżawy, a następnie importowania pliku bacpac, aby ją zainicjować. Żądanie importu może być umieszczane w kolejce przez kilka minut, zanim zostanie podjęta akcja.

Na końcu tego samouczka masz zestaw autonomicznych aplikacji dzierżawy, z każdej bazy danych zarejestrowanej w katalogu.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Trzy przykładowe aplikacje dzierżawy są wdrażane. Aby wdrożyć te aplikacje w mniej niż pięć minut, zobacz [Wdrażanie i eksplorowanie wzorca aplikacji autonomicznej SaaS.](saas-standaloneapp-get-started-deploy.md)

## <a name="provision-the-catalog"></a>Nadaj katalog

W tym zadaniu dowiesz się, jak aprowizować katalog używany do rejestrowania wszystkich baz danych dzierżawy. Wykonasz następujące zadania:

* **Aprowizuj bazę danych wykazu** przy użyciu szablonu zarządzania zasobami platformy Azure. Baza danych jest inicjowana przez importowanie pliku bacpac.
* **Zarejestruj przykładowe aplikacje dzierżawy,** które zostały wdrożone wcześniej.  Każdy dzierżawca jest zarejestrowany przy użyciu klucza skonstruowanego z skrótu nazwy dzierżawy.  Nazwa dzierżawy jest również przechowywana w tabeli rozszerzeń w katalogu.

1. W programie PowerShell ISE otwórz *...\Learning Modules\UserConfig.psm* i zaktualizuj ** \<\> ** wartość użytkownika do wartości użytej podczas wdrażania trzech przykładowych aplikacji.  **Zapisz plik**.
1. W programie PowerShell ISE otwórz *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* i ustaw **$Scenario = 1**. Wdrażanie katalogu dzierżawy i rejestrowanie wstępnie zdefiniowanych dzierżawców.

1. Dodaj punkt przerwania, umieszczając kursor w dowolnym `& $PSScriptRoot\New-Catalog.ps1`miejscu w wierszu, który mówi, a następnie naciśnij **klawisz F9**.

    ![ustawianie punktu przerwania dla śledzenia](media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Uruchom skrypt, naciskając **klawisz F5**.
1.  Po zatrzymaniu wykonywania skryptu w punkcie przerwania naciśnij **klawisz F11,** aby przejść do skryptu New-Catalog.ps1.
1.  Śledzenie wykonywania skryptu przy użyciu opcji menu debugowania, F10 i F11, aby przejść przez lub do wywoływanych funkcji.
    *   Aby uzyskać więcej informacji na temat debugowania skryptów programu PowerShell, zobacz [Porady dotyczące pracy z skryptami programu PowerShell i debugowania](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

Po zakończeniu skryptu katalog będzie istnieć i wszystkie przykładowe dzierżawy zostaną zarejestrowane.

Teraz spójrz na utworzone zasoby.

1. Otwórz [witrynę Azure portal](https://portal.azure.com/) i przejrzyj grupy zasobów.  Otwórz grupę zasobów **\<\> użytkownika wingtip-sa-catalog-** i zanotuj serwer katalogu i bazę danych.
1. Otwórz bazę danych w portalu i wybierz *Eksploratora danych* z menu po lewej stronie.  Kliknij polecenie Zaloguj, a następnie wprowadź hasło = **P\@ssword1**.


1. Eksploruj schemat bazy danych *tenantcatalog.*
   * Obiekty w `__ShardManagement` schemacie są dostarczane przez bibliotekę klienta elastycznej bazy danych.
   * Tabela `Tenants` `TenantsExtended` i widok są rozszerzenia dodane w przykładzie, które pokazują, jak można rozszerzyć katalog, aby zapewnić dodatkową wartość.
1. Uruchom kwerendę, `SELECT * FROM dbo.TenantsExtended`.

   ![Eksplorator danych](media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Alternatywą dla korzystania z Eksploratora danych można połączyć się z bazą danych z programu SQL Server Management Studio. Aby to zrobić, połącz się z


    Należy zauważyć, że nie należy edytować dane bezpośrednio w katalogu — zawsze należy używać interfejsów API zarządzania fragmentami.

## <a name="provision-a-new-tenant-application"></a>Aprowizowanie nowej aplikacji dzierżawy

W tym zadaniu dowiesz się, jak aprowizować aplikację jednej dzierżawy. Wykonasz następujące zadania:

* **Utwórz nową grupę zasobów** dla dzierżawcy.
* **Aprowizuj aplikację i bazę danych** do nowej grupy zasobów przy użyciu szablonu zarządzania zasobami platformy Azure.  Ta akcja obejmuje inicjowanie bazy danych ze wspólnym schematem i danymi referencyjnymi przez importowanie pliku bacpac.
* **Zainicjować bazę danych z podstawowymi informacjami o dzierżawie**. Akcja ta obejmuje określenie typu miejsca, które określa fotografię używaną jako tło na stronie internetowej wydarzenia.
* **Zarejestruj bazę danych w bazie danych katalogu**.

1. W programie PowerShell ISE otwórz *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* i ustaw **$Scenario = 2**. Wdrażanie katalogu dzierżawy i rejestrowanie wstępnie zdefiniowanych dzierżawców

1. Dodaj punkt przerwania w skrypcie, umieszczając kursor w `& $PSScriptRoot\New-TenantApp.ps1`dowolnym miejscu w wierszu 49, który mówi, , a następnie naciśnij **klawisz F9**.
1. Uruchom skrypt, naciskając **klawisz F5**.
1.  Po zatrzymaniu wykonywania skryptu w punkcie przerwania naciśnij **klawisz F11,** aby przejść do skryptu New-Catalog.ps1.
1.  Śledzenie wykonywania skryptu przy użyciu opcji menu debugowania, F10 i F11, aby przejść przez lub do wywoływanych funkcji.

Po dzierżawy został aprowizowany, nowa dzierżawa zdarzenia w witrynie sieci Web jest otwarty.

   ![czerwony klon wyścigi](media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Następnie można sprawdzić nowe zasoby utworzone w witrynie Azure portal.

   ![czerwony klon wyścigi zasobów](media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Aby zatrzymać rozliczenia, usuń grupy zasobów

Po zakończeniu eksplorowania próbki usuń wszystkie grupy zasobów utworzone w celu zatrzymania skojarzonego rozliczeń.

## <a name="additional-resources"></a>Zasoby dodatkowe

- Aby dowiedzieć się więcej o wielodostępnych aplikacjach bazy danych SaaS, zobacz [Wzorce projektowania dla aplikacji SaaS z wieloma dzierżawami.](saas-tenancy-app-design-patterns.md)

## <a name="next-steps"></a>Następne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Jak wdrożyć wingtip bilety SaaS autonomicznej aplikacji.
> * Informacje o serwerach i bazach danych, które tworzą aplikację.
> * Jak usunąć przykładowe zasoby, aby zatrzymać powiązane rozliczenia.

Można eksplorować, jak katalog jest używany do obsługi różnych scenariuszy między dzierżawców przy użyciu bazy danych na dzierżawcę wersji [aplikacji SaaS Wingtip Bilety.](saas-dbpertenant-wingtip-app-overview.md)
