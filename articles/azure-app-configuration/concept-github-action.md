---
title: Korzystanie z akcji usługi GitHub z synchronizacją konfiguracji aplikacji platformy Azure
description: Użyj akcji usługi GitHub, aby wyzwolić aktualizację wystąpienia konfiguracji aplikacji, gdy zdefiniowane akcje są wykonywane w repozytorium GitHub
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/14/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 269ae5630d1524cb8f89d3af8728892079f6eb5f
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899626"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>Synchronizowanie wystąpienia konfiguracji aplikacji za pomocą akcji usługi GitHub
Usługa Azure App Configuration używa akcji usługi GitHub do aktualizowania wystąpienia konfiguracji aplikacji, gdy jest wyzwalane przez akcję wykonywaną w repozytorium GitHub. Korzystając z przepływów pracy usługi GitHub, można aktualizować konfigurację aplikacji, umożliwiając integrację aktualizacji konfiguracji aplikacji w tym samym przepływie pracy, który służy do aktualizowania kodu aplikacji.

[Przepływ pracy](https://help.github.com/articles/about-github-actions#workflow) akcji usługi GitHub to zautomatyzowany proces zdefiniowany w repozytorium GitHub. Ten proces zawiera informacje dotyczące kompilowania i wdrażania projektu GitHub w witrynie GitHub. Usługa Azure App Configuration udostępnia akcję *synchronizacji konfiguracji aplikacji platformy Azure* w celu włączenia aktualizacji wystąpienia konfiguracji aplikacji w przypadku wprowadzenia zmian w repozytorium źródłowym. 

Przepływ pracy jest definiowany przez plik YAML (. yml) znaleziony w ścieżce `/.github/workflows/` repozytorium. Ta definicja zawiera różne kroki i parametry, które definiują przepływ pracy.

Zdarzenia GitHub, takie jak wypychanie do repozytorium, mogą wyzwalać przepływ pracy akcji usługi GitHub.  Na platformie Azure jest dostępna Akcja *synchronizacji konfiguracji aplikacji platformy Azure* , która umożliwia wyzwalanie aktualizacji wystąpienia konfiguracji aplikacji, gdy wystąpi określona akcja usługi GitHub. Dzięki temu zespoły mogą korzystać z podstawowych funkcji usługi GitHub podczas wypychania, przeglądania lub rozgałęziania plików konfiguracji aplikacji, tak samo jak w przypadku kodu aplikacji.

[Dokumentacja](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) usługi GitHub zawiera szczegółowy widok przepływów pracy i akcji usługi GitHub. 

## <a name="enable-github-actions-in-your-repository"></a>Włączanie akcji usługi GitHub w repozytorium
Aby rozpocząć korzystanie z tej akcji usługi GitHub, przejdź do repozytorium i wybierz kartę **Akcje** . Znajdź i wybierz akcję GitHub w portalu Marketplace, wyszukując frazę "Azure App Configuration Sync". 

> [!div class="mx-imgBorder"]
> ![wybierz kartę Akcja](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![wybrać akcję syn konfiguracji aplikacji](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Synchronizuj pliki konfiguracji po wypchnięciu
Ta akcja synchronizuje pliki konfiguracji aplikacji platformy Azure po wypchnięciu zmiany do `appsettings.json`. Gdy deweloper wprowadza i wypycha zmianę do `appsettings.json`, Akcja synchronizacji konfiguracji aplikacji aktualizuje wystąpienie konfiguracji aplikacji o nowe wartości.

Pierwsza sekcja tego przepływu pracy określa, że akcja jest wyzwalana *na* *wypchnięciu* zawierającym `appsettings.json` do gałęzi *głównej* . Druga sekcja zawiera zadania uruchamiane po wyzwoleniu akcji. Akcja sprawdza odpowiednie pliki i aktualizuje wystąpienie konfiguracji aplikacji przy użyciu parametrów połączenia przechowywanych jako wpis tajny w repozytorium.  Aby uzyskać więcej informacji o używaniu wpisów tajnych w usłudze GitHub, zobacz [ten artykuł](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) dotyczący tworzenia i używania zaszyfrowanych kluczy tajnych.

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
Poprzednia Akcja po prostu zaktualizował wystąpienie konfiguracji aplikacji przy każdej aktualizacji `appsettings.json`. Ta akcja wstawia dynamiczną etykietę dla każdej synchronizacji, co gwarantuje, że każda Synchronizacja może być jednoznacznie zidentyfikowana.  Pozwala to na szybkie zamapowanie zmian w konfiguracji.

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

Ze względu na głębokość 2, w powyższym przykładzie zwracamy następujący klucz: wartość para:

| Klucz | Wartość |
| --- | --- |
| Obiekt: wewnętrzny | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>Informacje o danych wejściowych akcji
Parametry wejściowe określają dane używane przez akcję podczas środowiska uruchomieniowego.  Poniższa tabela zawiera parametry wejściowe akceptowane przez synchronizację konfiguracji aplikacji i oczekiwane wartości dla każdego z nich.  Aby uzyskać więcej informacji na temat danych wejściowych akcji usługi GitHub, zobacz [dokumentację](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)usługi GitHub.

> [!Note]
> W identyfikatorach wejściowych nie jest rozróżniana wielkość liter.


| Nazwa wejściowa | Wymagana? | Wartość |
|----|----|----|
| configurationFile | Tak | Ścieżka do pliku konfiguracji w repozytorium względem katalogu głównego repozytorium.  Wzorce globalizowania są obsługiwane i mogą zawierać wiele plików. |
| format | Tak | Format pliku konfiguracji.  Prawidłowe formaty to: JSON, YAML i właściwości. |
| connectionString | Tak | Parametry połączenia dla wystąpienia konfiguracji aplikacji. Parametry połączenia powinny być przechowywane jako wpis tajny w repozytorium GitHub, a w przepływie pracy powinna być użyta tylko nazwa klucza tajnego. |
| rozdzielając | Tak | Separator używany podczas spłaszczania pliku konfiguracji do par klucz-wartość.  Prawidłowe wartości to:. , ; : - _ __ / |
| prefix | Nie | Prefiks, który ma zostać dodany do początku kluczy. |
| label | Nie | Etykieta użyta podczas ustawiania par klucz-wartość. Jeśli nie zostanie określony, zostanie użyta etykieta o wartości null. |
| surowszych | Nie | Wartość logiczna określająca, czy tryb Strict jest włączony. Wartość domyślna to false. |
| Ścisł | Nie | Maksymalna głębokość spłaszczania pliku konfiguracji.  Głębokość musi być liczbą dodatnią.  Wartość domyślna nie będzie mieć maksymalnej głębokości. |
| tags | Nie | Określa zestaw tagów dla par klucz-wartość.  Oczekiwany format to skonwertowaneja postać obiektu JSON o następującym kształcie: {[propertyName: String]: String;} Każda właściwość name-value jest tagiem. |

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o akcji usługi GitHub dotyczącej synchronizacji konfiguracji aplikacji oraz sposobie jej użycia do automatyzowania aktualizacji wystąpienia konfiguracji aplikacji. Aby dowiedzieć się, jak usługa Azure App Configuration reaguje na zmiany w parach klucz-wartość, przejdź do następnego [artykułu](./concept-app-configuration-event.md).
