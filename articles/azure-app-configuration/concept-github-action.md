---
title: Korzystanie z akcji usługi GitHub z synchronizacją konfiguracji aplikacji platformy Azure
description: Użyj akcji usługi GitHub, aby wyzwolić aktualizację wystąpienia konfiguracji aplikacji, gdy zdefiniowane akcje są wykonywane w repozytorium GitHub
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 9d60f1885a85fd7d45090f1cb4905a3d95d9d1d6
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523717"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>Synchronizowanie wystąpienia usługi App Configuration przy użyciu funkcji GitHub Actions
Usługa Azure App Configuration używa akcji usługi GitHub do wyzwalania aktualizacji wystąpienia konfiguracji aplikacji na podstawie akcji wykonywanych w repozytorium GitHub. Przepływy pracy usługi GitHub wyzwalają aktualizacje konfiguracji, umożliwiając integrację tych aktualizacji w ramach tego samego przepływu pracy, który służy do aktualizowania kodu aplikacji.

[Przepływ pracy](https://help.github.com/articles/about-github-actions#workflow) akcji usługi GitHub definiuje zautomatyzowany proces w repozytorium GitHub. Ten proces zawiera informacje dotyczące kompilowania i wdrażania projektu GitHub w witrynie GitHub. Usługa Azure App Configuration udostępnia akcję *synchronizacji konfiguracji aplikacji platformy Azure* w celu włączenia aktualizacji wystąpienia konfiguracji aplikacji w przypadku wprowadzenia zmian w repozytorium źródłowym. 

Plik YAML (. yml) znaleziony w ścieżce `/.github/workflows/` repozytorium definiuje przepływ pracy. Ta definicja zawiera kroki i parametry przepływu pracy.

Zdarzenia GitHub, takie jak wypychanie do repozytorium, mogą wyzwalać przepływ pracy akcji usługi GitHub.  Akcja *synchronizacji konfiguracji aplikacji platformy Azure* umożliwia wyzwalanie aktualizacji wystąpienia konfiguracji aplikacji, gdy wystąpi określona akcja usługi GitHub. Aktualizacje konfiguracji można wyzwalać podczas wypychania, przeglądania lub rozgałęziania plików konfiguracji aplikacji, tak jak w przypadku kodu aplikacji.

[Dokumentacja](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) usługi GitHub zawiera szczegółowy widok przepływów pracy i akcji usługi GitHub. 

## <a name="enable-github-actions-in-your-repository"></a>Włączanie akcji usługi GitHub w repozytorium
Aby rozpocząć korzystanie z tej akcji usługi GitHub, przejdź do repozytorium i wybierz kartę **Akcje** . kliknij pozycję **Nowy przepływ pracy**, a następnie **samodzielnie Skonfiguruj przepływ pracy**. Na koniec Wyszukaj ciąg "Azure App Configuration Sync" w portalu Marketplace.
> [!div class="mx-imgBorder"]
> ![wybierz kartę Akcja](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![wybrać akcję synchronizacji konfiguracji aplikacji](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Synchronizuj pliki konfiguracji po wypchnięciu
Ta akcja synchronizuje pliki konfiguracji aplikacji platformy Azure po wypchnięciu zmiany do `appsettings.json`. Gdy deweloper wypycha zmianę do `appsettings.json`, Akcja synchronizacji konfiguracji aplikacji aktualizuje wystąpienie konfiguracji aplikacji o nowe wartości.

Pierwsza sekcja tego przepływu pracy określa, że akcja jest wyzwalana *na* *wypchnięciu* zawierającym `appsettings.json` do gałęzi *głównej* . Druga sekcja zawiera zadania uruchamiane po wyzwoleniu akcji. Akcja sprawdza odpowiednie pliki i aktualizuje wystąpienie konfiguracji aplikacji przy użyciu parametrów połączenia przechowywanych jako wpis tajny w repozytorium.  Aby uzyskać więcej informacji o używaniu wpisów tajnych w usłudze GitHub, zobacz artykuł dotyczący tworzenia i używania zaszyfrowanych kluczy tajnych w witrynie [GitHub](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) .

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your                        
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }} 
          separator: ':' 
```

## <a name="use-a-dynamic-label-on-sync"></a>Użyj etykiety dynamicznej podczas synchronizacji
Poprzednia akcja spowoduje zaktualizowanie wystąpienia konfiguracji aplikacji przy każdej aktualizacji `appsettings.json`. Ta akcja wstawia dynamiczną etykietę dla każdej synchronizacji, co gwarantuje, że każda Synchronizacja może być jednoznacznie zidentyfikowana i umożliwi zamapowanie zmian w kodzie na zmiany w konfiguracji.

Pierwsza sekcja tego przepływu pracy określa, że akcja jest wyzwalana *na* *wypchnięciu* zawierającym `appsettings.json` do gałęzi *głównej* . Druga sekcja uruchamia zadanie, które tworzy unikatową etykietę aktualizacji konfiguracji na podstawie skrótu zatwierdzania. Następnie zadanie aktualizuje wystąpienie konfiguracji aplikacji przy użyciu nowych wartości i unikatowych etykiet dla tej aktualizacji.

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # Creates a label based on the branch name and the first 8 characters          
      # of the commit hash 
      - id: determine_label 
        run: echo ::set-output name=LABEL::"${GITHUB_REF#refs/*/}/${GITHUB_SHA:0:8}" 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: ${{ steps.determine_label.outputs.LABEL }} 
```

## <a name="use-strict-sync"></a>Użyj ścisłej synchronizacji
Gdy tryb Strict jest włączony, synchronizacja gwarantuje, że wystąpienie konfiguracji aplikacji jest zgodne z plikiem konfiguracji dla danego prefiksu i etykiety. Pary klucz-wartość o tym samym prefiksie i etykiecie, które nie znajdują się w pliku konfiguracji, są usuwane. 
 
Jeśli tryb Strict nie jest włączony, synchronizacja spowoduje ustawienie tylko wartości klucza z pliku konfiguracji. Żadna para klucz-wartość nie zostanie usunięta. 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: 'Label' 
          prefix: 'Prefix:' 
          strict: true 
```

## <a name="use-max-depth-to-limit-github-action"></a>Użyj maksymalnej głębokości, aby ograniczyć akcję GitHub
Domyślnym zachowaniem dla zagnieżdżonych atrybutów JSON jest spłaszczenie całego obiektu.  Poniższy kod JSON definiuje tę parę klucz-wartość:

| Klucz | Wartość |
| --- | --- |
| Obiekt: wewnętrzny: InnerKey | InnerValue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

Jeśli zagnieżdżony obiekt jest przeznaczony do wartości wypychanej do wystąpienia konfiguracji, można użyć wartości *głębokości* , aby zatrzymać spłaszczanie na odpowiedniej głębokości. 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          depth: 2 
```

Ze względu na głębokość 2, Poniższy przykład zwraca teraz następującą parę klucz-wartość:

| Klucz | Wartość |
| --- | --- |
| Obiekt: wewnętrzny | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>Informacje o danych wejściowych akcji
Parametry wejściowe określają dane używane przez akcję podczas środowiska uruchomieniowego.  Poniższa tabela zawiera parametry wejściowe akceptowane przez synchronizację konfiguracji aplikacji i oczekiwane wartości dla każdego z nich.  Aby uzyskać więcej informacji na temat danych wejściowych akcji usługi GitHub, zobacz [dokumentację](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)usługi GitHub.

> [!Note]
> W identyfikatorach wejściowych nie jest rozróżniana wielkość liter.


| Nazwa wejściowa | Wymagana? | Wartość |
|----|----|----|
| configurationFile | Tak | Ścieżka względna do pliku konfiguracji w repozytorium.  Wzorce globalizowania są obsługiwane i mogą zawierać wiele plików. |
| format | Tak | Format pliku konfiguracji.  Prawidłowe formaty to: JSON, YAML i właściwości. |
| connectionString | Tak | Parametry połączenia dla wystąpienia konfiguracji aplikacji. Parametry połączenia powinny być przechowywane jako wpis tajny w repozytorium GitHub, a w przepływie pracy powinna być użyta tylko nazwa klucza tajnego. |
| rozdzielając | Tak | Separator używany podczas spłaszczania pliku konfiguracji do par klucz-wartość.  Prawidłowe wartości to:. , ; : - _ __ / |
| prefix | Nie | Prefiks, który ma zostać dodany do początku kluczy. |
| label | Nie | Etykieta użyta podczas ustawiania par klucz-wartość. Jeśli nie zostanie określony, zostanie użyta etykieta o wartości null. |
| surowszych | Nie | Wartość logiczna określająca, czy tryb Strict jest włączony. Wartość domyślna to false. |
| ścisł | Nie | Maksymalna głębokość spłaszczania pliku konfiguracji.  Głębokość musi być liczbą dodatnią.  Wartość domyślna nie będzie mieć maksymalnej głębokości. |
| tagów | Nie | Określa zestaw tagów dla par klucz-wartość.  Oczekiwany format to skonwertowaneja postać obiektu JSON o następującym kształcie: {[propertyName: String]: String;} Każda właściwość name-value jest tagiem. |

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o akcji usługi GitHub dotyczącej synchronizacji konfiguracji aplikacji oraz sposobie jej użycia do automatyzowania aktualizacji wystąpienia konfiguracji aplikacji. Aby dowiedzieć się, jak usługa Azure App Configuration reaguje na zmiany w parach klucz-wartość, przejdź do następnego [artykułu](./concept-app-configuration-event.md).
