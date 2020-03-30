---
title: Wprowadzenie do automatycznego skalowania na platformie Azure
description: Dowiedz się, jak skalować zasoby aplikacji sieci Web, usługi w chmurze, maszyny wirtualnej lub skali maszyny wirtualnej ustawione na platformie Azure.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 0a40496e4d496d0062c6ee7a6ab935c1ad9b35a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75396350"
---
# <a name="get-started-with-autoscale-in-azure"></a>Rozpoczynanie pracy ze skalowaniem automatycznym na platformie Azure
W tym artykule opisano sposób konfigurowania ustawień skalowania automatycznego zasobu w witrynie Microsoft Azure portal.

Skalowanie automatyczne usługi Azure Monitor dotyczy tylko [zestawów skalowania maszyn wirtualnych,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [usług w chmurze,](https://azure.microsoft.com/services/cloud-services/) [usługi aplikacji — aplikacje sieci Web](https://azure.microsoft.com/services/app-service/web/)i usług [zarządzania interfejsami API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Odkryj ustawienia skalowania automatycznego w ramach subskrypcji
Można odkryć wszystkie zasoby, dla których skalowanie automatyczne ma zastosowanie w usłudze Azure Monitor. Aby wykonać wskazówki krok po kroku, należy wykonać następujące czynności:

1. Otwórz [witrynę Azure portal.][1]
1. Kliknij ikonę usługi Azure Monitor w lewym okienku.
  ![Otwórz monitor platformy Azure][2]
1. Kliknij **opcję Skalowanie automatyczne,** aby wyświetlić wszystkie zasoby, dla których ma zastosowanie skalowanie automatyczne, wraz z ich bieżącym stanem Skalowanie automatyczne.
  ![Odkryj skalowanie automatyczne w usłudze Azure Monitor][3]

Za pomocą okienka filtru u góry można zaoceń w dół listy, aby wybrać zasoby w określonej grupie zasobów, określonych typach zasobów lub określonym zasobie.

Dla każdego zasobu zostanie ną bieżącą liczbę wystąpień i stan Skalowanie automatyczne. Stan skalowania automatycznego może być:

- **Nie skonfigurowano:** Nie włączono jeszcze skalowania automatycznego dla tego zasobu.
- **Włączono:** Włączono skalowanie automatyczne dla tego zasobu.
- **Wyłączone:** Dla tego zasobu została wyłączona automatyczna skala.

## <a name="create-your-first-autoscale-setting"></a>Tworzenie pierwszego ustawienia skalowania automatycznego

Przejdźmy teraz do prostego przewodnika krok po kroku, aby utworzyć pierwsze ustawienie skalowania automatycznego.

1. Otwórz **blok Skalowania automatycznego** w usłudze Azure Monitor i wybierz zasób, który chcesz skalować. (Poniższe kroki używają planu usługi app service skojarzonego z aplikacją sieci web. Możesz [utworzyć pierwszą ASP.NET aplikacji sieci web na platformie Azure w ciągu 5 minut.][4])
1. Należy zauważyć, że bieżąca liczba wystąpień wynosi 1. Kliknij **pozycję Włącz skalowanie automatyczne**.
  ![Ustawienie skalowania dla nowej aplikacji sieci Web][5]
1. Podaj nazwę ustawienia skali, a następnie kliknij pozycję **Dodaj regułę**. Zwróć uwagę na opcje reguły skalowania, które otwierają się jako okienko kontekstu po prawej stronie. Domyślnie powoduje to ustawienie opcji skalowania liczby wystąpień o 1, jeśli procent procesora CPU zasobu przekracza 70 procent. Pozostaw go przy wartościach domyślnych i kliknij przycisk **Dodaj**.
  ![Tworzenie ustawienia skali dla aplikacji sieci Web][6]
1. Teraz utworzono pierwszą regułę skalowania. Należy zauważyć, że środowisko użytkownika zaleca najlepsze rozwiązania i stwierdza, że "Zaleca się, aby mieć co najmniej jedną skalę w regule." W tym celu:

    a. Kliknij pozycję **Dodaj regułę**.

    b. Ustaw **operator** na **mniej niż**.

    d. Ustaw **próg** na **20**.

    d. Ustaw **operację,** aby **zmniejszyć liczbę o**.

   Teraz powinno być ustawienie skali, które jest skalowane w poziomie/skaluje się w oparciu o użycie procesora CPU.
   ![Skala oparta na procesorze][8]
1. Kliknij przycisk **Zapisz**.

Gratulacje! Teraz pomyślnie utworzono pierwsze ustawienie skalowania, aby automatycznie skalować aplikację internetową na podstawie użycia procesora CPU.

> [!NOTE]
> Te same kroki mają zastosowanie do rozpoczęcia pracy z zestawem skalowania maszyny wirtualnej lub rolą usługi w chmurze.

## <a name="other-considerations"></a>Inne zagadnienia
### <a name="scale-based-on-a-schedule"></a>Skalowanie na podstawie harmonogramu
Oprócz skalowania na podstawie procesora CPU można ustawić skalę inaczej dla określonych dni tygodnia.

1. Kliknij **pozycję Dodaj warunek skali**.
1. Ustawienie trybu skalowania i reguł jest taka sama jak warunek domyślny.
1. Wybierz **pozycję Powtórz określone dni** dla harmonogramu.
1. Wybierz dni i godzinę rozpoczęcia/zakończenia, kiedy należy zastosować warunek skali.

![Warunek skalowania na podstawie harmonogramu][9]
### <a name="scale-differently-on-specific-dates"></a>Skaluj się inaczej w określonych terminach
Oprócz skalowania na podstawie procesora CPU można ustawić skalę inaczej dla określonych dat.

1. Kliknij **pozycję Dodaj warunek skali**.
1. Ustawienie trybu skalowania i reguł jest taka sama jak warunek domyślny.
1. Wybierz **pozycję Określ daty rozpoczęcia/zakończenia** dla harmonogramu.
1. Wybierz daty rozpoczęcia/zakończenia oraz godzinę rozpoczęcia/zakończenia, kiedy należy zastosować warunek skali.

![Warunek skalowania na podstawie dat][10]

### <a name="view-the-scale-history-of-your-resource"></a>Wyświetlanie historii skalowania zasobu
Za każdym razem, gdy zasób jest skalowany w górę lub w dół, zdarzenie jest rejestrowane w dzienniku aktywności. Historię skalowania zasobu można wyświetlić w ciągu ostatnich 24 godzin, przełączając się na kartę **Uruchom historię.**

![Historia uruchamiania][11]

Jeśli chcesz wyświetlić pełną historię skalowania (przez maksymalnie 90 dni), wybierz **pozycję Kliknij tutaj, aby wyświetlić więcej szczegółów**. Zostanie otwarty dziennik aktywności z wstępnie wybraną wstępnie wybraną alokacją skalowania automatycznego dla zasobu i kategorii.

### <a name="view-the-scale-definition-of-your-resource"></a>Wyświetlanie definicji skali zasobu
Skalowanie automatyczne jest zasobem usługi Azure Resource Manager. Definicję skali można wyświetlić w JSON, przełączając się na kartę **JSON.**

![Definicja skali][12]

W razie potrzeby można wprowadzać zmiany w JSON bezpośrednio. Zmiany te zostaną odzwierciedlone po ich zapisaniu.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Wyłącz skalowanie automatyczne i ręcznie skaluj wystąpienia
Może się okazać, że chcesz wyłączyć bieżące ustawienie skali i ręcznie skalować zasób.

Kliknij przycisk **Wyłącz skalowanie automatyczne** u góry.
![Wyłącz skalowanie automatyczne][13]

> [!NOTE]
> Ta opcja wyłącza konfigurację. Można jednak wrócić do niego po ponownym włączeniu skalowania automatycznego.

Teraz można ustawić liczbę wystąpień, które mają być skalowane ręcznie.

![Ustawianie skali ręcznej][14]

Zawsze możesz powrócić do skalowania automatycznego, klikając **pozycję Włącz skalowanie automatyczne,** a następnie **zapisz**.

## <a name="next-steps"></a>Następne kroki
- [Tworzenie alertu dziennika aktywności w celu monitorowania wszystkich operacji silnika skalowania automatycznego w ramach subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Tworzenie alertu dziennika aktywności w celu monitorowania wszystkich nieudanych operacji skalowania automatycznego w subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

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

