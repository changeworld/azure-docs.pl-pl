---
title: Wprowadzenie do usługi Azure Internet of Things (IoT)
description: Wprowadzenie z wyjaśnieniem podstaw usługi Azure IoT i usług IoT wraz z przykładami ułatwiającymi zilustrowanie zastosowań IoT.
author: robinsh
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 10/11/2018
ms.author: robinsh
ms.openlocfilehash: e1cb588d68153a88d8b55b2696b376c4eb8704f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61232255"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Co to jest usługa Azure Internet of Things (IoT)?

Azure Internet of Things (IoT) to kolekcja zarządzanych przez firmę Microsoft usług w chmurze, które łączą, monitorują i kontrolują miliardy zasobów Internetu rzeczy. Mówiąc prościej, na rozwiązanie IoT składa się co najmniej jedno urządzenie IoT i co najmniej jedna usługa zaplecza w chmurze, które komunikują się ze sobą. 

W tym artykule omówiono podstawowe informacje dotyczące IoT, przeanalizowano przypadki zastosowań i krótko objaśniono osiem dostępnych usług. Zrozumienie tego, jakie opcje są dostępne, pozwoli Ci ustalić, którym z nich warto przyjrzeć się bliżej, aby zaprojektować własny scenariusz.

## <a name="introduction"></a>Wprowadzenie

Oto główne elementy rozwiązania IoT: urządzenia, usługi zaplecza i komunikacja między nimi. 

### <a name="iot-devices"></a>Urządzenia IoT

Urządzenie zazwyczaj składa się z płytki drukowanej z dołączonymi czujnikami łączącymi się z Internetem. Wiele urządzeń komunikuje się za pośrednictwem mikroukładu sieci Wi-Fi. Poniżej przedstawiono kilka przykładów urządzeń IoT:

* czujniki ciśnienia na zdalnej pompie olejowej,
* czujniki temperatury i wilgotności w klimatyzatorze,
* akcelerometry w windzie,
* czujniki obecności w pomieszczeniu.

Dwa urządzenia, które są często używane do tworzenia prototypów, to układ podstawowy MX Chip IoT Devkit od firmy Microsoft i urządzenia Raspberry PI. Układ MX Chip Devkit ma wbudowane czujniki temperatury, ciśnienia i wilgotności, a także żyroskop i akcelerometr, magnetometr oraz układ sieci Wi-Fi. Raspberry PI to urządzenie IoT, do którego można dołączyć wiele różnego rodzaju czujników, co pozwala wybrać dokładnie to, co jest potrzebne w danym scenariuszu. 

Aby uzyskać więcej informacji na temat dostępnych urządzeń IoT, zapoznaj się z największym branżowym [wykazem urządzeń z certyfikatem IoT](https://catalog.azureiotsolutions.com/alldevices).

[Zestawy SDK urządzeń IoT](../iot-hub/iot-hub-devguide-sdks.md) umożliwiają tworzenie aplikacji uruchamianych na urządzeniach w celu realizacji odpowiednich zadań. Przy użyciu zestawów SDK można wysyłać dane telemetryczne do centrum IoT, odbierać komunikaty oraz aktualizacje z usługi IoT Hub i tak dalej.

### <a name="communication"></a>Komunikacja

Komunikacja urządzenia z usługami zaplecza może przebiegać w obu kierunkach. Poniżej przedstawiono kilka przykładowych sposobów, w jakie urządzenie może komunikować się z rozwiązaniem zaplecza.

#### <a name="examples"></a>Przykłady 

* Urządzenie może co 5 minut wysyłać informacje o temperaturze z ciężarówki-chłodni do usługi IoT Hub. 

* Usługa zaplecza może zażądać od urządzenia częstszego wysyłania danych telemetrycznych, aby ułatwić zdiagnozowanie problemu. 

* Urządzenie może wysyłać alerty oparte na wartościach rejestrowanych przez jego czujniki. Na przykład w przypadku monitorowania reaktora okresowego w zakładzie chemicznym można wysyłać alert, gdy temperatura przekroczy określoną wartość.

* Urządzenie może przesyłać informacje do pulpitu nawigacyjnego, który jest wyświetlany osobom pełniącym role operatorów. Na przykład w pomieszczeniu sterowania w rafinerii operatorom mogą być wyświetlane wartości temperatury i ciśnienia dla każdego rurociągu, a także wolumin materiału przepływającego przez ten rurociąg. 

Te i inne zadania można zaimplementować przy użyciu [zestawów SDK urządzeń IoT](../iot-hub/iot-hub-devguide-sdks.md).

#### <a name="connection-considerations"></a>Zagadnienia dotyczące połączeń

Podłączanie urządzeń w sposób bezpieczny i niezawodny jest często największym wyzwaniem w rozwiązaniach IoT. Wynika to z tego, że urządzenia IoT charakteryzują się innymi cechami niż pozostałe rozwiązania klienckie, takie jak przeglądarki i aplikacje mobilne. W szczególności urządzenia IoT:

* są często systemami osadzonymi bez osoby pełniącej rolę operatora (w odróżnieniu od telefonu);

* mogą być wdrażane w lokalizacjach zdalnych, gdzie dostęp fizyczny jest bardzo kosztowny;

* mogą być dostępne tylko za pośrednictwem zaplecza rozwiązania. Nie istnieje inny sposób interakcji z urządzeniem;

* mogą mieć ograniczone zasoby w zakresie zasilania i przetwarzania;

* mogą korzystać z przerywanej, powolnej lub kosztownej łączności sieciowej;

* mogą wymagać używania zastrzeżonych, niestandardowych lub branżowych protokołów aplikacji;

### <a name="back-end-services"></a>Usługi zaplecza 

Oto niektóre inne funkcje, które mogą zapewniać usługi zaplecza.

* Odbieranie telemetrii z urządzeń we właściwej skali i określanie sposobu przetwarzania i przechowywania tych danych.

* Analizowanie danych telemetrycznych w celu udostępnienia szczegółowych informacji, w czasie rzeczywistym lub po fakcie.

* Wysyłanie poleceń z chmury do określonego urządzenia. 

* Aprowizowanie urządzeń oraz kontrolowanie tego, które mogą łączyć się z infrastrukturą.

* Śledzenie stanu urządzeń i monitorowanie ich działania.

Na przykład w scenariuszu konserwacji predykcyjnej zaplecze chmury przechowuje historyczne dane telemetryczne. Rozwiązanie używa tych danych do identyfikowania potencjalnych nietypowych zachowań określonych pomp zanim spowodują one rzeczywisty problem. Za pomocą analizy danych może ono ustalić, że rozwiązanie prewencyjne polega na wysłaniu z powrotem do urządzenia polecenia podjęcia akcji naprawczej. Ten proces generuje pętlę automatycznego sprzężenia zwrotnego między urządzeniem i chmurą, która znacznie zwiększa wydajność rozwiązania.

## <a name="an-iot-example"></a>Przykład IoT

Poniżej przedstawiono przykład tego, jak dzięki technologii IoT pewna firma zaoszczędziła miliony dolarów. 

Ta firma to ogromna farma, na której hoduje się bydło — setki tysięcy krów. Śledzenie tak wielu krów i trzymanie ręki na pulsie ich zdrowia to prawdziwe wyzwanie, które wymaga częstego jeżdżenia samochodami. Na każdej krowie firma umieściła czujniki, które wysyłają informacje, takie jak współrzędne GPS i temperatura, do usługi zaplecza w celu zapisania ich w bazie danych.

Następnie usługa analityczna skanuje dane przychodzące i analizuje je dla każdej krowy w celu ustalenia odpowiedzi na pytania podobne do następujących:

* Czy krowa ma podwyższoną temperaturę? Jak długo krowa ma podwyższoną temperaturę? Jeśli dłużej niż jeden dzień, usługa pobiera współrzędne GPS i wysyła zlecenie odszukania krowy w celu ewentualnego podania jej antybiotyków. 

* Czy krowa przebywa w tym samym miejscu dłużej niż jeden dzień? Jeśli tak, usługa pobiera współrzędne GPS i wysyła zlecenie odszukania krowy. Czy krowa spadła ze wzniesienia? Czy krowa jest ranna? czy krowa potrzebuje pomocy? 

Zaimplementowanie tego rozwiązania IoT umożliwiło firmie szybkie sprawdzanie kondycji krów i podejmowanie ich leczenia, a także skrócenie czasu spędzanego na jeżdżeniu w celu sprawdzania kondycji zwierząt, co pozwoliło na oszczędzenie dużej ilości pieniędzy. Aby poznać więcej rzeczywistych przykładów tego, jak firmy wykorzystują IoT, zobacz [Microsoft Technical Case Studies for IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured) (Techniczne analizy przypadków firmy Microsoft dotyczące IoT). 

## <a name="iot-services"></a>Usługi IoT

Na platformie Azure istnieje kilka usług powiązanych z IoT i ustalenie, której najlepiej użyć, może być trudne. Niektóre z nich, takie jak IoT Central i akceleratory rozwiązań IoT, zapewniają szablony ułatwiające tworzenie własnych rozwiązań i szybkie rozpoczynanie pracy. Możesz również opracować całkowicie własne rozwiązanie za pomocą innych dostępnych usług — wszystko zależy od tego, ile pomocy chcesz uzyskać oraz ile chcesz mieć kontroli. Oto lista dostępnych usług oraz możliwości ich zastosowań.

1. [**IoT Central**](../iot-central/overview-iot-central.md): jest to rozwiązanie SaaS, które ułatwia łączenie i monitorowanie urządzeń IoT oraz zarządzanie nimi. Aby rozpocząć, wybierz szablon dla danego typu urządzenia, a następnie utwórz i przetestuj podstawową aplikację usługi IoT Central, której będą używać operatorzy urządzeń. Aplikacja usługi IoT Central umożliwi Ci też monitorowanie urządzeń i aprowizację nowych urządzeń. Ta usługa jest przeznaczona dla prostych rozwiązań, które nie wymagają znaczącego dostosowania usług. 

2. [**Akceleratory rozwiązań IoT**](/azure/iot-suite): jest to zbiór rozwiązań PaaS, za pomocą których można przyspieszyć opracowywanie rozwiązań IoT. Pracę rozpoczyna się od udostępnionego rozwiązania IoT, a następnie w pełni dostosowuje się to rozwiązanie do własnych potrzeb. Aby dostosować zaplecze, wymagane są umiejętności związane z językiem Java lub platformą .NET, natomiast w celu dostosowania wizualizacji — z językiem JavaScript. 

3. [**IoT Hub**](/azure/iot-hub/): ta usługa pozwala na łączenie się urządzeń z centrum IoT oraz na monitorowanie i kontrolowanie miliardów urządzeń IoT. Jest ona szczególnie przydatna, jeśli jest potrzebna komunikacja dwukierunkowa między urządzeniami IoT a zapleczem. Jest to usługa bazowa dla usługi IoT Central i akceleratorów rozwiązań IoT. 

4. [**Usługa IoT Hub Device Provisioning**](/azure/iot-dps/): jest to usługa pomocnika dla usługi IoT Hub, za pomocą której można bezpiecznie aprowizować urządzenia w centrum IoT. Usługa ta pozwala łatwo błyskawicznie aprowizować miliony urządzeń, zamiast aprowizować je jedno po drugim. 

5. [**IoT Edge**](/azure/iot-edge/): ta usługa jest oparta na usłudze IoT Hub. Za jej pomocą można analizować dane na urządzeniach IoT zamiast w chmurze. Dzięki przeniesieniu części obciążenia na brzeg można wysyłać mniej komunikatów do chmury. 

6. [**Azure Digital Twins**](../digital-twins/index.yml): ta usługa umożliwia tworzenie kompleksowych modeli środowiska fizycznego. Umożliwia ona modelowanie relacji i interakcji między osobami, obszarami i urządzeniami. Na przykład można określać potrzeby konserwacyjne dla fabryki, analizować wymagania energetyczne w czasie rzeczywistym dla sieci elektrycznej lub optymalizować użycie dostępnej przestrzeni w biurze.

7. [**Time Series Insights**](/azure/time-series-insights): ta usługa pozwala na przechowywanie i wizualizowanie dużych ilości danych szeregów czasowych generowanych przez urządzenia IoT oraz wykonywanie zapytań na tych danych. Można jej używać razem z usługą IoT Hub. 

8. [**Azure Maps**](/azure/azure-maps): ta usługa udostępnia informacje geograficzne aplikacjom internetowym i mobilnym. Dostępny jest pełny zestaw interfejsów API REST, a także internetowa kontrolka JavaScript, za pomocą których można tworzyć elastyczne aplikacje działające na komputerach oraz aplikacje mobilne dla urządzeń zarówno firmy Apple, jak i z systemem Windows.

## <a name="next-steps"></a>Kolejne kroki

Rzeczywiste przypadki zastosowań biznesowych i opisy używanej architektury można znaleźć na stronie [Microsoft Azure IoT Technical Case Studies](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured) (Techniczne analizy przypadków usługi Microsoft Azure IoT).

W [katalogu projektów IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) można znaleźć kilka przykładowych projektów do wypróbowania z układem IoT DevKit. 

Aby zapoznać się z bardziej wyczerpującym opisem różnych usług i sposobów ich używania, zobacz [Usługi i technologie Azure IoT](iot-services-and-technologies.md).

Szczegółowe omówienie architektury IoT można znaleźć w temacie [Architektura referencyjna IoT platformy Microsoft Azure](https://aka.ms/iotrefarchitecture).
