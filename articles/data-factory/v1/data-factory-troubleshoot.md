---
title: Rozwiązywanie problemów w usłudze Azure Data Factory
description: Dowiedz się, jak rozwiązywać problemy związane z używaniem usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 38fd14c1-5bb7-4eef-a9f5-b289ff9a6942
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
author: gauravmalhot
ms.author: gamal
ms.reviewer: maghan
manager: craigg
robots: noindex
ms.openlocfilehash: 5f2627932eb2fd427d934eba322230222b5e6958
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002947"
---
# <a name="troubleshoot-data-factory-issues"></a>Rozwiązywanie problemów z usługą Data Factory
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory. 

Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów w przypadku problemów, korzystając z usługi Azure Data Factory. W tym artykule nie są wyświetlane wszystkie możliwe problemy podczas korzystania z usługi, ale obejmuje pewne problemy i ogólne porady dotyczące rozwiązywania problemów.   

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Błąd: Subskrypcja nie jest zarejestrowana w celu używania przestrzeni nazw "Microsoft.DataFactory"
Wyświetlenie tego błędu oznacza, że dostawca zasobów usługi Azure Data Factory nie został zarejestrowany na maszynie. Wykonaj następujące czynności:

1. Uruchom program Azure PowerShell.
2. Zaloguj się do konta platformy Azure, używając następującego polecenia.

    ```powershell
    Connect-AzAccount
    ```
3. Uruchom następujące polecenie, aby zarejestrować dostawcę usługi Azure Data Factory.

    ```powershell        
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Problem: Błąd dotyczący nieautoryzowanego dostępu przy uruchamianiu polecenia cmdlet usługi Data Factory
Przypuszczalnie nie używasz prawidłowego konta lub subskrypcji platformy Azure w programie Azure PowerShell. Użyj następujących poleceń cmdlet, aby wybrać odpowiednie konto i subskrypcję platformy Azure do stosowania w programie Azure PowerShell.

1. Connect — AzAccount — Użyj odpowiedniego Identyfikatora użytkownika i hasła
2. Get-AzSubscription — Wyświetl wszystkie subskrypcje dla konta.
3. Wybierz AzSubscription &lt;Nazwa subskrypcji&gt; — wybierz odpowiednią subskrypcję. Użyj tego samego używanej do tworzenia fabryki danych w witrynie Azure portal.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Problem: Nie można uruchomić danych ekspresowej instalacji bramy zarządzania w witrynie Azure portal
Instalacja ekspresowa bramy zarządzania danymi wymaga przeglądarki Internet Explorer lub przeglądarki zgodnej z technologią Microsoft ClickOnce. Jeśli uruchomienie instalacji ekspresowej nie powiedzie się, wykonaj jedną z poniższych czynności:

* Użyj programu Internet Explorer lub przeglądarki internetowej zgodnej z Microsoft ClickOnce.

    Jeśli używasz przeglądarki Chrome, przejdź do [sklepu internetowego Chrome](https://chrome.google.com/webstore/), wyszukaj słowo kluczowe „ClickOnce”, wybierz jedno z rozszerzeń ClickOnce i je zainstaluj.

    Zrób to samo dla programu Firefox (zainstalować dodatek). Kliknij przycisk Otwórz menu na pasku narzędzi (trzy poziome linie w prawym górnym rogu), kliknij pozycję Dodatki, wyszukaj słowo kluczowe „ClickOnce”, wybierz jedno z rozszerzeń ClickOnce i je zainstaluj.
* Użyj **Instalacja ręczna** linku do tego samego bloku, w portalu. Takie podejście umożliwia Pobierz plik instalacyjny i uruchomić ją ręcznie. Po instalacji zakończy się pomyślnie, zostanie wyświetlone okno dialogowe Konfiguracja bramy zarządzania danymi. Skopiuj **klucz** z ekranu portalu i użyj go w menedżerze konfiguracji, aby ręcznie zarejestrować bramę w usłudze.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Problem: Nie można nawiązać połączenie z lokalnym serwerem SQL
Uruchom **Menedżera konfiguracji bramy zarządzania danych** na maszynie bramy i użyj **Rozwiązywanie problemów** kartę, aby przetestować połączenie z programem SQL Server na maszynie bramy. Zobacz [problemów bramy](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) porady dotyczące rozwiązywania problemów z bramy połączenia/problemy związane z usługą.   

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Problem: Wycinki danych wejściowych mają przez cały czas stan Oczekujący
Wycinki mogą mieć **oczekiwania** stanu z różnych powodów. Jedną z typowych przyczyn jest to, że **zewnętrznych** nie ustawiono właściwości **true**. Dowolnym zestawie danych, który jest wytwarzany poza zakresem usługi Azure Data Factory powinien być oznaczony przez **zewnętrznych** właściwości. Ta właściwość wskazuje, że dane są zewnętrzne i nie wspierają ich żadne potoki w fabryce danych. Wycinki danych są oznaczane jako **Gotowy**, gdy dane staną się dostępne w odpowiednim magazynie.

Zobacz poniższy przykład wykorzystania właściwości **external**. Opcjonalnie możesz określić **externalData*** Gdy ustawisz external na true.

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

### <a name="problem-hybrid-copy-operation-fails"></a>Problem: Operacja kopiowania hybrydowych kończy się niepowodzeniem.
Zobacz [problemów bramy](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) Rozwiązywanie problemów z usługą kopiowania do i z danymi lokalnymi, wykonując czynności przedstawione zapisać używaniem bramy zarządzania danymi.

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Problem: HDInsight na żądanie aprowizacji kończy się niepowodzeniem
Korzystając z połączonej usługi typu HDInsightOnDemand, należy określić nazwę linkedServiceName, która wskazuje na usługi Azure Blob Storage. Usługa Data Factory korzysta z tego magazynu do przechowywania dzienników i plików pomocniczych dla klastra HDInsight na żądanie.  Czasami inicjowanie obsługi klastra HDInsight na żądanie kończy się niepowodzeniem z następującym błędem:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Błąd ten wskazuje zazwyczaj, że lokalizacja konta magazynu określona w parametrze linkedServiceName znajduje się w innej lokalizacji centrum danych niż lokalizacja, w której następuje inicjowanie obsługi HDInsight. Przykład: Jeśli fabryki danych znajduje się w regionie zachodnie stany USA, a usługa Azure storage znajduje się w regionie wschodnie stany USA, aprowizacja na żądanie nie powiedzie się w regionie zachodnie stany USA.

Ponadto występuje druga właściwość JSON o nazwie additionalLinkedServiceNames, w której można określić dodatkowe konta magazynu dla usługi HDInsight na żądanie. Te dodatkowe połączone konta magazynu musi należeć do tej samej lokalizacji co klaster HDInsight lub zakończy się niepowodzeniem z powodu tego samego błędu.

### <a name="problem-custom-net-activity-fails"></a>Problem: Niestandardowe działania platformy .NET kończą się niepowodzeniem
Zobacz [debugowanie potoku za pomocą działania niestandardowego](data-factory-use-custom-activities.md#troubleshoot-failures) szczegółowy opis kroków.

## <a name="use-azure-portal-to-troubleshoot"></a>Rozwiązywanie problemów przy użyciu witryny Azure portal
### <a name="using-portal-blades"></a>Przy użyciu bloków w witrynie portal
Zobacz [monitorowanie potoku](data-factory-build-your-first-pipeline-using-editor.md#monitor-a-pipeline) kroków.

### <a name="using-monitor-and-manage-app"></a>Korzystanie z aplikacji Monitorowanie i zarządzanie
Zobacz [monitorowanie i zarządzanie nimi potoki fabryki danych przy użyciu aplikacji monitorowanie i zarządzanie](data-factory-monitor-manage-app.md) Aby uzyskać szczegółowe informacje.

## <a name="use-azure-powershell-to-troubleshoot"></a>Rozwiązywanie problemów przy użyciu programu Azure PowerShell
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Rozwiązywanie problemów z błędem za pomocą programu Azure PowerShell
Zobacz [fabryki danych monitorowania potoków przy użyciu programu Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md#monitor-pipeline) Aby uzyskać szczegółowe informacje.

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
