---
title: Rozpoczynanie pracy z funkcją automatycznego skalowania na platformie Azure
description: Dowiedz się, jak skalować zasób aplikacji sieci Web, usługi w chmurze, maszyny wirtualnej lub skalowania maszyn wirtualnych należy ustawić na platformie Azure.
author: rajram
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: rajram
ms.subservice: autoscale
ms.openlocfilehash: 0535c84a8ee0776c2c35a46d3c7510a2cd615cf6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60788594"
---
# <a name="get-started-with-autoscale-in-azure"></a>Rozpoczynanie pracy z funkcją automatycznego skalowania na platformie Azure
W tym artykule opisano sposób konfigurowania ustawienia automatycznego skalowania dla zasobu w portalu Microsoft Azure.

Skalowanie automatyczne platformy Azure Monitor ma zastosowanie tylko do [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [usług w chmurze](https://azure.microsoft.com/services/cloud-services/), [App Service — Web Apps](https://azure.microsoft.com/services/app-service/web/), i [usługi API Management](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Odkryj ustawień automatycznego skalowania w ramach subskrypcji
Może odnajdywać wszystkie zasoby, których Skalowanie automatyczne dotyczy w usłudze Azure Monitor. Aby uzyskać instrukcje krok po kroku przewodnik, wykonaj następujące czynności:

1. Otwórz [witryny Azure portal.][1]
1. Kliknij ikonę usługi Azure Monitor w okienku po lewej stronie.
  ![Otwórz monitora platformy Azure][2]
1. Kliknij przycisk **skalowania automatycznego** do wyświetlania wszystkich zasobów, dla których ma zastosowanie, wraz z ich bieżący stan skalowania automatycznego skalowania automatycznego.
  ![Odnajdywanie automatyczne skalowanie w usłudze Azure Monitor][3]

Okienko filtru u góry, aby zakres w dół na liście służy do wybierania zasobów w określonej grupie zasobów, określonych typów zasobów lub określonego zasobu.

Dla każdego zasobu znajduje się bieżąca liczba wystąpień i stan skalowania automatycznego. Stan skalowania automatycznego można:

- **Nieskonfigurowane**: Nie włączono automatycznego skalowania jeszcze dla tego zasobu.
- **Włączone**: Funkcja automatycznego skalowania ma włączone dla tego zasobu.
- **Wyłączone**: Funkcja automatycznego skalowania zostało wyłączone dla tego zasobu.

## <a name="create-your-first-autoscale-setting"></a>Utwórz swoje pierwsze ustawienie automatycznego skalowania

Przejdźmy teraz przechodzić przez proste przewodnik krok po kroku, aby utworzyć swoje pierwsze ustawienie automatycznego skalowania.

1. Otwórz **skalowania automatycznego** bloku w usłudze Azure Monitor i wybierz zasób, który ma być skalowana. (Użyto plan usługi App Service, skojarzone z aplikacją sieci web. Możesz [tworzenie pierwszej aplikacji sieci web ASP.NET na platformie Azure w ciągu 5 minut.] [4])
1. Należy pamiętać, że bieżąca liczba wystąpień jest 1. Kliknij przycisk **włączyć Skalowanie automatyczne**.
  ![Ustawienie Skala dla nowej aplikacji sieci web][5]
1. Podaj nazwę dla ustawienia skalowania, a następnie kliknij przycisk **Dodaj regułę**. Zapoznaj się z opcjami reguły skalowania, które Uruchom jako w okienku kontekstowym po prawej stronie. Domyślnie to ustawienie opcji skalować swoje liczbę wystąpień o 1, jeśli procent użycia procesora CPU zasobu przekracza 70 procent. Pozostaw wartości domyślne, a następnie kliknij przycisk **Dodaj**.
  ![Tworzenie Ustawienia skalowania dla aplikacji sieci web][6]
1. Teraz utworzyłeś swoją pierwszą regułę skalowania. Pamiętaj, że środowiska użytkownika zaleca najlepsze rozwiązania i informacją, że "zaleca się mieć co najmniej jeden skalowania w regule." Aby to zrobić:

    a. Kliknij przycisk **Dodaj regułę**.

    b. Ustaw **Operator** do **mniej niż**.

    c. Ustaw **próg** do **20**.

    d. Ustaw **operacji** do **Zmniejsz liczbę o**.

   Teraz powinny być zapisane ustawienia skalowania, skalować poza/skaluje się w oparciu o użycie procesora CPU.
   ![Skalowanie procesora CPU][8]
1. Kliknij pozycję **Zapisz**.

Gratulacje! Pomyślnie utworzono usługi pierwsze ustawienie skalowania automatycznego skalowania aplikacji sieci web na podstawie użycia procesora CPU.

> [!NOTE]
> Te same kroki mają zastosowanie do rozpocząć korzystanie z zestawu skalowania maszyn wirtualnych lub roli usługi w chmurze.

## <a name="other-considerations"></a>Inne zagadnienia
### <a name="scale-based-on-a-schedule"></a>Skalowanie na podstawie harmonogramu
Oprócz skalowania oparty na procesorze CPU skalowania można ustawić inaczej, określone dni tygodnia.

1. Kliknij przycisk **Dodaj warunek skalowania**.
1. Ustawienie trybu skalowania i zasad jest taka sama jak domyślne warunku.
1. Wybierz **Powtarzaj w określone dni** harmonogramu.
1. Wybierz dni i godziny rozpoczęcia i zakończenia, stosowania warunek skalowania.

![Warunek skalowania na podstawie harmonogramu][9]
### <a name="scale-differently-on-specific-dates"></a>Skalować w różny sposób w określonym dniu
Oprócz skalowania oparty na procesorze CPU skalowania można ustawić inaczej, dla określonej daty.

1. Kliknij przycisk **Dodaj warunek skalowania**.
1. Ustawienie trybu skalowania i zasad jest taka sama jak domyślne warunku.
1. Wybierz **określ daty rozpoczęcia i zakończenia** harmonogramu.
1. Wybierz daty rozpoczęcia i zakończenia oraz czas rozpoczęcia/zakończenia stosowania warunek skalowania.

![Warunek skalowania na podstawie dat][10]

### <a name="view-the-scale-history-of-your-resource"></a>Wyświetl historię skalowania zasobu
Zawsze, gdy zasób jest skalowany w górę lub w dół, zdarzenie jest rejestrowane w dzienniku aktywności. Można wyświetlić historię skalowania zasobu w ciągu ostatnich 24 godzin, przełączając się **historii uruchamiania** kartę.

![Historia uruchamiania][11]

Aby wyświetlić historię skalowanie pełną (przez maksymalnie 90 dni), należy zaznaczyć **kliknij tutaj, aby zobaczyć więcej szczegółów**. Zostanie otwarty w dzienniku aktywności z funkcją automatycznego skalowania wstępnie wybrana dla zasobów i kategorii.

### <a name="view-the-scale-definition-of-your-resource"></a>Wyświetl definicja skali zasobu
Automatyczne skalowanie jest zasobem usługi Azure Resource Manager. Definicja skalowania można wyświetlić w formacie JSON, przełączając się **JSON** kartę.

![Definicja skali][12]

Możesz wprowadzać zmiany w formacie JSON bezpośrednio, jeśli jest to wymagane. Te zmiany zostaną odzwierciedlone po ich zapisaniu.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Wyłączanie skalowania automatycznego i przeprowadź ręczne skalowanie wystąpień
Może to być razy, gdy zachodzi potrzeba wyłączenia bieżące ustawienia skalowania i przeprowadź ręczne skalowanie zasobu.

Kliknij przycisk **wyłączyć automatyczne skalowanie** znajdujący się u góry.
![Wyłączanie skalowania automatycznego][13]

> [!NOTE]
> Ta opcja powoduje wyłączenie konfigurację. Jednak możesz wrócić do niego po ponownie włączyć Skalowanie automatyczne.

Można teraz ustawić liczbę wystąpień, które chcesz, aby możliwe było skalowanie ręczne.

![Skalowanie ręczne zestawu][14]

Zawsze możesz wrócić do automatycznego skalowania, klikając **włączyć Skalowanie automatyczne** i następnie **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki
- [Utwórz działanie dziennika alertów można monitorować wszystkie operacje aparat skalowania automatycznego w ramach subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Utwórz działanie dziennika alertów można monitorować wszystkie nieudane operacje skalowania — w/skalowalnego w poziomie skalowania automatycznego w ramach subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-dotnet
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png

