---
title: Jak używać kafelków na pulpicie nawigacyjnym aplikacji platformy Azure IoT Central | Microsoft Docs
description: Jako Konstruktor można dowiedzieć się, jak używać kafelków na pulpitach nawigacyjnych aplikacji, pulpitach nawigacyjnych zestawu urządzeń i pulpitach nawigacyjnych urządzeń.
author: v-krghan
ms.author: v-krghan
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0803258c362279049a49a7eee00e7a4763621672
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952630"
---
# <a name="how-to-use-tiles"></a>Jak używać kafelków
Za pomocą kafelków możesz dostosować pulpity nawigacyjne aplikacji, pulpity nawigacyjne urządzeń i pulpity nawigacyjne zestawu urządzeń. Można dodać wiele kafelków do pulpitu nawigacyjnego i dostosować te kafelki w celu wyświetlenia informacji dotyczących aplikacji. Możesz również zmienić rozmiar kafelków i dostosować układ na dowolnym pulpicie nawigacyjnym. Poniższy zrzut ekranu przedstawia pulpit nawigacyjny aplikacji z różnymi kafelkami.

![Pulpit nawigacyjny aplikacji](media/howto-use-tiles/image1a.png)


Poniższa tabela zawiera podsumowanie użycia kafelków w usłudze Azure IoT Central:

 
| Tabliczek | Pulpit nawigacyjny | Opis
| ----------- | ------- | ------- |
| Link | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki łączy to kafelki, które wyświetlają tekst nagłówka i opisu. Użyj kafelka linku, aby umożliwić użytkownikowi nawigację do adresu URL powiązanego z Twoją aplikacją. |
| Image (Obraz) | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki obrazu wyświetlają obraz niestandardowy i można go kliknąć. Użyj kafelka obraz, aby dodać grafikę do pulpitu nawigacyjnego i opcjonalnie zezwolić użytkownikowi na przechodzenie do adresu URL, który jest istotny dla aplikacji.|
| Etykieta | Pulpity nawigacyjne aplikacji |Kafelki etykiet wyświetlają niestandardowy tekst na pulpicie nawigacyjnym. Możesz wybrać rozmiar tekstu. Użyj kafelka etykiety, aby dodać odpowiednie informacje do pulpitu nawigacyjnego, takie opisy, szczegóły kontaktu lub pomoc|
| Mapa | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki mapy wyświetlają lokalizację i stan urządzenia na mapie. Można na przykład wyświetlić, gdzie znajduje się urządzenie i czy jest włączony wentylator.|
| Wykres liniowy | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki wykresu liniowego przedstawiają wykres zagregowanej miary dla urządzenia w danym okresie czasu. Na przykład można wyświetlić wykres liniowy, który pokazuje średnią temperaturę i ciśnienie na urządzeniu w ciągu ostatniej godziny.|
| Wykres słupkowy | Pulpity nawigacyjne aplikacji i urządzeń |Kafelki wykresu słupkowego przedstawiają wykres zagregowanych pomiarów dla urządzenia w danym okresie czasu. Na przykład można wyświetlić wykres słupkowy, który pokazuje średnią temperaturę i ciśnienie na urządzeniu w ciągu ostatniej godziny. |
| Historia zdarzeń | Pulpity nawigacyjne aplikacji i urządzeń |Kafelek historia zdarzeń wyświetla zdarzenia dotyczące urządzenia w określonym czasie. Na przykład można użyć go do wyświetlenia wszystkich zmian temperatury, które wystąpiły dla urządzenia w ciągu ostatniej godziny. |
| Historia stanu | Pulpity nawigacyjne aplikacji i urządzeń |Kafelek Historia stanu zawiera wartości miary dla danego okresu. Na przykład można użyć go do wyświetlenia wartości temperatury dla urządzenia w ciągu ostatniej godziny.|
| Wskaźnik KPI | Pulpity nawigacyjne aplikacji i urządzeń | Kafelek KWW przedstawia zagregowane dane telemetryczne lub pomiarowe zdarzenia przez okres. Można na przykład użyć jej do wyświetlenia maksymalnej temperatury dla urządzenia w ciągu ostatniej godziny.|
| Ostatnia znana wartość | Pulpity nawigacyjne aplikacji i urządzeń |Kafelek Ostatnia znana wartość Wyświetla najnowszą wartość dla danych telemetrycznych lub stanu. Na przykład można użyć tego kafelka, aby wyświetlić najnowsze pomiary temperatury, ciśnienia i wilgotności dla urządzenia.|
| Siatka zestawu urządzeń | Pulpity nawigacyjne aplikacji i urządzeń | Siatka zestawu urządzeń wyświetla informacje o zestawie urządzeń. Użyj kafelka siatki zestawu urządzeń, aby wyświetlić informacje takie jak nazwa, lokalizacja i model wszystkich urządzeń w zestawie urządzeń.|


Aby dowiedzieć się więcej o konfigurowaniu pulpitu nawigacyjnego w aplikacji IoT Central platformy Azure, zobacz [Dodawanie kafelków do pulpitu nawigacyjnego](./howto-add-tiles-to-your-dashboard.md).
