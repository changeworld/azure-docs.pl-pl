---
title: Skrypt programowanie akcji z usługą HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostosować klastry Hadoop za pomocą akcji skryptu. Akcja skryptu można zainstalować dodatkowe oprogramowanie uruchomione w klastrze Hadoop lub zmienić konfigurację aplikacji zainstalowany w klastrze.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 836d68a8-8b21-4d69-8b61-281a7fe67f21
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 921da2db8e235e17611788cae7e976597bd76703
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34271619"
---
# <a name="develop-script-action-scripts-for-hdinsight-windows-based-clusters"></a>Tworzenie skryptów akcji skryptu dla klastrów usługi HDInsight w systemie Windows
Dowiedz się, jak napisać skrypty akcji skryptu dla usługi HDInsight. Uzyskać informacji na temat używania skryptów akcji skryptu, zobacz [HDInsight dostosować klastry za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster.md). Aby uzyskać ten sam artykuł, przeznaczony dla klastrów usługi HDInsight opartych na systemie Linux, zobacz [skryptów tworzenie akcji skryptu dla usługi HDInsight](hdinsight-hadoop-script-actions-linux.md).



> [!IMPORTANT]
> Kroki opisane w tym dokumencie działa tylko w przypadku klastrów usługi HDInsight opartych na systemie Windows. HDInsight jest dostępna tylko w systemie Windows dla wersji starszej niż HDInsight 3.4. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows). Uzyskać przy użyciu akcji skryptu z klastrów z systemem Linux, zobacz [programowanie akcji z usługą HDInsight (Linux) w skrypcie](hdinsight-hadoop-script-actions-linux.md).
>
>



Akcja skryptu można zainstalować dodatkowe oprogramowanie uruchomione w klastrze Hadoop lub zmienić konfigurację aplikacji zainstalowany w klastrze. Akcje skryptu to skrypty uruchamiane w węzłach klastra, gdy klastry usługi HDInsight są wdrażane i są wykonywane po węzłów w klastrze Zakończ konfigurację usługi HDInsight. Akcja skryptu jest wykonywane w ramach uprawnień konta administratora systemu i zapewnia pełne prawa dostępu do węzłów klastra. Każdy klaster można podać listę akcji skryptu do wykonania w kolejności, w jakiej zostały określone.

> [!NOTE]
> Jeśli wystąpi następujący komunikat o błędzie:
>
> System.Management.Automation.CommandNotFoundException; ExceptionMessage: Termin "Zapisz HDIFile" nie został rozpoznany jako nazwa polecenia cmdlet, funkcji, pliku skryptu lub program wykonywalny. Sprawdź pisownię nazwy lub jeśli ścieżki został uwzględniony, sprawdź, czy ścieżka jest poprawna i spróbuj ponownie.
> Jest on, ponieważ nie włączono metody pomocnicze.  Zobacz [metody pomocnicze dla niestandardowych skryptów](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts).
>
>

## <a name="sample-scripts"></a>Przykładowe skrypty
Do tworzenia klastrów usługi HDInsight w systemie operacyjnym Windows, akcja skryptu jest skrypt programu PowerShell systemu Azure. Poniższy skrypt to przykład dla konfigurowania plików konfiguracji lokacji:

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

Skrypt przyjmuje cztery parametry, nazwę pliku konfiguracji, właściwości, aby zmodyfikować wartość, którą chcesz skonfigurować oraz opis. Na przykład:

    hive-site.xml hive.metastore.client.socket.timeout 90

Te parametry wartość hive.metastore.client.socket.timeout 90 w pliku gałęzi site.xml.  Wartość domyślna to 60 sekund.

Ten przykładowy skrypt można znaleźć w [ https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1 ](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1).

Usługa HDInsight zapewnia kilka skryptów do instalowania dodatkowych składników w klastrach HDInsight:

| Name (Nazwa) | Skrypt |
| --- | --- |
| **Zainstaluj Spark** |https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. Zobacz [instalacji i używania platformy Spark w usłudze HDInsight clusters][hdinsight-install-spark]. |
| **Zainstalować język R** |https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. Zobacz [instalacji i użyj R w klastrach HDInsight] [hdinsight-r skrypty]. |
| **Zainstaluj Solr** |https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. Zobacz [instalacji i używania Solr w usłudze HDInsight clusters](hdinsight-hadoop-solr-install.md). |
| - **Zainstaluj Giraph** |https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. Zobacz [instalacji i używania Giraph w usłudze HDInsight clusters](hdinsight-hadoop-giraph-install.md). |

Akcja skryptu można wdrożyć przy użyciu portalu Azure, programu Azure PowerShell lub przy użyciu zestawu .NET SDK usługi HDInsight.  Aby uzyskać więcej informacji, zobacz [HDInsight dostosować klastry za pomocą akcji skryptu][hdinsight-cluster-customize].

> [!NOTE]
> Przykładowe skrypty działa tylko z klastra usługi HDInsight w wersji 3.1 lub nowszy. Aby uzyskać więcej informacji o wersjach klastra usługi HDInsight, zobacz [wersji klastra usługi HDInsight](hdinsight-component-versioning.md).
>
>

## <a name="helper-methods-for-custom-scripts"></a>Metody pomocnicze dla niestandardowych skryptów
Metody pomocnicze akcji skryptu są narzędzia, które można użyć podczas pisania skryptów niestandardowych. Te metody są zdefiniowane w [ https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1 ](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1)i może być uwzględniony w skryptach przy użyciu poniższego polecenia:

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

Poniżej przedstawiono metody pomocnicze, które są udostępniane przez ten skrypt:

| Metoda pomocnika | Opis |
| --- | --- |
| **Zapisz HDIFile** |Pobierz plik z określonego identyfikatora URI (Uniform Resource) do lokalizacji na dysku lokalnym, który jest skojarzony z węzłem maszyny Wirtualnej Azure przypisana do klastra. |
| **Rozwiń węzeł HDIZippedFile** |Rozpakuj plik z rozszerzeniem zip. |
| **Invoke-HDICmdScript** |Uruchom skrypt z cmd.exe. |
| **HDILog zapisu** |Zapisywania danych wyjściowych skryptu niestandardowego używanych w przypadku akcji skryptu. |
| **Get-Services** |Pobierz listę usług uruchomionych na komputerze, w którym skrypt jest wykonywany. |
| **Get-Service** |Przy użyciu nazwy określonej usługi jako dane wejściowe, uzyskać szczegółowe informacje dla określonej usługi (nazwy usługi, przetworzyć identyfikator, stan, itp.) na komputerze, na którym skrypt jest wykonywany. |
| **Get-HDIServices** |Pobierz listę uruchomionych na komputerze, w którym skrypt jest wykonywany usługi HDInsight. |
| **Get-HDIService** |O określonej nazwie usługi HDInsight jako dane wejściowe, uzyskać szczegółowe informacje dla określonej usługi (nazwy usługi, przetworzyć identyfikator, stan, itp.) na komputerze, na którym skrypt jest wykonywany. |
| **Get-ServicesRunning** |Pobierz listę usług, które są uruchomione na komputerze, na którym skrypt jest wykonywany. |
| **Get-ServiceRunning** |Sprawdź, czy określonej usługi (według nazwy) działa na komputerze, którym skrypt jest wykonywany. |
| **Get-HDIServicesRunning** |Pobierz listę uruchomionych na komputerze, w którym skrypt jest wykonywany usługi HDInsight. |
| **Get-HDIServiceRunning** |Sprawdź, czy określonej usługi HDInsight (według nazwy) działa na komputerze, którym skrypt jest wykonywany. |
| **Get-HDIHadoopVersion** |Pobierz wersję platformy Hadoop zainstalowany na komputerze, na którym skrypt jest wykonywany. |
| **Test-IsHDIHeadNode** |Sprawdź, czy komputer, na którym skrypt jest wykonywany jest węzła głównego. |
| **Test-IsActiveHDIHeadNode** |Sprawdź, czy komputer, na którym skrypt jest wykonywany jest aktywnego węzła głównego. |
| **Test-IsHDIDataNode** |Sprawdź, czy komputer, na którym skrypt jest wykonywany jest węzeł danych. |
| **Edit-HDIConfigFile** |Edytuj pliki konfiguracji hive-site.xml, core-site.xml, system plików hdfs-site.xml, mapred-site.xml lub yarn-site.xml. |

## <a name="best-practices-for-script-development"></a>Najlepsze rozwiązania dotyczące tworzenia skryptów
Podczas opracowywania niestandardowego skryptu dla klastra usługi HDInsight, istnieje kilka najlepszych rozwiązań, które należy wziąć pod uwagę:

* Sprawdź, czy wersja usługi Hadoop

    HDInsight w wersji 3.1 (Hadoop 2.4) lub nowszym pomocy technicznej, aby zainstalować składniki niestandardowe w klastrze za pomocą akcji skryptu. W skrypcie niestandardowe, należy użyć **Get HDIHadoopVersion** metody pomocniczej, aby sprawdzić wersję platformy Hadoop, przed kontynuowaniem wykonywania innych zadań w skrypcie.
* Podaj stabilna linki do zasobów skryptu

    Użytkownicy upewnij się, że wszystkie skrypty i inne artefaktów używane w dostosowania klastra nadal dostępne w okresie istnienia klastra i wersje tych plików nie należy zmieniać na czas trwania. Te zasoby są wymagane, jeśli wymagana jest ponownym węzłów w klastrze. Najlepszym rozwiązaniem jest pobrać i zarchiwizowanie wszystkich na koncie magazynu, która kontroluje użytkownika. To konto może być domyślne konto magazynu ani żadnych dodatkowych kont magazynu określony w czasie wdrażania dostosowanego klastra.
    Platforma Spark i R dostosowane klastra przykłady podane w dokumentacji, na przykład istnieje lokalną kopię zasoby na tym koncie magazynu: https://hdiconfigactions.blob.core.windows.net/.
* Upewnij się, że skrypt dostosowywania klastra jest idempotentności

    Należy oczekiwać, że węzły klastra usługi HDInsight są odtworzyć z obrazu okres istnienia klastra. Skrypt dostosowywania klastra jest uruchamiane przy każdym klastrze zostanie odtworzone z obrazu. Ten skrypt muszą być zaprojektowane jako idempotentności w tym sensie, że po ponownej instalacji systemu, skrypt należy upewnij się, że klaster jest zwracana do tej samej dostosowywać stan, który znajdował się tuż po skrypt został uruchomiony po raz pierwszy, gdy klaster został utworzony. Na przykład, jeżeli skryptu niestandardowego zainstalować aplikację na D:\AppLocation przy pierwszym uruchomieniu, następnie przy każdym uruchomieniu kolejne, po ponownym, skrypt należy sprawdzić, czy aplikacja znajduje się w lokalizacji D:\AppLocation przed kontynuowaniem z innymi kroki opisane w temacie skrypt.
* Zainstaluj składniki niestandardowe w optymalny lokalizacji

    Jeśli węzły klastra są odtworzyć z obrazu, dysku C:\ zasobów i dysku D:\ system można ponownie sformatowany, co grozi utratą danych i aplikacji, które były zainstalowane na tych dyskach. Ta utrata również może się zdarzyć, jeśli węzeł maszyny wirtualnej platformy Azure (VM), który jest częścią klastra ulegnie awarii i zostało zastąpione przez nowy węzeł. Składniki można zainstalować na dysku D:\, lub w lokalizacji C:\apps w klastrze. Wszystkich innych lokalizacji na dysku C:\ są zastrzeżone. Określ lokalizację, w których aplikacje lub biblioteki ma być zainstalowany w klastrze dostosowywania skryptu.
* Zapewni to wysoką dostępność architektury klastra

    HDInsight ma architekturę aktywny / pasywny wysokiej dostępności, jednego z węzła głównego w trybie aktywnym (w którym są uruchomione usługi HDInsight) i innych węzła głównego jest w trybie rezerwy (w którym HDInsight usługi nie działają). Węzły przełącznika tryby aktywnym i pasywnym, jeśli usługi HDInsight są przerywane. Jeśli akcja skryptu jest używany do instalowania usług na obu węzłach głównych wysokiej dostępności, należy pamiętać, mechanizm pracy awaryjnej usługi HDInsight nie jest automatycznie awaryjnie tych usług zainstalowane przez użytkownika. Dlatego użytkownik zainstalował usług na HDInsight węzłów głównych, które powinny być wysokiej dostępności musisz mieć własne mechanizm pracy awaryjnej w trybie aktywny / pasywny lub być w trybie aktywny / aktywny.

    Akcja skryptu HDInsight polecenie jest uruchamiane na obu węzłach głównych, gdy rola head węzła jest określony jako wartość *ClusterRoleCollection* parametru. Tak podczas projektowania niestandardowego skryptu, upewnij się, że skrypt jest świadome tego Instalatora. Nie należy uruchamiać do problemów, gdy te same usługi są zainstalowane i uruchomione na obu węzłów głównych i ich przechodzili konkurujących ze sobą. Pamiętaj też, że dane zostały utracone podczas ponownej instalacji systemu, więc oprogramowania zainstalowanych za pomocą akcji skryptu musi być odporny na takie zdarzenia. Aplikacje powinny zaprojektowane w taki sposób, aby pracować z danymi wysokiej dostępności, która jest dystrybuowana do wielu węzłów. Maksymalnie 1/5 węzłów w klastrze mogą można odtworzyć z obrazu w tym samym czasie.
* Konfigurowanie niestandardowych składników można używać magazynu obiektów Blob platformy Azure

    Niestandardowych składników, które należy zainstalować na węzłach klastra może być domyślna konfiguracja korzystania z magazynu systemu Distributed plików Hadoop (HDFS). Należy zmienić konfigurację, aby zamiast tego użyj magazynu obiektów Blob platformy Azure. Na odtworzenia z obrazu klastra sformatowany pobiera system plików HDFS i zostaną utracone wszystkie dane są przechowywane. Zamiast tego przy użyciu magazynu obiektów Blob platformy Azure zapewnia, że dane są przechowywane.

## <a name="common-usage-patterns"></a>Wspólne wzorce użycia
Ta sekcja zawiera wskazówki dotyczące implementowania niektóre typowe wzorce użycia, które możesz napotkać podczas pisania skryptu niestandardowego.

### <a name="configure-environment-variables"></a>Konfigurowanie zmiennych środowiskowych
Często w rozwoju akcji skryptu, uważasz, że trzeba ustawić zmienne środowiskowe. Na przykład najbardziej prawdopodobnym scenariuszem jest podczas pobierania pliku binarnego z zewnętrznej witryny, zainstaluj go w klastrze i Dodaj lokalizację miejsca do Twojej zmiennej środowiskowej "PATH", w którym jest zainstalowany. Poniższy fragment kodu pokazano, jak ustawić zmienne środowiskowe w skryptu niestandardowego.

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

Ta instrukcja ustawia zmienną środowiskową **MDS_RUNNER_CUSTOM_CLUSTER** na wartość "true", a także ustawia zakres tej zmiennej jako komputera. Należy pamiętać, że zmienne środowiskowe są ustawiane na odpowiedni zakres — komputera lub użytkownika. Zobacz [tutaj] [ 1] Aby uzyskać więcej informacji na temat ustawiania zmiennych środowiskowych.

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Dostęp do lokalizacji, w którym są przechowywane niestandardowe skrypty
Skrypty używane w celu dostosowania klastra musi być albo w domyślne konto magazynu dla klastra lub w publicznego kontenera tylko do odczytu na inne konto magazynu. Jeśli skrypt uzyskuje dostęp do zasobów znajdujących się w innym miejscu zasobów musi być publicznie do odczytu. Na przykład można uzyskać dostępu do pliku i zapisz go przy użyciu polecenia SaveFile HDI.

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

W tym przykładzie, upewnij się, że kontener `somecontainer` na koncie magazynu `somestorageaccount` jest dostępny publicznie. W przeciwnym razie skrypt zgłasza wyjątek nie znaleziono i zakończyć się niepowodzeniem.

### <a name="pass-parameters-to-the-add-azurermhdinsightscriptaction-cmdlet"></a>Przekazania parametrów do polecenia cmdlet Add-AzureRmHDInsightScriptAction
Aby przekazać wiele parametrów polecenia cmdlet Add-AzureRmHDInsightScriptAction, należy sformatować wartość ciągu zawiera wszystkie parametry skryptu. Na przykład:

    "-CertifcateUri wasb:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"

lub

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### <a name="throw-exception-for-failed-cluster-deployment"></a>Zgłoszenie wyjątku dla wdrożenia klastra nie powiodło się
Aby dokładnie bądź na bieżąco z faktu, że klaster dostosowanie nie powiodło się zgodnie z oczekiwaniami, ważne jest, aby zgłosić wyjątek, niepowodzeniem tworzenia klastra. Na przykład można przetworzyć pliku, jeśli istnieje i obsługę w przypadku błędu, gdy plik nie istnieje. Może to zapewnić skrypt zakończy pracę bezpiecznie i stan klastra jest poprawnie znany. Poniższy fragment kodu przedstawiono przykładowy sposób można to osiągnąć:

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

W tym fragmencie Jeśli plik nie istnieje, spowodowałoby to stan, w którym skrypt faktycznie opuszcza bezpiecznie po wydrukowaniu komunikat o błędzie, a klastra osiągnie uruchomiona przy założeniu, że "pomyślnie" ukończyć procesu dostosowywania klastra. Aby dokładnie powiadamianych fakt, że klaster dostosowania zasadniczo nie powiodło się zgodnie z oczekiwaniami ze względu na Brak pliku, więcej należy zgłosić wyjątek, niepowodzeniem, krok dostosowania klastra. Aby to zrobić, należy użyć poniższy fragment kodu przykładowej zamiast tego.

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## <a name="checklist-for-deploying-a-script-action"></a>Lista kontrolna wdrażania akcji skryptu
Poniżej przedstawiono kroki, które Wybraliśmy podczas przygotowania do wdrożenia tych skryptów:

1. Umieścić pliki, które zawierają niestandardowe skrypty w miejscu, który jest dostępny dla węzłów klastra podczas wdrażania. Może to być domyślne lub dodatkowych kont magazynu określony w czasie wdrażania klastra lub innych kontenera magazynu publicznie.
2. Dodaj kontroli w skryptach, aby upewnić się, że są one wykonywane idempotently, dzięki czemu skrypt mogą być wykonywane wiele razy w tym samym węźle.
3. Użyj `Write-Output` polecenia cmdlet programu Azure PowerShell do drukowania do STDOUT, a także STDERR. Nie używaj `Write-Host`.
4. Użyj folder plików tymczasowych, takiego jak `$env:TEMP`, aby zachować pobrany plik używany przez skrypty i następnie wyczyść je po wykonaniu skryptów.
5. Zainstaluj oprogramowanie niestandardowe tylko na D:\ lub C:\apps. Są one zastrzeżone nie należy używać innych lokalizacji na dysku C:. Instalowanie plików na dysku C: poza folderem C:\apps może spowodować błędy instalacji podczas reimages węzła.
6. W przypadku, gdy zmieniono ustawienia na poziomie systemu operacyjnego lub plików konfiguracyjnych usługi Hadoop, możesz ponownie uruchomić usługi HDInsight, dzięki czemu mogą one odbierać wszystkie ustawienia poziomu systemu operacyjnego, takich jak zmiennych środowiskowych w skryptach.

## <a name="debug-custom-scripts"></a>Debugowanie skryptów niestandardowych
Dzienniki błędów skryptów są przechowywane wraz z innymi dane wyjściowe w domyślne konto magazynu określone dla klastra podczas jego tworzenia. Dzienniki są przechowywane w tabeli o nazwie *u < \cluster-name-fragment >< \time-stamp > setuplog*. Są to dzienniki zagregowane, które rekordy z wszystkie węzły (węzła głównego i węzły procesów roboczych), na których skrypt jest uruchamiany w klastrze.

Jest łatwy sposób Sprawdź dzienniki do użycia narzędzi HDInsight Tools for Visual Studio. Dla instalacji narzędzi, zobacz [rozpocząć korzystanie z narzędzi Visual Studio Hadoop dla usługi HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-or-update-data-lake-tools-for-visual-studio)

**Aby sprawdzić dziennika za pomocą programu Visual Studio**

1. Otwórz program Visual Studio.
2. Kliknij przycisk **widoku**, a następnie kliknij przycisk **Eksploratora serwera**.
3. Kliknij prawym przyciskiem myszy "Azure", kliknij przycisk Połącz z **subskrypcji platformy Microsoft Azure**, a następnie wprowadź swoje poświadczenia.
4. Rozwiń węzeł **magazynu**, rozwiń konto magazynu Azure używany jako domyślny system plików, rozwiń **tabel**, a następnie kliknij dwukrotnie nazwę tabeli.

Można również zdalnego w węzłach klastra, aby wyświetlić zarówno STDOUT i STDERR niestandardowych skryptów. Dzienniki w każdym węźle są specyficzne tylko dla tego węzła i logują się do **C:\HDInsightLogs\DeploymentAgent.log**. Te pliki dziennika rejestrować wszystkie dane wyjściowe skryptu niestandardowego. Przykład fragment dziennika akcji skryptu Spark wygląda następująco:

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


W tym dzienniku jest jasne, czy akcja skryptu Spark zostało wykonane na Maszynie wirtualnej o nazwie HEADNODE0 i że nie zwrócono wyjątek podczas wykonywania.

W przypadku, gdy wystąpi błąd wykonania, dane wyjściowe opisujące on również znajduje się w tym pliku dziennika. Informacje zawarte w tych dziennikach powinna być pomocnych w debugowaniu problemów skryptu, które mogą wystąpić.

## <a name="see-also"></a>Zobacz także
* [Dostosowywanie klastrów usługi HDInsight przy użyciu akcji skryptu][hdinsight-cluster-customize]
* [Zainstalować i używać platformy Spark w usłudze hdinsight][hdinsight-install-spark]
* [Zainstalować i używać Solr w klastrach HDInsight](hdinsight-hadoop-solr-install.md).
* [Zainstalować i używać Giraph w klastrach HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx
