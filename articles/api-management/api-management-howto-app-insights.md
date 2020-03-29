---
title: Integracja usługi Azure API Management z usługą Azure Application Insights
titleSuffix: Azure API Management
description: Dowiedz się, jak rejestrować i wyświetlać zdarzenia z usługi Azure API Management w usłudze Azure Application Insights.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 12aeea8393a00d7d2662c826f847265bdbdc0119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442712"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Jak zintegrować usługę Azure API Management z usługą Azure Application Insights

Usługa Azure API Management umożliwia łatwą integrację z usługą Azure Application Insights — rozszerzalną usługą dla deweloperów sieci Web, którzy twórzą aplikacje i zarządzają nimi na wielu platformach. W tym przewodniku przechodzi przez każdy krok takiej integracji i opisano strategie zmniejszania wpływu na wydajność wystąpienia usługi zarządzania interfejsami API.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten przewodnik, musisz mieć wystąpienie usługi Azure API Management. Jeśli go nie masz, najpierw wykonaj [samouczek.](get-started-create-service-instance.md)

## <a name="create-an-azure-application-insights-instance"></a>Tworzenie wystąpienia usługi Azure Application Insights

Przed użyciem usługi Azure Application Insights należy najpierw utworzyć wystąpienie usługi.

1. Otwórz **witrynę Azure portal** i przejdź do **usługi Application Insights**.  
    ![Tworzenie statystyk aplikacji](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Kliknij pozycję **+ Dodaj**.  
    ![Tworzenie statystyk aplikacji](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Wypełnij formularz. Wybierz **opcję Ogólne** jako typ **aplikacji**.
4. Kliknij przycisk **Utwórz**.

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Tworzenie połączenia między usługą Azure Application Insights a wystąpieniem usługi Azure API Management

1. Przejdź do **wystąpienia usługi Azure API Management** w **witrynie Azure portal**.
2. Wybierz z menu po lewej stronie **pozycję Application Insights.**
3. Kliknij pozycję **+ Dodaj**.  
    ![Rejestrator aplikacji](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Wybierz poprzednio utworzone **wystąpienie usługi Application Insights** i podaj krótki opis.
5. Kliknij przycisk **Utwórz**.
6. Właśnie utworzono rejestrator usługi Azure Application Insights z kluczem instrumentacji. Powinien on teraz pojawić się na liście.  
    ![Rejestrator aplikacji](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> Za sceną [logger](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger/createorupdate) jednostki jest tworzony w wystąpieniu zarządzania interfejsami API, zawierający klucz instrumentacji wystąpienia usługi Application Insights.

## <a name="enable-application-insights-logging-for-your-api"></a>Włącz rejestrowanie usługi Application Insights dla interfejsu API

1. Przejdź do **wystąpienia usługi Azure API Management** w **witrynie Azure portal**.
2. Wybierz pozycję **Interfejsy API** z menu po lewej stronie.
3. Kliknij interfejs API, w tym przypadku **demo konferencji INTERFEJSU API**.
4. Przejdź do karty **Ustawienia** na górnym pasku.
5. Przewiń w dół do sekcji **Dzienniki diagnostyki.**  
    ![Rejestrator aplikacji](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Zaznacz pole **włącz.**
7. Wybierz dołączony rejestrator w menu rozwijanym **Miejsce docelowe.**
8. Wprowadź **100** jako **próbkowanie (%)** i zaznacz pole wyboru **Zawsze rejestruj błędy.**
9. Kliknij przycisk **Zapisz**.

> [!WARNING]
> Zastąpienie wartości domyślnej **0** w polu **Pierwszy bajt treści** może znacznie zmniejszyć wydajność interfejsów API.

> [!NOTE]
> Za sceną [jednostka diagnostyczna](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/diagnostic/createorupdate) o nazwie "applicationinsights" jest tworzony na poziomie interfejsu API.

| Nazwa ustawienia                        | Typ wartości                        | Opis                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Włączanie                              | wartość logiczna                           | Określa, czy rejestrowanie tego interfejsu API jest włączone.                                                                                                                                                                                                                                                                                                |
| Element docelowy                         | Rejestrator usługi Azure Application Insights | Określa rejestrator usługi Azure Application Insights, który ma być używany                                                                                                                                                                                                                                                                                           |
| Pobieranie próbek (%)                        | decimal                           | Wartości od 0 do 100 (procent). <br/> Określa, jaki procent żądań będzie rejestrowany w usłudze Azure Application Insights. Próbkowanie 0% oznacza zero zarejestrowanych żądań, podczas gdy próbkowanie w 100% oznacza, że wszystkie zarejestrowane żądania. <br/> To ustawienie służy do zmniejszania wpływu na wydajność żądań rejestrowania do usługi Azure Application Insights (zobacz sekcję poniżej). |
| Zawsze rejestruj błędy                   | wartość logiczna                           | Jeśli to ustawienie jest zaznaczone, wszystkie błędy będą rejestrowane w usłudze Azure Application Insights, niezależnie od ustawienia **próbkowania.**                                                                                                                                                                                                                  |
| Podstawowe opcje: Nagłówki              | list                              | Określa nagłówki, które będą rejestrowane w usłudze Azure Application Insights dla żądań i odpowiedzi.  Domyślnie: nie są rejestrowane żadne nagłówki.                                                                                                                                                                                                             |
| Podstawowe opcje: Pierwsze bajty ciała  | liczba całkowita                           | Określa, ile pierwszych bajtów treści są rejestrowane w usłudze Azure Application Insights dla żądań i odpowiedzi.  Domyślnie: treść nie jest rejestrowana.                                                                                                                                                                                                    |
| Opcje zaawansowane: szczegółowość         |                                   | Określa poziom szczegółowości. Rejestrowane będą tylko niestandardowe ślady o wyższym poziomie ważności. Domyślnie: Informacje.                                                                                                                                                                                                                               |
| Opcje zaawansowane: Żądanie frontu  |                                   | Określa, czy i jak *żądania frontend* będą rejestrowane w usłudze Azure Application Insights. *Żądanie frontend* jest żądanie przychodzące do usługi Azure API Management usługi.                                                                                                                                                                        |
| Opcje zaawansowane: Reakcja frontendu |                                   | Określa, czy i jak *odpowiedzi frontend* będą rejestrowane w usłudze Azure Application Insights. *Odpowiedź frontend* jest odpowiedzią wychodzącą z usługi Azure API Management.                                                                                                                                                                   |
| Opcje zaawansowane: Żądanie wewnętrznej bazy danych   |                                   | Określa, czy i jak *żądania wewnętrznej bazy danych* będą rejestrowane w usłudze Azure Application Insights. *Żądanie wewnętrznej bazy danych* jest żądaniem wychodzącym z usługi Azure API Management.                                                                                                                                                                        |
| Opcje zaawansowane: odpowiedź wewnętrznej bazy danych  |                                   | Określa, czy i jak *odpowiedzi wewnętrznej bazy danych* będą rejestrowane w usłudze Azure Application Insights. *Odpowiedź wewnętrznej bazy danych* jest odpowiedzią przychodzącą do usługi Azure API Management.                                                                                                                                                                       |

> [!NOTE]
> Można określić rejestratory na różnych poziomach — pojedynczy rejestrator interfejsu API lub rejestrator dla wszystkich interfejsów API.
>  
> Jeśli określisz oba:
> + jeśli są to różne rejestratory, to oba będą używane (multipleksowanie dzienników),
> + jeśli są one te same rejestratory, ale mają różne ustawienia, a następnie jeden dla pojedynczego interfejsu API (poziom bardziej szczegółowy) zastąpi jeden dla wszystkich interfejsów API.

## <a name="what-data-is-added-to-azure-application-insights"></a>Jakie dane są dodawane do usługi Azure Application Insights

Usługa Azure Application Insights otrzymuje:

+ *Żądanie* elementu telemetrii dla każdego żądania przychodzącego *(żądanie frontendu,* *odpowiedź frontendu),*
+ Element telemetrii *zależności* dla każdego żądania przekazywanego do usługi wewnętrznej bazy danych *(żądanie wewnętrznej bazy danych,* *odpowiedź wewnętrznej bazy danych),*
+ Element telemetrii *wyjątku* dla każdego żądania nie powiodło się.

Nieudane żądanie jest żądaniem, które:

+ nie powiodła się z powodu zamkniętego połączenia klienta, lub
+ wyzwolił *sekcję błędu* w zasadach API, lub
+ ma odpowiedź kod stanu HTTP pasujący do 4xx lub 5xx.

## <a name="performance-implications-and-log-sampling"></a>Wpływ na wydajność i próbkowanie dziennika

> [!WARNING]
> Rejestrowanie wszystkich zdarzeń może mieć poważne konsekwencje dla wydajności, w zależności od szybkości żądań przychodzących.

Na podstawie wewnętrznych testów obciążenia włączenie tej funkcji spowodowało zmniejszenie przepływności o 40%-50%, gdy szybkość żądania przekraczała 1000 żądań na sekundę. Usługa Azure Application Insights została zaprojektowana do używania analizy statystycznej do oceny wydajności aplikacji. Nie jest przeznaczony do systemu inspekcji i nie nadaje się do rejestrowania każdego indywidualnego żądania dla interfejsów API dużej ilości.

Można manipulować liczbą zarejestrowanych żądań, dostosowując ustawienie **próbkowanie** (zobacz powyższe kroki). Wartość 100% oznacza, że wszystkie żądania są rejestrowane, podczas gdy 0% odzwierciedla nie rejestrowania w ogóle. **Próbkowanie** pomaga zmniejszyć ilość danych telemetrycznych, skutecznie zapobiegając znacznemu pogorszeniu wydajności, a jednocześnie niosąc korzyści z rejestrowania.

Pomijanie rejestrowania nagłówków i treści żądań i odpowiedzi będzie również miało pozytywny wpływ na łagodzenie problemów z wydajnością.

## <a name="video"></a>Film wideo

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Następne kroki

+ Dowiedz się więcej o [usłudze Azure Application Insights](https://docs.microsoft.com/azure/application-insights/).
+ Rozważ [rejestrowanie za pomocą usługi Azure Event Hubs](api-management-howto-log-event-hubs.md).
