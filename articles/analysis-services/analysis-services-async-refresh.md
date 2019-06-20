---
title: Odświeżanie asynchroniczne dla modeli usług Azure Analysis Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak napisać Odświeżanie asynchroniczne za pomocą interfejsu API REST.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 63b64df457af5b7d3d2bd5901f73d89ccd3c913a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65506973"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Odświeżanie asynchroniczne za pomocą interfejsu API REST

Za pomocą dowolnego języka programowania obsługującego wywołania REST, można wykonać operacji asynchronicznych odświeżania danych na modeli tabelarycznych usług Azure Analysis Services. W tym synchronizacji repliki tylko do odczytu dla zapytania skalowalnego w poziomie. 

Operacje odświeżania danych może zająć trochę czasu, w zależności od szeregu czynników, takich jak ilość danych, poziom optymalizacji za pomocą partycji itd. Tradycyjnie wywołania tych operacji z istniejących metod, takich jak przy użyciu [TOM](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (tabelaryczny Model obiektów), [PowerShell](https://docs.microsoft.com/sql/analysis-services/powershell/analysis-services-powershell-reference) poleceń cmdlet, lub [TMSL](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) (modelu tabelarycznego Scripting Language). Te metody mogą jednak wymagać często zawodnych, długoterminowych połączeń HTTP.

Interfejs API REST usług Azure Analysis Services umożliwia wykonywanie operacji odświeżania danych przeprowadzanych asynchronicznie. Za pomocą interfejsu API REST, długoterminowych połączeń HTTP z aplikacji klienckich nie są niezbędne. Istnieją także inne wbudowane funkcje niezawodności, takich jak automatyczne ponawianie prób i wsadowej operacji zatwierdzenia.

## <a name="base-url"></a>Podstawowy adres URL

Podstawowy adres URL ma następujący format:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Na przykład należy wziąć pod uwagę modelu na serwerze o nazwie myserver, znajdującego się w regionie zachodnie stany USA Azure o nazwie AdventureWorks. Nazwa serwera to:

```
asazure://westus.asazure.windows.net/myserver 
```

Podstawowy adres URL dla tej nazwy serwera jest:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Przy użyciu podstawowego adresu URL, zasobów i operacji może być dołączona na podstawie następujących parametrów: 

![Odświeżanie asynchroniczne](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Wszystko, co kończy się **s** jest kolekcją.
- Wszystko, co kończy się **()** jest funkcją.
- Czymkolwiek to zasobów/obiektów.

Na przykład służy zlecenie WPIS w kolekcji odświeżania można wykonać operacji odświeżania:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Authentication

Wszystkie wywołania, musi zostać uwierzytelniony przy użyciu prawidłowego tokenu usługi Azure Active Directory (OAuth 2) w nagłówku autoryzacji i musi spełniać następujące wymagania:

- Token należy do użytkownika token usług lub aplikacji jednostki usługi.
- Token musi mieć prawidłowy odbiorców, ustaw `https://*.asazure.windows.net`.
- Użytkownik lub aplikacja musi mieć wystarczające uprawnienia na serwerze lub modelu do wykonania żądanej wywołania. Poziom uprawnień jest określany przez role w ramach modelu lub grupy administratorów na serwerze.

    > [!IMPORTANT]
    > Obecnie **administrator serwera** uprawnienia roli są niezbędne.

## <a name="post-refreshes"></a>/Refreshes WPIS

Aby wykonać operację odświeżania, umożliwia zlecenie WPIS w kolekcji /refreshes Dodaj nowy element odświeżania do kolekcji. Nagłówek lokalizacji, w odpowiedzi zawiera identyfikator odświeżania. Aplikacja kliencka można odłączyć i sprawdź stan później, jeśli jest to wymagane, ponieważ jest asynchroniczne.

Tylko jedną operację odświeżania jest akceptowana w czasie dla modelu. Jeśli jest bieżący uruchomioną operację odświeżania, a inny zostanie przesłany, zwracany jest 409 Konflikt kod stanu HTTP.

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

Określanie parametrów nie jest wymagana. Wartość domyślna jest stosowana.

| Name (Nazwa)             | Typ  | Opis  |Domyślne  |
|------------------|-------|--------------|---------|
| `Type`           | Enum  | Typ przetwarzania do wykonania. Typy są wyrównane z TMSL [polecenia odświeżenia](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/refresh-command-tmsl) typów: pełne, clearValues, Oblicz dataOnly, automatyczne i defragmentacji. Dodaj typ nie jest obsługiwany.      |   Automatyczne      |
| `CommitMode`     | Enum  | Określa, jeśli obiekty zostaną zatwierdzone, w plikach wsadowych lub tylko wtedy, gdy jest to pełny. Tryby obejmują: domyślnie partialBatch transakcyjnych.  |  transakcyjne       |
| `MaxParallelism` | Int   | Ta wartość określa maksymalną liczbę wątków, w którym można uruchomić polecenia przetwarzania równoległego. Ta wartość jest powiązana z właściwości MaxParallelism, które można ustawić w TMSL [sekwencji poleceń](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/sequence-command-tmsl) lub przy użyciu innych metod.       | 10        |
| `RetryCount`     | Int   | Wskazuje liczbę przypadków, gdy operacja zostanie ponowiona zakończy się niepowodzeniem.      |     0    |
| `Objects`        | Array | Tablica obiektów do przetworzenia. Każdy obiekt zawiera: "table" podczas przetwarzania całą tabelę lub "table" i "partycji" podczas przetwarzania partycji. Jeśli nie określono żadnych obiektów, cały model zostanie odświeżony. |   Proces cały model      |

CommitMode jest równy partialBatch. Jest używany podczas wykonywania ładowania początkowego dużych zestawów danych, który może potrwać do godziny. W przypadku niepowodzenia operacji odświeżania po zatwierdzeniu pomyślnie co najmniej jedna partia pomyślnie zatwierdzić partii pozostaną zatwierdzone (go nie wycofa pomyślnie zatwierdzona partie).

> [!NOTE]
> W czasie pisania rozmiar partii jest wartością MaxParallelism, ale można zmienić tę wartość.

## <a name="get-refreshesrefreshid"></a>Pobierz /refreshes/\<refreshId >

Aby sprawdzić stan operacji odświeżania, należy użyć zlecenie GET na identyfikatorze odświeżania. Oto przykład treść odpowiedzi. Jeśli operacja jest w toku, **inProgress** zwracany jest w stanie.

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

Aby uzyskać listę operacji odświeżania historyczne dla modelu, należy użyć zlecenie GET w kolekcji /refreshes. Oto przykład treść odpowiedzi. 

> [!NOTE]
> W czasie pisania przechowywania i zwrócony z ostatnich 30 dni operacji odświeżania, ale można zmienić ten numer.

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

Aby anulować operację odświeżania w toku, należy użyć zlecenie DELETE na identyfikatorze odświeżania.

## <a name="post-sync"></a>/ POST Sync

Wykonanie operacji odświeżania, może być konieczne zsynchronizować nowych danych z repliki dla zapytania skalowalnego w poziomie. Do wykonywania operacji synchronizowania dla modelu, należy użyć zlecenie WPIS dla funkcji/Sync. Nagłówek lokalizacji, w odpowiedzi zawiera identyfikator operacji synchronizacji.

## <a name="get-sync-status"></a>Pobierz stan/Sync

Aby sprawdzić stan operacji synchronizacji, należy użyć zlecenia GET, przekazując identyfikator operacji jako parametr. Oto przykład treści odpowiedzi:

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

- 0: Replikacja. Pliki bazy danych są replikowane do folderu docelowego.
- 1: Ponownego wypełniania. Baza danych jest wypełnienia w wystąpieniach serwera tylko do odczytu.
- 2: Ukończone. Pomyślnie ukończono operację synchronizacji.
- 3: Nie powiodło się. Operacja synchronizacji nie powiodło się.
- 4\. Finalizowanie. Operacja synchronizacji została ukończona, ale wykonuje czynności czyszczenia.

## <a name="code-sample"></a>Przykład kodu

Poniżej przedstawiono przykładowy kod języka C#, w które ułatwią rozpoczęcie pracy, [RestApiSample w serwisie GitHub](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Aby użyć przykładowego kodu

1.  Klonuj lub Pobierz repozytorium. Otwórz rozwiązanie RestApiSample.
2.  Znajdź wiersz **klienta. BaseAddress =...** i podaj swoje [bazowy adres URL](#base-url).

Przykładowy kod używa [nazwy głównej usługi](#service-principal) uwierzytelniania.

### <a name="service-principal"></a>Nazwa główna usługi

Zobacz [Tworzenie jednostki usługi — witryna Azure portal](../active-directory/develop/howto-create-service-principal-portal.md) i [Dodawanie jednostki usługi do roli administratora serwera](analysis-services-addservprinc-admins.md) Aby uzyskać więcej informacji na temat konfigurowania jednostki usługi i przypisanie niezbędnych uprawnień w jako platformy Azure . Po wykonaniu czynności należy wykonać następujące dodatkowe czynności:

1.  Przykładowy kod zawiera **ciągu urząd =...** , Zastąp **wspólnej** z Twoją organizacją dzierżawy identyfikatora.
2.  Komentarz lub usuń znaczniki komentarza, klasa ClientCredential służy do tworzenia wystąpienia obiektu poświadczenie. Upewnij się, \<Identyfikatora aplikacji > i \<klucz aplikacji > wartości są używane w sposób bezpieczny lub uwierzytelniania opartego na certyfikatach dla jednostki usługi.
3.  Uruchom przykład.


## <a name="see-also"></a>Zobacz także

[Przykłady](analysis-services-samples.md)   
[Interfejs API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)   


