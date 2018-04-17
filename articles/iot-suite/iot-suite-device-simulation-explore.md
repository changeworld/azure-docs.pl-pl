---
title: Rozpoczynanie pracy z rozwiązaniem do symulacji urządzenia - Azure | Dokumentacja firmy Microsoft
description: Pakiet IoT rozwiązania symulacji to narzędzie, które mogą służyć do celów w zakresie projektowania i testowania rozwiązania IoT. Usługa symulacji jest autonomicznym oferty, który może być używany w połączeniu z innymi wstępnie skonfigurowanych rozwiązań lub używana o rozwiązaniach niestandardowych.
services: iot device simulation
suite: iot-suite
author: troyhopwood
manager: timlt
ms.author: troyhop
ms.service: iot-suite
ms.date: 12/15/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 1452508822f08d4554f419a72f7e9e6018a52469
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="device-simulation-walkthrough"></a>Wskazówki symulacji urządzenia

Symulacji urządzenia w usłudze Azure IoT to narzędzie, które mogą służyć do celów w zakresie projektowania i testowania rozwiązania IoT. Symulacji urządzenia jest autonomicznym oferty, że można użyć w połączeniu z innymi wstępnie skonfigurowanych rozwiązań lub rozwiązaniach niestandardowych.

W tym samouczku przedstawiono niektóre funkcje symulacji urządzenia. Przedstawiono sposób działania i można go użyć do przetestowania własne rozwiązania IoT.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

>[!div class="checklist"]
> * Skonfiguruj symulację
> * Uruchamianie i zatrzymywanie symulację
> * Wyświetl dane telemetryczne metryki

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka należy wdrożone wystąpienie symulacji urządzenia IoT Azure w ramach subskrypcji platformy Azure.

Jeśli wdrożono nie jeszcze symulacji urządzenia, ale należy wykonać [wdrażanie symulacji urządzenia IoT Azure](iot-suite-device-simulation-deploy.md) samouczka.

## <a name="configuring-device-simulation"></a>Konfigurowanie symulacji urządzenia

Można skonfigurować i uruchomić symulacji urządzenia całkowicie z poziomu pulpitu nawigacyjnego. Otwórz pulpit nawigacyjny z pakiet IoT [elastycznie rozwiązań](https://www.azureiotsuite.com/) strony. Kliknij przycisk **uruchamianie** w nowe wdrożenia symulacji urządzenia.

### <a name="target-iot-hub"></a>Centrum IoT docelowego

Możesz użyć symulacji urządzenia z wstępnie przygotowany Centrum IoT lub z innych Centrum IoT:

![Centrum IoT docelowego](media/iot-suite-device-simulation-explore/targethub.png)

> [!NOTE]
> Możliwość użycia wstępnie przygotowany Centrum IoT tylko jest dostępna, jeśli wybrano opcję utworzenia nowego centrum IoT po wdrożeniu symulacji urządzenia. Jeśli nie masz Centrum IoT, zawsze można utworzyć nową z [portalu Azure](https://portal.azure.com).

Pod kątem określonych Centrum IoT, podaj **iothubowner** parametry połączenia. Możesz uzyskać te parametry połączenia z [portalu Azure](https://portal.azure.com):

1. Na stronie konfiguracji centrum IoT w portalu Azure kliknij **zasady dostępu współużytkowanego**.
1. Kliknij przycisk **iothubowner**.
1. Skopiuj albo klucz podstawowy lub pomocniczy.
1. Wklej tę wartość w polu tekstowym w docelowym Centrum IoT.

![Centrum IoT docelowego](media/iot-suite-device-simulation-explore/connectionstring.png)

### <a name="device-model"></a>Model urządzenia

Model urządzenia umożliwia wybranie odpowiedniego typu urządzenia, aby symulować. Można wybrać jeden z modeli wstępnie skonfigurowanych urządzeń lub zdefiniować listę czujników modelu urządzeń niestandardowych:

![Model urządzenia](media/iot-suite-device-simulation-explore/devicemodel.png)

#### <a name="pre-configured-device-models"></a>Modele urządzeń wstępnie skonfigurowane

Symulacji urządzenia udostępnia trzy modele wstępnie skonfigurowanych urządzeń. Modele urządzeń dla chłodniach, windy i pojazdów są dostępne.

Modele urządzeń wstępnie skonfigurowane zawiera wiele czujniki z zaawansowanych zachowania zdefiniowanego w pliku języka JavaScript. Te niestandardowe zachowania nie są obsługiwane w interfejsie użytkownika sieci web. 

W poniższej tabeli przedstawiono listę konfiguracji dla każdego modelu wstępnie skonfigurowanych urządzeń:

| Model urządzenia | Czujnik | Jednostka | 
| -------------| ------ | -----| 
| Chłodnica | wilgotność | % |
| | pressure | psig | 
| | Temperatury | F | 
| Krótka | FLOOR | 
| | wibrację | mm | 
| | Temperatura | F | 
| Ciężarówka | Szerokość geograficzna | |
| | Długość geograficzna | | 
| | Szybkość | mil na godzinę | 
| | cargotemperature | F | 

#### <a name="custom-device-model"></a>Model urządzenia niestandardowe

Modele urządzeń niestandardowych umożliwiają czujników modelu, lepiej reprezentujących własnych urządzeń. Niestandardowe urządzenie może mieć maksymalnie 10 czujników niestandardowych.

Po wybraniu typu modelu urządzeń niestandardowych, można dodać nowego czujników, klikając **+ Dodaj czujnik**.

![Dodawanie czujników](media/iot-suite-device-simulation-explore/customsensors.png)

Niestandardowe czujników mieć następujące właściwości:

| Pole | Opis |
| ----- | ----------- |
| Nazwy czujnika | Przyjazna nazwa dla czujnika, takich jak **temperatury** lub **szybkości**. |
| Zachowanie | Zachowania Włącz danych telemetrycznych do różnią się w jednej wiadomości dalej, aby symulować danych rzeczywistych. **Przyrost** zwiększa się kolejno każdy komunikat wysyłany zaczynając od wartości minimalnej wartości. Gdy wartość maksymalna jest spełniony, następnie rozpoczyna się za pośrednictwem ponownie na wartość minimalna. **Zmniejszenie** działa w taki sam sposób jak **przyrostu** , ale liczy w dół. **Losowe** zachowanie generuje losowe wartość z zakresu od wartości minimalnej i maksymalnej wartości. |
| Wartość minimalna | Najniższy numer reprezentujący dopuszczalny zakres. |
| Wartość maksymalna | Największa liczba reprezentująca dopuszczalny zakres. |
| Jednostka | Jednostki miary dla czujnika, takich jak ° F lub mil na godzinę. |

### <a name="number-of-devices"></a>Liczba urządzeń

Symulacji urządzenia umożliwia obecnie symulować maksymalnie 20 000 urządzeń.

![Liczba urządzeń](media/iot-suite-device-simulation-explore/numberofdevices.png)

### <a name="telemetry-frequency"></a>Częstotliwość telemetrii

Częstotliwość telemetrii umożliwia określenie, jak często wysyłać dane do Centrum IoT symulowanego urządzenia. Jak często co 10 sekund lub jako rzadko może wysyłać dane co 99 godzin, 59 min i 59 sekund.

![Częstotliwość telemetrii](media/iot-suite-device-simulation-explore/frequency.png)

> [!NOTE]
> Jeśli używane są inne niż wstępnie przygotowany Centrum IoT Centrum IoT, należy rozważyć limitów wiadomości Centrum IoT docelowej. Na przykład jeśli masz 1000 urządzeń symulowane wysyłania danych telemetrycznych co 10 sekund do Centrum S1 można osiągnąć limit dane telemetryczne dla koncentratora w ponad godzinę.

### <a name="simulation-duration"></a>Czas trwania symulacji

Można wybrać do uruchomienia symulacji przez określony czas lub zostanie uruchomiony do momentu jawnego zatrzymania. Po wybraniu określony okres czasu, można wybrać żadnych czas trwania od 10 do 99 godziny, 59 minut i sekund 59.

![Czas trwania symulacji](media/iot-suite-device-simulation-explore/duration.png)

### <a name="start-and-stop-the-simulation"></a>Uruchamianie i zatrzymywanie symulacji

Gdy zostaną dodane wszystkie dane konfiguracyjne niezbędne do formularza, **Start symulacji** przycisk jest aktywny. Aby rozpocząć symulację, kliknij ten przycisk.

![Rozpocznij symulację](media/iot-suite-device-simulation-explore/start.png)

Jeśli zostanie określona przez określony czas, w symulacji, następnie jest automatycznie zatrzymywana po tym czasie. Użytkownik może zawsze zatrzymywać symulację wcześniej, klikając **zatrzymać symulacji.**

Jeśli wybrano opcję uruchamiania symulacji przez czas nieokreślony, a następnie uruchomieniu, dopóki nie klikniesz **zatrzymać symulacji**. Można zamknąć przeglądarkę i wróć do strony symulacji urządzenia, aby zatrzymać symulacji w dowolnym momencie.

![Zatrzymaj symulację](media/iot-suite-device-simulation-explore/stop.png)

> [!NOTE]
> Tylko jeden symulacji może działać jednocześnie. Musisz zatrzymać uruchomione symulacji przed rozpoczęciem nowej symulacji.
