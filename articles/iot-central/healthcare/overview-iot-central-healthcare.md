---
title: Twórz rozwiązania dla służby zdrowia dzięki usłudze Azure IoT Central | Dokumenty firmy Microsoft
description: Dowiedz się, jak tworzyć rozwiązania dla służby zdrowia przy użyciu szablonów aplikacji Usługi Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 2aee5c56b267f9243b69a48dfd0f5f64196c5f23
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77021494"
---
# <a name="building-healthcare-solutions-with-azure-iot-central"></a>Tworzenie rozwiązań dla opieki zdrowotnej za pomocą usługi Azure IoT Central 



Dowiedz się, jak tworzyć rozwiązania dla służby zdrowia za pomocą usługi Azure IoT Central przy użyciu szablonów aplikacji.

## <a name="what-is-continuous-patient-monitoring-template"></a>Co to jest szablon ciągłego monitorowania pacjenta?

W przestrzeni IoT opieki zdrowotnej ciągłe monitorowanie pacjentów jest jednym z kluczowych czynników umożliwiających zmniejszenie ryzyka readmisji, skuteczniejsze zarządzanie chorobami przewlekłymi oraz poprawę wyników pacjentów. Ciągłe monitorowanie pacjenta można podzielić na dwie główne kategorie:

1. **Monitorowanie pacjenta**: Za pomocą urządzeń do noszenia i innych urządzeń medycznych w szpitalu, zespoły opieki mogą monitorować parametry życiowe pacjenta i choroby bez konieczności wysyłania pielęgniarki, aby sprawdzić pacjenta kilka razy dziennie. Zespoły opieki mogą zrozumieć moment, w którym pacjent potrzebuje krytycznej uwagi poprzez powiadomienia i skutecznie nadaje priorytet swojemu czasowi.
1. **Zdalne monitorowanie pacjenta**: Dzięki zastosowaniu medycznych wearables i wyników zgłoszonych przez pacjentów (PRO) do monitorowania pacjentów poza szpitalem, ryzyko readmisji może zostać obniżone. Można zbierać dane pochodzące od pacjentów cierpiących na choroby przewlekłe i pacjentów z rehabilitacją, aby upewnić się, że pacjenci przestrzegają planów opieki i że mogą pojawić się alarmy o pogorszeniu stanu pacjentów zespołom opieki, zanim staną się krytyczne.

Ten szablon aplikacji może służyć do tworzenia rozwiązań dla obu kategorii ciągłego monitorowania pacjenta. Korzyści:

* Bezproblemowe łączenie różnych rodzajów medycznych noszonych do instancji IoT Central.
* Monitoruj urządzenia i zarządzaj nimi, aby zapewnić ich zdrowie.
* Tworzenie niestandardowych reguł wokół danych urządzenia, aby wyzwolić odpowiednie alerty.
* Eksportuj dane dotyczące kondycji pacjenta do interfejsu API platformy Azure dla FHIR, zgodnego magazynu danych.
* Eksportuj zagregowane informacje o istniejących lub nowych aplikacjach biznesowych.

>[!div class="mx-imgBorder"] 
>![CPM-panel](media/in-patient-dashboard.png)

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć tworzenie rozwiązania do ciągłego monitorowania pacjenta:

* [Wdrażanie szablonu aplikacji](tutorial-continuous-patient-monitoring.md)
* [Zobacz przykładową architekturę](concept-continuous-patient-monitoring-architecture.md)