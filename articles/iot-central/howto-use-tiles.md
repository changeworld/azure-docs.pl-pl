---
title: Sposób używania fragmentacji w pulpit nawigacyjny aplikacji usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Jako Konstruktor Dowiedz się, jak używać kafelków na pulpitach nawigacyjnych aplikacji, pulpity nawigacyjne zestawu urządzeń i pulpitów nawigacyjnych z urządzenia.
author: v-krghan
ms.author: v-krghan
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 26322461e4f53bffff2fb182df6c45ed3b83c4bf
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503528"
---
# <a name="how-to-use-tiles"></a>Sposób używania fragmentacji
Kafelki umożliwia dostosowywanie pulpitów nawigacyjnych z aplikacji i pulpitów nawigacyjnych z urządzenia i urządzenia ustawiania pulpitów nawigacyjnych. Można dodać wiele kafelków do pulpitu nawigacyjnego i dostosowywać te Kafelki, aby przedstawiały informacje istotne dla twojej aplikacji. Można także zmienianie rozmiaru kafelków i dostosować układ na dowolnym pulpicie nawigacyjnym. Poniższy zrzut ekranu przedstawia pulpit nawigacyjny aplikacji z różnych fragmentów.

![Pulpit nawigacyjny aplikacji](media/howto-use-tiles/image1a.png)


Poniższa tabela zawiera podsumowanie użycia Kafelki w usłudze Azure IoT Central:

 
| Kafelek | Pulpit nawigacyjny | Opis
| ----------- | ------- | ------- |
| Łącze | Aplikacjami i urządzeniami ustawiania pulpitów nawigacyjnych |Link Kafelki są aktywne Kafelki, zawierające tekst nagłówek i opis. Użyj kafelka łącze, aby włączyć użytkownika, aby przejść do adresu URL, który jest powiązany z aplikacją. |
| Image | Aplikacjami i urządzeniami ustawiania pulpitów nawigacyjnych |Kafelki obrazów wyświetlania obrazu niestandardowego i może być aktywne. Użyj Kafelek obrazu, aby dodać grafiki do pulpitu nawigacyjnego i opcjonalnie włączyć użytkownika, aby przejść do adresu URL, która jest odpowiednia dla aplikacji.|
| Label | Pulpity nawigacyjne aplikacji |Etykieta kafelków wyświetlać tekst niestandardowy na pulpicie nawigacyjnym. Możesz wybrać rozmiar tekstu. Użyj kafelka etykiety, aby dodać odpowiednie informacje do pulpitu nawigacyjnego takie opisy, szczegóły dotyczące kontaktu lub pomoc|
| Mapa | Aplikacjami i urządzeniami ustawiania pulpitów nawigacyjnych |Kafelków map wyświetlanie lokalizacji i stan urządzenia na mapie. Na przykład można wyświetlać, gdy urządzenie jest i czy wentylatora jest włączone.|
| Wykres liniowy | Pulpity nawigacyjne z aplikacjami i urządzeniami |Linii wykresu kafelków wyświetlać wykres agregacji miary dla urządzenia w przedziale czasu. Na przykład można wyświetlić wykres liniowy, który przedstawia średnią temperaturę i ciśnienie na urządzeniu w ciągu ostatniej godziny|
| Wykres słupkowy | Pulpity nawigacyjne z aplikacjami i urządzeniami |Wykres słupkowy kafelków wyświetlać wykres agregacji miar dla urządzenia w przedziale czasu. Na przykład można wyświetlić wykres słupkowy, który przedstawia średnią temperaturę i ciśnienie na urządzeniu w ciągu ostatniej godziny |
| Migracji historii zdarzeń. | Pulpity nawigacyjne z aplikacjami i urządzeniami |Zdarzenie historii kafelka Wyświetla zdarzenia dla urządzenia w przedziale czasu. Na przykład służy do wyświetlenia dla zmian temperatury wystąpienia dla urządzenia w ciągu ostatniej godziny. |
| Historia stanu | Pulpity nawigacyjne z aplikacjami i urządzeniami |Historia stanu Kafelek wyświetla wartości miar w przedziale czasu. Na przykład służy do wyświetlenia wartości temperatury dla urządzenia w ciągu ostatniej godziny.|
| KLUCZOWY WSKAŹNIK WYDAJNOŚCI | Pulpity nawigacyjne z aplikacjami i urządzeniami | Kluczowy wskaźnik wydajności, Kafelek Wyświetla agregacji miary telemetrii i zdarzeń w czasie. Na przykład służy do pokazywania maksymalna temperatura osiągnięty dla urządzenia w ciągu ostatniej godziny.|
| Ostatniej znanej wartości | Pulpity nawigacyjne z aplikacjami i urządzeniami |Ostatniej znanej wartości Kafelek Wyświetla najnowszą wartość telemetrii lub stan miary. Na przykład można użyć tego kafelka, aby wyświetlić najbardziej aktualne pomiarów temperatury i ciśnienia wilgotność urządzenia.|
| Siatka zestawu urządzeń | Aplikacjami i urządzeniami ustawiania pulpitów nawigacyjnych | Urządzenie ustawione Siatka wyświetla informacje o zestawie urządzeń. Użyj kafelka siatki zestawu urządzeń, aby pokazać informacje, takie jak nazwa, lokalizacja i model wszystkich urządzeń w zestawie urządzeń.|


Aby dowiedzieć się więcej na temat sposobu konfigurowania pulpit nawigacyjny w aplikacji usługi Azure IoT Central, zobacz [pulpit nawigacyjny aplikacji Konfigurowanie](howto-configure-homepage.md).
