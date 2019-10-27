---
title: Tworzenie aplikacji inteligentnego analizowania analiz przy użyciu IoT Central | Microsoft Docs
description: Dowiedz się, jak utworzyć aplikację do monitorowania miernika inteligentnego przy użyciu szablonów aplikacji platformy Azure IoT Central.
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: ba031000ed64ff00e9c502378bd2bbca2f363433
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72955347"
---
# <a name="tutorial-create-and-walk-through-the-smart-meter-monitoring-app-template"></a>Samouczek: Tworzenie i przechodzenie przez szablon aplikacji do monitorowania mierników inteligentnych 

Ten samouczek przeprowadzi Cię przez proces tworzenia aplikacji do monitorowania mierników inteligentnych, która obejmuje przykładowy model urządzenia z symulowanymi danymi. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz bezpłatnie aplikację miernika inteligentnego
> * Przewodnik po aplikacji
> * Oczyszczanie zasobów


Jeśli nie masz subskrypcji, [Utwórz konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Wymagania wstępne
- Brak
- Zalecana jest subskrypcja platformy Azure, ale nie jest wymagana do wypróbowania

## <a name="create-a-smart-meter-monitoring-app"></a>Tworzenie aplikacji do monitorowania mierników inteligentnych 

Tę aplikację można utworzyć w trzech prostych krokach:

1. Otwórz [stronę główną usługi Azure IoT Central](https://apps.azureiotcentral.com) i kliknij przycisk **Kompiluj** , aby utworzyć nową aplikację. 

2. Wybierz kartę **energia** , a następnie kliknij pozycję **Utwórz aplikację** na kafelku aplikacji **inteligentnego monitorowania miernika** .

    > [!div class="mx-imgBorder"]
    > ![](media/tutorial-iot-central-smart-meter/smart-meter-build.png) aplikacji kompilacji
    

3. **Utworzenie aplikacji** spowoduje otwarcie formularza **nowej aplikacji** . Wypełnij żądane szczegóły, jak pokazano na poniższej ilustracji:
    * **Nazwa aplikacji**: wybierz nazwę aplikacji IoT Central. 
    * **Adres URL**: Wybierz adres URL IoT Central, platforma sprawdzi swoją unikatowość.
    * **7-dniowa bezpłatna wersja próbna**: Jeśli masz już subskrypcję platformy Azure, zalecane jest ustawienie domyślne. Jeśli nie masz subskrypcji platformy Azure, Zacznij od bezpłatnej wersji próbnej.
    * **Informacje o rozliczeniach**: sama aplikacja jest bezpłatna. Szczegóły katalogu, subskrypcji platformy Azure i regionu są wymagane do aprowizacji zasobów aplikacji.
    * Kliknij przycisk **Utwórz** w dolnej części strony, a Twoja aplikacja zostanie utworzona na minutę lub w ten sposób.     
        > [!div class="mx-imgBorder"]
        > ![](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app.png) formularz nowej aplikacji


### <a name="verify-the-application-and-simulated-data"></a>Weryfikowanie aplikacji i danych symulowanych

Nowo utworzona aplikacja inteligentnego miernika jest aplikacją i można ją zmodyfikować w dowolnym momencie. Upewnij się, że aplikacja została wdrożona i działa zgodnie z oczekiwaniami przed jej zmodyfikowaniem.

Aby sprawdzić Tworzenie aplikacji i symulację danych, przejdź do **pulpitu nawigacyjnego**. Jeśli widzisz kafelki z niektórymi danymi, wdrożenie aplikacji zakończyło się pomyślnie. Generowanie danych przez symulację danych może potrwać kilka minut, więc Zapewnij 1-2 minut. 

## <a name="application-walk-through"></a>Przewodnik po aplikacji
Po pomyślnym wdrożeniu szablonu aplikacji jest on dostępny przy użyciu przykładowego urządzenia miernika inteligentnego, modelu urządzenia i pulpitu nawigacyjnego. 

Adatum to fikcyjna firma energetyczna, która monitoruje i zarządza inteligentnymi miernikami. Na pulpicie nawigacyjnym monitorowania mierników inteligentnych widoczne są właściwości, dane i przykładowe polecenia inteligentnego miernika. Umożliwia operatorom i zespołom pomocy technicznej przeprowadzenie aktywnego wykonywania następujących działań przed uwzględnieniem zdarzeń pomocy technicznej: 
* Przejrzyj najnowsze informacje o mierniku i jego zainstalowaną lokalizację na mapie
* Proaktywne sprawdzenie stanu sieci i połączenia 
* Monitoruj minimalne i maksymalne odczyty napięcia dla kondycji sieci 
* Przejrzyj trendy dotyczące energii, zasilania i napięcia, aby przechwytywać nietypowe wzorce 
* Śledź całkowite zużycie energii na potrzeby planowania i rozliczeń
* Operacje związane z poleceniami i kontrolami, takie jak ponowne łączenie licznika i aktualizacja wersji oprogramowania układowego. W szablonie przyciski poleceń pokazują możliwe funkcje i nie wysyłają prawdziwych poleceń. 

> [!div class="mx-imgBorder"]
> ![pulpitu nawigacyjnego monitorowania mierników inteligentnych](media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png)

### <a name="devices"></a>Urządzenia
Aplikacja jest dostarczana z przykładowym urządzeniem miernika inteligentnego. Aby wyświetlić szczegóły urządzenia, kliknij kartę **urządzenia** .

> [!div class="mx-imgBorder"]
> ![urządzeń mierników inteligentnych](media/tutorial-iot-central-smart-meter/smart-meter-devices.png)

Kliknij link przykładowe urządzenie **SM0123456789** , aby wyświetlić szczegóły urządzenia. Możesz zaktualizować właściwości z możliwością zapisu urządzenia na stronie **Właściwości aktualizacji** i wizualizować zaktualizowane wartości na pulpicie nawigacyjnym.

> [!div class="mx-imgBorder"]
> Właściwości inteligentnego miernika ![](media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png)

### <a name="device-template"></a>Szablon urządzenia
Kliknij kartę **Szablony urządzeń** , aby wyświetlić model urządzenia miernika inteligentnego. Model zawiera wstępnie zdefiniowane interfejsy dla danych, właściwości, poleceń i widoków.

> [!div class="mx-imgBorder"]
> ![szablonów urządzeń inteligentnych mierników](media/tutorial-iot-central-smart-meter/smart-meter-device-template.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli zdecydujesz, aby nie używać tej aplikacji, Usuń aplikację, wykonując następujące czynności:

1. Z menu po lewej stronie Otwórz kartę Administracja
2. Wybierz pozycję Ustawienia aplikacji, a następnie kliknij przycisk Usuń u dołu strony. 

    > [!div class="mx-imgBorder"]
    > ![usunąć aplikacji](media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png)


## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej o architekturze aplikacji mierników inteligentnych, zapoznaj się z artykułem koncepcji. 
* Twórz bezpłatnie szablony aplikacji mierników inteligentnych: [inteligentnego pomiaru aplikacji](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
