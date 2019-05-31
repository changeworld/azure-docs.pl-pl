---
title: Sposób integracji usługi Azure API Management z usługą Azure Application Insights | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rejestrować i przeglądać zdarzenia z usługi Azure API Management w usłudze Azure Application Insights.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 3bbab82831fba389cd4bf172e7ea762d5971579b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241845"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Sposób integracji usługi Azure API Management z usługą Azure Application Insights

Usługa Azure API Management umożliwia łatwą integrację z usługą Azure Application Insights — rozszerzalną usługę dla deweloperów sieci web, tworzenia aplikacji i zarządzanie nimi na wielu platformach. Ten przewodnik przeprowadzi na każdym kroku takiej integracji i opis strategii ograniczania wydajności wpływu na wystąpienia usługi API Management.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skorzystać z tego przewodnika, musisz mieć wystąpienia usługi Azure API Management. Jeśli nie masz, wykonaj [samouczek](get-started-create-service-instance.md) pierwszy.

## <a name="create-an-azure-application-insights-instance"></a>Tworzenie wystąpienia usługi Azure Application Insights

Zanim użyjesz usługi Azure Application Insights, należy najpierw utworzyć wystąpienie usługi.

1. Otwórz **witryny Azure portal** i przejdź do **usługi Application Insights**.  
    ![Tworzenie usługi App Insights](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Kliknij pozycję **+ Dodaj**.  
    ![Tworzenie usługi App Insights](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Wypełnij formularz. Wybierz **ogólne** jako **typ aplikacji**.
4. Kliknij pozycję **Utwórz**.

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Utwórz połączenie między usługą Azure Application Insights i wystąpienie usługi Azure API Management

1. Przejdź do swojej **wystąpienia usługi Azure API Management** w **witryny Azure portal**.
2. Wybierz **usługi Application Insights** z menu po lewej stronie.
3. Kliknij pozycję **+ Dodaj**.  
    ![App Insights rejestratora](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Wybierz wcześniej utworzony **usługi Application Insights** wystąpienia i podaj krótki opis.
5. Kliknij pozycję **Utwórz**.
6. Rejestrator usługi Azure Application Insights jest właśnie utworzony przy użyciu klucza instrumentacji. Będzie teraz widoczny na liście.  
    ![App Insights rejestratora](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> Za sceną [rejestratora](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger/createorupdate) w wystąpieniu usługi API Management, zawierającego klucz Instrumentacji usługi Application Insights wystąpienia zostanie utworzona jednostka.

## <a name="enable-application-insights-logging-for-your-api"></a>Włączanie rejestrowania usługi Application Insights dla interfejsu API

1. Przejdź do swojej **wystąpienia usługi Azure API Management** w **witryny Azure portal**.
2. Wybierz pozycję **Interfejsy API** z menu po lewej stronie.
3. Polecenie interfejsu API, w tym przypadku **Demo Conference API**.
4. Przejdź do **ustawienia** kartę na górnym pasku.
5. Przewiń w dół do **dzienniki diagnostyczne** sekcji.  
    ![App Insights rejestratora](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Sprawdź **Włącz** pole.
7. Wybierz swoje dołączonych rejestratora w **docelowy** listy rozwijanej.
8. Dane wejściowe **100** jako **próbkowania (%)** i **błędy dziennika zawsze** pola wyboru.
9. Kliknij pozycję **Zapisz**.

> [!WARNING]
> Zastępowanie wartości domyślne **0** w **pierwsze bajty treści** pola może znacznie zmniejszyć wydajność z interfejsów API.

> [!NOTE]
> Za sceną [diagnostycznych](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/diagnostic/createorupdate) jednostki o nazwie "applicationinsights" jest tworzone na poziomie interfejsu API.

| Nazwa ustawienia                        | Typ wartości                        | Opis                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Włączenie                              | wartość logiczna                           | Określa, czy jest włączone rejestrowanie tego interfejsu API.                                                                                                                                                                                                                                                                                                |
| Miejsce docelowe                         | Usługa Azure Application Insights rejestratora | Określa Rejestrator usługi Azure Application Insights do użycia                                                                                                                                                                                                                                                                                           |
| Próbkowanie (%)                        | decimal                           | Wartości z zakresu od 0 do 100 (procent). <br/> Określa, jaki procent żądań, które będą rejestrowane do usługi Azure Application Insights. próbkowania 0% oznacza zero żądań rejestrowane podczas próbkowania 100% oznacza, że wszystkie żądania rejestrowane. <br/> To ustawienie jest używane w celi zmniejszenia wpływu na wydajność żądań logowania do usługi Azure Application Insights (zobacz sekcję poniżej). |
| Zawsze Rejestruj błędy                   | wartość logiczna                           | Jeśli to ustawienie jest zaznaczone, będą rejestrowane wszystkie błędy do usługi Azure Application Insights, niezależnie od wartości **próbkowania** ustawienie.                                                                                                                                                                                                                  |
| Opcje podstawowe: Nagłówki              | list                              | Określa nagłówki, które są zapisywane do usługi Azure Application Insights dla żądań i odpowiedzi.  Wartość domyślna: nie nagłówki są rejestrowane.                                                                                                                                                                                                             |
| Opcje podstawowe: Pierwsze bajty treści  | liczba całkowita                           | Określa, ile pierwszych bajtów treści są rejestrowane do usługi Azure Application Insights na potrzeby żądań i odpowiedzi.  Wartość domyślna: treść nie jest zalogowany.                                                                                                                                                                                              |
| Zaawansowane opcje: Żądanie serwera sieci Web  |                                   | Określa, czy i jak *żądań frontonu* będą rejestrowane w usłudze Azure Application Insights. *Żądanie frontonu* to żądanie przychodzące do usługi Azure API Management.                                                                                                                                                                        |
| Zaawansowane opcje: Odpowiedź serwera sieci Web |                                   | Określa, czy i jak *odpowiedzi frontonu* będą rejestrowane w usłudze Azure Application Insights. *Odpowiedź frontonu* jest wychodzący odpowiedź z usługi Azure API Management.                                                                                                                                                                   |
| Zaawansowane opcje: Żądanie wewnętrznej bazy danych   |                                   | Określa, czy i jak *żądań zaplecza* będą rejestrowane w usłudze Azure Application Insights. *Żądanie zaplecza* jest żądania wychodzące z usługi Azure API Management.                                                                                                                                                                        |
| Zaawansowane opcje: Odpowiedzi zaplecza  |                                   | Określa, czy i jak *odpowiedzi zaplecza* będą rejestrowane w usłudze Azure Application Insights. *Odpowiedzi zaplecza* jest odpowiedzią na przychodzące do usługi Azure API Management.                                                                                                                                                                       |

> [!NOTE]
> Możesz określić rejestratorów na różnych poziomach - jednym Rejestrator interfejsu API lub rejestratora dla wszystkich interfejsów API.
>  
> Jeśli określisz zarówno:
> + Jeśli są one różne rejestratorów, obie z nich zostanie użyty (funkcje multipleksowania dzienników)
> + Jeśli są one ten sam rejestratorów, ale mają różne ustawienia, a następnie jeden do jednego interfejsu API (bardziej szczegółowym poziomie) spowoduje przesłonięcie, jeden dla wszystkich interfejsów API.

## <a name="what-data-is-added-to-azure-application-insights"></a>Jakie dane są dodawane do usługi Azure Application Insights

Usługa Azure Application Insights odbiera:

+ *Żądanie* elementu telemetrii, dla każdego żądania przychodzącego (*żądania frontonu*, *odpowiedzi frontonu*),
+ *Zależność* elementu telemetrii, dla każdego żądania przekazywane do usługi zaplecza (*żądania zaplecza*, *odpowiedzi zaplecza*),
+ *Wyjątek* elementu telemetrii, dla każdego żądania nie powiodło się.

Nie powiodło się żądanie jest żądaniem, które:

+ nie powiodło się z powodu połączenie klienta zamknięte lub
+ wyzwalane *na błąd* części zasad interfejsu API, lub
+ ma 4xx pasującego kod stanu HTTP odpowiedzi lub 5xx.

## <a name="performance-implications-and-log-sampling"></a>Wpływ na wydajność i próbkowania dziennika

> [!WARNING]
> Wszystkie zdarzenia może mieć wpływ na wydajność poważny, w zależności od szybkość odbierania żądań.

Oparte na testach obciążenia wewnętrznego, włączenie tej funkcji spowodowany redukcji 40 – 50% przepływności liczba żądań przekracza 1000 żądań na sekundę. Usługa Azure Application Insights jest przeznaczony do stosowania analizy statystycznej do oceny wydajności aplikacji. Nie może być to system inspekcji, a nie jest przeznaczone do rejestrowania każdego pojedynczego żądania dla dużej liczby interfejsów API.

Możesz manipulować liczba żądań, które są rejestrowane przez dostosowanie **próbkowania** ustawienie (zobacz powyższe kroki). Wartość 100% oznacza, że wszystkie żądania są rejestrowane, gdy 0% odzwierciedla nie rejestrowania na wszystkich. **Próbkowanie** pozwala zmniejszyć ilość danych telemetrii, efektywnie uniemożliwia znacznego obniżenia wydajności, badając nadal korzyści wynikające z rejestracji.

Pomijanie rejestrowanie nagłówki i treść żądania i odpowiedzi będzie mieć pozytywny wpływ na złagodzenia problemy z wydajnością.

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Kolejne kroki

+ Dowiedz się więcej o [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights/).
+ Należy wziąć pod uwagę [rejestrowanie za pomocą usługi Azure Event Hubs](api-management-howto-log-event-hubs.md).
