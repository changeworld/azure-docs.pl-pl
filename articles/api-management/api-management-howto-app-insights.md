---
title: Integrowanie usługi Azure API Management z usługą Azure Application Insights | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 7740da505f7635944536252d60ec2c2039295975
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320583"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Integrowanie usługi Azure API Management z usługą Azure Application Insights

Zarządzanie interfejsami API Azure umożliwia łatwą integrację z usługą Azure Application Insights - rozszerzalną usługę sieci web deweloperom tworzenie i zarządzanie aplikacjami na wielu platformach. Ten przewodnik przeprowadzi Cię przez każdy krok takiej integracji i opisano strategie zmniejszenia wpływu na Twoje wystąpienie usługi Zarządzanie interfejsami API.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego przewodnika, należy mieć wystąpienia usługi Azure API Management. Jeśli nie masz, wykonać [samouczek](get-started-create-service-instance.md) pierwszy.

## <a name="create-an-azure-application-insights-instance"></a>Utwórz wystąpienie usługi Azure Application Insights

Przed użyciem usługi Azure Application Insights, należy najpierw utworzyć wystąpienia usługi.

1. Otwórz **portalu Azure** i przejdź do **usługi Application Insights**.  
    ![Tworzenie usługi App Insights](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Kliknij pozycję **+ Dodaj**.  
    ![Tworzenie usługi App Insights](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Wypełnij formularz. Wybierz **ogólne** jako **typu aplikacji**.
4. Kliknij przycisk **Utwórz**.

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Utwórz połączenie między usługą Azure Application Insights i wystąpienie usługi Zarządzanie interfejsami API Azure

1. Przejdź do Twojej **wystąpienia usługi Azure API Management** w **portalu Azure**.
2. Wybierz **usługi Application Insights** z menu po lewej stronie.
3. Kliknij pozycję **+ Dodaj**.  
    ![App Insights rejestratora](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Wybierz wcześniej utworzony **usługi Application Insights** wystąpienia i podaj krótki opis.
5. Kliknij przycisk **Utwórz**.
6. Przy użyciu klucza Instrumentacji właśnie utworzony rejestratora Azure Application Insights. Powinien zostać wyświetlony na liście.  
    ![App Insights rejestratora](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

## <a name="enable-application-insights-logging-for-your-api"></a>Włączanie rejestrowania usługi Application Insights dla interfejsu API

1. Przejdź do Twojej **wystąpienia usługi Azure API Management** w **portalu Azure**.
2. Wybierz **interfejsów API** z menu po lewej stronie.
3. W takim przypadku polecenie interfejsu API, **API konferencji pokaz**.
4. Przejdź do **ustawienia** kartę na górnym pasku.
5. Przewiń w dół do **dzienników diagnostycznych** sekcji.  
    ![App Insights rejestratora](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Sprawdź **włączyć** pole.
7. Wybierz użytkownika rejestratora dołączonych w **docelowego** listy rozwijanej.
8. Dane wejściowe **100** jako **próbkowania (%)** i znaczników **zawsze Rejestruj błędy** wyboru.
9. Dane wejściowe **1024** w **pierwszych bajtów treści** pola.
10. Kliknij pozycję **Zapisz**.

| Nazwa ustawienia                        | Typ wartości                        | Opis                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Włączanie                              | wartość logiczna                           | Określa, czy jest włączone rejestrowanie tego interfejsu API.                                                                                                                                                                                                                                                                                                |
| Element docelowy                         | Azure rejestratora usługi Application Insights | Określa Rejestrator Azure Application Insights do użycia                                                                                                                                                                                                                                                                                           |
| Próbkowanie (%)                        | Decimal                           | Wartości z zakresu od 0 do 100 (procent). <br/> Określa, jaki procent żądań, które będą rejestrowane z usługą Azure Application Insights. próbkowania 0% oznacza zero żądań rejestrowane podczas próbkowania 100% oznacza, że wszystkie żądania rejestrowane. <br/> To ustawienie jest stosowane do zmniejszenia wpływu na wydajność żądań logowania do usługi Azure Application Insights (zobacz sekcję poniżej). |
| Zawsze Rejestruj błędy                   | wartość logiczna                           | Jeśli to ustawienie jest zaznaczone, będą rejestrowane wszystkie błędy z usługą Azure Application Insights, niezależnie od tego **próbkowania** ustawienie.                                                                                                                                                                                                                  |
| Opcje podstawowe: nagłówki              | lista                              | Określa nagłówki, które będą rejestrowane z usługą Azure Application Insights dla żądań i odpowiedzi.  Wartość domyślna: nagłówki nie są rejestrowane.                                                                                                                                                                                                             |
| Opcje podstawowe: Pierwszych bajtów treści  | liczba całkowita                           | Określa, ile pierwszych bajtów w treści są zalogowani Azure Application Insights dla żądań i odpowiedzi.  Wartość domyślna: treść nie jest zalogowany.                                                                                                                                                                                              |
| Zaawansowane opcje: Żądanie frontonu  |                                   | Określa, czy i jak *żądań frontonu* będą rejestrowane w usłudze Azure Application Insights. *Żądanie frontonu* jest żądania przychodzące do usługi Azure API Management.                                                                                                                                                                        |
| Zaawansowane opcje: Odpowiedź frontonu |                                   | Określa, czy i jak *odpowiedzi frontonu* będą rejestrowane w usłudze Azure Application Insights. *Odpowiedź frontonu* jest wychodzący odpowiedź z usługi Azure API Management.                                                                                                                                                                   |
| Zaawansowane opcje: Żądanie zaplecza   |                                   | Określa, czy i jak *żądań zaplecza* będą rejestrowane w usłudze Azure Application Insights. *Żądanie zaplecza* to żądania wychodzącego z usługi Azure API Management.                                                                                                                                                                        |
| Zaawansowane opcje: Odpowiedź zaplecza  |                                   | Określa, czy i jak *odpowiedzi zaplecza* będą rejestrowane w usłudze Azure Application Insights. *Odpowiedź zaplecza* jest odpowiedzią przychodzących do usługi Azure API Management.                                                                                                                                                                       |

> [!NOTE]
> Można określić rejestratorów na różnych poziomach — jednego interfejsu API rejestratora lub rejestratora dla wszystkich interfejsów API.
>  
> Jeśli możesz określić ich obu:
> + Jeśli obie z nich zostanie użyta (zwielokrotnianie dzienników), są różne rejestratorów
> + Jeśli są tego samego rejestratorów, ale ma inne ustawienia, a następnie dla jednego interfejsu API (bardziej szczegółowym poziomie) zastąpią dla wszystkich interfejsów API.

## <a name="what-data-is-added-to-azure-application-insights"></a>Jakie dane są dodawane do usługi Azure Application Insights

Odbiera Azure Application Insights:

+ *Żądanie* telemetrii elementu, dla każdego żądania przychodzącego (*żądania frontonu*, *odpowiedzi frontonu*),
+ *Zależności* telemetrii elementu, dla każdego żądania przekazywane do usługi zaplecza (*żądania zaplecza*, *odpowiedzi zaplecza*),
+ *Wyjątek* elementu telemetrii, dla każdego żądania nie powiodło się.

Nie powiodło się żądanie jest żądaniem, które:

+ nie powiodło się z powodu połączenie zamknięte klienta lub
+ wyzwalane *na błąd* sekcji zasad interfejsu API, lub
+ ma 4xx pasującego kod stanu HTTP odpowiedzi lub 5xx.

## <a name="performance-implications-and-log-sampling"></a>Wpływ na wydajność i dziennika próbkowania

> [!WARNING]
> Wszystkie zdarzenia może mieć wpływ na wydajność poważny, w zależności od szybkości żądań przychodzących.

Oparte na testach obciążenia wewnętrznego, włączenie tej funkcji spowodowany zmniejszenie 40 do 50% przepływność liczby żądań przekroczyła 1000 żądań na sekundę. Azure Application Insights umożliwia analizę statystyczną do oceny wydajności aplikacji. Nie ma być systemu inspekcji, a nie jest przeznaczone do rejestrowania każdej pojedynczej żądania dla dużych interfejsów API.

Liczba żądań, które są rejestrowane przez dostosowanie wartości właściwości można manipulować **próbkowania** ustawienie (zobacz kroków opisanych powyżej). Wartość 100% oznacza, że wszystkie żądania są rejestrowane, gdy 0% odzwierciedla w ogóle nie rejestrowania. **Próbkowanie** pozwala zmniejszyć ilość danych telemetrycznych, efektywnie uniemożliwia zmniejszenie wydajności znaczące, badając wciąż z zalet rejestrowania.

Pomijanie rejestrowanie nagłówki i treści żądania i odpowiedzi będą także mieć pozytywny wpływ na złagodzenia problemy z wydajnością.

## <a name="next-steps"></a>Kolejne kroki

+ Dowiedz się więcej o [Azure Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/).
+ Należy wziąć pod uwagę [rejestrowania w usłudze Azure Event Hubs](api-management-howto-log-event-hubs.md).
