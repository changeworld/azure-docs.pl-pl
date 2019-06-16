---
title: pozyskiwanie danych z usługi Logstash do usługi Azure Data Explorer
description: W tym artykule dowiesz się, jak można pozyskać danych (załaduj) w Eksploratorze danych platformy Azure z programu Logstash
author: tamirkamara
ms.author: takamara
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 86f6732cbf2409d3c79a3d7709100e8af24988a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66494539"
---
# <a name="ingest-data-from-logstash-to-azure-data-explorer"></a>pozyskiwanie danych z usługi Logstash do usługi Azure Data Explorer

[Logstash](https://www.elastic.co/products/logstash) to potok przetwarzania danych po stronie serwera typu open source, który pozyskuje dane z wielu źródeł jednocześnie, przekształca dane, a następnie wysyła dane do Twojej ulubionej „przechowalni”. W tym artykule będzie wysyłać dane do Eksploratora danych platformy Azure, czyli usługi eksploracji danych szybka i wysoce skalowalnych danych dzienników i danych telemetrycznych. Na początku utworzysz tabelę i mapowanie danych w klastrze testowym, a następnie przekierujesz usługę Logstash w celu wysłania danych do tabeli, a następnie zweryfikowania wyników.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* [Baza danych i klaster testowy](create-cluster-database-portal.md) usługi Azure Data Explorer
* [Instrukcje dotyczące instalacji](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html) usługi Logstash w wersji 6+

## <a name="create-a-table"></a>Tworzenie tabeli

Po utworzeniu klastra i bazy danych nadszedł czas na utworzenie tabeli.

1. Uruchom następujące polecenie w oknie zapytania bazy danych w celu utworzenia tabeli:

    ```Kusto
    .create table logs (timestamp: datetime, message: string)
    ```

1. Uruchom następujące polecenie, aby potwierdzić, że nowa tabela `logs` została utworzona i jest pusta:
    ```Kusto
    logs
    | count
    ```

## <a name="create-a-mapping"></a>Tworzenie mapowania

Mapowanie jest używane przez usługę Azure Data Explorer do przekształcania danych przychodzących do docelowego schematu tabeli. Następujące polecenie tworzy nowe mapowanie o nazwie `basicmsg`, które wyodrębnia właściwości z przychodzącego kodu JSON zgodnie z informacjami w elemencie `path` i wysyła je do elementu `column`.

Uruchom następujące polecenie w oknie zapytania:

```Kusto
.create table logs ingestion json mapping 'basicmsg' '[{"column":"timestamp","path":"$.@timestamp"},{"column":"message","path":"$.message"}]'
```

## <a name="install-the-logstash-output-plugin"></a>Instalowanie wtyczki danych wyjściowych usługi Logstash

Wtyczka danych wyjściowych usługi Logstash wyjściowych komunikuje się z usługą Azure Data Explorer i wysyła dane do usługi.
Uruchom następujące polecenie w katalogu głównym usługi Logstash, aby zainstalować wtyczkę:

```sh
bin/logstash-plugin install logstash-output-kusto
```

## <a name="configure-logstash-to-generate-a-sample-dataset"></a>Konfigurowanie usługi Logstash w celu wygenerowania przykładowego zestawu danych

Usługa Logstash może wygenerować przykładowe zdarzenia, których można użyć do testowania kompleksowego potoku.
Jeśli już używasz usługi Logstash i masz dostęp do własnego strumienia zdarzeń, przejdź do następnej sekcji. 

> [!NOTE]
> Jeśli używasz własnych danych, zmień obiekty tabeli i mapowanie zdefiniowane w poprzednich krokach.

1. Edytuj nowy plik tekstowy, który będzie zawierać wymagane ustawienia potoku (przy użyciu vi):

    ```sh
    vi test.conf
    ```

1. Wklej następujące ustawienia informujące usługę Logstash o konieczności wygenerowania 1000 zdarzeń testowych:

    ```ruby
    input {
        stdin { }
        generator {
            message => "Test Message 123"
            count => 1000
        }
    }
    ```

Ta konfiguracja obejmuje również dodatek danych wejściowych `stdin`, który umożliwi samodzielne pisanie większej liczby wiadomości (pamiętaj, aby użyć klawisza *Enter* w celu przesłania ich do potoku).

## <a name="configure-logstash-to-send-data-to-azure-data-explorer"></a>Konfigurowanie usługi Logstash w celu wysyłania danych do usługi Azure Data Explorer

Wklej następujące ustawienia w pliku konfiguracji użytym w poprzednim kroku. Zastąp wszystkie symbole zastępcze wartościami odpowiednimi dla konfiguracji. Aby uzyskać więcej informacji, zobacz [Creating an AAD Application (Tworzenie aplikacji usługi AAD)](/azure/kusto/management/access-control/how-to-provision-aad-app). 

```ruby
output {
    kusto {
            path => "/tmp/kusto/%{+YYYY-MM-dd-HH-mm-ss}.txt"
            ingest_url => "https://ingest-<cluster name>.kusto.windows.net/"
            app_id => "<application id>"
            app_key => "<application key/secret>"
            app_tenant => "<tenant id>"
            database => "<database name>"
            table => "<target table>" # logs as defined above
            mapping => "<mapping name>" # basicmsg as defined above
    }
}
```

| Nazwa parametru | Opis |
| --- | --- |
| **path** | Dodatek usługi Logstash zapisuje zdarzenia w plikach tymczasowych przed ich wysłaniem do usługi Azure Data Explorer. Ten parametr zawiera ścieżkę, w której należy zapisać pliki, oraz wyrażenie czasu na potrzeby rotacji pliku. Pozwolą one na wyzwalanie przekazywania w usłudze Azure Data Explorer.|
| **ingest_url** | Punkt końcowy Kusto na potrzeby komunikacji dotyczącej pozyskiwania.|
| **app_id**, **app_key** i **app_tenant**| Poświadczenia wymagane do połączenia z usługą Azure Data Explorer. Pamiętaj, aby używać aplikacji z uprawnieniami do pozyskiwania. |
| **database**| Nazwa bazy danych, w której zostaną umieszczone zdarzenia. |
| **table** | Nazwa docelowej tabeli, w której zostaną umieszczone zdarzenia. |
| **mapping** | Mapowanie jest używane do mapowania ciągu JSON przychodzącego zdarzenia w prawidłowym formacie wiersza (definiuje, która właściwość przechodzi w którą kolumnę). |

## <a name="run-logstash"></a>Uruchamianie usługi Logstash

Jesteśmy teraz gotowi do uruchomienia usługi Logstash i przetestowania naszych ustawień.

1. W katalogu głównym usługi Logstash uruchom następujące polecenie:

    ```sh
    bin/logstash -f test.conf
    ```

    Na ekranie powinny pojawić się informacje, a następnie 1000 komunikatów wygenerowanych przez naszą przykładową konfigurację. W tym momencie można również wprowadzić ręcznie więcej komunikatów.

1. Po kilku minutach uruchom następujące zapytanie usługi Data Explorer, aby wyświetlić komunikaty w zdefiniowanej tabeli:

    ```Kusto
    logs
    | order by timestamp desc
    ```

1. Wybieranie klawiszy Ctrl+C w celu zakończenia działania usługi Logstash

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Uruchom następujące polecenie w bazie danych, aby wyczyścić tabelę `logs`:

```Kusto
.drop table logs
```

## <a name="next-steps"></a>Kolejne kroki

* [Pisanie zapytań](write-queries.md)