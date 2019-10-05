---
title: Omówienie usługi Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Dowiedz się więcej na temat usługi Azure Digital bliźniaczych reprezentacji, rozwiązania Azure IoT na potrzeby analizy przestrzennej.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.date: 09/17/2019
ms.topic: overview
ms.service: digital-twins
services: digital-twins
ms.custom: mvc
ms.openlocfilehash: 2194afe4784d1b59dc5fd5628e3a9223d13f92be
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949292"
---
# <a name="overview-of-azure-digital-twins"></a>Omówienie usługi Azure Digital bliźniaczych reprezentacji

Azure Digital bliźniaczych reprezentacji w wersji zapoznawczej to usługa Azure IoT, która tworzy kompleksowe modele środowiska fizycznego. Można utworzyć wykresy analizy przestrzennej, aby modelować relacje i interakcje między osobami, miejscami i urządzeniami.

Za pomocą usługi Azure Digital bliźniaczych reprezentacji można wysyłać zapytania o dane z miejsca fizycznego, a nie z wielu różnych czujników. Ta usługa ułatwia tworzenie, wysoce skalowalnych, rozproszonych środowisk, które łączą dane przesyłane strumieniowo w świecie cyfrowym i fizycznym. Aplikacje są rozszerzane o te jednoznaczne funkcje kontekstowe. 

Usługa Azure Digital bliźniaczych reprezentacji ma zastosowanie do wszystkich typów środowisk, takich jak magazyny, biura, szkoły, szpitale i banki. Może być nawet używany dla stadiums, fabryk, parkingów, parków, sieci inteligentnych i miast. Poniżej przedstawiono kilka scenariuszy, w których usługa Azure Digital bliźniaczych reprezentacji może być przydatna:

- Przewidywanie potrzeb konserwacji dla fabryki.
- Analizuj wymagania dotyczące energii w czasie rzeczywistym dla siatki elektrycznej.
- Zoptymalizuj wykorzystanie dostępnego miejsca dla biura.
- Śledź dzienną temperaturę w kilku Stanach.
- Monitoruj zajęte ścieżki drona.
- Zidentyfikuj pojazdy autonomiczne.
- Analizuj poziomy zajętości dla budynku.
- Znajdź rejestr kasowy najgorętszym w sklepie.

Każdy rzeczywisty scenariusz biznesowy to prawdopodobnie odpowiednie wystąpienie cyfrowe można zainicjować za pomocą usługi Azure Digital bliźniaczych reprezentacji.

Poniższe wideo przeniesie bliżej usługi Azure Digital bliźniaczych reprezentacji.

> [!VIDEO https://www.youtube.com/embed/TvN_NxpgyzQ]

## <a name="key-capabilities"></a>Kluczowe możliwości

Usługa Azure Digital bliźniaczych reprezentacji oferuje następujące kluczowe możliwości.

### <a name="spatial-intelligence-graph"></a>Wykres analizy przestrzennej

[*Wykres analizy przestrzennej*](./concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph)lub *Wykres przestrzenny*jest wirtualną reprezentacją środowiska fizycznego. Można jej użyć do modelowania relacji między osobami, miejscami i urządzeniami.

Rozważmy aplikację narzędzia inteligentnego, która obejmuje kilka mierników użycia energii elektrycznej połączonych w klubie. Firma narzędzia inteligentnego musi dokładnie monitorować i przewidzieć użycie energii elektrycznej oraz rozliczanie. Każde urządzenie i czujnik muszą być modelowane przy użyciu kontekstu dotyczącego lokalizacji i klienta, który ma być rozliczany. Możesz użyć grafu analizy przestrzennej do modelowania tego rodzaju relacji złożonych.

### <a name="digital-twin-object-models"></a>Cyfrowe modele obiektów bliźniaczych

[Modele obiektów z cyframi cyfrowymi](./concepts-objectmodel-spatialgraph.md#digital-twins-object-models) są wstępnie zdefiniowanymi protokołami urządzeń i schematem danych. Dopasowują one potrzebę specyficzną dla domeny rozwiązania, aby przyspieszyć i uprościć programowanie.

Na przykład aplikacja pokojowa może korzystać ze wstępnie zdefiniowanych typów miejsca, takich jak kampus, budynek, piętro i pokój.

### <a name="multiple-and-nested-tenants"></a>Dzierżawy z wieloma i zagnieżdżonymi

Możesz tworzyć rozwiązania, które są w bezpieczny sposób skalowane i mogą być ponownie używane dla wielu dzierżawców. Można również utworzyć wiele dzierżawców, do których można uzyskać dostęp i używać ich w sposób izolowany i bezpieczny.

Przykładem jest aplikacja wykorzystująca miejsce, która jest skonfigurowana w celu izolowania danych dzierżawy od danych innych dzierżawców w ramach jednego budynku. Lub aplikacja służy do łączenia danych dla jednej dzierżawy z wieloma budynkami.

### <a name="advanced-compute-capabilities"></a>Zaawansowane możliwości obliczeniowe

Dzięki [funkcjom zdefiniowanym przez użytkownika](./concepts-user-defined-functions.md)można definiować i uruchamiać funkcje niestandardowe dla przychodzących [danych urządzenia](./concepts-device-ingress.md) , aby wysyłać sygnały do wstępnie zdefiniowanych punktów końcowych. Ta zaawansowana funkcja ulepsza dostosowanie i automatyzację zadań urządzeń.

Przykładem jest aplikacja Smart rolnictwo, która obejmuje funkcję zdefiniowaną przez użytkownika do oceny odczytów czujnika gleby i prognoz pogodowych. Aplikacja wysyła następnie sygnały o potrzebach nawadniania.

### <a name="built-in-access-control"></a>Wbudowana kontrola dostępu

Za pomocą funkcji zarządzania dostępem i tożsamości, takich jak [Kontrola dostępu oparta na rolach](./security-role-based-access-control.md) i [Azure Active Directory](./security-authenticating-apis.md), można bezpiecznie kontrolować dostęp dla poszczególnych użytkowników i urządzeń.

Przykładem jest aplikacja do zarządzania urządzeniami, która jest skonfigurowana tak, aby umożliwić pasażerom pokoju ustawianie temperatury w określonym zakresie. Menedżerowie urządzeń mogą ustawiać temperaturę w dowolnym pokoju do dowolnej wartości.

### <a name="ecosystem"></a>Ekosystem

Możesz połączyć wystąpienie usługi Azure Digital bliźniaczych reprezentacji z wieloma zaawansowanymi usługami platformy Azure. Te usługi obejmują Azure Stream Analytics, Azure AI i Azure Storage. Obejmują one również Azure Maps, rzeczywistość mieszana firmy Microsoft, Dynamics 365 lub Office 365.

Przykładem jest inteligentna aplikacja do tworzenia biura, która używa usługi Azure Digital bliźniaczych reprezentacji do reprezentowania zespołów i urządzeń znajdujących się w wielu piętrach. Jako że urządzenia przesyłają strumieniowo dane na żywo do wystąpienia z zainicjowaną obsługą wieloosiową, Stream Analytics przetwarzają te dane, aby zapewnić możliwość podejmowania odpowiednich działań. Dane są przechowywane w usłudze Azure Storage i konwertowane w formacie pliku możliwego do udostępnienia. Plik jest dystrybuowany w całej organizacji przy użyciu pakietu Office 365.

## <a name="solutions-that-benefit-from-azure-digital-twins"></a>Rozwiązania korzystające z usługi Azure Digital bliźniaczych reprezentacji

Usługa Azure Digital bliźniaczych reprezentacji jest przydatna do reprezentowania świata fizycznego i jego wielu relacji. Upraszcza to modelowanie IoT, przetwarzanie danych, obsługę zdarzeń i śledzenie urządzeń. Rozważ kilka z następujących scenariuszy w kilku branżach. Korzystają one z używania do:

* Pokaż firmie zarządzania właściwościami poziomy zajętego miejsca w czasie, aby zgłębiać wgląd w szczegółowe informacje o najlepszych sposobach konfigurowania jego tworzenia.
* Wyzwól bilety kolejności pracy dla aplikacji mobilnej. Służy do wysyłania strażników zabezpieczeń i planowania Janitorial i innych usług w przestrzeni handlowej lub miejscu sportowych.
* Pokaż w budynku, które pokoje są zajęte w czasie rzeczywistym. Następnie Pomóż użytkownikom rezerwować miejsca pracy, które pasują do swoich potrzeb.
* Śledź miejsce, w którym znajdują się zasoby.
* Zoptymalizuj opłaty za pojazdy elektryczne przez modelowanie preferencji użytkownika i ograniczeń związanych z siatką energii.

## <a name="azure-digital-twins-in-the-context-of-other-iot-services"></a>Usługa Azure Digital bliźniaczych reprezentacji w kontekście innych usług IoT

Usługa Azure Digital bliźniaczych reprezentacji korzysta z usługi Azure IoT Hub, aby połączyć urządzenia IoT i czujniki, które zachowują wszystko aktualne w świecie fizycznym. Na poniższym diagramie przedstawiono sposób, w jaki usługa Azure Digital bliźniaczych reprezentacji odnosi się do innych usług Azure IoT.

[![Azure Digital bliźniaczych reprezentacji to usługa oparta na platformie Azure IoT Hub](media/overview/azure-digital-twins-in-iot-ecosystem.png)](media/overview/azure-digital-twins-in-iot-ecosystem.png#lightbox)

Aby uzyskać więcej informacji na temat IoT, zobacz [technologie i rozwiązania Azure IoT](../iot-fundamentals/iot-services-and-technologies.md).

## <a name="next-steps"></a>Następne kroki

Przejdź do krótkiej wersji demonstracyjnej dotyczącej usługi Azure Digital bliźniaczych reprezentacji:

>[!div class="nextstepaction"]
>[Szybki Start: znajdowanie dostępnych pokojów przy użyciu usługi Azure Digital bliźniaczych reprezentacji](./quickstart-view-occupancy-dotnet.md)

Dokładnie obejrzyj aplikację zarządzania obiektami za pomocą usługi Azure Digital bliźniaczych reprezentacji:

>[!div class="nextstepaction"]
>[Samouczek: wdrażanie usługi Azure Digital bliźniaczych reprezentacji i Konfigurowanie wykresu przestrzennego](./tutorial-facilities-setup.md)

Dowiedz się więcej na temat podstawowych koncepcji usługi Azure Digital bliźniaczych reprezentacji:

>[!div class="nextstepaction"]
>[Omówienie modelu obiektów Digital bliźniaczych reprezentacji i wykresu analizy przestrzennej](./concepts-objectmodel-spatialgraph.md)