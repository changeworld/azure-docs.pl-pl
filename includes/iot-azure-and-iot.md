---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/25/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 1dced2eecafa45b0d172b807dc492942d461de71
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919033"
---
# <a name="azure-and-the-internet-of-things"></a>Platforma Azure i Internet rzeczy

Witamy na platformie Microsoft Azure oraz w Internecie rzeczy (IoT). W tym artykule opisano typowe cechy rozwiązania IoT w chmurze. Rozwiązania IoT wymagają bezpiecznej, dwukierunkowej komunikacji między urządzeniami, których liczba może być liczona nawet w milionach, oraz zaplecza rozwiązania. Rozwiązanie może na przykład korzystać z automatycznej analizy predykcyjnej w celu uzyskiwania wglądu w dane ze strumienia zdarzeń przesyłanego z urządzenia do chmury.

## <a name="iot-solution-architecture"></a>Architektura rozwiązania IoT

Poniższy schemat przedstawia kluczowe elementy typowej architektury rozwiązania IoT. Diagram jest niezależny od szczegółów konkretnej implementacji, takich jak użyte usługi platformy Azure i systemy operacyjne urządzeń. W ramach tej architektury urządzenia IoT zbierają dane, które wysyłają do bramy chmury. Brama chmury udostępnia dane do przetwarzania przez inne usługi zaplecza. Te usługi zaplecza mogą dostarczyć dane do:

* Innych aplikacji biznesowych.
* Osób pełniących rolę operatorów za pośrednictwem pulpitu nawigacyjnego lub innego urządzenia do prezentacji.

![Architektura rozwiązania IoT][img-solution-architecture]

> [!NOTE]
> Szczegółowe omówienie architektury IoT można znaleźć w temacie [Architektura referencyjna IoT platformy Microsoft Azure][lnk-refarch].

### <a name="device-connectivity"></a>Łączność urządzeń

W ramach architektury rozwiązania IoT urządzenia zwykle wysyłają dane telemetryczne do chmury w celu przechowywania i przetwarzania. Na przykład w scenariuszu konserwacji predykcyjnej zaplecze rozwiązania może używać strumienia danych z czujnika do określenia, kiedy dana pompa wymaga konserwacji. Urządzenia mogą również odbierać komunikaty przesyłane z chmury do urządzenia oraz odpowiadać na nie, odczytując komunikaty z punktu końcowego w chmurze. W tym samym przykładzie zaplecze rozwiązania może wysyłać komunikaty do innych pomp w przepompowni w celu zainicjowania zmiany trasy przepływów przed samym rozpoczęciem zaplanowanej konserwacji. Procedura ta zapewnia możliwość rozpoczęcia pracy od razu po przybyciu inżyniera serwisu.

Podłączanie urządzeń w sposób bezpieczny i niezawodny jest często największym wyzwaniem w rozwiązaniach IoT. Wynika to z tego, że urządzenia IoT charakteryzują się innymi cechami niż pozostałe rozwiązania klienckie, takie jak przeglądarki i aplikacje mobilne. W szczególności urządzenia IoT:

* są często systemami osadzonymi bez osoby pełniącej rolę operatora (w odróżnieniu od telefonu);
* mogą być wdrażane w lokalizacjach zdalnych, gdzie dostęp fizyczny jest bardzo kosztowny;
* mogą być dostępne tylko za pośrednictwem zaplecza rozwiązania. Nie istnieje inny sposób interakcji z urządzeniem;
* mogą mieć ograniczone zasoby w zakresie zasilania i przetwarzania;
* mogą korzystać z przerywanej, powolnej lub kosztownej łączności sieciowej;
* mogą wymagać używania zastrzeżonych, niestandardowych lub branżowych protokołów aplikacji;
* mogą być tworzone przy użyciu szerokiej gamy popularnych platform sprzętowych i programowych.

Oprócz powyższych ograniczeń każde rozwiązanie IoT musi być również skalowalne, bezpieczne i niezawodne.

W zależności od protokołu komunikacyjnego i dostępności sieci urządzenie może się komunikować z chmurą bezpośrednio albo przy użyciu bramy pośredniej. Architektury IoT często mają kombinację tych dwóch wzorców komunikacji.

### <a name="data-processing-and-analytics"></a>Przetwarzanie danych i analiza

W nowoczesnych rozwiązaniach IoT przetwarzanie danych może nastąpić w chmurze lub po stronie urządzenia. Przetwarzanie po stronie urządzenia jest określane jako *obliczenia brzegowe*. Wybór miejsca przetwarzania danych zależy od takich czynników, jak:

* Ograniczenia sieci. Jeśli przepustowość między urządzeniami i chmurą jest ograniczona, stanowi to zachętę do zwiększenia obliczeń brzegowych.
* Czas odpowiedzi. Jeśli jest wymagane działanie urządzenia w czasie bliskim rzeczywistemu, lepszym rozwiązaniem może być przetworzenie odpowiedzi na samym urządzeniu. Na przykład ramię robota, które musi zostać zatrzymane w nagłym wypadku.
* Środowisko prawne. Niektórych danych nie można wysłać do chmury.

Ogólnie rzecz biorąc przetwarzanie danych zarówno na brzegu, jak i w chmurze to kombinacja następujących możliwości:

* odbierania telemetrii z urządzeń we właściwej skali i określania sposobu przetwarzania i przechowywania tych danych;
* analizowania danych telemetrycznych w celu udostępnienia szczegółowych informacji, czy są one określane w czasie rzeczywistym, czy też po fakcie;
* wysyłania poleceń z chmury lub urządzenia bramy do określonego urządzenia.

Ponadto zaplecze chmury IoT powinno zapewniać:

* Możliwości rejestracji urządzenia, które umożliwiają:
    * Aprowizację urządzeń.
    * Kontrolowanie tego, które urządzenia są uprawnione do łączenia się z Twoją infrastrukturą.
* Zarządzanie urządzeniami w celu śledzenia stanu Twoich urządzeń i monitorowania ich działania.

Na przykład w scenariuszu konserwacji predykcyjnej zaplecze chmury przechowuje historyczne dane telemetryczne. Rozwiązanie używa tych danych do identyfikowania potencjalnych nietypowych zachowań określonych pomp zanim spowodują one rzeczywisty problem. Za pomocą analizy danych może ono ustalić, że rozwiązanie prewencyjne polega na wysłaniu z powrotem do urządzenia polecenia podjęcia akcji naprawczej. Ten proces generuje pętlę automatycznego sprzężenia zwrotnego między urządzeniem i chmurą, która znacznie zwiększa wydajność rozwiązania.

### <a name="presentation-and-business-connectivity"></a>Prezentacja i łączność biznesowa

Warstwa prezentacji i łączności biznesowej umożliwia użytkownikom końcowym interakcję z rozwiązaniem IoT i urządzeniami. Pozwala użytkownikom wyświetlać i analizować dane zbierane z urządzeń. Widoki te mogą mieć formę pulpitów nawigacyjnych i raportów analizy biznesowej, które zawierają zarówno dane historyczne, jak i dane uzyskiwane niemal w czasie rzeczywistym. Na przykład operator może sprawdzić stan określonej przepompowni i wyświetlić wszystkie alerty wygenerowane przez system. Ta warstwa umożliwia także integrację zaplecza rozwiązania IoT z istniejącymi aplikacjami branżowymi w celu ich powiązania w ramach procesów biznesowych i przepływów pracy przedsiębiorstwa. Na przykład akcelerator rozwiązania do konserwacji predykcyjnej można zintegrować z systemem planowania w celu zarezerwowania wizyty serwisanta w przepompowni, gdy zidentyfikuje ono pompę wymagającą konserwacji.

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-iot-hub]: ../articles/iot-hub/about-iot-hub.md
[lnk-iot-suite]: ../articles/iot-accelerators/about-iot-accelerators.md
[lnk-machinelearning]: https://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT solution accelerators]: https://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
