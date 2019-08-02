---
title: Wykonywanie pakietów SSIS w Azure Data Factory z SSDT | Microsoft Docs
description: Dowiedz się, jak uruchamiać pakiety usług SSIS na platformie Azure z SSDT.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/31/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 9ddf427d7d749dff1af45b3f6f83d20a89e1aae0
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678413"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Wykonywanie pakietów SSIS na platformie Azure z SSDT
W tym artykule opisano funkcję projektów SQL Server Integration Services (SSIS) z włączoną obsługą platformy Azure w programie SQL Server Data Tools (SSDT), która umożliwia uruchamianie pakietów na platformie Azure-SSIS Integration Runtime (IR) w systemie Azure Data Factory (ADF).  Ta funkcja umożliwia testowanie istniejących pakietów usług SSIS przed podjęciem & Shift/migrowanie ich na platformę Azure lub opracowywanie nowych pakietów usług SSIS do uruchamiania na platformie Azure.

Za pomocą tej funkcji można utworzyć nowe środowisko Azure-SSIS IR lub dołączyć istniejący zestaw do projektów SSIS, a następnie wykonać na nim pakiety.  Obsługujemy uruchamianie pakietów, które mają zostać wdrożone w katalogu usług SSIS (SSISDB) w modelu wdrażania projektu i które mają zostać wdrożone w systemach plików/udziałach plików/Azure Files w modelu wdrażania pakietów. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby skorzystać z tej funkcji, Pobierz i zainstaluj najnowsze SSDT z rozszerzeniem projektów SSIS dla programu Visual Studio z tego [miejsca](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) lub jako autonomiczny Instalator w [tym miejscu](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer).

## <a name="azure-enable-ssis-projects"></a>Azure — Włączanie projektów SSIS
W witrynie SSDT można tworzyć nowe projekty SSIS z obsługą platformy Azure przy użyciu szablonu **projektu usług Integration Services (z obsługą platformy Azure)** .

![Nowy projekt SSIS obsługujący platformę Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Alternatywnie możesz włączyć platformę Azure — Włącz istniejące projekty SSIS, klikając prawym przyciskiem myszy węzeł projektu w panelu Eksplorator rozwiązań SSDT, aby wypróbować menu, a następnie wybierając element menu **obsługujące platformę Azure** .

![Azure — Włączanie istniejącego projektu SSIS](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

Platforma Azure — włączenie istniejących projektów usług SSIS wymaga ustawienia wersji serwera docelowego jako najnowszej obsługiwanej przez środowisko Azure-SSIS IR, która jest obecnie **SQL Server 2017**, więc jeśli jeszcze tego nie zrobiono, okno dialogowe zostanie wyświetlona.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>Połącz projekty z obsługą platformy Azure z usługą SSIS w Azure Data Factory
Łącząc swoje projekty z platformą Azure z usługami SSIS w podajniku APD, można przekazać pakiety do Azure Files i uruchamiać je na platformie Azure-SSIS IR.  Aby to zrobić, wykonaj następujące kroki:

1. Kliknij prawym przyciskiem myszy projekt lub **połączone zasoby platformy Azure** w panelu Eksplorator rozwiązań SSDT, aby wyskakujące menu, a następnie wybierz pozycję **Połącz z usługą SSIS w Azure Data Factory** , aby uruchomić **Kreatora połączeń usług SSIS w**ramach usługi ADF.

   ![Łączenie z usługą SSIS w ramach podajnika APD](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. Na stronie **wprowadzenie do programu SSIS w ramach podajnika APD** Przejrzyj wprowadzenie i kliknij przycisk **dalej** , aby kontynuować.

   ![Wprowadzenie do programu SSIS w ramach podajnika APD](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. Na stronie **Wybierz środowisko IR usługi SSIS w ramach podajnika APD** wybierz istniejący zestaw APD i środowisko IR Azure-SSIS, aby uruchomić pakiety, lub Utwórz nowe, jeśli nie masz żadnego z nich.
   - Aby wybrać istniejące środowisko Azure-SSIS IR, najpierw wybierz odpowiednią subskrypcję platformy Azure i ADF.
   - W przypadku wybrania istniejącego podajnika APD, który nie ma żadnego środowiska Azure-SSIS IR, kliknij przycisk **Utwórz środowisko IR** , aby utworzyć nowy element w portalu i aplikacji ADF.
   - Jeśli wybierzesz istniejącą subskrypcję platformy Azure, która nie ma żadnego podajnika APD, kliknij przycisk **Utwórz usługę SSIS IR** , aby uruchomić **Kreatora tworzenia Integration Runtime**, w którym możesz wprowadzić lokalizację i prefiks, aby automatycznie utworzyć nową platformę Azure Grupa zasobów, Data Factory i środowisko IR SSIS w Twoim imieniu, nazywane w następującym wzorcu: **YourPrefix-RG/DF/IR-YourCreationTime**.
   
   ![Wybierz środowisko IR usług SSIS w usłudze ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. Na stronie **Wybieranie usługi Azure Storage** Wybierz istniejące konto usługi Azure Storage, aby przekazać pakiety do Azure Files, lub Utwórz nowe, jeśli nie masz żadnego z nich.
   - Aby wybrać istniejące konto usługi Azure Storage, najpierw wybierz odpowiednią subskrypcję platformy Azure.
   - W przypadku wybrania tej samej subskrypcji platformy Azure jako środowiska IR Azure-SSIS, która nie ma żadnego konta usługi Azure Storage, kliknij przycisk **Utwórz usługę Azure Storage** , aby nam utworzyć nową nazwę w Twoim imieniu w tej samej lokalizacji, w której znajduje się środowisko Azure-SSIS IR, o nazwie Łączenie prefiksu nazwy środowiska Azure-SSIS IR i daty jego utworzenia.
   - Jeśli wybierzesz inną subskrypcję platformy Azure, która nie ma konta usługi Azure Storage, kliknij przycisk **Utwórz usługę Azure Storage** , aby utworzyć nowy element na Azure Portal.
   
   ![Wybieranie pozycji Azure Storage](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. Kliknij przycisk **Połącz** , aby zakończyć połączenie.  Zostanie wyświetlone wybrane konto usługi Azure-SSIS IR i usługi Azure Storage w węźle **połączone zasoby platformy Azure** w panelu Eksplorator rozwiązań SSDT.  Odświeżamy również stan środowiska Azure-SSIS IR, ale możesz nim zarządzać, klikając prawym przyciskiem myszy jego węzeł, aby wyskakujące menu, a następnie wybierając element menu **Start\Stop\Manage** , który przeprowadzi Cię do portalu/aplikacji w celu wykonania tej czynności.

## <a name="execute-ssis-packages-in-azure"></a>Wykonywanie pakietów SSIS na platformie Azure
### <a name="starting-package-executions"></a>Rozpoczynanie wykonywania pakietów
Po nawiązaniu połączenia między projektami a programem SSIS w ramach podajnika APD można uruchamiać pakiety na platformie Azure-SSIS IR.  Dostępne są dwie opcje rozpoczęcia wykonywania pakietów:
-  Kliknij przycisk **Uruchom** na pasku narzędzi SSDT, aby rozwinąć menu i wybrać element menu **wykonaj w systemie Azure** 

   ![Wykonaj na platformie Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Kliknij prawym przyciskiem myszy węzeł pakiet w Eksplorator rozwiązań panelu SSDT, aby wyskakujące menu, a następnie wybierz pozycję **Wykonaj pakiet w menu platformy Azure** .

   ![Wykonaj pakiet na platformie Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Wykonywanie pakietów na platformie Azure wymaga posiadania działającego środowiska Azure-SSIS IR, więc jeśli środowisko Azure-SSIS IR zostało zatrzymane, zostanie wyświetlone okno dialogowe.  Bez czasu instalacji niestandardowej ten proces powinien zostać zakończony w ciągu 5 minut, ale może potrwać około 20-30 minut w przypadku usługi Azure-SSIS IR przyłączania do sieci wirtualnej.  Po wykonaniu pakietów na platformie Azure Możesz zatrzymać środowisko Azure-SSIS IR, aby zarządzać jego uruchomionym kosztem, klikając prawym przyciskiem myszy węzeł w Eksplorator rozwiązań panelu SSDT, aby wyskakujące menu, a następnie wybierając element menu **Start\Stop\Manage** , który przeprowadzi Cię do portalu ADF/ aplikacja, aby to zrobić.

### <a name="checking-package-execution-logs"></a>Sprawdzanie dzienników wykonywania pakietów
Po rozpoczęciu wykonywania pakietu będziemy formatować i wyświetlać dziennik w oknie postępu SSDT.  W przypadku długiego uruchomionego pakietu będziemy okresowo aktualizować dziennik przez minuty.  Aby zatrzymać wykonywanie pakietu, kliknij przycisk **Zatrzymaj** na pasku narzędzi SSDT, który natychmiast go anuluje.  Możesz również tymczasowo znaleźć dane pierwotne dziennika w swojej ścieżce Universal Naming Convention (UNC): `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`, ale zostanie ona oczyszczona po upływie jednego dnia.

### <a name="switching-package-protection-level"></a>Przełączanie poziomu ochrony pakietu
Wykonywanie pakietów SSIS na platformie Azure nie obsługuje poziomów ochrony **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** .  W związku z tym, jeśli Twoje pakiety są skonfigurowane z tymi, tymczasowo przełączymy je odpowiednio do **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**, z losowo generowanymi hasłami podczas przekazywania pakiety do Azure Files do wykonania na platformie Azure-SSIS IR.

> [!NOTE]
> Jeśli pakiety zawierają zadania wykonywania pakietów, które odwołują się do innych pakietów skonfigurowanych przy użyciu poziomów ochrony **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** , należy ręcznie skonfigurować te inne pakiety do programu przed wykonaniem pakietów Użyj odpowiednio **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**.

Jeśli Twoje pakiety zostały już skonfigurowane przy użyciu poziomów ochrony **EncryptSensitiveWithPassword**/**EncryptAllWithPassword** , firma Microsoft nie zmieni ich, ale nadal będzie używać losowo generowanych haseł podczas przekazywania pakiety do Azure Files do wykonania na platformie Azure-SSIS IR.

### <a name="using-package-configuration-file"></a>Korzystanie z pliku konfiguracji pakietu
W przypadku używania plików konfiguracji pakietów w modelu wdrażania pakietów do zmiany wartości zmiennych w czasie wykonywania, firma Microsoft automatycznie przekaże te pliki do Azure Files do wykonania na platformie Azure-SSIS IR.

### <a name="using-execute-package-task"></a>Korzystanie z zadania Execute Package
Jeśli pakiety zawierają zadania wykonywania pakietów, które odwołują się do innych pakietów przechowywanych w lokalnych systemach plików, należy wykonać następujące dodatkowe czynności konfiguracyjne:

1. Przekaż pozostałe pakiety do Azure Files w ramach tego samego konta usługi Azure Storage połączonego z projektami i uzyskaj nową ścieżkę UNC, np.`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Zastąp ścieżkę pliku tych innych pakietów w Menedżerze połączeń plików zadania wykonywania pakietów z nową ścieżką UNC
   - Jeśli maszyna z systemem SSDT nie może uzyskać dostępu do nowej ścieżki UNC, możesz zmienić ścieżkę pliku w panelu Właściwości Menedżera połączeń plików
   - Alternatywnie można użyć zmiennej dla ścieżki pliku do przypisania właściwej wartości w czasie wykonywania

Jeśli pakiety zawierają zadania wykonywania pakietów, które odwołują się do innych pakietów w tym samym projekcie, nie jest wymagana żadna dodatkowa konfiguracja.

## <a name="next-steps"></a>Następne kroki
Po upewnieniu się, że pakiety na platformie Azure są zgodne z usługą SSDT, można je wdrożyć i uruchamiać jako działania pakietu SSIS w potokach ADF, zobacz [uruchamianie pakietów SSIS jako działania pakietu SSIS w potokach ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
