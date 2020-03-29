---
title: Rozwiązywanie problemów z usługą Azure Data Factory
description: Dowiedz się, jak rozwiązywać problemy z korzystaniem z usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 38fd14c1-5bb7-4eef-a9f5-b289ff9a6942
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: anandsub
robots: noindex
ms.openlocfilehash: 81ae5c3c702108d854e4dfde93001d5c99875666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931580"
---
# <a name="troubleshoot-data-factory-issues"></a>Rozwiązywanie problemów z usługą Data Factory
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory. 

Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów podczas korzystania z usługi Azure Data Factory. W tym artykule nie zawiera listy wszystkich możliwych problemów podczas korzystania z usługi, ale obejmuje niektóre problemy i ogólne wskazówki dotyczące rozwiązywania problemów.   

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Błąd: subskrypcja nie jest zarejestrowana do korzystania z przestrzeni nazw „Microsoft.DataFactory”
Wyświetlenie tego błędu oznacza, że dostawca zasobów usługi Azure Data Factory nie został zarejestrowany na maszynie. Wykonaj następujące czynności:

1. Uruchom program Azure PowerShell.
2. Zaloguj się do konta platformy Azure przy użyciu następującego polecenia.

    ```powershell
    Connect-AzAccount
    ```
3. Uruchom następujące polecenie, aby zarejestrować dostawcę usługi Azure Data Factory.

    ```powershell        
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Problem: Nieautoryzowany błąd podczas uruchamiania polecenia cmdlet usługi Data Factory
Przypuszczalnie nie używasz prawidłowego konta lub subskrypcji platformy Azure w programie Azure PowerShell. Użyj następujących poleceń cmdlet, aby wybrać odpowiednie konto i subskrypcję platformy Azure do stosowania w programie Azure PowerShell.

1. Connect-AzAccount - Użyj odpowiedniego identyfikatora użytkownika i hasła
2. Get-AzSubscription — wyświetl wszystkie subskrypcje konta.
3. Select-AzSubscription &lt;nazwa&gt; subskrypcji — wybierz odpowiednią subskrypcję. Użyj tego samego, którego używasz do utworzenia fabryki danych w witrynie Azure portal.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Problem: nie można uruchomić instalatora usługi Data Management Gateway Express z witryny Azure portal
Instalacja ekspresowa bramy zarządzania danymi wymaga przeglądarki Internet Explorer lub przeglądarki zgodnej z technologią Microsoft ClickOnce. Jeśli uruchomienie instalacji ekspresowej nie powiedzie się, wykonaj jedną z poniższych czynności:

* Użyj programu Internet Explorer lub przeglądarki sieci Web zgodnej z programem Microsoft ClickOnce.

    Jeśli korzystasz z Chrome, przejdź do [sklepu internetowego Chrome](https://chrome.google.com/webstore/), wyszukaj za pomocą słowa kluczowego "ClickOnce", wybierz jedno z rozszerzeń ClickOnce i zainstaluj je.

    Zrób to samo dla Firefoksa (zainstaluj dodatek). Kliknij przycisk Otwórz menu na pasku narzędzi (trzy poziome linie w prawym górnym rogu), kliknij pozycję Dodatki, wyszukaj słowo kluczowe „ClickOnce”, wybierz jedno z rozszerzeń ClickOnce i je zainstaluj.
* Użyj łącza **Ręczna konfiguracja** wyświetlana na tym samym bloku w portalu. Ta metoda służy do pobierania pliku instalacyjnego i uruchamiania go ręcznie. Po pomyślnym zakończeniu instalacji zostanie wyświetlone okno dialogowe Konfiguracja bramy zarządzania danymi. Skopiuj **klucz** z ekranu portalu i użyj go w menedżerze konfiguracji, aby ręcznie zarejestrować bramę w usłudze.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Problem: Nie można połączyć się z lokalnym programem SQL Server
Uruchom **program Menedżer konfiguracji bramy zarządzania danymi** na komputerze bramy i użyj karty **Rozwiązywanie problemów,** aby przetestować połączenie z programem SQL Server z komputera bramy. Aby uzyskać wskazówki dotyczące rozwiązywania problemów związanych z połączeniem/bramą, zobacz [Rozwiązywanie problemów z bramą.](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)   

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Problem: Plasterki wejściowe są w stanie oczekiwania na zawsze
Plastry mogą być w stanie **oczekiwania** z różnych powodów. Jednym z najczęstszych powodów jest to, że właściwość **zewnętrzna** nie jest ustawiona na **true**. Każdy zestaw danych, który jest produkowany poza zakresem usługi Azure Data Factory powinny być oznaczone właściwością **zewnętrzną.** Ta właściwość wskazuje, że dane są zewnętrzne i nie są wspierane przez żadne potoki w fabryce danych. Wycinki danych są oznaczane jako **Gotowy**, gdy dane staną się dostępne w odpowiednim magazynie.

Zobacz poniższy przykład wykorzystania właściwości **external**. Opcjonalnie można określić **externalData*** po ustawieniu zewnętrznego na true.

Dodatkowe informacje na temat tej właściwości można znaleźć w artykule [Zestawy danych](data-factory-create-datasets.md).

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

### <a name="problem-hybrid-copy-operation-fails"></a>Problem: Operacja kopiowania hybrydowego kończy się niepowodzeniem
Zobacz [Rozwiązywanie problemów z bramą,](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) aby uzyskać instrukcje rozwiązywania problemów z kopiowaniem do/z lokalnego magazynu danych przy użyciu bramy zarządzania danymi.

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Problem: Inicjowanie obsługi administracyjnej HDInsight na żądanie kończy się niepowodzeniem
Podczas korzystania z połączonej usługi typu HDInsightOnDemand należy określić nazwę linkedServiceName, która wskazuje usługę Azure Blob Storage. Usługa Data Factory korzysta z tego magazynu do przechowywania dzienników i plików pomocniczych dla klastra HDInsight na żądanie.  Czasami inicjowanie obsługi klastra HDInsight na żądanie kończy się niepowodzeniem z następującym błędem:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Błąd ten wskazuje zazwyczaj, że lokalizacja konta magazynu określona w parametrze linkedServiceName znajduje się w innej lokalizacji centrum danych niż lokalizacja, w której następuje inicjowanie obsługi HDInsight. Przykład: jeśli fabryka danych znajduje się w zachodnie stany USA, a magazyn platformy Azure znajduje się we wschodnich stanach zjednoczonych, inicjowanie obsługi administracyjnej na żądanie kończy się niepowodzeniem w zachodnie stany USA.

Ponadto występuje druga właściwość JSON o nazwie additionalLinkedServiceNames, w której można określić dodatkowe konta magazynu dla usługi HDInsight na żądanie. Te dodatkowe połączone konta magazynu powinny znajdować się w tej samej lokalizacji co klaster HDInsight lub kończy się niepowodzeniem z tym samym błędem.

### <a name="problem-custom-net-activity-fails"></a>Problem: Niestandardowa aktywność platformy .NET kończy się niepowodzeniem
Zobacz [Debugowanie potoku z działaniem niestandardowym, aby](data-factory-use-custom-activities.md#troubleshoot-failures) uzyskać szczegółowe kroki.

## <a name="use-azure-portal-to-troubleshoot"></a>Rozwiązywanie problemów za pomocą witryny Azure Portal
### <a name="using-portal-blades"></a>Korzystanie z ostrzy portalu
Zobacz [Monitorowanie potoku,](data-factory-monitor-manage-pipelines.md) aby uzyskać instrukcje.

### <a name="using-monitor-and-manage-app"></a>Korzystanie z aplikacji Monitorowanie i zarządzanie
Zobacz [Monitorowanie potoków fabryki danych i zarządzanie nimi przy użyciu funkcji Monitoruj i zarządzaj aplikacją, aby](data-factory-monitor-manage-app.md) uzyskać szczegółowe informacje.

## <a name="use-azure-powershell-to-troubleshoot"></a>Rozwiązywanie problemów za pomocą programu Azure PowerShell
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Rozwiązywanie problemów z błędem za pomocą programu Azure PowerShell
Zobacz [Monitorowanie potoków fabryki danych przy użyciu programu Azure PowerShell,](data-factory-monitor-manage-pipelines.md) aby uzyskać szczegółowe informacje.

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: https://go.microsoft.com/fwlink/?LinkId=516971

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
