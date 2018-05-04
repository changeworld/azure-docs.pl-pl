---
title: Odświeżanie asynchroniczne dla modeli usług Azure Analysis Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kod Odświeżanie asynchroniczne przy użyciu interfejsu API REST.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: d1862c5ed83033eb8de74459f26260864c646dfa
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Odświeżanie asynchroniczne przy użyciu interfejsu API REST
Przy użyciu języka programowania, który obsługuje wywołania REST, można wykonywać operacje asynchroniczne odświeżanie danych w sieci modele tabelaryczne usług Azure Analysis Services. W tym synchronizacji repliki tylko do odczytu dla zapytania skalowania w poziomie. 

Operacji odświeżania danych może zająć trochę czasu, w zależności od szeregu czynników, takich jak ilość danych, poziom optymalizacji za pomocą partycji itd. Te operacje mają tradycyjnie został wywołany z istniejących metod, takich jak przy użyciu [TOMASZ](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (tabelarycznym Object Model) [PowerShell](https://docs.microsoft.com/sql/analysis-services/powershell/analysis-services-powershell-reference) poleceń cmdlet, lub [TMSL](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) (modelu tabelarycznego Skrypty języka). Jednak te metody może wymagać często zawodnych, długotrwałą połączeń HTTP.

Interfejs API REST dla usług Azure Analysis Services umożliwia przeprowadzane asynchronicznie operacji odświeżania danych. Za pomocą interfejsu API REST, długotrwałe połączenia HTTP z aplikacji klienta nie są konieczne. Istnieją także inne wbudowane funkcje niezawodności, takie jak automatyczne ponawianie i wsadów zatwierdzeń.

## <a name="base-url"></a>Podstawowy adres URL

Podstawowy adres URL ma następujący format:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Rozważmy na przykład modelu o nazwie AdventureWorks na serwer o nazwie MójSerwer, znajdującego się w regionie Zachód nam Azure. Nazwa serwera jest:

```
asazure://westus.asazure.windows.net/myserver 
```

Podstawowy adres URL dla tej nazwy serwera jest:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Przy użyciu podstawowego adresu URL, zasobów i operacje można dołączać na podstawie następujących parametrów: 

![Odświeżanie asynchroniczne](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Wszystko, co kończy się **s** jest kolekcją.
- Wszystko, co kończy **()** jest funkcją.
- Cokolwiek innego jest obiektu/zasobów.

Na przykład umożliwia zlecenie POST w kolekcji odświeża wykonaj operację odświeżania:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Authentication

Wszystkie wywołania musi zostać uwierzytelniony z prawidłowym tokenem usługi Azure Active Directory (OAuth 2) w nagłówku autoryzacji i musi spełniać następujące wymagania:

- Token musi być token użytkownika lub nazwy głównej usługi aplikacji.
- Token musi mieć poprawną odbiorców ustawioną `https://*.asazure.windows.net`.
- Użytkownik lub aplikacja musi mieć wystarczające uprawnienia na serwerze lub modelu do żądanego wywoływania. Poziom uprawnień zależy od ról w modelu lub grupy administratorów na serwerze.

    > [!IMPORTANT]
    > Obecnie **administratora serwera** niezbędne są uprawnienia roli.

## <a name="post-refreshes"></a>POST /refreshes

Aby wykonać operację odświeżania, umożliwia zlecenie POST w kolekcji /refreshes Dodaj nowy element odświeżania w kolekcji. Nagłówek lokalizacji w odpowiedzi zawiera identyfikator odświeżania. Aplikacja kliencka można odłączyć i sprawdzić stan później, jeśli jest to wymagane, ponieważ jest asynchroniczne.

Tylko jedną operację odświeżania jest akceptowany w czasie dla modelu. Jeśli jest bieżącym uruchomioną operację odświeżania, a inny przesłaniu, zwracany jest kod stanu HTTP konflikt 409.

Treść może wyglądać w następujący sposób:

```
{
    "Type": "Full",
    "CommitMode": "transactional",
    "MaxParallelism": 2,
    "RetryCount": 2,
    "Objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer"
        },
        {
            "table": "DimDate"
        }
    ]
}
```

### <a name="parameters"></a>Parametry
Określanie parametrów nie jest wymagane. Wartość domyślna została zastosowana.

|Name (Nazwa)  |Typ  |Opis  |Domyślne  |
|---------|---------|---------|---------|
|Typ     |  wyliczenia       |  Typ przetwarzanie ma zostać wykonane. Typy są zgodne z TMSL [Odśwież polecenia](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/refresh-command-tmsl) typy: pełne, clearValues, obliczenia tylko dane automatyczne, dodawanie i defragmentacji.       |   Automatyczne      |
|CommitMode     |  wyliczenia       |  Określa, czy obiekty zostaną również zatwierdzone w partiach lub po zakończeniu. Tryby obejmują: partialBatch transakcyjna, domyślnie.  |  transakcyjne       |
|MaxParallelism     |   Int      |  Ta wartość określa maksymalną liczbę wątków, na którym do uruchomienia polecenia przetwarzania równoległego. Ta wartość jest wyrównywana z właściwości MaxParallelism, które można ustawić w TMSL [sekwencji polecenia](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/sequence-command-tmsl) lub przy użyciu innych metod.       | 10        |
|retryCount    |    Int     |   Wskazuje, ile razy ponowi operację przed niepowodzeniem.      |     0    |
|Obiekty     |   Tablica      |   Tablica obiektów do przetworzenia. Każdy obiekt obejmuje: "table" podczas przetwarzania całą tabelę lub "table" i "partycji" podczas przetwarzania partycji. Jeśli nie określono żadnych obiektów, jest odświeżany całego modelu. |   Proces całego modelu      |

CommitMode jest równa partialBatch. Jest używany podczas operacją ładowania początkowego dużych zestawów danych może potrwać do godziny. W przypadku niepowodzenia operacji odświeżania po pomyślnie zatwierdzania jednego lub więcej instancji pomyślnie zatwierdzony partii pozostaną zatwierdzone (go nie cofnie pomyślnie zatwierdzony partie).

> [!NOTE]
> W czasie zapisywania rozmiar partii jest wartością MaxParallelism, ale można zmienić tę wartość.

## <a name="get-refreshesrefreshid"></a>GET /refreshes/\<refreshId >

Aby sprawdzić stan operacji odświeżania, należy użyć zlecenie GET na identyfikator odświeżania. Oto przykład treści odpowiedzi. Jeśli operacja jest w toku, **w toku** zwracany jest w stanie.

```
{
    "startTime": "2017-12-07T02:06:57.1838734Z",
    "endTime": "2017-12-07T02:07:00.4929675Z",
    "type": "full",
    "status": "succeeded",
    "currentRefreshType": "full",
    "objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer",
            "status": "succeeded"
        },
        {
            "table": "DimDate",
            "partition": "DimDate",
            "status": "succeeded"
        }
    ]
}
```

## <a name="get-refreshes"></a>Pobierz /refreshes

Aby uzyskać listę operacji odświeżania historyczne dla modelu, użyj zlecenie GET w kolekcji /refreshes. Oto przykład treści odpowiedzi. 

> [!NOTE]
> W czasie zapisywania przechowywania i zwrócony z ostatnich 30 dni operacji odświeżania, ale można zmienić ten numer.

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-09T01:58:04.76",
        "endTime": "2017-12-09T01:58:12.607",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T02:05:48.32",
        "endTime": "2017-12-07T02:05:54.913",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>Usuń /refreshes/\<refreshId >

Aby anulować operację odświeżania w toku, użyj polecenia DELETE na identyfikator odświeżania.

## <a name="post-sync"></a>/ POST Sync

Posiadanie wykonać operacji odświeżania, może być niezbędne do synchronizowania nowych danych z repliki dla zapytania skalowania w poziomie. Aby operacja Synchronizuj dla modelu, należy użyć zlecenie POST w funkcji/Sync. Nagłówek lokalizacji w odpowiedzi zawiera identyfikator operacji synchronizacji.

## <a name="get-sync-status"></a>Pobierz stan/Sync

Aby sprawdzić stan operacji synchronizacji, użyj zlecenie GET, przekazując identyfikator operacji jako parametr. Oto przykład treści odpowiedzi:

```
{
    "operationId": "cd5e16c6-6d4e-4347-86a0-762bdf5b4875",
    "database": "AdventureWorks2",
    "UpdatedAt": "2017-12-09T02:44:26.18",
    "StartedAt": "2017-12-09T02:44:20.743",
    "syncstate": 2,
    "details": null
}
```

Wartości `syncstate`:

- 0: replikacji. Pliki bazy danych są replikowane do folderu docelowego.
- 1: przywrócenie z magazynu trwałego. Jest on rehydrated bazy danych na serwer tylko do odczytu wystąpienia.
- 2: zakończone. Operacja synchronizacji zakończyła się pomyślnie.
- 3: nie powiodło się. Operacja synchronizacji nie powiodła się.
- 4: Trwa kończenie. Operacja synchronizacji zostało ukończone, ale wykonuje kroki oczyszczania.

## <a name="code-sample"></a>Przykład kodu

Oto przykład kodu C# ułatwiające rozpoczęcie pracy, [RestApiSample w serwisie GitHub](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Aby użyć tego przykładu kodu

1.  Klonuj lub Pobierz repozytorium. Otwórz rozwiązanie RestApiSample.
2.  Znajdź wiersz **klienta. Właściwość BaseAddress =...** i podaj Twojej [bazowy adres URL](#base-url).

Przykładowy kod można używać logowania interakcyjnego, nazwę użytkownika/hasło lub [nazwy głównej usługi](#service-principal).

#### <a name="interactive-login-or-usernamepassword"></a>Logowania interakcyjnego lub nazwy użytkownika i hasła

Ta forma uwierzytelniania wymaga aplikacji Azure można utworzyć z uprawnieniami interfejsu API przypisane. 

1.  W portalu Azure kliknij **Utwórz zasób** > **usługi Azure Active Directory** > **rejestracji aplikacji**  >   **Nowej rejestracji aplikacji**.

    ![Nowej rejestracji aplikacji](./media/analysis-services-async-refresh/aas-async-app-reg.png)


2.  W **Utwórz**, wpisz nazwę, wybierz **natywnego** typu aplikacji. Dla **identyfikator URI przekierowania**, wprowadź **urn: ietf:wg:oauth:2.0:oob**, a następnie kliknij przycisk **Utwórz**.

    ![Ustawienia](./media/analysis-services-async-refresh/aas-async-app-reg-name.png)

3.  Wybierz aplikację, a następnie skopiuj i Zapisz **identyfikator aplikacji**.

    ![Skopiuj identyfikator aplikacji](./media/analysis-services-async-refresh/aas-async-app-id.png)

4.  W **ustawienia**, kliknij przycisk **wymagane uprawnienia** > **Dodaj**.

    ![Dodaj dostęp do interfejsu API](./media/analysis-services-async-refresh/aas-async-add.png)

5.  W **wybierz interfejs API**, typ **usług Azure Analysis Services** do wyszukiwania, a następnie wybierz go.

    ![Wybieranie interfejsu API](./media/analysis-services-async-refresh/aas-async-select-api.png)

6.  Wybierz **odczytywanie i zapisywanie wszystkich modeli**, a następnie kliknij przycisk **wybierz**. Gdy są zaznaczone obie opcje, kliknij przycisk **gotowe** do dodania uprawnień. Może upłynąć kilka minut propagacji.

    ![Wybierz odczytu i zapisu wszystkich modeli](./media/analysis-services-async-refresh/aas-async-select-read.png)

7.  W przykładowym kodzie znaleźć **UpdateToken()** metody. Sprawdź zawartość tej metody.
8.  Znajdź **string clientID =...** , a następnie wprowadź **identyfikator aplikacji** skopiowany w kroku 3.
9.  Uruchom przykład.

#### <a name="service-principal"></a>Jednostka usługi

Zobacz [Utwórz usługę podmiotu zabezpieczeń - portalu Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md) i [dodać nazwy głównej usługi do roli administratora serwera](analysis-services-addservprinc-admins.md) Aby uzyskać więcej informacji na temat sposobu ustawiania nazwy głównej usługi i przypisz odpowiednie uprawnienia w Azure AS . Po wykonaniu czynności należy wykonać następujące dodatkowe czynności:

1.  W przykładowym kodzie znaleźć **urzędu string =...** , Zastąp **wspólnej** organizacji dzierżawy identyfikatora.
2.  Komentarz/usuń znaczniki komentarza, klasa poświadczeń klienta służy do tworzenia wystąpienia obiektu Faktury korygu. Upewnij się, \<identyfikator aplikacji > i \<klucz aplikacji > wartości są dostępne w sposób bezpieczny, lub Użyj uwierzytelniania opartego na certyfikatach dla nazwy główne usług.
3.  Uruchom przykład.


## <a name="see-also"></a>Zobacz także

[Przykłady](analysis-services-samples.md)   
[Interfejs API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)   


