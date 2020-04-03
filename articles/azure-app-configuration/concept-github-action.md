---
title: Synchronizowanie repozytorium Usługi GitHub z konfiguracją aplikacji
description: Użyj akcji GitHub, aby automatycznie zaktualizować wystąpienie konfiguracji aplikacji podczas aktualizowania repozytorium Usługi GitHub.
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 602ccddf97938022df3c5903b573608558fe5d35
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585477"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>Synchronizowanie repozytorium Usługi GitHub z konfiguracją aplikacji

Zespoły, które chcą nadal korzystać z istniejących praktyk kontroli źródła, mogą używać akcji Usługi GitHub do automatycznej synchronizacji repozytorium GitHub z ich magazynem konfiguracji aplikacji. Dzięki temu można wprowadzać zmiany w plikach konfiguracyjnych w zwykły sposób, podczas gdy uzyskanie korzyści konfiguracji aplikacji, takich jak: <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Scentralizowana konfiguracja poza kodem <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Aktualizowanie konfiguracji bez ponownego rozmieszczania całej aplikacji <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Integracja z usługami, takimi jak usługa Azure App Service i funkcje. 

[Przepływ pracy](https://help.github.com/articles/about-github-actions#workflow) akcji GitHub definiuje zautomatyzowany proces w repozytorium GitHub. Akcja *synchronizacji konfiguracji aplikacji platformy Azure* wyzwala aktualizacje wystąpienia konfiguracji aplikacji po wprowadzeniu zmian w repozytorium źródłowym. Używa pliku YAML (.yml) znalezionego `/.github/workflows/` w ścieżce repozytorium do definiowania kroków i parametrów. Aktualizacje konfiguracji można wyzwolić podczas wypychania, przeglądania lub rozgałęzienia plików konfiguracyjnych aplikacji, tak jak w przypadku kodu aplikacji.

[Dokumentacja](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) usługi GitHub zawiera szczegółowy widok przepływów pracy i akcji usługi GitHub. 

## <a name="enable-github-actions-in-your-repository"></a>Włączanie akcji usługi GitHub w repozytorium
Aby rozpocząć korzystanie z tej akcji GitHub, przejdź do repozytorium i wybierz kartę **Akcje.** Kliknij **nowy przepływ pracy**, a następnie **skonfiguruj przepływ pracy samodzielnie.** Na koniec wyszukaj w portalu marketplace hasło "Synchronizacja konfiguracji aplikacji platformy Azure".
> [!div class="mx-imgBorder"]
> ![Wybierz kartę Akcja](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Wybierz akcję synchronizacji konfiguracji aplikacji](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Synchronizowanie plików konfiguracyjnych po wypychanie
Ta akcja synchronizuje pliki konfiguracji aplikacji platformy `appsettings.json`Azure, gdy zmiana jest wypychany do . Gdy deweloper wypycha `appsettings.json`zmiany do , akcja synchronizacja konfiguracji aplikacji aktualizuje wystąpienie konfiguracji aplikacji z nowymi wartościami.

Pierwsza sekcja tego przepływu pracy określa, że akcja wyzwala `appsettings.json` *na* *wypychanie* zawierające do gałęzi *głównej.* Druga sekcja zawiera listę zadań uruchamianych po wyzwoleniu akcji. Akcja wyewidencjonuje odpowiednie pliki i aktualizuje wystąpienie konfiguracji aplikacji przy użyciu ciągu połączenia przechowywanego jako klucz tajny w repozytorium.  Aby uzyskać więcej informacji na temat używania wpisów tajnych w usłudze GitHub, zobacz [artykuł usługi GitHub](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) na temat tworzenia zaszyfrowanych wpisów tajnych i używania ich.

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

## <a name="use-a-dynamic-label-on-sync"></a>Używanie etykiety dynamicznej podczas synchronizacji
Poprzednia akcja aktualizuje wystąpienie `appsettings.json` konfiguracji aplikacji za każdym razem, gdy jest aktualizowane. Ta akcja wstawia etykietę dynamiczną na każdej synchronizacji, zapewniając, że każda synchronizacja może być jednoznacznie zidentyfikowana i umożliwiająca mapowanie zmian kodu w celu zmiany konfiguracji.

Pierwsza sekcja tego przepływu pracy określa, że akcja wyzwala `appsettings.json` *na* *wypychanie* zawierające do gałęzi *głównej.* W drugiej sekcji jest uruchamiane zadanie, które tworzy unikatową etykietę dla aktualizacji konfiguracji na podstawie skrótu zatwierdzenia. Zadanie następnie aktualizuje wystąpienie konfiguracji aplikacji o nowe wartości i unikatową etykietę dla tej aktualizacji.

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

## <a name="use-strict-sync"></a>Korzystanie ze ścisłej synchronizacji
Gdy tryb ścisły jest włączony, synchronizacja zapewnia, że wystąpienie konfiguracji aplikacji jest dokładnie zgodne z plikiem konfiguracji dla danego prefiksu i etykiety. Pary klucz-wartość z tym samym prefiksem i etykietą, których nie ma w pliku konfiguracyjnym, są usuwane. 
 
Jeśli tryb ścisły nie jest włączony, synchronizacja ustawi tylko wartości klucza z pliku konfiguracyjnego. Żadne pary klucz-wartość nie zostaną usunięte. 

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

## <a name="use-max-depth-to-limit-github-action"></a>Użyj maksymalnej głębokości, aby ograniczyć działanie GitHub
Domyślnym zachowaniem zagnieżdżonych atrybutów JSON jest spłaszczenie całego obiektu.  JSON poniżej definiuje tę parę klucz-wartość:

| Klucz | Wartość |
| --- | --- |
| Obiekt:Wewnętrzny:Wewnętrzny klawisz | Wartość wewnętrzna |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

Jeśli obiekt zagnieżdżony ma być wartością wypchniętą do wystąpienia konfiguracji, można użyć wartości *głębokości,* aby zatrzymać spłaszczenie na odpowiedniej głębokości. 

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

Biorąc pod uwagę głębokość 2, powyższy przykład zwraca teraz następującą parę klucz-wartość:

| Klucz | Wartość |
| --- | --- |
| Obiekt:Wewnętrzny | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>Opis danych wejściowych akcji
Parametry wejściowe określają dane używane przez akcję w czasie wykonywania.  Poniższa tabela zawiera parametry wejściowe zaakceptowane przez synchronizację konfiguracji aplikacji i oczekiwane wartości dla każdego z nich.  Aby uzyskać więcej informacji na temat danych wejściowych akcji usługi GitHub, zobacz [dokumentację](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)usługi GitHub.

> [!Note]
> Identyfikatory wejściowe są niewrażliwe na wielkości liter.


| Nazwa wejściowa | Wymagana? | Wartość |
|----|----|----|
| Configurationfile | Tak | Ścieżka względna do pliku konfiguracyjnego w repozytorium.  Wzorce glob są obsługiwane i mogą zawierać wiele plików. |
| format | Tak | Format pliku konfiguracyjnego.  Prawidłowe formaty to: JSON, YAML, właściwości. |
| Parametry połączenia | Tak | Parametry połączenia dla wystąpienia konfiguracji aplikacji. Parametry połączenia powinny być przechowywane jako klucz tajny w repozytorium GitHub i tylko nazwa tajnego powinna być używana w przepływie pracy. |
| Separator | Tak | Separator używany podczas spłaszczania pliku konfiguracyjnego do par klucza-wartości.  Prawidłowe wartości to: . , ; : - _ __ / |
| Prefiks | Nie | Prefiks ma zostać dodany do początku kluczy. |
| label | Nie | Etykieta używana podczas ustawiania par klucz-wartość. Jeśli nie określono, używana jest etykieta zerowa. |
| ściśle | Nie | Wartość logiczna określająca, czy tryb ścisły jest włączony. Wartość domyślna to false. |
| Głębokość | Nie | Maksymalna głębokość spłaszczenia pliku konfiguracyjnego.  Głębokość musi być liczbą dodatnią.  Wartość domyślna nie będzie miała maksymalnej głębokości. |
| tags | Nie | Określa zestaw znaczników dla par klucz-wartość.  Oczekiwany format jest ciągiem znaków nych obiektu JSON o następującym kształcie: { [propertyName: string]: string; } Każda nazwa właściwości wartość staje się tagiem. |

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o akcji GitHub synchronizacji konfiguracji aplikacji i o tym, jak można ją używać do automatyzacji aktualizacji wystąpienia konfiguracji aplikacji. Aby dowiedzieć się, jak konfiguracja aplikacji platformy Azure reaguje na zmiany w parach klucz-wartość, przejdź do następnego [artykułu](./concept-app-configuration-event.md).
