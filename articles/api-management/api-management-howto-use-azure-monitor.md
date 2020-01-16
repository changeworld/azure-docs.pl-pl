---
title: Monitorowanie opublikowanych interfejsów API w usłudze Azure API Management | Microsoft Docs
description: Wykonaj kroki tego samouczka, aby dowiedzieć się, jak monitorować interfejs API w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: b06301ab424a29d8f0e31e8f4dee26265327896b
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028358"
---
# <a name="monitor-published-apis"></a>Monitorowanie opublikowanych interfejsów API

Dzięki usłudze Azure Monitor możesz wykonywać wizualizacje i zapytania, ustalać trasy, archiwizować i podejmować działania dotyczące metryk lub dzienników pochodzących z zasobów platformy Azure.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetlanie dzienników aktywności
> * Wyświetlanie dzienników diagnostycznych
> * Wyświetlanie metryk interfejsu API 
> * Konfigurowanie reguły alertu, gdy interfejs API odbiera nieautoryzowane wywołania

W poniższym filmie wideo pokazano, jak monitorować usługę API Management przy użyciu usługi Azure Monitor. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]

## <a name="prerequisites"></a>Wymagania wstępne

+ Poznaj [terminologię dotyczącą usługi Azure API Management](api-management-terminology.md).
+ Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
+ Ponadto wykonaj zadania z następującego samouczka: [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>Wyświetlanie metryk interfejsów API

Usługa API Management emituje metryki co minutę, oferując wgląd w stan i kondycję interfejsów API w czasie zbliżonym do rzeczywistego. Poniżej znajduje się podsumowanie niektórych dostępnych metryk:

* Pojemność: ułatwia podejmowanie decyzji dotyczących uaktualniania/obniżania wersji usług APIM. Metryka jest emitowana co minutę i odzwierciedla pojemność bramy w momencie raportowania. Wartość metryki należy do zakresu od 0 do 100 i jest obliczana w oparciu o zasoby bramy, takie jak wykorzystanie procesora i pamięci.
* Całkowita liczba żądań bramy: liczba żądań interfejsu API w danym okresie. 
* Żądania bramy zakończone powodzeniem: liczba żądań interfejsu API, które otrzymały kody odpowiedzi HTTP informujące o powodzeniu, w tym 304, 307 i mniejsze niż 301 (na przykład 200).
* Żądania bramy zakończone niepowodzeniem: liczba żądań interfejsu API, które otrzymały kody odpowiedzi HTTP informujące o błędzie, w tym 400 i większe niż 500.
* Nieautoryzowane żądania bramy: liczba żądań interfejsu API, które otrzymały kody odpowiedzi HTTP, w tym 401, 403 i 429.
* Inne żądania bramy: liczba żądań interfejsu API, które otrzymały kody odpowiedzi HTTP nienależące do żadnej z powyższych kategorii (na przykład 418).

![wykres metryk](./media/api-management-azure-monitor/apim-monitor-metrics.png)

Aby uzyskać dostęp do metryk:

1. Wybierz pozycję **Metryki** w menu w dolnej części strony.

    ![metrics](./media/api-management-azure-monitor/api-management-metrics-blade.png)

1. Z listy rozwijanej wybierz interesujące Cię metryki. Na przykład **żądania**. 
1. Wykres przedstawia łączną liczbę wywołań interfejsu API.
1. Wykres może być filtrowany przy użyciu wymiarów metryk **żądań** . Na przykład kliknij pozycję **Dodaj filtr**, wybierz pozycję **kod odpowiedzi zaplecza**, wprowadź 500 jako wartość. Teraz wykres pokazuje liczbę żądań, które zakończyły się niepowodzeniem w zapleczu interfejsu API.   

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Konfigurowanie reguły alertu na potrzeby nieautoryzowanego żądania

Można skonfigurować odbieranie alertów w oparciu o metryki i dzienniki aktywności. Usługa Azure Monitor umożliwia skonfigurowanie alertu powodującego wykonywanie następujących czynności po jego wyzwoleniu:

* Wysłanie powiadomienia e-mail
* Wywołanie elementu webhook
* Wywołanie aplikacji logiki platformy Azure

Aby skonfigurować alerty:

1. Wybierz pozycję **alerty** na pasku menu w dolnej części strony.

    ![alerts](./media/api-management-azure-monitor/alert-menu-item.png)

2. Kliknij **nową regułę alertu** dla tego alertu.
3. Kliknij pozycję **Dodaj warunek**.
4. Wybierz pozycję **metryki** na liście rozwijanej Typ sygnału.
5. Wybierz pozycję **nieautoryzowane żądanie bramy** jako sygnał do monitorowania.

    ![alerts](./media/api-management-azure-monitor/signal-type.png)

6. W widoku **Konfiguracja logiki sygnału** Określ próg, po którym alert powinien zostać wyzwolony, a następnie kliknij pozycję **gotowe**.

    ![alerts](./media/api-management-azure-monitor/threshold.png)

7. Wybierz istniejącą grupę akcji lub Utwórz nową. W poniższym przykładzie wiadomość e-mail zostanie wysłana do administratorów. 

    ![alerts](./media/api-management-azure-monitor/action-details.png)

8. Podaj nazwę, opis reguły alertu i wybierz poziom ważności. 
9. Naciśnij pozycję **Utwórz regułę alertu**.
10. Teraz spróbuj wywołać interfejs API konferencji bez klucza interfejsu API. Alert zostanie wyzwolony, a wiadomość e-mail zostanie wysłana do administratorów. 

## <a name="activity-logs"></a>Dzienniki aktywności

Dzienniki aktywności udostępniają szczegółowe dane operacji wykonywanych w stosunku do usług API Management. Korzystając z tych dzienników, można określić rodzaj, użytkownika i czas każdej operacji zapisu (PUT, POST, DELETE) wykonanej względem usług API Management.

> [!NOTE]
> Dzienniki aktywności nie obejmują operacji odczytu (GET) ani operacji wykonywanych w witrynie Azure Portal albo przy użyciu oryginalnych interfejsów API zarządzania.

Dostęp do dzienników aktywności można uzyskać w usłudze API Management, a dostęp do wszystkich zasobów platformy Azure — w usłudze Azure Monitor. 

![dzienniki aktywności](./media/api-management-azure-monitor/apim-monitor-activity-logs.png)

Aby wyświetlić dzienniki aktywności:

1. Wybierz wystąpienie usługi APIM.
2. Kliknij pozycję **Dziennik aktywności**.

    ![dziennik aktywności](./media/api-management-azure-monitor/api-management-activity-logs-blade.png)

3. Wybierz żądany zakres filtrowania i kliknij przycisk **Zastosuj**.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Dzienniki diagnostyczne zawierają bogate informacje o operacjach i błędach, które są ważne w przypadku inspekcji, a także pomagają rozwiązywać problemy. Dzienniki diagnostyczne różnią się od dzienników aktywności. Dzienniki aktywności udostępniają szczegółowe dane operacji wykonywanych w stosunku do zasobów platformy Azure. Dzienniki diagnostyczne udostępniają szczegółowe dane operacji wykonanych przez zasób.

Aby skonfigurować dzienniki diagnostyczne:

1. Wybierz wystąpienie usługi APIM.
2. Kliknij pozycję **Ustawienia diagnostyczne**.

    ![dzienniki diagnostyczne](./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png)

3. Kliknij pozycję **Włącz diagnostykę**. Dzienniki diagnostyczne wraz z metrykami można zarchiwizować na koncie magazynu, przesyłać strumieniowo do usługi Event Hub lub wysłać do dziennika usługi Azure Monitor. 

Usługa API Management udostępnia obecnie dzienniki diagnostyczne (przetwarzane w trybie wsadowym co godzinę) dotyczące poszczególnych żądań interfejsu AP. Każdy wpis jest zgodny z następującym schematem:

```json
{  
    "isRequestSuccess" : "",
    "time": "",
    "operationName": "",
    "category": "",
    "durationMs": ,
    "callerIpAddress": "",
    "correlationId": "",
    "location": "",
    "httpStatusCodeCategory": "",
    "resourceId": "",
    "properties": {   
        "method": "", 
        "url": "", 
        "clientProtocol": "", 
        "responseCode": , 
        "backendMethod": "", 
        "backendUrl": "", 
        "backendResponseCode": ,
        "backendProtocol": "",  
        "requestSize": , 
        "responseSize": , 
        "cache": "", 
        "cacheTime": "", 
        "backendTime": , 
        "clientTime": , 
        "apiId": "",
        "operationId": "", 
        "productId": "", 
        "userId": "", 
        "apimSubscriptionId": "", 
        "backendId": "",
        "lastError": { 
            "elapsed" : "", 
            "source" : "", 
            "scope" : "", 
            "section" : "" ,
            "reason" : "", 
            "message" : ""
        } 
    }      
}  
```

| Właściwość  | Typ | Opis |
| ------------- | ------------- | ------------- |
| isRequestSuccess | wartość logiczna | Wartość true, jeśli żądanie HTTP zostało zakończone z kodem stanu odpowiedzi z zakresu 2xx lub 3xx |
| time | data i godzina | Sygnatura czasowa rozpoczęcia przetwarzania żądania przez bramę |
| operationName | string | Wartość stała „Microsoft.ApiManagement/GatewayLogs” |
| category | string | Wartość stała „GatewayLogs” |
| durationMs | liczba całkowita | Liczba milisekund od momentu odebrania żądania przez bramę do momentu, gdy odpowiedź na chwilę zostanie wysłana w całości. Obejmuje to clienTime, cacheTime i backendTime. |
| callerIpAddress | string | Adres IP bezpośredniego modułu wywołującego bramy (może być pośrednik) |
| correlationId | string | Unikatowy identyfikator żądania HTTP przypisany przez usługę API Management |
| location | string | Nazwa regionu platformy Azure, w którym znajdowała się brama przetwarzająca żądanie |
| httpStatusCodeCategory | string | Kategoria kodu stanu odpowiedzi HTTP: Powodzenie (301 lub mniej albo 304 lub 307), Bez autoryzacji (401, 403, 429), Błąd (400, od 500 do 600), Inne |
| resourceId | string | Identyfikator API Management Resource/SUBSCRIPTIONS/\<subskrypcji >/RESOURCEGROUPS/\<Grupa zasobów >/PROVIDERS/MICROSOFT. Nazwa APIMANAGEMENT/SERVICE/\< |
| properties | obiekt | Właściwości bieżącego żądania |
| method | string | Metoda HTTP żądania przychodzącego |
| url | string | Adres URL żądania przychodzącego |
| clientProtocol | string | Wersja protokołu HTTP żądania przychodzącego |
| responseCode | liczba całkowita | Kod stanu odpowiedzi HTTP wysłanej do klienta |
| backendMethod | string | Metoda HTTP żądania wysłanego do zaplecza |
| backendUrl | string | Adres URL żądania wysłanego do zaplecza |
| backendResponseCode | liczba całkowita | Kod odpowiedzi HTTP odebranej z zaplecza |
| backendProtocol | string | Wersja protokołu HTTP żądania wysłanego do zaplecza | 
| requestSize | liczba całkowita | Liczba bajtów odebranych od klienta podczas przetwarzania żądania | 
| responseSize | liczba całkowita | Liczba bajtów wysłanych do klienta podczas przetwarzania żądania | 
| cache | string | Stan zaangażowania pamięci podręcznej usługi API Management w przetwarzanie żądania (tj. trafienie, chybienie, brak) | 
| cacheTime | liczba całkowita | Liczba milisekund spędzonych na wykonywaniu ogólnych operacji we/wy pamięci podręcznej w usłudze API Management (łączenie, wysyłanie i odbieranie bajtów) | 
| backendTime | liczba całkowita | Liczba milisekund spędzonych na wykonywaniu ogólnych operacji we/wy zaplecza (łączenie, wysyłanie i odbieranie bajtów) | 
| clientTime | liczba całkowita | Liczba milisekund spędzonych na wykonywaniu ogólnych operacji we/wy klienta (łączenie, wysyłanie i odbieranie bajtów) | 
| apiId | string | Identyfikator jednostki interfejsu API dla bieżącego żądania | 
| operationId | string | Identyfikator jednostki operacji dla bieżącego żądania | 
| productId | string | Identyfikator jednostki produktu dla bieżącego żądania | 
| userId | string | Identyfikator jednostki użytkownika dla bieżącego żądania | 
| apimSubscriptionId | string | Identyfikator jednostki subskrypcji dla bieżącego żądania | 
| backendId | string | Identyfikator jednostki zaplecza dla bieżącego żądania | 
| LastError | obiekt | Ostatni błąd przetwarzania żądania | 
| elapsed | liczba całkowita | Liczba milisekund upływających od momentu odebrania żądania przez bramę i momentu wystąpienia błędu | 
| source | string | Nazwa wewnętrznej procedury obsługi przetwarzania lub zasad, które spowodowały błąd | 
| scope | string | Zakres dokumentu zasad zawierający zasady, które spowodowały błąd | 
| section | string | Sekcja dokumentu zasad zawierająca zasady, które spowodowały błąd | 
| reason | string | Przyczyna błędu | 
| message | string | Komunikat o błędzie | 

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetlanie dzienników aktywności
> * Wyświetlanie dzienników diagnostycznych
> * Wyświetlanie metryk interfejsu API
> * Konfigurowanie reguły alertu, gdy interfejs API odbiera nieautoryzowane wywołania

Przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Śledzenie wywołań](api-management-howto-api-inspector.md)
