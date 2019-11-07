---
title: Tworzenie rozwiązań detalicznych za pomocą usługi Azure IoT Central | Microsoft Docs
description: Informacje na temat tworzenia połączonej logistyki, cyfrowego centrum dystrybucji, monitorowania, wyewidencjonowywania i zarządzania magazynem w sklepie, rozwiązań detalicznych za pomocą usługi Azure IoT Central przy użyciu szablonów aplikacji.
author: KishorIoT
ms.author: nandab
ms.date: 10/22/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 7da5d7b80dfd07b742af5ff6225f26207747e58c
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615335"
---
# <a name="building-retail-solutions-with-azure-iot-central"></a>Tworzenie rozwiązań dla handlu detalicznego za pomocą usługi Azure IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Azure IoT Central to platforma aplikacji Internet rzeczy (IoT) dla konstruktorów rozwiązań, która upraszcza tworzenie aplikacji & Zarządzanie skalowalnymi aplikacjami. W tym artykule wyróżnimy kilka szablonów aplikacji dotyczących handlu detalicznego w ramach IoT Central. Konstruktory rozwiązań mogą korzystać z opublikowanych szablonów do kompilowania rozwiązań IoT w celu zoptymalizowania łańcucha dostaw, ulepszania środowiska w sklepie dla klientów i śledzenia bardziej wydajnego spisu.

> [!div class="mx-imgBorder"]
> ![Omówienie usługi Azure IoT Handle](./media/overview-iot-central-retail/retail-app-templates.png)


## <a name="what-is-connected-logistics-solution"></a>Co to jest połączone rozwiązanie logistyczne?
Globalne wydatki na logistykę powinny dotrzeć do TRN $2020, największego PKB według branż. Transport rzeczy jest większością (70%) całkowitego kosztu logistyki. Dostawcy usług wysyłkowych podlegają intensywnej presji i ograniczeniom konkurencji. dostawcy 3PL mają ciągle zmniejszające się ramy czasowe i zwiększają koszty związane z wynagrodzeniem. Logistyki są dalej ograniczone przez ryzyko związane z geopolityczną i ekstremalnymi zdarzeniami klimatycznymi oraz przestępstwem. 

Korzystając z czujników IoT, możemy zbierać & monitorowanie warunków, takich jak temperatury, wilgotność, pochylenie, wstrząsy, światło i miejsce wysyłki za pośrednictwem GPS w ramach transformacji wielomodalnej, czyli powietrza, wody, ziemi. Dane zbierane z czujników, urządzeń, Pogoda i zdarzeń & mogą być zintegrowane z opartymi na chmurze systemami analizy biznesowej. Połączone rozwiązania logistyczne to,
* Translimitowanie wysyłek dzięki śledzeniu & śledzenia w czasie rzeczywistym 
* Integralność wysyłki dzięki monitorowaniu warunków otoczenia w czasie rzeczywistym & zimny łańcuch
* Bezpieczeństwo przed kradzieżą, utratą lub uszkodzeniem wysyłki
* Ogrodzenie geograficzne, Optymalizacja tras, zarządzanie flotą. Analiza pojazdu
* Prognozowanie & przewidywalności przy wyjściu & przyjęcia 

### <a name="out-of-box-experience"></a>Środowisko pracy poza Box
Partnerzy mogą wykorzystać szablon do tworzenia kompleksowych, połączonych rozwiązań logistycznych & z korzyściami. Ten opublikowany szablon koncentruje się na łączności urządzeń, konfiguracji & zarządzania urządzeniami w programie IoT Central. 

> [!div class="mx-imgBorder"]
> ![połączonego pulpitu nawigacyjnego logistyki](./media/overview-iot-central-retail/connected-logistics-dashboard1.png)

> [!div class="mx-imgBorder"]
> ![połączonego pulpitu nawigacyjnego logistyki](./media/overview-iot-central-retail/connected-logistics-dashboard2.png)

Należy pamiętać, że powyższy pulpit nawigacyjny jest przykładowym doświadczeniem i można ją całkowicie dostosować do żądanego przypadku użycia.

Wprowadzenie do kompleksowego [samouczka](./tutorial-iot-central-connected-logistics-pnp.md) , który przeprowadzi Cię przez proces tworzenia rozwiązania przy użyciu jednego z połączonych szablonów rozwiązań logistycznych.



## <a name="what-is-digital-distribution-center-solution"></a>Co to jest rozwiązanie centrum dystrybucji cyfrowej?
Ponieważ coraz więcej producentów i detalistów opracowują światowe obecność, ich łańcuchy dostaw zostały rozgałęzienia, aby stać się bardziej skomplikowane niż kiedykolwiek wcześniej. Centra dystrybucji stają się podstawowym wyzwaniem. Centrum dystrybucji/magazyny nie są w trakcie rozkładu ciśnienia z handlu elektronicznego. Konsumenci oczekują teraz szerokiego wyboru produktów, które mają być dostępne, a dla tych towarów można dotrzeć w ciągu jednego do dwóch dni zakupu. Centra dystrybucji muszą dostosowywać się do tych trendów, jednocześnie utrzymując istniejące nieefektywność. 

Obecnie overreliance na robociznę ręczną oznacza Wybieranie i konta pakowania dla 55-65% kosztów centrum dystrybucji. Chociaż nie jest to wystarczające, aby ręczna robocizna spowalniał centrum dystrybucji, gwałtownie zmieniające się potrzeby związane z zatrudnieniem (w dniach świątecznych przez 10X) sprawiają, że jest to jeszcze trudniejsze do zaspokajania woluminów wysyłkowych. Ta sezonowa fluktuacja skutkuje dużym obrotem, a prawdopodobieństwo wystąpienia błędów i konieczności kosztownego zwiększenia nakładu pracy.
Rozwiązania oparte na kamerach z obsługą IoT mogą dostarczać zalety transformacji, włączając w to cyfrową pętlę opinii. Ten strumień danych z centrum dystrybucji prowadzi do szczegółowych informacji umożliwiających podjęcie odpowiednich działań, które z kolei powodują lepsze dane.

Korzyści to, 
* Aparaty fotograficzne monitorują towary w miarę ich przybycia i przechodzą przez system przenośnika
* Zidentyfikuj wadliwe towary i wyślij je do naprawy
* Efektywne śledzenie zamówień
* Obniżenie kosztów, Zwiększona produktywność & Maksymalizuj użycie

### <a name="out-of-box-experience"></a>Środowisko pracy poza Box
Partnerzy mogą korzystać z tego szablonu aplikacji w celu utworzenia cyfrowego centrum dystrybucji w celu uzyskania szczegółowych informacji & wyższych korzyści. Opublikowany szablon jest ukierunkowany na konfigurację łączności urządzeń & zarządzania kamerą i urządzeniami brzegowymi w programie IoT Central. 

> [!div class="mx-imgBorder"]
> ![pulpitu nawigacyjnego Centrum dystrybucji cyfrowej](./media/overview-iot-central-retail/digital-distribution-center-dashboard.png)

Należy pamiętać, że powyższy pulpit nawigacyjny jest przykładowym doświadczeniem i można ją całkowicie dostosować do żądanego przypadku użycia.

Wprowadzenie do kompleksowego [samouczka](./tutorial-iot-central-digital-distribution-center-pnp.md) , który przeprowadzi Cię przez proces tworzenia rozwiązania wykorzystującego jeden z szablonów centrum dystrybucji cyfrowej.



## <a name="what-is-in-store-analytics-condition-monitoring"></a>Co to jest monitorowanie warunku analizy w sklepie?
W dzisiejszej konkurencyjnej wersji detalicznej są szukane nowe sposoby oferowania klientom unikatowych lub specjalnych w celu kierowania ruchu sieciowego przez ich magazyny fizyczne. Wiele sprzedawców detalicznych potwierdza znaczenie warunków środowiska w ich sklepie jako narzędzie do odróżnienia od konkurencji. Detaliści chcą upewnić się, że utrzymują one wrażenie, że w swoich sklepach w swoich magazynach są w pełni przyjemne, aby zapewnić klientom komfort pracy.  

Szablon aplikacji monitorowanie warunków analizy w sklepie w ramach IoT Central zawiera konstruktora rozwiązań z kanwą, której można użyć do utworzenia kompleksowego rozwiązania. Szablon aplikacji umożliwia im cyfrowe łączenie i monitorowanie środowiska sklepu detalicznego przy użyciu różnych urządzeń czujników. Te urządzenia czujnika przechwytują znaczące sygnały, które mogą być konwertowane na usługi Business Insights, dzięki czemu sprzedawcy detaliczni mają zmniejszyć koszty operacyjne i tworzyć środowiska, z którymi polubili się klienci.

Szablon aplikacji umożliwia:

*  Bezproblemowo łącz różne czujniki IoT z wystąpieniem aplikacji IoT Central.
*  Monitoruj kondycję sieci czujnika i zarządzaj nią, a także urządzeniami bramy w środowisku.
*  Utwórz reguły niestandardowe wokół warunków środowiska w magazynie, aby wyzwolić odpowiednie alerty.
*  Przekształć warunki środowiska w sklepie w szczegółowe informacje, które mogą być używane przez zespół sklepów detalicznych.
* Wyeksportuj zagregowany wgląd w istniejące lub nowe aplikacje biznesowe, które umożliwiają członkom działu sprzedaży detalicznej.

### <a name="out-of-box-experience"></a>Środowisko pracy poza Box
Szablon aplikacji zawiera zestaw szablonów urządzeń i środowisko obsługi operatora. Używa zestawu symulowanych urządzeń do wypełnienia elementów pulpitu nawigacyjnego. Po wdrożeniu aplikacji IoT Central przy użyciu szablonu aplikacji [monitorowanie warunków analizy w sklepie](https://aka.ms/conditiontemplate) zostanie umieszczony domyślny pulpit nawigacyjny aplikacji, jak pokazano poniżej. 

> [!div class="mx-imgBorder"]
> ![monitorowanie warunku analizy w sklepie](./media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png)

Należy pamiętać, że powyższy pulpit nawigacyjny jest przykładowym doświadczeniem i można ją całkowicie dostosować do żądanego przypadku użycia. 

Wprowadzenie do kompleksowego [samouczka](./tutorial-in-store-analytics-create-app-pnp.md) , który przeprowadzi Cię przez proces tworzenia rozwiązania przy użyciu jednego z szablonów monitorowania warunków analizy w sklepie.



## <a name="what-is-in-store-analytics-checkout"></a>Co to jest wyewidencjonowanie analizy w sklepie?
W coraz większym stopniu konkurencyjnym nowoczesne detalisty stale zmniejszają nacisk na zapewnienie środowiska w sklepie, które przekracza oczekiwania klientów i utrzymuje ich zwrot. Chociaż kilku detalistów rozpoczął wdrażanie technologii w celu spełnienia tego wymagania, obszar, który jest bardzo niezauważalny, to środowisko wyewidencjonowywania.

Szablon aplikacji do wyewidencjonowania analitycznego w sklepie w ramach IoT Central umożliwia konstruktorom rozwiązań tworzenie środowisk, które umożliwiają pracownikom detalicznym uzyskiwanie szczegółowych informacji dotyczących strefy wyewidencjonowywania w sklepie. Korzysta ona z zestawu symulowanych urządzeń w celu określenia stanu zajętości dla każdego ze śladów wyewidencjonowania w sklepie detalicznym. Czujniki umożliwiają przechwycenie liczby osób oraz średni czas oczekiwania dla każdego z nich.

Szablon ułatwia konstruktorowi rozwiązań przyspieszenie planów przechodzenia do rynku przez udostępnienie podstawowego rozwiązania IoT, które umożliwia im: 

* Bezproblemowo łącz różne czujniki IoT z wystąpieniem aplikacji IoT Central.
* Monitoruj kondycję sieci czujnika i zarządzaj nią, a także urządzeniami bramy w środowisku.
* Utwórz reguły niestandardowe wokół stanu wyewidencjonowania w magazynie, aby wyzwolić odpowiednie alerty.
* Przekształć warunki wyewidencjonowania w sklepie w szczegółowe informacje, które mogą być używane przez zespół sklepów detalicznych.
* Wyeksportuj zagregowany wgląd w istniejące lub nowe aplikacje biznesowe, które umożliwiają członkom działu sprzedaży detalicznej.

### <a name="out-of-box-experience"></a>Środowisko pracy poza Box
Szablon aplikacji zawiera zestaw szablonów urządzeń i środowisko obsługi operatora. Używa zestawu symulowanych urządzeń do wypełnienia elementów pulpitu nawigacyjnego. Po wdrożeniu aplikacji IoT Central za pomocą szablonu aplikacji do [wyewidencjonowania analiz w sklepie](https://aka.ms/checkouttemplate) zostanie umieszczony domyślny pulpit nawigacyjny aplikacji, jak pokazano poniżej. 

> [!div class="mx-imgBorder"]
> ![wyewidencjonowania analiz w sklepie](./media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png)

Należy pamiętać, że powyższy pulpit nawigacyjny jest przykładowym doświadczeniem i można ją całkowicie dostosować do żądanego przypadku użycia. 


Zacznij korzystać z [kompleksowego samouczka](./tutorial-in-store-analytics-create-app-pnp.md) , który przeprowadzi Cię przez proces tworzenia rozwiązania wykorzystującego jeden z szablonów wyewidencjonowania w sklepie.


## <a name="what-is-smart-inventory-management-solution"></a>Co to jest rozwiązanie do zarządzania magazynem inteligentnym?
"Spis" to zapasy towarów przechowywanych przez sprzedawcę detaliczną. Każdy sprzedawca detaliczny potrzebuje spisu, aby zająć się dostawami i czasem realizacji logistyki. Spis jest raczej najbardziej cennym zasobem, do którego każdy detalista potrzebuje do wymiany. W dzisiejszej omnichannel świecie zarządzanie spisem jest wymaganiem krytycznym, które zapewnia, że odpowiedni produkt znajduje się w odpowiednim miejscu w odpowiednim czasie. Przechowywanie zbyt dużej lub zbyt małej ilości danych może spowodować zakłócenie działalności detalicznej. W każdym roku sprzedawcy detaliczni tracą 8-10% przychodu ze względu na brak możliwości zarządzania zapasami.

Dane IoT włączane przez identyfikację częstotliwości radiowych (RFID), sygnały nawigacyjne & kamer są okazją do zakwestionowania tego ogromnego wyzwania w skali. Łączność i analiza w czasie rzeczywistym związane z sygnałami IoT stają się zmieniaczem gier dla woes zapasów detalicznych.  Dane zbierane z czujników, urządzeń, Pogoda i zdarzeń & mogą być zintegrowane z opartymi na chmurze systemami analizy biznesowej.  
Zalety zarządzania magazynem inteligentnym to, 
* Chroni organizację przed zapasami i zapewnia odpowiedni poziom usług klienta. 
* Szczegółowa analiza & wgląd w dokładność spisu w czasie niemal rzeczywistym
* Wybór odpowiedniej ilości spisu, który wystarcza do realizacji zamówień klienta

### <a name="out-of-box-experience"></a>Środowisko pracy poza Box
Partnerzy mogą korzystać z szablonu, aby opracowywać kompleksowe rozwiązania do zarządzania magazynem inteligentnego & z korzyściami. Ten opublikowany szablon koncentruje się na łączności urządzeń, konfiguracji & zarządzania RFID & czytników niskich energii Bluetooth w IoT Central. 

> [!div class="mx-imgBorder"]
> ![pulpitu nawigacyjnego zarządzania magazynem inteligentnym](./media/overview-iot-central-retail/smart-inventory-management-dashboard.png)

Należy pamiętać, że powyższy pulpit nawigacyjny jest przykładowym doświadczeniem i można ją całkowicie dostosować do żądanego przypadku użycia. 

Wprowadzenie do kompleksowego [samouczka](./tutorial-iot-central-smart-inventory-management-pnp.md) , który przeprowadzi Cię przez proces tworzenia rozwiązania wykorzystującego jeden z szablonów zarządzania magazynem inteligentnym.


## <a name="next-steps"></a>Następne kroki
Aby rozpocząć tworzenie rozwiązania do sprzedaży detalicznej:
* Wprowadzenie do [kompleksowego samouczka](./tutorial-in-store-analytics-create-app-pnp.md) , który przeprowadzi Cię przez proces tworzenia rozwiązania wykorzystującego jeden z szablonów aplikacji do analizy w sklepie.
* Dowiedz się, jak wdrożyć [szablon rozwiązanych rozwiązań logistycznych](./tutorial-iot-central-connected-logistics-pnp.md)
* Dowiedz się, jak wdrożyć [szablon centrum dystrybucji cyfrowej](./tutorial-iot-central-digital-distribution-center-pnp.md)
* Dowiedz się, jak wdrożyć [szablon zarządzania magazynem inteligentnym](./tutorial-iot-central-smart-inventory-management-pnp.md)
* Dowiedz się więcej o IoT Central zapoznaj się z [omówieniem IoT Central](../core/overview-iot-central-pnp.md)
