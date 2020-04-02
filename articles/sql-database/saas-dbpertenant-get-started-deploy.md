---
title: Samouczek SaaS bazy danych na dzierżawcę
description: Wdrażanie i eksplorowanie wielodostępnych aplikacji SaaS Wingtip Tickets, która demonstruje wzorzec bazy danych na dzierżawę i inne wzorce SaaS przy użyciu usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 3182daa4ebf3becc824b600d1e487e12b875b275
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529650"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Wdrażanie i eksplorowanie wielodostępnejastosyny SaaS, który używa wzorca bazy danych na dzierżawę z bazy danych SQL

W tym samouczku można wdrożyć i eksplorować Wingtip Bilety SaaS bazy danych na dzierżawcę aplikacji (Wingtip). Aplikacja używa wzorca bazy danych na dzierżawę do przechowywania danych wielu dzierżawców. Aplikacja została zaprojektowana do prezentowanie funkcji usługi Azure SQL Database, które upraszczają sposób włączania scenariuszy SaaS.

Pięć minut po **wybraniu opcji Wdrażanie na platformie Azure**masz wielodostępną aplikację SaaS. Aplikacja zawiera bazę danych SQL, która działa w chmurze. Aplikacja jest wdrażana z trzech przykładowych dzierżaw, każdy z własną bazą danych. Wszystkie bazy danych są wdrażane w puli elastycznej SQL. Aplikacja jest wdrażana w ramach subskrypcji platformy Azure. Masz pełny dostęp do eksplorowania i pracy z poszczególnymi składnikami aplikacji. Kod źródłowy aplikacji C# i skrypty zarządzania są dostępne w [repozytorium WingtipTicketsSaaS-DbPerTenant GitHub][github-wingtip-dpt].

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Jak wdrożyć aplikację SaaS Wingtip.
> - Gdzie można uzyskać kod źródłowy aplikacji i skrypty zarządzania.
> - Informacje o serwerach, pulach i bazach danych, które tworzą aplikację.
> - Jak dzierżawcy są mapowane do swoich danych z *katalogu*.
> - Jak aprowizować nową dzierżawę.
> - Jak monitorować aktywność dzierżawy w aplikacji.

[Seria powiązanych samouczków](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) oferuje eksplorowanie różnych wzorców projektowania i zarządzania SaaS. Samouczki kompilacji poza tym początkowym wdrożeniem. Korzystając z samouczków, można sprawdzić skrypty dostarczone, aby zobaczyć, jak różne wzorce SaaS są implementowane. Skrypty pokazują, jak funkcje bazy danych SQL upraszczają tworzenie aplikacji SaaS.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, upewnij się, że jest zainstalowany program Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Wdrażanie aplikacji SaaS Wingtip Tickets

### <a name="plan-the-names"></a>Planowanie nazw

W krokach tej sekcji należy podać wartość użytkownika, która jest używana, aby upewnić się, że nazwy zasobów są globalnie unikatowe. Należy również podać nazwę dla grupy zasobów, która zawiera wszystkie zasoby utworzone przez wdrożenie aplikacji. Dla fikcyjnej osoby o imieniu Ann Finley, proponujemy:

- **Użytkownik**: *af1* składa się z inicjałów Ann Finley plus cyfra. Jeśli aplikacja zostanie wdrożona po raz drugi, użyj innej wartości. Przykładem jest af2.
- **Grupa zasobów**: *wingtip-dpt-af1* wskazuje, że jest to aplikacja bazy danych na dzierżawcę. Dołącz nazwę użytkownika af1, aby skorelować nazwę grupy zasobów z nazwami zasobów, które zawiera.

Wybierz swoje imiona i nazwiska już teraz i zapisz je.

### <a name="steps"></a>Kroki

1. Aby otworzyć szablon wdrożenia bazy danych SaaS dla bazy danych Wingtip na dzierżawę w witrynie Azure portal, wybierz pozycję **Wdrażanie na platformie Azure**.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Wprowadź wartości w szablonie dla wymaganych parametrów.

    > [!IMPORTANT]
    > Niektóre uwierzytelniania i zapory serwera są celowo niezabezpieczone do celów demonstracyjnych. Zaleca się utworzenie nowej grupy zasobów. Nie używaj istniejących grup zasobów, serwerów ani pul. Nie używaj tej aplikacji, skryptów ani żadnych wdrożonych zasobów w produkcji. Usuń tę grupę zasobów po zakończeniu pracy z aplikacją, aby zatrzymać powiązane rozliczenia.

    - **Grupa zasobów**: Wybierz **pozycję Utwórz nowy**i podaj unikatową nazwę wybraną wcześniej dla grupy zasobów.
    - **Lokalizacja**: Wybierz lokalizację z listy rozwijanej.
    - **Użytkownik**: Użyj wartości nazwy użytkownika wybranej wcześniej.

1. Wdrażanie aplikacji.

    a. Wybierz opcję wyrażenia zgody na warunki i postanowienia.

    b. Wybierz pozycję **Kup**.

1. Aby monitorować stan wdrożenia, wybierz **pozycję Powiadomienia** (ikona dzwonka po prawej stronie pola wyszukiwania). Wdrożenie aplikacji SaaS Wingtip Tickets trwa około pięciu minut.

   ![Wdrożenie zakończyło się pomyślnie](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Pobieranie i odblokowywanie skryptów zarządzania biletami Wingtip

Podczas wdrażania aplikacji pobierz kod źródłowy i skrypty zarządzania.

> [!IMPORTANT]
> Zawartość wykonywalna (skrypty i biblioteki DLL) może być blokowana przez system Windows, gdy pliki zip są pobierane z zewnętrznego źródła i wyodrębniane. Postępuj zgodnie z instrukcjami, aby odblokować plik zip przed wyodrębnieniem skryptów. Odblokowanie zapewnia, że skrypty są dozwolone do uruchomienia.

1. Przejdź do [repozytorium WingtipTicketsSaaS-DbPerTenant GitHub][github-wingtip-dpt].
1. Wybierz przycisk **Clone or download** (Sklonuj lub pobierz).
1. Wybierz **pozycję Pobierz kod ZIP**, a następnie zapisz plik.
1. Kliknij prawym przyciskiem myszy plik **WingtipTicketsSaaS-DbPerTenant-master.zip,** a następnie wybierz polecenie **Właściwości**.
1. Na karcie **Ogólne** wybierz pozycję **Odblokuj** > **zastosuj**.
1. Wybierz **przycisk OK**i wyodrębnij pliki

Skrypty znajdują się w ... \\WingtipTicketsSaaS-DbPerTenant-master\\Folder modułów szkoleniowych.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Aktualizowanie pliku konfiguracji użytkownika dla tego wdrożenia

Przed uruchomieniem skryptów należy zaktualizować grupę zasobów i wartości użytkownika w pliku Konfiguracyjny użytkownika. Ustaw te zmienne na wartości używane podczas wdrażania.

1. W programie PowerShell ISE otwórz ... \\Moduły\\szkoleniowe**UserConfig.psm1**
1. Zaktualizuj nazwę i **nazwę** **resourcegroup** z określonymi wartościami dla wdrożenia (tylko w wierszach 10 i 11).
1. Zapisz zmiany.

Te wartości są odwoływane w prawie każdym skrypcie.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aplikacja prezentuje miejsca, w których odbywają się wydarzenia. Rodzaje miejsc obejmują sale koncertowe, kluby jazzowe i kluby sportowe. W Wingtip Tickets miejsca są rejestrowane jako najemcy. Bycie najemcą daje miejsce łatwy sposób na listę wydarzeń i sprzedawać bilety swoim klientom. Każde miejsce otrzymuje spersonalizowaną stronę internetową, aby wymienić swoje wydarzenia i sprzedawać bilety.

Wewnętrznie w aplikacji każdy dzierżawca pobiera bazę danych SQL wdrożoną w puli elastycznej SQL.

Strona centrum **zdarzeń** centralnej zawiera listę łączy do dzierżaw w ramach wdrożenia.

1. Użyj adresu URL, aby otworzyć Centrum http://events.wingtip-dpt.&ltzdarzeń&gt;w przeglądarce sieci Web: ;user .trafficmanager.net. Zastąp &lt;użytkownika&gt; wartością użytkownika wdrożenia.

    ![Centrum zdarzeń](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Wybierz **Fabrikam Jazz Club** w Centrum wydarzeń.

    ![Zdarzenia](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Aplikacja Wingtip używa [*usługi Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md) do kontrolowania dystrybucji żądań przychodzących. Adres URL dostępu do strony zdarzeń dla określonej dzierżawy używa następującego formatu:

- http://events.wingtip-dpt.&lt;użytkownik&gt;.trafficmanager.net/fabrikamjazzclub

    Części poprzedniego formatu są wyjaśnione w poniższej tabeli.

    | Część adresu URL        | Opis       |
    | :-------------- | :---------------- |
    | wydarzenia.wingtip-dpt | Zdarzenia części aplikacji Wingtip.<br /><br /> *-dpt* odróżnia *implementacji bazy danych na dzierżawcę* Wingtip Tickets od innych implementacji. Przykładami są *implementacje pojedynczej* aplikacji na dzierżawę *(-sa)* lub *wielodostępne bazy danych* (*-mt).* |
    | . * &lt;użytkownik&gt;* | *af1* w przykładzie. |
    | trafficmanager.net/ .trafficmanager.net/ | Traffic Manager, podstawowy adres URL. |
    | klub fabrikamjazzc | Identyfikuje dzierżawcę o nazwie Fabrikam Jazz Club. |
    | &nbsp; | &nbsp; |

- Nazwa dzierżawy jest analizowana z adresu URL przez aplikację zdarzeń.
- Nazwa dzierżawy jest używana do tworzenia klucza.
- Klucz jest używany do uzyskiwania dostępu do katalogu w celu uzyskania lokalizacji bazy danych dzierżawcy.
  - Katalog jest implementowany przy użyciu *zarządzania mapami niezależnego fragmentu*.
- Centrum zdarzeń używa rozszerzonych metadanych w katalogu do konstruowania adresów URL stron listy zdarzeń dla każdej dzierżawy.

W środowisku produkcyjnym zazwyczaj tworzysz rekord DNS CNAME, aby [*skierować firmową domenę*](../traffic-manager/traffic-manager-point-internet-domain.md) internetową na nazwę DNS Menedżera ruchu.

> [!NOTE]
> To może nie być od razu oczywiste, co korzystanie z menedżera ruchu jest w tym samouczku. Celem tej serii samouczków jest zaprezentowanie wzorców, które mogą obsługiwać skalę złożonego środowiska produkcyjnego. W takim przypadku na przykład można mieć wiele aplikacji sieci web dystrybuowane na całym świecie, współlokowane z bazami danych i trzeba będzie menedżera ruchu do trasy między tymi wystąpieniami.
Inny zestaw samouczków, który ilustruje korzystanie z menedżera ruchu jednak są [geo-restore](saas-dbpertenant-dr-geo-restore.md) i [samouczki replikacji geograficznej.](saas-dbpertenant-dr-geo-replication.md) W tych samouczkach menedżer ruchu jest używany do pomocy w przełączeniu się do wystąpienia odzyskiwania aplikacji SaaS w przypadku regionalnej awarii.

## <a name="start-generating-load-on-the-tenant-databases"></a>Rozpoczęcie generowanie obciążenia baz danych dzierżawy

Teraz, gdy aplikacja jest wdrażana, umieśćmy ją do pracy.

*Skrypt Demo-LoadGenerator* PowerShell uruchamia obciążenie, które działa dla wszystkich baz danych dzierżawy. Rzeczywiste obciążenie wielu aplikacji SaaS jest sporadyczne i nieprzewidywalne. Aby symulować tego typu obciążenia, generator generuje obciążenie z losowo kolce lub wybuchy aktywności na każdej dzierżawy. Wybuchy występują w losowych odstępach czasu. To trwa kilka minut dla wzorca obciążenia, aby pojawić się. Pozwól generatorowi działać przez co najmniej trzy lub cztery minuty przed monitorowaniem obciążenia.

1. W programie PowerShell ISE otwórz ... \\Moduły\\szkoleniowe\\Utilities*Demo-LoadGenerator.ps1* skrypt.
2. Naciśnij klawisz F5, aby uruchomić skrypt i uruchomić generator obciążenia. Pozostaw domyślne wartości parametrów na razie.
3. Zaloguj się do konta platformy Azure i wybierz subskrypcję, której chcesz użyć, jeśli to konieczne.

Skrypt generatora obciążenia uruchamia zadanie w tle dla każdej bazy danych w katalogu, a następnie zatrzymuje. Po ponownym uruchomieniu skryptu generatora obciążenia, zatrzymuje wszystkie zadania w tle, które są uruchomione przed uruchomieniem nowych.

### <a name="monitor-the-background-jobs"></a>Monitorowanie zadań w tle

Jeśli chcesz kontrolować i monitorować zadania w tle, użyj następujących poleceń cmdlet:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

### <a name="demo-loadgeneratorps1-actions"></a>Akcje Demo-LoadGenerator.ps1

*Demo-LoadGenerator.ps1* naśladuje aktywne obciążenie transakcji klientów. W poniższych krokach opisano sekwencję akcji inicjowanych *przez Demo-LoadGenerator.ps1:*

1. *Demo-LoadGenerator.ps1* uruchamia *LoadGenerator.ps1* na pierwszym planie.

    - Oba pliki .ps1 są przechowywane w\\folderach\\Narzędzia modułów szkoleniowych.

2. *LoadGenerator.ps1* pętli za pośrednictwem wszystkich baz danych dzierżawy w katalogu.

3. *LoadGenerator.ps1* uruchamia zadanie programu PowerShell w tle dla każdej bazy danych dzierżawy:

    - Domyślnie zadania w tle są uruchamiane przez 120 minut.
    - Każde zadanie powoduje obciążenie oparte na procesorze CPU w jednej bazie danych dzierżawy przez wykonanie *sp_CpuLoadGenerator*. Intensywność i czas trwania obciążenia zależy `$DemoScenario`od .
    - *sp_CpuLoadGenerator* pętli wokół instrukcji SQL SELECT, która powoduje duże obciążenie procesora CPU. Przedział czasu między problemami SELECT różni się w zależności od wartości parametrów, aby utworzyć sterowane obciążenie procesora CPU. Poziomy obciążenia i interwały są losowo, aby symulować bardziej realistyczne obciążenia.
    - Ten plik .sql jest przechowywany w obszarze *WingtipTenantDB\\dbo\\StoredProcedures\\*.

4. Jeśli `$OneTime = $false`generator obciążenia uruchamia zadania w tle, a następnie kontynuuje uruchamianie. Co 10 sekund monitoruje dla wszystkich nowych dzierżaw, które są aprowizowane. Jeśli ustawisz, `$OneTime = $true`LoadGenerator uruchamia zadania w tle, a następnie przestaje działać na pierwszym planie. W tym samouczku, zostaw `$OneTime = $false`.

   Jeśli chcesz zatrzymać lub ponownie uruchomić generator obciążenia, należy użyć funkcji Ctrl-C lub Stop Operation Ctrl-Break.

   Jeśli pozostawisz generator obciążenia uruchomiony na pierwszym planie, użyj innego wystąpienia programu PowerShell ISE, aby uruchomić inne skrypty programu PowerShell.

&nbsp;

Przed kontynuowaniem następnej sekcji pozostaw generator obciążenia uruchomiony w stanie wywoływania zadania.

## <a name="provision-a-new-tenant"></a>Aprowizacja nowej dzierżawy

Początkowe wdrożenie tworzy trzy przykładowe dzierżawy. Teraz należy utworzyć inną dzierżawę, aby zobaczyć wpływ na wdrożoną aplikację. W aplikacji Wingtip przepływ pracy w celu aprowizowania nowych dzierżaw jest wyjaśniony w [samouczek aprowizowania i katalogu](saas-dbpertenant-provision-and-catalog.md). W tej fazie należy utworzyć nową dzierżawę, która trwa mniej niż jedną minutę.

1. Otwórz nową program PowerShell ISE.
2. Otwórz... \\Moduły szkoleniowe\Aprowizowanie i katalog\\*Demo-ProvisionAndCatalog.ps1*.
3. Aby uruchomić skrypt, naciśnij klawisz F5. Zostaw teraz wartości domyślne.

   > [!NOTE]
   > Wiele skryptów SaaS Wingtip używa *$PSScriptRoot* do przeglądania folderów w celu wywoływania funkcji w innych skryptach. Ta zmienna jest obliczana tylko wtedy, gdy pełny skrypt jest wykonywany przez naciśnięcie klawisza F5.Wyróżnianie i uruchamianie zaznaczenia za pomocą F8 może spowodować błędy. Aby uruchomić skrypty, naciśnij klawisz F5.

Nowa baza danych dzierżawy jest:

- Utworzony w puli elastycznej SQL.
- Zainicjowany.
- Zarejestrowany w katalogu.

Po pomyślnym inicjowaniu obsługi administracyjnej witryny *zdarzenia* nowej dzierżawy pojawia się w przeglądarce.

![Nowa dzierżawa](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Odśwież Centrum zdarzeń, aby nowa dzierżawa pojawiła się na liście.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Sprawdzanie działania serwerów, pul i baz danych dzierżaw

Teraz, gdy rozpoczęto uruchamianie obciążenia względem kolekcji dzierżawców, przyjrzyjmy się niektórym zasobom, które zostały wdrożone.

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do listy serwerów SQL. Następnie otwórz **serwer catalog-dpt-&lt;USER.&gt; **
    - Serwer wykazu zawiera dwie bazy danych, **tenantcatalog** i **basetenantdb** (baza danych szablonów, która jest kopiowana w celu utworzenia nowych dzierżaw).

   ![Bazy danych](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Wróć do listy serwerów SQL.

3. Otwórz **serwer tenants1-dpt-&lt;USER,&gt; ** który przechowuje bazy danych dzierżawy.

4. Zobacz następujące elementy:

    - Każda baza danych dzierżawy jest **bazą** danych elastic standard w puli standardowej 50 eDTU.
    - Red Maple Racing bazy danych jest dzierżawy bazy danych, które zostały zainicjowane wcześniej.

   ![Serwer z bazami danych](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Monitorowanie puli

Po *loadgenerator.ps1* działa przez kilka minut, wystarczającej ilości danych powinny być dostępne, aby rozpocząć patrząc na niektóre możliwości monitorowania. Te możliwości są wbudowane w pule i bazy danych.

Przejdź do **serwera&lt;tenants1-dpt-&gt;user**i wybierz **pozycję Pool1,** aby wyświetlić wykorzystanie zasobów dla puli. Na poniższych wykresach generator obciążenia działał przez jedną godzinę.

   ![Pula monitorów](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- Pierwszy wykres, oznaczone **wykorzystaniem zasobów,** pokazuje wykorzystanie eDTU puli.
- Drugi wykres przedstawia wykorzystanie eDTU pięciu najbardziej aktywnych baz danych w puli.

Dwa wykresy ilustrują, że pule elastyczne i baza danych SQL są dobrze dostosowane do nieprzewidywalnych obciążeń aplikacji SaaS. Wykresy pokazują, że cztery bazy danych są pęknięcie do aż 40 eDTU, a jednak wszystkie bazy danych są wygodnie obsługiwane przez pulę 50-eDTU. Pula 50 eDTU może obsługiwać jeszcze większe obciążenia. Jeśli bazy danych są aprowizacji jako pojedyncze bazy danych, każdy z nich musi być S2 (50 DTU) do obsługi serii. Koszt czterech pojedynczych baz danych S2 jest prawie trzykrotnie droższy od puli. W rzeczywistych sytuacjach klienci bazy danych SQL uruchamiają do 500 baz danych w 200 pulach eDTU. Aby uzyskać więcej informacji, zobacz [samouczek monitorowania wydajności](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

- Aby uzyskać więcej informacji, zobacz dodatkowe [samouczki, które opierają się na aplikacji bazy danych SaaS Wingtip na dzierżawcę](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- Aby dowiedzieć się więcej o pulach elastycznych, zobacz [Co to jest pula elastyczna SQL platformy Azure?](sql-database-elastic-pool.md).
- Aby dowiedzieć się więcej o zadaniach elastycznych, zobacz [Zarządzanie skalowanych w poziomie baz danych w chmurze.](elastic-jobs-overview.md)
- Aby dowiedzieć się więcej o wielodostępnych aplikacjach SaaS, zobacz [Wzorce projektowania wielodostępnych aplikacji SaaS](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Następne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Jak wdrożyć aplikację SaaS bilety Wingtip.
> - Informacje o serwerach, pulach i bazach danych, które tworzą aplikację.
> - Jak dzierżawcy są mapowane do swoich danych z *katalogu*.
> - Jak aprowizować nowych dzierżawców.
> - Jak wyświetlić wykorzystanie puli do monitorowania aktywności dzierżawy.
> - Jak usunąć przykładowe zasoby, aby zatrzymać powiązane rozliczenia.

Następnie spróbuj [samouczek aprowizować i katalogować](saas-dbpertenant-provision-and-catalog.md).

<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant
