---
title: Wprowadzenie do skalowania automatycznego na platformie Azure
description: Dowiedz się, jak można skalować aplikacji sieci Web zasobu, usługa w chmurze, maszyny wirtualnej lub skalowania maszyny wirtualnej należy ustawić na platformie Azure.
author: rajram
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: rajram
ms.component: autoscale
ms.openlocfilehash: 2781e718e3829c13dcc8cdd998936cfba30d8550
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263650"
---
# <a name="get-started-with-autoscale-in-azure"></a>Wprowadzenie do skalowania automatycznego na platformie Azure
W tym artykule opisano, jak skonfigurować ustawienia automatycznego skalowania dla zasobu w portalu Microsoft Azure.

Azure Monitor skalowania automatycznego dotyczy tylko zestawy skalowania maszyny wirtualnej, usługi w chmurze, planów usługi aplikacji Azure i środowiska usługi aplikacji. 

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Wykryj ustawienia skalowania automatycznego w ramach subskrypcji
Umożliwia odnalezienie wszystkich zasobów, dla których ma zastosowanie w monitorze Azure skalowania automatycznego. Przewodnik krok po kroku, wykonaj następujące kroki:

1. Otwórz [portalu Azure.][1]
2. Kliknij ikonę Azure w lewym okienku.
  ![Otwórz Monitor Azure][2]
3. Kliknij przycisk **skalowania automatycznego** do wyświetlania wszystkich zasobów, dla których jest stosowane wraz z ich bieżący stan skalowania automatycznego skalowania automatycznego.
  ![Odnajdywanie automatycznego skalowania w Monitorze systemu Azure][3]

Okienko filtru u góry do zakresu w dół listy wybrać zasoby w określonej grupy zasobów, określonych typów zasobów ani konkretnego zasobu.

Dla każdego zasobu można znaleźć bieżącą liczbę wystąpień i stan automatycznego skalowania. Stan automatycznego skalowania może być:

- **Nieskonfigurowane**: nie włączono automatycznego skalowania jeszcze dla tego zasobu.
- **Włączone**: funkcja automatycznego skalowania zostało włączone dla tego zasobu.
- **Wyłączone**: funkcja automatycznego skalowania zostało wyłączone dla tego zasobu.

## <a name="create-your-first-autoscale-setting"></a>Tworzenie pierwszego Ustawienia skalowania automatycznego

Umożliwia teraz przejść przez prosty przewodnik krok po kroku, aby utworzyć pierwszy ustawienia skalowania automatycznego.

1. Otwórz **skalowania automatycznego** bloku Azure Monitor i wybierz zasób, który ma być skalowana. (Plan usługi aplikacji skojarzone z aplikacją sieci web Użyj następujące kroki. Możesz [tworzenie pierwszej aplikacji sieci web platformy ASP.NET na platformie Azure w ciągu 5 minut.] [4])
2. Należy pamiętać, że bieżąca liczba wystąpień jest 1. Kliknij przycisk **Włączanie automatycznego skalowania**.
  ![Ustawienia skalowania dla nowej aplikacji sieci web][5]
3. Podaj nazwę dla ustawienia skalowania, a następnie kliknij przycisk **dodać regułę**. Zwróć uwagę, otwórz okienko kontekstu po prawej stronie Opcje reguły skalowania. Domyślnie to ustawienie opcji skalowania z liczbą wystąpień przez 1, jeśli procent użycia procesora CPU zasobu przekracza 70 procent. Pozostaw wartości domyślne, a następnie kliknij przycisk **Dodaj**.
  ![Utwórz ustawienie skali dla aplikacji sieci web][6]
4. Został już utworzony pierwszej reguły skalowania. Zauważ, że środowiska użytkownika zalecane najlepsze rozwiązania i stwierdza, że "jest zalecane w celu zapewnienia skalowania co najmniej jedną regułę." W tym celu:
  
    a. Kliknij przycisk **dodać regułę**. 

    b. Ustaw **Operator** do **mniej niż**.

    c. Ustaw **próg** do **20**.

    d. Ustaw **operacji** do **zmniejszyć liczbę przez**.

   Teraz powinna mieć ustawienia skalowania czy skale poza/możliwość skalowania w oparciu o użycie procesora CPU.
   ![Oparte na Procesorze skali][8]
5. Kliknij pozycję **Zapisz**.

Gratulacje! Ustawienia skalowania pierwszy aby stosować automatyczne skalowanie aplikacji sieci web oparta na użycie procesora CPU teraz została pomyślnie utworzona.

> [!NOTE] 
> Te same działania mają zastosowanie do wprowadzenie do zestawu skalowania maszyn wirtualnych lub w chmurze usługi roli.

## <a name="other-considerations"></a>Inne zagadnienia
### <a name="scale-based-on-a-schedule"></a>Skala zgodnie z harmonogramem
Oprócz skali oparte na Procesorze Twoje skali można ustawić inaczej, określone dni tygodnia.

1. Kliknij przycisk **Dodaj warunek skali**.
2. Ustawienie trybu skali i zasad jest taka sama jak domyślnego warunku.
3. Wybierz **Powtórz określone dni** harmonogramu.
4. Wybierz dni i godziny rozpoczęcia i zakończenia stosowania warunku skali.

![Skala warunkiem na podstawie harmonogramu][9]
### <a name="scale-differently-on-specific-dates"></a>Skalowanie inaczej w określonym dniu
Oprócz skali oparte na Procesorze na skalę można ustawić inaczej, dla określonej daty.

1. Kliknij przycisk **Dodaj warunek skali**.
2. Ustawienie trybu skali i zasad jest taka sama jak domyślnego warunku.
3. Wybierz **określ daty rozpoczęcia i zakończenia** harmonogramu.
4. Wybierz daty rozpoczęcia i zakończenia oraz czas rozpoczęcia i zakończenia stosowania warunku skali.

![Warunek skali na podstawie dat][10]

### <a name="view-the-scale-history-of-your-resource"></a>Wyświetl historię skalowania zasobu
Zawsze, gdy zasób jest skalowana w górę lub w dół, zdarzenie jest rejestrowane w dzienniku aktywności. Można wyświetlić historię skalowania zasobu w ciągu ostatnich 24 godzin, przez przełączenie do **Historia uruchomień** kartę.

![Historia uruchamiania][11]

Jeśli chcesz wyświetlić historię pełną skali (w przypadku maksymalnie 90 dni), wybierz **kliknij tutaj, aby zobaczyć więcej szczegółów**. Umożliwia otwarcie dziennika aktywności z automatycznego skalowania jest wstępnie zaznaczona dla zasobu i kategorii.

### <a name="view-the-scale-definition-of-your-resource"></a>Widok definicji skalowania zasobu
Skalowania automatycznego jest zasobem usługi Azure Resource Manager. Definicja skali można wyświetlić w formacie JSON przez przełączenie do **JSON** kartę.

![Definicja skali][12]

Należy wprowadzić zmiany w formacie JSON, jeśli jest to wymagane. Te zmiany zostaną uwzględnione po ich zapisaniu.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Wyłączanie automatycznego skalowania i ręcznie skalować swoich wystąpień
Może to być razy, jeśli chcesz wyłączyć bieżące ustawienia skalowania i ręcznie skalować zasób.

Kliknij przycisk **wyłączanie automatycznego skalowania** na górze.
![Wyłączanie automatycznego skalowania][13]

> [!NOTE] 
> Ta opcja powoduje wyłączenie konfiguracji. Jednak możesz wrócić do niego po włączeniu skalowania automatycznego ponownie. 

Można teraz ustawić liczba wystąpień, które chcesz skalować ręcznie.

![Zestaw skali ręczne][14]

Możesz zawsze wrócić do automatycznego skalowania, klikając **Włączanie automatycznego skalowania** , a następnie **zapisać**.

## <a name="next-steps"></a>Kolejne kroki
- [Utwórz działanie dziennika alertów monitorowania wszystkich operacji aparatu skalowania automatycznego na subskrypcję](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Utwórz działanie dziennika alertów można monitorować wszystkie nieudane operacje w/skali skalowalnych w poziomie skalowania automatycznego na subskrypcję](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/monitoring-autoscale-get-started/azure-monitor-launch.png
[3]: ./media/monitoring-autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-dotnet
[5]: ./media/monitoring-autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/monitoring-autoscale-get-started/scale-in-recommendation.png
[8]: ./media/monitoring-autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/monitoring-autoscale-get-started/scale-condition-schedule.png
[10]: ./media/monitoring-autoscale-get-started/scale-condition-dates.png
[11]: ./media/monitoring-autoscale-get-started/scale-history.png
[12]: ./media/monitoring-autoscale-get-started/scale-definition-json.png
[13]: ./media/monitoring-autoscale-get-started/disable-autoscale.png
[14]: ./media/monitoring-autoscale-get-started/set-manualscale.png

