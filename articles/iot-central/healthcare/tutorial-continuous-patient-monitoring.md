---
title: Tworzenie aplikacji do ciągłego monitorowania pacjentów za pomocą usługi Azure IoT Central | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację do ciągłego monitorowania pacjentów przy użyciu szablonów aplikacji Usługi Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 35ac39109bfcb4dc63b738c947d2ad8caf8ac0a6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77021291"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Samouczek: Wdrażanie i instruktażowy szablon aplikacji do ciągłego monitorowania pacjentów



W tym samouczku pokazano, jako konstruktora rozwiązań, jak rozpocząć wdrażanie szablonu aplikacji ciągłego monitorowania pacjentów W centrum IoT. Dowiesz się, jak wdrożyć szablon, co jest dołączone po wyjęciu z pudełka i co możesz zrobić dalej.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie szablonu aplikacji
> * Przechodzenie przez szablon aplikacji

## <a name="create-an-application-template"></a>Tworzenie szablonu aplikacji

Przejdź do [witryny menedżera aplikacji Usługi Azure IoT Central](https://apps.azureiotcentral.com/). Wybierz **pozycję Zbuduj** z lewego paska nawigacyjnego, a następnie kliknij kartę Opieka **zdrowotna.** 

>[!div class="mx-imgBorder"] 
>![Menedżer aplikacji Opieka zdrowotna](media/app-manager-health.png)

Kliknij przycisk **Utwórz aplikację,** aby rozpocząć tworzenie aplikacji, a następnie zaloguj się za pomocą konta osobistego, służbowego lub szkolnego firmy Microsoft. Spowoduje to wyświetlenie **strony Nowa aplikacja.**

![Tworzenie aplikacji Dla zdrowia](media/app-manager-health-create.png)

![Tworzenie informacji rozliczeniowych aplikacji Dla służby zdrowia](media/app-manager-health-create-billinginfo.png)

Aby utworzyć aplikację:

1. Usługa Azure IoT Central automatycznie sugeruje nazwę aplikacji na podstawie wybranego szablonu. Możesz zaakceptować tę nazwę lub wprowadzić własną przyjazną nazwę aplikacji, taką jak **ciągłe monitorowanie pacjenta.** Usługa Azure IoT Central generuje również unikatowy prefiks adresu URL na podstawie nazwy aplikacji. Możesz zmienić ten prefiks adresu URL na coś bardziej pamiętnego, jeśli chcesz.

2. Można wybrać, czy chcesz utworzyć aplikację przy użyciu *bezpłatnego* planu cenowego, czy jednego *ze standardowych* planów cenowych. Aplikacje utworzone za pomocą bezpłatnego planu są bezpłatne przez siedem dni przed ich wygaśnięciem i pozwalają na maksymalnie pięć bezpłatnych urządzeń. Aplikację z bezpłatnego planu można przenieść do standardowego planu cenowego w dowolnym momencie przed jej wygaśnięciem. Jeśli wybierzesz bezpłatny plan, musisz wprowadzić informacje kontaktowe i zdecydować, czy mają być odbierane informacje i porady od firmy Microsoft. Aplikacje utworzone przy użyciu standardowego planu obsługują maksymalnie dwa bezpłatne urządzenia i wymagają wprowadzenia informacji o subskrypcji platformy Azure w celu rozliczenia.

3. Wybierz **pozycję Utwórz** u dołu strony, aby wdrożyć aplikację.

## <a name="walk-through-the-application-template"></a>Przechodzenie przez szablon aplikacji

### <a name="dashboards"></a>Pulpity nawigacyjne

Po wdrożeniu szablonu aplikacji najpierw wylądujesz na **panelu monitorowania pacjenta Lamna.** Lamna Healthcare to fikcyjny system szpitalny, który zawiera dwa szpitale: Woodgrove Hospital i Burkville Hospital. Na tym pulpicie nawigacyjnym operatora dla szpitala Woodgrove zobaczysz informacje i dane telemetryczne dotyczące urządzeń w tym szablonie wraz z zestawem poleceń, zadań i akcji, które można podjąć. Z pulpitu nawigacyjnego można:

* Zobacz dane telemetryczne urządzenia i właściwości, takie jak **poziom naładowania baterii** urządzenia lub jego stan **łączności.**

* Zobacz **plan piętra** i lokalizację urządzenia Smart Vitals Patch.

* **Reprowizuj** smart vitals patch dla nowego pacjenta.

* Zobacz przykład **pulpitu nawigacyjnego dostawcy,** który zespół opieki szpitalnej może zobaczyć, aby śledzić swoich pacjentów.

* Zmień **stan pacjenta** urządzenia, aby wskazać, czy urządzenie jest używane w scenariuszu szpitalnym czy zdalnym.

>[!div class="mx-imgBorder"] 
>![Lamna w szpitalu](media/lamna-in-patient.png)

Możesz również kliknąć przejdź **do zdalnego pulpitu nawigacyjnego pacjenta,** aby zobaczyć drugi pulpit nawigacyjny operatora używany w szpitalu Burkville. Ten pulpit nawigacyjny zawiera podobny zestaw akcji, danych telemetrycznych i informacji. Ponadto można zobaczyć wiele urządzeń używanych i mieć możliwość **aktualizacji oprogramowania układowego** na każdym.

>[!div class="mx-imgBorder"] 
>![Lamna zdalna](media/lamna-remote.png)

Na obu pulpitach nawigacyjnych zawsze można połączyć się z powrotem do tej dokumentacji.

### <a name="device-templates"></a>Szablony urządzeń

Po kliknięciu karty **Szablony urządzeń** zobaczysz, że istnieją dwa różne typy urządzeń, które są częścią szablonu:

* **Smart Vitals Patch**: To urządzenie reprezentuje poprawkę, która mierzy różne rodzaje znaków życiowych. Może być stosowany do monitorowania pacjentów w szpitalu i poza nim. Jeśli klikniesz szablon, zobaczysz, że oprócz wysyłania danych urządzenia, takich jak poziom naładowania baterii i temperatura urządzenia, łatka wysyła również dane dotyczące zdrowia pacjenta, takie jak szybkość oddechu i ciśnienie krwi.

* **Smart Knee Brace**: To urządzenie reprezentuje klamrę kolanową, którą pacjenci mogą używać podczas odzyskiwania po operacji wymiany stawu kolanowego. Jeśli klikniesz ten szablon, zobaczysz możliwości, takie jak zakres ruchu i przyspieszenia, oprócz danych urządzenia.

>[!div class="mx-imgBorder"] 
>![Szablon urządzenia Smart Vitals Patch](media/smart-vitals-device-template.png)

Jeśli klikniesz kartę **Grupy urządzeń,** zobaczysz również, że te szablony urządzeń automatycznie mają utworzone dla nich grupy urządzeń.

### <a name="rules"></a>Reguły

Podczas przeskakiwania do karty reguł w szablonie aplikacji zostaną wyświetlenia trzech reguł:

* **Wysoka temperatura stężenia nawiasu**klamrowego: Ta reguła jest wyzwalana, gdy&deg;temperatura urządzenia inteligentnej klamry kolanowej jest większa niż 95 F w ciągu 5 minut okna. Można użyć tej reguły, aby ostrzec pacjenta i zespół opieki i schłodzić urządzenie zdalnie.

* **Wykryto upadek:** Ta reguła jest wyzwalana w przypadku wykrycia upadku pacjenta. Za pomocą tej reguły można skonfigurować akcję do wdrażania zespołu operacyjnego, aby pomóc pacjentowi, który upadł.

* **Niski poziom naładowania baterii:** Ta zasada jest wyzwalana, gdy poziom naładowania baterii w urządzeniu spada poniżej 10%. Możesz użyć tej reguły, aby wyzwolić powiadomienie dla pacjenta, aby naładować swoje urządzenie.

>[!div class="mx-imgBorder"] 
>![Reguła wysokiej temperatury stężenia](media/brace-temp-rule.png)

### <a name="devices"></a>Urządzenia

Kliknij kartę **Urządzenia,** a następnie wybierz **wystąpienie inteligentnej klamry kolanowej**. Zobaczysz, że istnieją trzy widoki, aby eksplorować informacje o wybranym urządzeniu. Widoki te są tworzone i publikowane podczas tworzenia szablonu urządzenia dla urządzenia, co oznacza, że będą spójne na wszystkich urządzeniach, które łączysz lub symulujesz.

Widok **pulpitu nawigacyjnego** zawiera omówienie danych telemetrycznych i właściwości, które pochodzą z urządzenia, które są zorientowane na operatora.

Karta **Właściwości** umożliwia edytowanie właściwości chmury i właściwości urządzenia odczytu/zapisu.

Karta **Polecenia** umożliwia uruchamianie poleceń, które zostały modelowane jako część szablonu urządzenia.

>[!div class="mx-imgBorder"] 
>![Widoki stężenia kolanowego](media/knee-brace-dashboard.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz nadal korzystać z tej aplikacji, usuń aplikację, odwiedzając **pozycję Administracja > ustawienia aplikacji** i kliknij przycisk **Usuń**.

>[!div class="mx-imgBorder"] 
>![Usuń aplikację](media/admin-delete.png)

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak utworzyć pulpit nawigacyjny dostawcy, który łączy się z aplikacją IoT Central.

> [!div class="nextstepaction"]
> [Tworzenie pulpitu nawigacyjnego dostawcy](howto-health-data-triage.md)