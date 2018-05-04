---
title: Rozwiązywanie problemów z fabryki danych Azure
description: Dowiedz się, jak rozwiązać problemy z przy użyciu fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 38fd14c1-5bb7-4eef-a9f5-b289ff9a6942
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: douglasl
robots: noindex
ms.openlocfilehash: a9a4f7b19d6ef08562d17474e252ce72fd0977dc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="troubleshoot-data-factory-issues"></a>Rozwiązywanie problemów z usługą Data Factory
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory, która jest ogólnie dostępna (GA). 

Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów w przypadku problemów przy użyciu fabryki danych Azure. W tym artykule nie są wyświetlane wszystkie możliwe problemy podczas korzystania z usługi, ale obejmuje pewne problemy i ogólne porady dotyczące rozwiązywania problemów.   

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Błąd: subskrypcja nie jest zarejestrowana do korzystania z przestrzeni nazw „Microsoft.DataFactory”
Wyświetlenie tego błędu oznacza, że dostawca zasobów usługi Azure Data Factory nie został zarejestrowany na maszynie. Wykonaj następujące czynności:

1. Uruchom program Azure PowerShell.
2. Zaloguj się do konta platformy Azure przy użyciu następującego polecenia.

    ```powershell
    Connect-AzureRmAccount
    ```
3. Uruchom następujące polecenie, aby zarejestrować dostawcę usługi fabryka danych Azure.

    ```powershell        
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Problem: Nieautoryzowanego błąd podczas uruchamiania polecenia cmdlet usługi fabryka danych
Przypuszczalnie nie używasz prawidłowego konta lub subskrypcji platformy Azure w programie Azure PowerShell. Użyj następujących poleceń cmdlet, aby wybrać odpowiednie konto i subskrypcję platformy Azure do stosowania w programie Azure PowerShell.

1. Connect-AzureRmAccount - Użyj Identyfikatora prawo użytkownika i hasła
2. Get-AzureRmSubscription - wyświetlić wszystkie subskrypcje dla konta.
3. SELECT-AzureRmSubscription &lt;Nazwa subskrypcji&gt; — Wybierz subskrypcję, do prawej. Użyj tego samego umożliwiające tworzenie fabryki danych w portalu Azure.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Problem: Nie można uruchomić instalacji Express danych zarządzania bramy z portalu Azure
Instalacja ekspresowa bramy zarządzania danymi wymaga przeglądarki Internet Explorer lub przeglądarki zgodnej z technologią Microsoft ClickOnce. Jeśli uruchomienie instalacji ekspresowej nie powiedzie się, wykonaj jedną z poniższych czynności:

* Użyj programu Internet Explorer lub przeglądarki sieci web zgodne Microsoft ClickOnce.

    Jeśli używasz przeglądarki Chrome, przejdź do [sklepu internetowego Chrome](https://chrome.google.com/webstore/), wyszukaj słowo kluczowe „ClickOnce”, wybierz jedno z rozszerzeń ClickOnce i je zainstaluj.

    Wykonaj te same czynności dla przeglądarki Firefox (Instalowanie dodatku). Kliknij przycisk Otwórz menu na pasku narzędzi (trzy poziome linie w prawym górnym rogu), kliknij pozycję Dodatki, wyszukaj słowo kluczowe „ClickOnce”, wybierz jedno z rozszerzeń ClickOnce i je zainstaluj.
* Użyj **Podręcznika instalacji** łącza wyświetlany na tym samym bloku w portalu. Takie podejście umożliwia Pobierz plik instalacyjny i uruchomić go ręcznie. Po pomyślnej instalacji, zostanie wyświetlone okno dialogowe konfiguracji bramy zarządzania danymi. Skopiuj **klucz** z ekranu portalu i użyj go w menedżerze konfiguracji, aby ręcznie zarejestrować bramę w usłudze.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Problem: Nie można nawiązać połączenia z lokalnego serwera SQL
Uruchom **Menedżera konfiguracji bramy zarządzania danymi** na komputerze bramy i użyj **Rozwiązywanie problemów** kartę, aby przetestować połączenie z programem SQL Server na maszynie bramy. Zobacz [rozwiązywania problemów bramy](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) porady dotyczące rozwiązywania problemów z bramy/połączenia problemy związane z.   

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Problem: Wycinki danych wejściowych znajdują się w Oczekiwanie na kiedykolwiek stanu
Wycinki może być w **oczekiwania** stanu z różnych przyczyn. Typowe przyczyny jest to, że **zewnętrznych** nie ustawiono właściwości **true**. Wszelkie zestawu danych, który jest tworzony poza zakresem fabryki danych Azure powinien być oznaczony przez **zewnętrznych** właściwości. Ta właściwość wskazuje, że danych jest zewnętrzne i nie kopii zapasowej przez dowolnego potoki w fabryce danych. Wycinki danych są oznaczane jako **Gotowy**, gdy dane staną się dostępne w odpowiednim magazynie.

Zobacz poniższy przykład wykorzystania właściwości **external**. Opcjonalnie można określić **externalData*** podczas ustawiania zewnętrznych na wartość true.

Zobacz [zestawów danych](data-factory-create-datasets.md) artykułu, aby uzyskać więcej informacji o tej właściwości.

```json
{
  "name": "CustomerTable",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "MyLinkedService",
    "typeProperties": {
      "folderPath": "MyContainer/MySubFolder/",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      }
    }
  }
}
```

Aby rozwiązać ten problem, dodaj właściwość **external** i opcjonalną sekcję **externalData** do definicji JSON tabeli wejściowej i utwórz tabelę ponownie.

### <a name="problem-hybrid-copy-operation-fails"></a>Problem: Operacja kopiowania hybrydowego kończy się niepowodzeniem.
Zobacz [rozwiązywania problemów bramy](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) kroki rozwiązywania problemów z kopiowaniem do/z lokalnymi danymi z magazynu przy użyciu bramy zarządzania danymi.

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Problem: HDInsight na żądanie obsługi kończy się niepowodzeniem
Korzystając z połączonej usługi typu HDInsightOnDemand, należy określić linkedServiceName, wskazujące do magazynu obiektów Blob Azure. Usługa Data Factory korzysta z tego magazynu do przechowywania dzienników i plików pomocniczych dla klastra HDInsight na żądanie.  Czasami inicjowanie obsługi klastra HDInsight na żądanie kończy się niepowodzeniem z następującym błędem:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Błąd ten wskazuje zazwyczaj, że lokalizacja konta magazynu określona w parametrze linkedServiceName znajduje się w innej lokalizacji centrum danych niż lokalizacja, w której następuje inicjowanie obsługi HDInsight. Przykład: Jeśli fabrykę danych zachodnie stany USA i Magazyn Azure to w wschodnie stany USA, udostępniania na żądanie kończy się niepowodzeniem w zachodnie stany USA.

Ponadto występuje druga właściwość JSON o nazwie additionalLinkedServiceNames, w której można określić dodatkowe konta magazynu dla usługi HDInsight na żądanie. Te dodatkowe połączone konta magazynu musi należeć do tej samej lokalizacji co klaster usługi HDInsight lub jego wykonanie nie powiodło się ten sam błąd.

### <a name="problem-custom-net-activity-fails"></a>Problem: Niestandardowe działania .NET nie powiedzie się.
Zobacz [debugowania potoku z działań niestandardowych](data-factory-use-custom-activities.md#troubleshoot-failures) szczegółowy opis kroków.

## <a name="use-azure-portal-to-troubleshoot"></a>Rozwiązywanie problemów za pomocą portalu Azure
### <a name="using-portal-blades"></a>Za pomocą portalu bloków
Zobacz [potoku Monitor](data-factory-build-your-first-pipeline-using-editor.md#monitor-a-pipeline) czynności.

### <a name="using-monitor-and-manage-app"></a>Korzystanie z aplikacji Monitorowanie i zarządzanie
Zobacz [monitorowanie i zarządzanie nimi przy użyciu monitora i zarządzanie aplikacjami potoki fabryki danych](data-factory-monitor-manage-app.md) szczegółowe informacje.

## <a name="use-azure-powershell-to-troubleshoot"></a>Rozwiązywanie problemów przy użyciu programu Azure PowerShell
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Rozwiązywanie problemów z błędem przy użyciu programu Azure PowerShell
Zobacz [fabryki danych monitora potoków przy użyciu programu Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md#monitor-pipeline) szczegółowe informacje.

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
