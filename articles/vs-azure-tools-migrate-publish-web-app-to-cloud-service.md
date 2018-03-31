---
title: Jak migrować i publikowanie aplikacji sieci Web do usługi w chmurze platformy Azure w programie Visual Studio | Dokumentacja firmy Microsoft
description: Dowiedz się, jak migracji i publikowanie aplikacji sieci web do usługi w chmurze platformy Azure przy użyciu programu Visual Studio
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 9394adfd-a645-4664-9354-dd5df08e8c91
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/10/2017
ms.author: ghogen
ms.openlocfilehash: 682be267bbb1cf495d85af5786309a98f885fc23
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-migrate-and-publish-a-web-application-to-an-azure-cloud-service-from-visual-studio"></a>Porady: migracja i publikowanie aplikacji sieci Web do usługi w chmurze platformy Azure w programie Visual Studio

Uwzględnienie hostingu usług i możliwość skalowania Azure, możesz chcieć migrację i wdrażanie aplikacji sieci web do usługi w chmurze Azure. Wymagane są tylko minimalne zmiany. W tym artykule omówiono wdrażanie usług w chmurze. usługi aplikacji, zobacz [wdrażanie aplikacji sieci web w usłudze Azure App Service](app-service/app-service-deploy-local-git.md).

> [!Important]
> Ta migracja jest obsługiwana tylko dla określonych projektów programu ASP.NET, Silverlight, usługi WCF i przepływu pracy WCF. Nie jest obsługiwane dla projektów platformy ASP.NET Core. Zobacz [obsługiwane szablony projektów](#supported-project-templates).

## <a name="migrate-a-project-to-cloud-services"></a>Migrowanie projektu do usługi w chmurze

1. Kliknij prawym przyciskiem myszy projekt aplikacji sieci web i wybierz **Konwertuj > Konwertuj na projekt usługi w chmurze Microsoft Azure**. (Należy pamiętać, że to polecenie nie jest wyświetlane, jeśli masz już projekt roli sieci web w rozwiązaniu).
1. Visual Studio tworzy projekt usługi w chmurze w rozwiązania zawierającego roli sieci web. Nazwa tego projektu jest taka sama jako projektu aplikacji za pomocą plus sufiks `.Azure`.
1. Ustawia również programu Visual Studio **Kopiuj lokalnie** właściwości na wartość true dla dowolne zestawy, które są wymagane dla MVC 2, MVC 3, MVC 4 i aplikacji biznesowych Silverlight. Ta właściwość dodaje te zestawy do pakietu usługi, który jest używany do wdrożenia.

   > [!Important]
   > Jeśli masz inne zestawy lub pliki, które są wymagane dla tej aplikacji sieci web, należy ręcznie ustawić właściwości dla tych plików. Aby uzyskać informacje na temat ustawiania tych właściwości, zobacz [obejmują pliki w pakiecie usługi](#include-files-in-the-service-package).

### <a name="errors-and-warnings"></a>Błędy i ostrzeżenia

Wszelkie ostrzeżenia lub błędy, które występują wskazują na problemy, aby rozwiązać problem, przed wdrożeniem na platformie Azure, takich jak brak zestawów.

Jeśli skompilować aplikację, uruchom lokalnie przy użyciu emulatora obliczeń lub opublikowania go w usłudze Azure, zostanie wyświetlony błąd: "określona ścieżka i nazwa pliku jest zbyt długa." Ten błąd wskazuje, że długość nazwy FQDN projektu platformy Azure przekracza 146 znaków. Aby rozwiązać ten problem, należy przenieść rozwiązania do innego folderu o krótszej ścieżce.

Aby uzyskać więcej informacji o sposobie traktuje wszystkie ostrzeżenia jako błędy, zobacz [skonfigurować projekt usługi w chmurze Azure z programem Visual Studio](vs-azure-tools-configuring-an-azure-project.md).

### <a name="test-the-migration-locally"></a>Testowanie migracji lokalnie

1. W programie Visual Studio **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt usługi w chmurze dodany i wybierz **Ustaw jako projekt startowy**.
1. Wybierz **Debuguj > Rozpocznij debugowanie** (F5), można uruchomić debugowania środowiska platformy Azure. To środowisko zawiera w szczególności emulacji różne usługi platformy Azure.

### <a name="use-an-azure-sql-database-for-your-application"></a>Użyj bazy danych SQL Azure dla aplikacji

Jeśli parametry połączenia aplikacji sieci web, który korzysta z lokalną bazą danych programu SQL Server, należy zamiast tego należy zmigrować bazę danych do bazy danych SQL Azure i zaktualizować parametry połączenia. Instrukcje z tego procesu można znaleźć w następujących tematach:

- [Migracja bazy danych programu SQL Server z bazą danych SQL w chmurze](sql-database/sql-database-cloud-migrate.md)
- [.NET (C#) za pomocą programu Visual Studio do nawiązywania połączeń i zapytania i bazy danych Azure SQL](sql-database/sql-database-connect-query-dotnet-visual-studio.md).

## <a name="publish-the-application-to-azure-cloud-service"></a>Publikowanie aplikacji do usługi w chmurze Azure

1. Tworzenie konta usługi i Magazyn niezbędne chmury w subskrypcji platformy Azure zgodnie z opisem w [przygotowanie do publikowania lub wdrażania aplikacji Azure z programu Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).
1. W programie Visual Studio, kliknij prawym przyciskiem myszy projekt aplikacji, a następnie wybierz **publikowanie do systemu Microsoft Azure...**  (który różni się od polecenie "Publikuj...".).
1. W **publikowanie aplikacji platformy Azure** wyświetlony, zaloguj się przy użyciu konta z subskrypcją platformy Azure, a następnie wybierz **Dalej >**.
1. W **Ustawienia > typowe ustawienia** , a następnie wybierz docelowej usługi w chmurze z **usługi w chmurze** listy rozwijanej, wraz z wybranego środowiska i konfiguracji. 
1. W **Ustawienia > Ustawienia zaawansowane**, wybierz konto magazynu do użycia, a następnie wybierz **Dalej >**.
1. W **diagnostyki**, wybierz, czy chcesz wysłać informacje do usługi Application Insights.
1. Wybierz **Dalej >** Aby wyświetlić podsumowanie, zaznacz opcję **publikowania** rozpocząć wdrażanie.
1. Visual Studio zostanie otwarte okno Dziennik aktywności, gdzie można śledzić postępu:

    ![VST_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. (Opcjonalnie) Aby anulować proces wdrażania, kliknij prawym przyciskiem myszy pozycji w dzienniku aktywności i wybierz polecenie **Anuluj i Usuń**. To polecenie powoduje zatrzymanie procesu wdrażania i usuwa środowiska wdrażania z platformy Azure. Uwaga: Aby usunąć to środowisko wdrażania po jej wdrożeniu, należy użyć [portalu Azure](https://portal.azure.com).
1. (Opcjonalnie) Po uruchomiona wystąpienia roli, Visual Studio automatycznie wyświetlany środowiska wdrażania w **Eksploratora serwera > usługi w chmurze** węzła. W tym miejscu można wyświetlić stan wystąpień poszczególnych ról.
1. Aby uzyskać dostęp do aplikacji po wdrożeniu, wybierz strzałkę obok danego wdrożenia, gdy stan **Ukończono** pojawia się w **dziennik aktywności platformy Azure** wraz z adresu URL. Zobacz poniższą tabelę, aby uzyskać więcej informacji dotyczących sposobu uruchamiania aplikacji sieci web dla określonego typu z platformy Azure.

## <a name="using-the-compute-emulator-and-starting-application-in-azure"></a>Przy użyciu emulatora obliczeń i uruchamianie aplikacji na platformie Azure

Wszystkie typy aplikacji mogą być uruchamiane w przeglądarce połączony debuger programu Visual Studio, wybierając **Debuguj > Rozpocznij debugowanie** (F5). Z projektem pusta aplikacja sieci Web ASP.NET, należy najpierw dodać `.aspx` strony w aplikacji oraz ustaw go jako strony początkowej dla projektu sieci web.

W poniższej tabeli przedstawiono szczegółowe informacje dotyczące uruchamiania aplikacji na platformie Azure:

   | Typ aplikacji sieci Web | działające na platformie Azure |
   | --- | --- | --- |
   | Aplikacja sieci Web ASP.NET<br/>(w tym MVC 2, MVC 3, MVC 4) | Wybierz adres URL w **wdrożenia** karcie **dziennik aktywności platformy Azure**. |
   | ASP.NET pusta aplikacja sieci Web | Jeśli masz domyślne `.aspx` strony w aplikacji, wybierz adres URL w **wdrożenia** karcie **dziennik aktywności platformy Azure**. Aby przejść do innej strony, wprowadź następujący adres URL w przeglądarce: `<deployment_url>/<page_name>.aspx` |
   | Aplikacji Silverlight<br/>Aplikacja biznesowa Silverlight<br/>Aplikacja nawigacji Silverlight | Przejdź do odpowiedniej strony aplikacji przy użyciu następujących postać adresu URL: `<deployment_url>/<page_name>.aspx` |
    Aplikacja usługi WCF<br/>Aplikacja usługi przepływu pracy WCF | Ustaw `.svc` pliku jako strony początkowej w projekcie usługi WCF. Następnie przejdź do `<deployment_url>/<service_file>.svc` |
   | ASP.NET Dynamic jednostek<br/>ASP.NET Dynamic Data Linq do SQL | Zaktualizuj parametry połączenia, zgodnie z opisem w następnej sekcji. Następnie przejdź do `<deployment_url>/<page_name>.aspx`. Linq do SQL należy użyć bazy danych Azure SQL. |

## <a name="update-a-connection-string-for-aspnet-dynamic-entities"></a>Zaktualizuj parametry połączenia dla jednostek ASP.NET Dynamic

1. Utwórz bazę danych SQL Azure dla aplikacji sieci web ASP.NET dynamiczne jednostek, zgodnie z opisem we wcześniejszej części (#use-an-azuresql-database-for-your-application).
1. Dodawanie tabel i pól, które są potrzebne dla tej bazy danych z portalu Azure.
1. Określ parametry połączenia w `web.config` pliku o następującym formacie, a następnie zapisz plik:

    ```xml
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

    Aktualizacja *connectionString* wartości z parametrów połączenia ADO.NET dla bazy danych SQL Azure w następujący sposób:

    ```xml
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

## <a name="supported-project-templates"></a>Szablony projektów obsługiwanych

Aplikacje, które mogą być migrowane i publikowane w usługach w chmurze musi używać jednego z szablonów w poniższej tabeli. Platformy ASP.NET Core nie jest obsługiwane.

| Grupa szablonów | Szablon projektu |
| --- | --- |
| Sieć Web | Aplikacja sieci Web platformy ASP.NET (.NET Framework) |
| Sieć Web | Aplikacja sieci Web platformy ASP.NET MVC 2 |
| Sieć Web | Aplikacja sieci Web platformy ASP.NET MVC 3 |
| Sieć Web | ASP.NET MVC4 Web Application |
| Sieć Web | Aplikacja sieci Web ASP.NET pusty (lub lokacji) |
| Sieć Web | Aplikacja pusty sieci Web platformy ASP.NET MVC 2 |
| Sieć Web | Aplikacja sieci Web ASP.NET Dynamic Data Entities |
| Sieć Web | ASP.NET Dynamic Data Linq do aplikacji sieci Web SQL |
| Silverlight | Aplikacji Silverlight |
| Silverlight | Aplikacja biznesowa Silverlight |
| Silverlight | Aplikacja nawigacji Silverlight |
| WCF | Aplikacja usługi WCF |
| WCF | Aplikacja usługi przepływu pracy WCF |
| Przepływ pracy | Aplikacja usługi przepływu pracy WCF |

## <a name="next-steps"></a>Kolejne kroki

- [Przygotowanie do publikowania lub wdrażania aplikacji Azure w programie Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md)
- [Konfigurowanie o nazwie poświadczenia uwierzytelniania](vs-azure-tools-setting-up-named-authentication-credentials.md).
