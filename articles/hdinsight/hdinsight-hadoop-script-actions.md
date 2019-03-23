---
title: Opracowywanie akcji skryptu za pomocą HDInsight — Azure
description: Dowiedz się, jak dostosowywać klastry usługi Hadoop przy użyciu akcji skryptu. Akcja skryptu może służyć do instalowania dodatkowego oprogramowania w klastrze usługi Hadoop lub zmienić konfigurację aplikacji instalowanych w klastrze.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: d8f7808401b2e11a38b239a353e3b7af2ffcffb3
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361306"
---
# <a name="develop-script-action-scripts-for-hdinsight-windows-based-clusters"></a>Tworzenie akcji skryptu skryptów dla klastrów HDInsight Windows
Dowiedz się, jak można tworzyć skrypty Akcja skryptu dla HDInsight. Aby uzyskać informacji na temat za pomocą skryptów akcji skryptu, zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md). Aby uzyskać ten sam artykuł, przeznaczony dla klastrów HDInsight opartych na systemie Linux, zobacz [skrypty opracowywanie akcji skryptu do HDInsight](hdinsight-hadoop-script-actions-linux.md).


> [!IMPORTANT]  
> Kroki opisane w tym dokumencie działają tylko w przypadku klastrów HDInsight z systemem Windows. HDInsight jest dostępna tylko na Windows dla wersji starszej niż HDInsight 3.4. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows). Aby uzyskać informacji na temat klastrów opartych na systemie Linux za pomocą akcji skryptu, zobacz [opracowywanie akcji z HDInsight (Linux) skryptu](hdinsight-hadoop-script-actions-linux.md).


Akcja skryptu może służyć do instalowania dodatkowego oprogramowania, uruchomiony w klastrze usługi Apache Hadoop lub zmienić konfigurację aplikacji instalowanych w klastrze. Akcje skryptu to skrypty, działających w węzłach klastra, gdy klastry HDInsight są wdrażane i są one wykonywane, gdy węzły w klastrze Zakończ konfigurację HDInsight. Akcja skryptu jest wykonywane w ramach uprawnień konta administratora systemu i zapewnia pełne prawa dostępu do węzłów klastra. Każdy klaster można podać listę akcji skryptu do wykonania w kolejności, w którym są określone.

> [!NOTE]  
> Jeśli wystąpi następujący komunikat o błędzie:
>
> System.Management.Automation.CommandNotFoundException; ExceptionMessage : Termin "Save-HDIFile" nie został rozpoznany jako nazwa polecenia cmdlet, funkcji, pliku skryptu lub program wykonywalny. Sprawdź pisownię nazwy lub jeśli ścieżka został uwzględniony, sprawdź, czy ścieżka jest poprawna i spróbuj ponownie.
> 
> Jest on, ponieważ nie włączono metody pomocnika.  Zobacz [metody pomocnika do niestandardowych skryptów](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sample-scripts"></a>Przykładowe skrypty
W przypadku tworzenia klastrów HDInsight w systemie operacyjnym Windows, akcji skryptu jest skrypt programu Azure PowerShell. Poniższy skrypt to przykład konfigurowania plików konfiguracji lokacji:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    param (
        [parameter(Mandatory)][string] $ConfigFileName,
        [parameter(Mandatory)][string] $Name,
        [parameter(Mandatory)][string] $Value,
        [parameter()][string] $Description
    )

    if (!$Description) {
        $Description = ""
    }

    $hdiConfigFiles = @{
        "hive-site.xml" = "$env:HIVE_HOME\conf\hive-site.xml";
        "core-site.xml" = "$env:HADOOP_HOME\etc\hadoop\core-site.xml";
        "hdfs-site.xml" = "$env:HADOOP_HOME\etc\hadoop\hdfs-site.xml";
        "mapred-site.xml" = "$env:HADOOP_HOME\etc\hadoop\mapred-site.xml";
        "yarn-site.xml" = "$env:HADOOP_HOME\etc\hadoop\yarn-site.xml"
    }

    if (!($hdiConfigFiles[$ConfigFileName])) {
        Write-HDILog "Unable to configure $ConfigFileName because it is not part of the HDI configuration files."
        return
    }

    [xml]$configFile = Get-Content $hdiConfigFiles[$ConfigFileName]

    $existingproperty = $configFile.configuration.property | where {$_.Name -eq $Name}

    if ($existingproperty) {
        $existingproperty.Value = $Value
        $existingproperty.Description = $Description
    } else {
        $newproperty = @($configFile.configuration.property)[0].Clone()
        $newproperty.Name = $Name
        $newproperty.Value = $Value
        $newproperty.Description = $Description
        $configFile.configuration.AppendChild($newproperty)
    }

    $configFile.Save($hdiConfigFiles[$ConfigFileName])

    Write-HDILog "$configFileName has been configured."

Skrypt przyjmuje cztery parametry, nazwa pliku konfiguracji, właściwości, którą chcesz zmodyfikować wartość, którą chcesz ustawić i opis. Na przykład:

    hive-site.xml hive.metastore.client.socket.timeout 90

Te parametry wartość hive.metastore.client.socket.timeout 90 w pliku gałęzi site.xml.  Wartość domyślna to 60 sekund.

Ten przykładowy skrypt można również znaleźć na [ https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1 ](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1).

HDInsight udostępnia szereg skryptów do instalowania dodatkowych składników w klastrach HDInsight:

| Name (Nazwa) | Skrypt |
| --- | --- |
| **Instalowanie platformy Spark** | `https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1`. Zobacz [instalacji i używania klastrów platformy Apache Spark w HDInsight][hdinsight-install-spark]. |
| **Instalowanie języka R** | `https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1`. Zobacz [instalacji i używania języka R w klastrach HDInsight](r-server/r-server-hdinsight-manage.md#install-additional-r-packages-on-the-cluster). |
| **Zainstalować system Giraph** | `https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1`. Zobacz [instalacji i używania Giraph Apache na HDInsight clusters](hdinsight-hadoop-giraph-install.md). |
| **Wstępne ładowanie bibliotek technologii Hive** | `https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1`. Zobacz [Dodaj Apache Hive bibliotek w klastrach HDInsight](hdinsight-hadoop-add-hive-libraries.md) |


Akcja skryptu można wdrożyć w witrynie Azure portal, programu Azure PowerShell lub przy użyciu zestawu .NET SDK HDInsight.  Aby uzyskać więcej informacji, zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptu] [hdinsight — klastra — Dostosowywanie].

> [!NOTE]  
> Przykładowe skrypty działają tylko w przypadku klastra HDInsight w wersji 3.1 lub nowszej. Aby uzyskać więcej informacji na temat wersji klastra HDInsight, zobacz [wersji klastra HDInsight](hdinsight-component-versioning.md).
>
>

## <a name="helper-methods-for-custom-scripts"></a>Metody pomocnicze dla niestandardowych skryptów
Metody pomocnicze akcji skryptu są narzędzia, których można używać podczas pisania skryptów niestandardowych. Te metody są zdefiniowane w [ https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1 ](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1)i mogą zostać uwzględnione w skryptach, korzystając z poniższego przykładu:

    # Download config action module from a well-known directory.
    $CONFIGACTIONURI = "https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1";
    $CONFIGACTIONMODULE = "C:\apps\dist\HDInsightUtilities.psm1";
    $webclient = New-Object System.Net.WebClient;
    $webclient.DownloadFile($CONFIGACTIONURI, $CONFIGACTIONMODULE);

    # (TIP) Import config action helper method module to make writing config action easy.
    if (Test-Path ($CONFIGACTIONMODULE))
    {
        Import-Module $CONFIGACTIONMODULE;
    }
    else
    {
        Write-Output "Failed to load HDInsightUtilities module, exiting ...";
        exit;
    }

Poniżej przedstawiono metody pomocnika, które są udostępniane przez ten skrypt:

| Metoda pomocnika | Opis |
| --- | --- |
| **Save-HDIFile** |Miejsce na dysku lokalnym, który jest skojarzony z węzłem maszyny Wirtualnej platformy Azure, przypisane do klastra, należy pobrać plik z określonego identyfikatora URI (Uniform Resource). |
| **Expand-HDIZippedFile** |Rozpakuj skompresowany plik. |
| **Invoke-HDICmdScript** |Uruchom skrypt z cmd.exe. |
| **HDILog zapisu** |Zapisują dane wyjściowe z niestandardowego skryptu używanych w przypadku akcji skryptu. |
| **Get-Services** |Zostanie wyświetlona lista usług uruchomiona na komputerze, w którym skrypt jest wykonywany. |
| **Get-Service** |O nazwie określonej usługi jako dane wejściowe, Uzyskaj szczegółowe informacje dotyczące określonej usługi (nazwa usługi, przetwarzanie identyfikator, stan, itp.) na komputerze, na którym skrypt jest wykonywany. |
| **Get-HDIServices** |Zostanie wyświetlona lista usług HDInsight uruchomionego na komputerze, w którym skrypt jest wykonywany. |
| **Get-HDIService** |Przy użyciu określonej nazwy usługi HDInsight jako dane wejściowe, Uzyskaj szczegółowe informacje dotyczące określonej usługi (nazwa usługi, przetwarzanie identyfikator, stan, itp.) na komputerze, na którym skrypt jest wykonywany. |
| **Get-ServicesRunning** |Pobierz listę usług, które są uruchomione na komputerze, na którym skrypt jest wykonywany. |
| **Get-ServiceRunning** |Sprawdź, czy określonej usługi (według nazwy) działa na komputerze, którym skrypt jest wykonywany. |
| **Get-HDIServicesRunning** |Zostanie wyświetlona lista usług HDInsight uruchomionego na komputerze, w którym skrypt jest wykonywany. |
| **Get-HDIServiceRunning** |Sprawdź, czy określona usługa HDInsight (według nazwy) działa na komputerze, którym skrypt jest wykonywany. |
| **Get-HDIHadoopVersion** |Pobierz wersję usługi Hadoop jest zainstalowany na komputerze, na którym skrypt jest wykonywany. |
| **Test-IsHDIHeadNode** |Sprawdź, czy komputer, na którym skrypt jest wykonywany jest węzła głównego. |
| **Test-IsActiveHDIHeadNode** |Sprawdź, czy komputer, na którym skrypt jest wykonywany jest aktywny węzeł główny. |
| **Test-IsHDIDataNode** |Sprawdź, czy komputer, na którym skrypt jest wykonywany jest węzeł danych. |
| **Edit-HDIConfigFile** |Edytuj pliki konfiguracji programu hive-site.xml, core-site.xml, system plików hdfs-site.xml, mapred-site.xml lub yarn-site.xml. |

## <a name="best-practices-for-script-development"></a>Najlepsze rozwiązania dotyczące tworzenia skryptów
Podczas tworzenia niestandardowego skryptu dla klastra usługi HDInsight istnieje kilka najlepszych rozwiązań, które należy uwzględnić:

* Sprawdź, czy wersja usługi Hadoop.

    HDInsight w wersji 3.1 (Hadoop 2.4) i nowszych pomocy technicznej za pomocą akcji skryptu, aby zainstalować składniki niestandardowe w klastrze. W skrypcie niestandardowe, należy użyć **Get HDIHadoopVersion** metody pomocnika, aby sprawdzić wersję platformy Hadoop, przed kontynuowaniem wykonywania innych zadań w skrypcie.
* Podaj stałe linki do zasobów skryptu.

    Użytkowników powinien sprawdzać, czy wszystkie skrypty i inne artefakty, używany w dostosowaniu klastra pozostają dostępne przez cały czas życia klastra i że wersje tych plików nie zmieniają się przez czas trwania. Te zasoby są wymagane, jeśli wymagana jest odtwarzanie z obrazu z węzłów w klastrze. Najlepszym rozwiązaniem jest do pobrania i Archiwizuj wszystko na koncie magazynu, który kontroluje użytkownika. To konto może zawierać domyślne konto magazynu i dodatkowych kont magazynu określone w czasie wdrażania klastra dostosowane.
    Platforma Spark i R dostosowane przykłady klastra, w dokumentacji, na przykład znajduje się lokalną kopię zasoby na tym koncie magazynu: https://hdiconfigactions.blob.core.windows.net/.
* Upewnij się, że skrypt dostosowania klastra jest idempotentna.

    Należy oczekiwać, że węzły klastra usługi HDInsight są odtwarzany z obrazu w okresie istnienia klastra. Skrypt dostosowania klastra jest uruchamiane zawsze, gdy klaster jest odtwarzany z obrazu. Ten skrypt muszą być zaprojektowane jako idempotentne, w tym sensie, że na odtwarzanie z obrazu, skrypt należy upewnić się, że klaster jest zwracany do tej samej dostosowane stan, który był po skrypt został uruchomiony po raz pierwszy, gdy został utworzony klaster. Na przykład, jeśli niestandardowego skryptu zainstalowanym jej pierwszym uruchomieniu aplikacji na D:\AppLocation, przy każdym uruchomieniu kolejnych, od odtwarzanie z obrazu, skrypt powinien Sprawdź, czy aplikacja istnieje w lokalizacji D:\AppLocation przed wykonaniem innych kroków w skrypt.
* Zainstaluj składniki niestandardowe w lokalizacji optymalne.

    Gdy węzły klastra są odtworzone z obrazu, C:\ zasobów dysku i dysk systemowy D:\ mogą być ponownie sformatowany, co spowoduje utratę danych i aplikacji, które były zainstalowane na tych dyskach. Taka utrata również może się zdarzyć, jeśli węzeł maszyn wirtualnych (VM), który jest częścią klastra ulegnie awarii i został zastąpiony nowym węźle. Składniki można zainstalować na dysku D:\, lub w lokalizacji C:\apps w klastrze. Wszystkie inne lokalizacje na dysku C:\ są zastrzeżone. Określ lokalizację, w której są aplikacje lub biblioteki ma być zainstalowany w klastrze dostosowywania skryptu.
* Zapewnij wysoką dostępność architektury klastra.

    HDInsight ma aktywny / pasywny architekturę wysokiej dostępności, jednego z węzłem głównym w trybie aktywnym (w którym są uruchomione usługi HDInsight) i innych węzła głównego jest w trybie rezerwy (w których HDInsight nie uruchomiono usługi). Jeśli usługi HDInsight są przerywane, węzły przełączyć tryby aktywnym i pasywnym. Jeśli akcja skryptu jest używany do instalowania usług na obu węzłów głównych w celu zapewnienia wysokiej dostępności, należy pamiętać, że mechanizm pracy awaryjnej HDInsight nie będzie mógł automatycznie w trybie Failover te zainstalowane przez użytkowników usługi. Dlatego zainstalowane przez użytkowników usług HDInsight węzły główne, które powinny być o wysokiej dostępności, musisz mieć ich własny mechanizm pracy awaryjnej, jeśli w trybie aktywny / pasywny lub działać w trybie aktywny aktywny.

    Polecenie Akcja skryptu HDInsight działa na zarówno węzły główne, gdy rola węzeł główny jest określony jako wartość *ClusterRoleCollection* parametru. Dlatego podczas projektowania niestandardowego skryptu, upewnij się, że skrypt ma informacje o tej konfiguracji. Nie należy uruchamiać w problemy, w którym te same usługi jest zainstalowana i uruchomiona na oba węzły główne i ich znajdą się konkurującymi ze sobą. Ponadto należy pamiętać, że dane zostaną utracone podczas odtwarzanie z obrazu, więc oprogramowania zainstalowanych za pomocą akcji skryptu musi być odporny na takie zdarzenia. Aplikacje powinny być zaprojektowane do pracy z danymi o wysokiej dostępności, która jest rozłożona na wiele węzłów. W tym samym czasie można odtworzyć z obrazu maksymalnie 1/5 węzłów w klastrze.
* Konfigurowanie składników niestandardowych, aby korzystać z magazynu obiektów Blob platformy Azure.

    Składniki niestandardowe, które są instalowane w węzłach klastra może być domyślną konfigurację, aby używać magazynu plików System (HDFS, Hadoop Distributed). Należy zmienić konfigurację, aby zamiast tego użyj usługi Azure Blob storage. Na klastrze odtworzenia z obrazu systemu plików HDFS pobiera sformatowany i utracisz wszelkie dane, które są przechowywane. Zamiast tego za pomocą usługi Azure Blob storage zapewnia przechowywania danych.

## <a name="common-usage-patterns"></a>Typowe wzorce użycia
Ta sekcja zawiera wskazówki dotyczące implementowania niektóre typowe wzorce użycia, które możesz napotkać podczas zapisywania skryptu niestandardowego.

### <a name="configure-environment-variables"></a>Konfigurowanie zmiennych środowiskowych
Często podczas tworzenia akcji skryptu, uważasz, że trzeba ustawić zmienne środowiskowe. Na przykład najbardziej prawdopodobnym scenariuszem jest po Pobierz plik binarny z witryny zewnętrznej, zainstaluj go w klastrze i Dodaj lokalizację testowanego miejsca do zmiennej środowiskowej "PATH", w którym jest zainstalowany. Poniższy fragment kodu pokazuje, jak ustawić zmienne środowiskowe w skryptu niestandardowego.

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

Ta instrukcja ustawia zmienną środowiskową **MDS_RUNNER_CUSTOM_CLUSTER** na wartość "prawda", a także ustawia zakres tej zmiennej jako komputera. Należy pamiętać, że zmienne środowiskowe są ustawiane w zakresie odpowiednich — komputera lub użytkownika. Zapoznaj się [tutaj] [ 1] Aby uzyskać więcej informacji na temat ustawiania zmiennych środowiskowych.

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Dostęp do lokalizacji, w którym są przechowywane niestandardowe skrypty
Skrypty służące do dostosowywania klastra musi być albo w domyślne konto magazynu dla klastra lub w publicznego kontenera tylko do odczytu na inne konto magazynu. Jeśli skrypt uzyskuje dostęp do zasobów znajdujących się gdzie indziej zasobów musi być publicznie do odczytu. Na przykład możesz chcieć uzyskać dostęp do pliku i zapisz go za pomocą polecenia SaveFile HDI.

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

W tym przykładzie należy zagwarantować, że kontener `somecontainer` na koncie magazynu `somestorageaccount` jest publicznie dostępny. W przeciwnym razie skrypt zgłasza wyjątek "Nie znaleziono" i zakończyć się niepowodzeniem.

### <a name="pass-parameters-to-the-add-azhdinsightscriptaction-cmdlet"></a>Parametry są przekazywane do polecenia cmdlet Add-AzHDInsightScriptAction
Aby przekazać wiele parametrów do polecenia cmdlet Add-AzHDInsightScriptAction, należy sformatować wartość ciągu, który zawiera wszystkie parametry skryptu. Na przykład:

    "-CertifcateUri wasb:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"

lub

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### <a name="throw-exception-for-failed-cluster-deployment"></a>Zgłoszenie wyjątku dla wdrożenia klastra nie powiodło się
Jeśli chcesz dokładniej bądź na bieżąco z faktu, że Dostosowywanie klastra nie powiodła się zgodnie z oczekiwaniami, ważne jest, aby zgłosić wyjątek i niepowodzenie tworzenia klastra. Na przykład możesz chcieć przetwarzania pliku, jeśli istnieje i obsługiwać przypadki błędów, gdy plik nie istnieje. Będzie to upewnij się, że skrypt zakończy pracę bez problemu zmieniała, stan klastra poprawnie wiadomo, że. Poniższy fragment kodu przedstawiono przykładowy sposób osiągnięcia tego:

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

W tym fragmencie kodu Jeśli plik nie istnieje, jej doprowadziłoby do stanu, w którym skrypt faktycznie kończy działanie bez problemu zmieniała po drukowanie komunikat o błędzie, a klaster osiąga stan działania, przy założeniu, że "pomyślnie" Ukończono proces dostosowywania klastra. Jeśli chcesz dokładnie otrzymywać powiadomienia, że Dostosowywanie klastra zasadniczo nie powiodła się zgodnie z oczekiwaniami, ze względu na Brak pliku, bardziej odpowiednie zgłoszenie wyjątku i zakończyć się niepowodzeniem w kroku dostosowania klastra. Można to osiągnąć należy użyć poniższy fragment kodu z próbki.

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## <a name="checklist-for-deploying-a-script-action"></a>Lista kontrolna wdrażania akcji skryptu
Poniżej przedstawiono kroki, które podczas przygotowywania do wdrożenia tych skryptów:

1. Umieść pliki, które zawierają niestandardowych skryptów w miejscu, który jest dostępny dla węzłów klastra podczas wdrażania. Może to być dowolny domyślne lub dodatkowych kont magazynu określonych podczas przeprowadzania wdrożenia klastra lub dowolnego kontenera publicznie dostępnego magazynu.
2. Dodaj testy w skryptach, aby upewnić się, że są one wykonywane idempotently, tak, aby skrypt mogą być wykonywane wiele razy w innym węźle.
3. Użyj `Write-Output` polecenia cmdlet programu Azure PowerShell, aby drukować do strumienia wyjściowego STDOUT, a także STDERR. Nie używaj `Write-Host`.
4. Użyć folderu plików tymczasowych, takich jak `$env:TEMP`, aby zachować pobrany plik, które są używane przez skrypty i następnie oczyścić je po wykonaniu skryptów.
5. Instalowanie oprogramowania niestandardowego tylko na D:\ lub C:\apps. Nie można używać innych lokalizacji na dysku C:, ponieważ są one zarezerwowane. Instalowanie plików na dysku C: poza folderem C:\apps może spowodować błędy instalacji podczas odtwarza z obrazu węzła.
6. W przypadku, gdy zostały zmienione ustawienia na poziomie systemu operacyjnego lub plików konfiguracyjnych usługi Hadoop, można ponownie uruchomić usługi HDInsight, dzięki czemu można wczytać wszystkie ustawienia na poziomie systemu operacyjnego, takie jak zmienne środowiskowe ustawione w skryptach.

## <a name="debug-custom-scripts"></a>Debugowanie skryptów niestandardowych
Dzienniki błędów skryptów są przechowywane, oraz inne dane wyjściowe w domyślne konto magazynu, który został określony dla klastra podczas jego tworzenia. Dzienniki są przechowywane w tabeli o nazwie *u < \cluster-name-fragment >< \time-stamp > setuplog*. Są to zagregowane dzienniki, które mają rekordy ze wszystkie węzły (węzeł główny i węzły procesów roboczych), na których skrypt zostanie uruchomiony w klastrze.

Prosty sposób, aby zapoznać się z dziennikami jest korzystanie z narzędzi HDInsight dla programu Visual Studio. Dotyczące instalowania narzędzi, zobacz [rozpoczęcie korzystania z narzędzi Visual Studio Hadoop dla HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-or-update-data-lake-tools-for-visual-studio)

**Aby sprawdzić w dzienniku za pomocą programu Visual Studio**

1. Otwórz program Visual Studio.
2. Kliknij przycisk **widoku**, a następnie kliknij przycisk **Eksploratora serwera**.
3. Kliknij prawym przyciskiem myszy "Azure", kliknij przycisk Połącz z **subskrypcji platformy Microsoft Azure**, a następnie wprowadź swoje poświadczenia.
4. Rozwiń **magazynu**, rozwiń konto usługi Azure storage używany jako domyślny system plików, rozwiń węzeł **tabel**, a następnie kliknij dwukrotnie nazwę tabeli.

Możesz również zdalny do strumienia wyjściowego STDOUT i STDERR znajdują się niestandardowych skryptów w węzłach klastra. Dzienniki w każdym węźle, są specyficzne tylko dla tego węzła i jest się zalogowanym **C:\HDInsightLogs\DeploymentAgent.log**. Te pliki dziennika rejestrować wszystkie dane wyjściowe z niestandardowego skryptu. Przykład fragment dziennika akcji skryptu Spark wygląda następująco:

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;

    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand;
    Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;


W tym dzienniku jest jasne, że akcja skryptu Spark została wykonana na maszynie Wirtualnej o nazwie HEADNODE0 i że żadne wyjątki były generowane podczas wykonywania.

W przypadku, gdy wystąpi błąd wykonania, opisujące jego dane wyjściowe również znajdują się w tym pliku dziennika. Informacje zawarte w tych dziennikach powinna być pomocne w debugowaniu problemów skryptu, które mogą wystąpić.

## <a name="see-also"></a>Zobacz także
* [Dostosowywanie klastrów HDInsight za pomocą akcji skryptu] [hdinsight — klastra — Dostosowywanie]
* [Instalowanie i używanie platformy Apache Spark w klastrach HDInsight][hdinsight-install-spark]
* [Instalowanie i używanie Apache Giraph w klastrach HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx
