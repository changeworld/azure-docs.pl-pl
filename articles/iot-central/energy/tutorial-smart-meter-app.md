---
title: 'Samouczek: Tworzenie aplikacji do analizy inteligentnych liczników za pomocą usługi IoT Central'
description: 'Samouczek: Dowiedz się, jak utworzyć aplikację do monitorowania inteligentnych liczników przy użyciu szablonów aplikacji Usługi Azure IoT Central.'
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 9e954e9c1a7efa43a19849b1c5b40284ec84eeed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77016004"
---
# <a name="tutorial-create-and-walk-through-the-smart-meter-monitoring-app-template"></a>Samouczek: Tworzenie i przechodzenie przez szablon aplikacji do monitorowania inteligentnych liczników 



W tym samouczku można przejść przez proces tworzenia aplikacji do monitorowania inteligentnych liczników, która zawiera przykładowy model urządzenia z symulowanych danych. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji Smart Meter za darmo
> * Przejście do aplikacji
> * Oczyszczanie zasobów


Jeśli nie masz subskrypcji, [utwórz bezpłatne konto próbne](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Wymagania wstępne
- Brak
- Subskrypcja platformy Azure jest zalecana, ale nie jest wymagana do wypróbowania

## <a name="create-a-smart-meter-monitoring-app"></a>Tworzenie aplikacji do monitorowania inteligentnych liczników 

Możesz utworzyć tę aplikację w trzech prostych krokach:

1. Otwórz [stronę główną usługi Azure IoT Central](https://apps.azureiotcentral.com) i kliknij przycisk **Utwórz,** aby utworzyć nową aplikację. 

2. Wybierz kartę **Energia** i kliknij pozycję **Utwórz aplikację** w obszarze Kafelek aplikacji **monitorowania inteligentnych liczników.**

    > [!div class="mx-imgBorder"]
    > ![Tworzenie aplikacji](media/tutorial-iot-central-smart-meter/smart-meter-build.png)
    

3. **Utwórz aplikację** otworzy nowy formularz **aplikacji.** Wypełnij wymagane dane, jak pokazano na poniższym rysunku:
    * **Nazwa aplikacji**: Wybierz nazwę aplikacji IoT Central. 
    * **ADRES URL:** Wybierz centralny adres URL IoT, platforma zweryfikuje jego unikatowość.
    * **7-dniowa bezpłatna wersja próbna:** jeśli masz już subskrypcję platformy Azure, zalecane jest ustawienie domyślne. Jeśli nie masz subskrypcji platformy Azure, zacznij od bezpłatnej wersji próbnej.
    * **Informacje rozliczeniowe**: Sama aplikacja jest bezpłatna. Szczegóły katalogu, platformy Azure i regionu są wymagane do aprowizowania zasobów dla aplikacji.
    * Kliknij przycisk **Utwórz** u dołu strony, a aplikacja zostanie utworzona za minutę.

        ![Nowy formularz zgłoszeniowy](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app.png)

        ![Nowe informacje o rozliczeniach formularza wniosku](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Weryfikacja aplikacji i symulowanych danych

Nowo utworzona aplikacja inteligentnych liczników to Twoja aplikacja i możesz ją zmodyfikować w dowolnym momencie. Upewnijmy się, że aplikacja jest wdrażana i działa zgodnie z oczekiwaniami przed jej zmodyfikowaniem.

Aby zweryfikować tworzenie aplikacji i symulację danych, przejdź do **pulpitu nawigacyjnego**. Jeśli widzisz kafelki z niektórymi danymi, wdrożenie aplikacji zakończyło się pomyślnie. Symulacja danych może potrwać kilka minut, aby wygenerować dane, więc daj mu 1-2 minuty. 

## <a name="application-walk-through"></a>Przejście do aplikacji
Po pomyślnym wdrożeniu szablonu aplikacji jest dostarczany z przykładowym urządzeniem inteligentnego licznika, modelem urządzenia i pulpitem nawigacyjnym. 

Adatum to fikcyjna firma energetyczna, która monitoruje i zarządza inteligentnymi licznikami. Na pulpicie nawigacyjnym monitorowania inteligentnych liczników są widoczne właściwości inteligentnego licznika, dane i przykładowe polecenia. Umożliwia operatorom i zespołom pomocy technicznej proaktywne wykonywanie następujących działań, zanim zmieni się w zdarzenia pomocy technicznej: 
* Przejrzyj najnowsze informacje o liczniku i jego zainstalowaną lokalizację na mapie
* Proaktywne sprawdzanie sieci liczników i stanu połączenia 
* Monitorowanie odczytów napięcia Min i Max dla kondycji sieci 
* Przejrzyj trendy w zakresie energii, energii i napięcia, aby wychwycić wszelkie nietypowe wzorce 
* Śledzenie całkowitego zużycia energii do celów planowania i rozliczeń
* Operacje dowodzenia i sterowania, takie jak ponowne podłączenie licznika i aktualizacja wersji oprogramowania układowego. W szablonie przyciski poleceń pokazują możliwe funkcje i nie wysyłają prawdziwych poleceń. 

> [!div class="mx-imgBorder"]
> ![Inteligentny pulpit nawigacyjny monitorowania liczników](media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png)

### <a name="devices"></a>Urządzenia
Aplikacja jest wyposażona w przykładowe urządzenie inteligentnego miernika. Szczegóły urządzenia można wyświetlić, klikając kartę **Urządzenia.**

> [!div class="mx-imgBorder"]
> ![Inteligentne urządzenia pomiarowe](media/tutorial-iot-central-smart-meter/smart-meter-devices.png)

Kliknij łącze przykładowe urządzenie **SM0123456789,** aby wyświetlić szczegóły urządzenia. Można zaktualizować zapisywalne właściwości urządzenia na stronie **Aktualizuj właściwości** i wizualizować zaktualizowane wartości na pulpicie nawigacyjnym.

> [!div class="mx-imgBorder"]
> ![Właściwości inteligentnych liczników](media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png)

### <a name="device-template"></a>Szablon urządzenia
Kliknij kartę **Szablony urządzeń,** aby wyświetlić model urządzenia inteligentnego licznika. Model ma wstępnie zdefiniowany interfejs danych, właściwości, poleceń i widoków.

> [!div class="mx-imgBorder"]
> ![Szablony inteligentnych urządzeń pomiarowych](media/tutorial-iot-central-smart-meter/smart-meter-device-template.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli zdecydujesz się nie kontynuować korzystania z tej aplikacji, usuń aplikację, wykonując następujące kroki:

1. W lewym okienku otwórz kartę Administracja
2. Wybierz pozycję Ustawienia aplikacji i kliknij przycisk Usuń u dołu strony. 

    > [!div class="mx-imgBorder"]
    > ![Usuwanie aplikacji](media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png)


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o architekturze aplikacji inteligentnych liczników zapoznaj się [z artykułem koncepcyjnym](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-smart-meter-app)
* Tworzenie szablonów aplikacji inteligentnych liczników za darmo: [aplikacja inteligentnych liczników](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* Dowiedz się więcej o UIOT Central, zobacz [Omówienie IoT Central](https://docs.microsoft.com/azure/iot-central/)
