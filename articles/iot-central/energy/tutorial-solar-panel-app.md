---
title: 'Samouczek: Tworzenie aplikacji do monitorowania karnych na energię słoneczną za pomocą aplikacji IoT Central'
description: 'Samouczek: Dowiedz się, jak utworzyć aplikację panelu słonecznego przy użyciu szablonów aplikacji Usługi Azure IoT Central.'
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: d5ea3d3420cb598693ccaede7ee10d2f8c4fd839
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77025779"
---
# <a name="tutorial-create-and-walk-through-the-solar-panel-monitoring-app-template"></a>Samouczek: Tworzenie i przechodzenie przez szablon aplikacji do monitorowania panelu słonecznego 



W tym samouczku można przejść przez proces tworzenia aplikacji do monitorowania panelu słonecznego, która zawiera przykładowy model urządzenia z symulowanych danych. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:


> [!div class="checklist"]
> * Tworzenie aplikacji panelu słonecznego za darmo
> * Przejście do aplikacji
> * Oczyszczanie zasobów


Jeśli nie masz subskrypcji, [utwórz bezpłatne konto próbne](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Wymagania wstępne
- Brak
- Subskrypcja platformy Azure jest zalecana, ale nie jest wymagana do wypróbowania


## <a name="create-a-solar-panel-monitoring-app"></a>Tworzenie aplikacji do monitorowania paneli słonecznych 

Możesz utworzyć tę aplikację w trzech prostych krokach:

1. Otwórz [stronę główną usługi Azure IoT Central](https://apps.azureiotcentral.com) i kliknij przycisk **Utwórz,** aby utworzyć nową aplikację. 

2. Wybierz kartę **Energia** i kliknij pozycję **Utwórz aplikację** w obszarze Kafelek aplikacji **monitorowania panelu słonecznego.** 

    > [!div class="mx-imgBorder"]
    > ![Tworzenie aplikacji](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
3. **Utwórz aplikację** otworzy nowy formularz **aplikacji.** Wypełnij wymagane dane, jak pokazano na poniższym rysunku:
    * **Nazwa aplikacji**: Wybierz nazwę aplikacji IoT Central. 
    * **ADRES URL:** Wybierz centralny adres URL IoT, platforma zweryfikuje jego unikatowość.
    * **7-dniowa bezpłatna wersja próbna:** jeśli masz już subskrypcję platformy Azure, zalecane jest ustawienie domyślne. Jeśli nie masz subskrypcji platformy Azure, zacznij od bezpłatnej wersji próbnej.
    * **Informacje rozliczeniowe**: Sama aplikacja jest bezpłatna. Szczegóły katalogu, platformy Azure i regionu są wymagane do aprowizowania zasobów dla aplikacji.
    * Kliknij przycisk **Utwórz** u dołu strony, a aplikacja zostanie utworzona za minutę.
        ![Nowy formularz zgłoszeniowy](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![Nowe informacje o rozliczeniach formularza wniosku](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Weryfikacja aplikacji i symulowanych danych

Nowo utworzona aplikacja panelu słonecznego jest aplikacją i można ją zmodyfikować w dowolnym momencie. Upewnijmy się, że aplikacja jest wdrażana i działa zgodnie z oczekiwaniami przed jej zmodyfikowaniem.

Aby zweryfikować tworzenie aplikacji i symulację danych, przejdź do **pulpitu nawigacyjnego**. Jeśli widzisz kafelki z niektórymi danymi, wdrożenie aplikacji zakończyło się pomyślnie. Symulacja danych może potrwać kilka minut, aby wygenerować dane, więc daj mu 1-2 minuty. 

## <a name="application-walk-through"></a>Przejście do aplikacji
Po pomyślnym wdrożeniu szablonu aplikacji jest dostarczany z przykładowym urządzeniem inteligentnym licznikiem, modelem urządzenia i pulpitem nawigacyjnym.

Adatum to fikcyjna firma energetyczna, która monitoruje i zarządza panelami słonecznymi. Na pulpicie nawigacyjnym monitorowania panelu słonecznego są widoczne właściwości panelu słonecznego, dane i przykładowe polecenia. Umożliwia operatorom i zespołom pomocy technicznej proaktywne wykonywanie następujących działań, zanim zmieni się w zdarzenia pomocy technicznej:
* Przejrzyj najnowsze informacje o panelu i jego zainstalowaną lokalizację na mapie
* Proaktywne sprawdzanie stanu panelu i stanu połączenia
* Przejrzyj trendy w wytwarzania energii i temperaturze, aby wychwytywać wszelkie nietypowe wzorce
* Śledzenie całkowitego wytwarzania energii do celów planowania i rozliczeń
* Operacje dowodzenia i sterowania, takie jak aktywowanie panelu i aktualizacja wersji oprogramowania układowego. W szablonie przyciski poleceń pokazują możliwe funkcje i nie wysyłają prawdziwych poleceń.

> [!div class="mx-imgBorder"]
> ![Panel monitoringu panelu słonecznego](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Urządzenia
Aplikacja jest wyposażona w przykładowe urządzenie panelu słonecznego. Szczegóły urządzenia można wyświetlić, klikając kartę **Urządzenia.**

> [!div class="mx-imgBorder"]
> ![Urządzenia z panelami słonecznymi](media/tutorial-iot-central-solar-panel/solar-panel-device.png)


Kliknij łącze przykładowego urządzenia **SP0123456789,** aby wyświetlić szczegóły urządzenia. Na stronie **Aktualizuj właściwości** można zaktualizować zapisywalne właściwości urządzenia i wizualizować zaktualizowane wartości na pulpicie nawigacyjnym. 

> [!div class="mx-imgBorder"]
> ![Właściwości panelu słonecznego](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Szablon urządzenia
Kliknij kartę **Szablony urządzeń,** aby wyświetlić model urządzenia panelu słonecznego. Model ma wstępnie zdefiniowany interfejs danych, właściwości, poleceń i widoków.

> [!div class="mx-imgBorder"]
> ![Szablon urządzeń panelu słonecznego](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli zdecydujesz się nie kontynuować korzystania z tej aplikacji, usuń aplikację, wykonując następujące kroki:

1. W lewym okienku otwórz kartę Administracja
2. Wybierz pozycję Ustawienia aplikacji i kliknij przycisk Usuń u dołu strony. 

    > [!div class="mx-imgBorder"]
    > ![Usuwanie aplikacji](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o architekturze aplikacji panelu słonecznego zapoznaj się [z artykułem koncepcyjnym](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-solar-panel-app)
* Tworzenie szablonów aplikacji panel słoneczny za darmo: [aplikacja panel słoneczny](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Dowiedz się więcej o UIOT Central, zobacz [Omówienie IoT Central](https://docs.microsoft.com/azure/iot-central/)

