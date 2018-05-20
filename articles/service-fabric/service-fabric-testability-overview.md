---
title: Omówienie usługi analiza błędów | Dokumentacja firmy Microsoft
description: W tym artykule opisano usługę analiza błędów w sieci szkieletowej usług dla wywołania usterek i uruchamiania scenariuszy testowania usług.
services: service-fabric
documentationcenter: .net
author: anmolah
manager: timlt
editor: vturecek
ms.assetid: 1f064276-293a-4989-a513-e0d0b9fdf703
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: 807e4588e23ea01c5ce435282d7af59bb108e6c6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="introduction-to-the-fault-analysis-service"></a>Wprowadzenie do usługi analiza błędów
Usługi analizy błędów jest przeznaczony do testowania usług, które są wbudowane w usługi sieć szkieletowa usług Microsoft Azure. W usłudze analiza błędów można wywołać znaczenie błędów i uruchomienia testu pełne scenariusze aplikacji. Te błędy i scenariusze wykonywania i sprawdzania poprawności wiele stanów i przejść, które usługa może wystąpić w jego okres istnienia, w sposób kontrolowany, bezpieczne i zgodne.

Akcje są poszczególnych usterek przeznaczonych dla usługi do testowania go. Projektant usługi można użyć je jako bloków konstrukcyjnych, aby zapisać złożonych zadań. Na przykład:

* Uruchom ponownie węzeł, aby symulować dowolną liczbę sytuacji, w którym ponownego uruchomienia komputera lub maszyny Wirtualnej.
* Przenieś repliki usługi stanowej do symulowania równoważenia obciążenia i trybu failover lub uaktualniania aplikacji.
* Wywołaj utraty kworum usługi stanowej, aby uniknąć sytuacji, gdy nie można kontynuować operacji zapisu, ponieważ nie ma wystarczającej liczby replik "kopii zapasowych" i "secondary" nowe dane.
* Wywołanie usługi stanowej, aby uniknąć sytuacji, gdy całkowicie wyczyszczeniem wszystkich stanów w pamięci poza utraty danych.

Scenariusze są złożonych operacji składa się z co najmniej jednej akcji. Usługi analizy błędów zawiera dwa wbudowane pełne scenariusze:

* Scenariusz chaos
* Scenariusz trybu failover

## <a name="testing-as-a-service"></a>Testowanie jako usługa
Usługi analizy błędów jest usługą systemu sieci szkieletowej usług, która zostanie automatycznie uruchomiony przy użyciu klastra sieci szkieletowej usług. Ta usługa działa jako host iniekcji błędów, wykonywania testów scenariusza i analizy kondycji. 

![Błąd analizy usługi][0]

Po zainicjowaniu akcji lub testu scenariusza błędów polecenia są wysyłane do usługi analizy błędów do uruchomienia scenariusz akcji lub test awarii. Usługi analizy błędów jest obiektem stanowym tak, aby można niezawodnie Uruchom scenariuszy i usterek i Zweryfikuj wyniki. Na przykład scenariusza testu długotrwałe może być niezawodnie wykonywane przez usługę analiza błędów. I ponieważ testy są wykonywane w klastrze, usługi można sprawdzić stan klastra i usługi, aby zapewnić więcej szczegółowych informacji o awarii.

## <a name="testing-distributed-systems"></a>Testowanie systemów rozproszonych
Sieć szkieletowa usług sprawia, że zapisywanie i aplikacje skalowalne rozproszone znacznie łatwiejsze zarządzanie. Usługi analizy błędów sprawia, że testowanie aplikacji rozproszonej podobnie łatwiejsze. Istnieją trzy główne problemy, które muszą zostać rozwiązane podczas testowania:

1. Symuluje/generowanie błędów, które mogą wystąpić w rzeczywistych scenariuszach: jedną z najważniejszych aspektów usługi sieć szkieletowa jest możliwość aplikacji rozproszonych odzyskać z różnych błędów. Aby sprawdzić, czy aplikacja jest w stanie odzyskać z tych błędów, Potrzebujemy jednak mechanizm do symulowania/wygenerowania tych błędów rzeczywistych w środowisku testowym kontrolowane.
2. Możliwość generowania błędów skorelowane: podstawowe błędy w systemie, takie jak awarie sieci i błędy maszyn łatwych do utworzenia pojedynczo. Generowanie znaczących scenariusze, które mogą wystąpić w świecie rzeczywistym w wyniku interakcji z tych błędów pojedynczych jest nieuproszczony.
3. Ujednolicone i środowisko na różnych poziomach programowanie i wdrożenie: istnieje wiele systemów iniekcji błędów, które mogą wykonywać różnych typów błędów. Jednak środowisko we wszystkich z nich jest niska, podczas przenoszenia z jednego pola deweloperów uruchomione te same testy w środowisku testowym duża, aby ich użycie w przypadku testów w środowisku produkcyjnym.

Gdy istnieje wiele mechanizmów rozwiązywania tych problemów, system, który jest taki sam jak wymagane gwarancje — od środowiska deweloperskiego jednego pola, testowanie w produkcji klastrów — Brak. Usługi analizy błędów ułatwia deweloperom aplikacji, skoncentrować się na temat testowania ich logiki biznesowej. Usługi analizy błędów zawiera wszystkie funkcje, które są potrzebne do testowania interakcji usługi o podstawowym systemie rozproszonej.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Symuluje generowania scenariuszy awarii rzeczywistych
Aby przetestować niezawodności systemu rozproszonego przed awariami, potrzebujemy mechanizm umożliwiający generowanie błędów. Gdy teoretycznie generowania awarii, jak węzeł w dół prawdopodobnie łatwe, rozpoczyna się naciśnięcie ten sam zestaw spójności problemów, które podejmuje próbę rozwiązania sieci szkieletowej usług. Na przykład jeśli chcemy wyłączyć węzła, przepływ pracy, wymagane jest:

1. Od klienta należy wydać żądanie zamknięcia węzła.
2. Wyślij żądanie do prawidłowego węzła.
   
    a. Jeśli węzeł nie zostanie znaleziony, jeżeli nie.
   
    b. Jeśli węzeł zostanie znaleziony, powinien on zwrócić tylko jeśli węzeł zostanie zamknięta.

Aby sprawdzić błąd z punktu widzenia testu, testu musi wiedzieć, że przy powstaniu jest ten błąd, błąd wystąpi. Gwarancji, że Usługa Service Fabric realizuje jest to, że albo węzeł zostanie umieszczona w dół lub już nie działa, jeśli polecenie osiągnięto węzła. W obu przypadkach testu powinien móc prawidłowo przyczyny o stanie i powodzenie lub niepowodzenie poprawnie w jego poprawności. System zaimplementowana poza sieci szkieletowej usług do tego samego zestawu błędów można osiągnęła wiele sieci, sprzętu i problemy z oprogramowaniem, które mogłyby uniemożliwiać zapewnienie poprzedniego gwarancji. Obecności problemy wspomniano wcześniej, stan klastra w celu obejścia problemów konfigurację sieci szkieletowej usług i dlatego usługa analiza błędów nadal będą mogły umożliwiają prawidłowego zestawu gwarancje.

### <a name="generating-required-events-and-scenarios"></a>Generowanie zdarzeń wymagane i scenariusze
Spójnie symulację awarii rzeczywistych skomplikowane jest zacząć, nawet silniejsze jest możliwość generowania skorelowane błędów. Na przykład utraty danych odbywa się w usługa stanowa utrwalonego, gdy wystąpi następujących czynności:

1. Tylko do zapisu kworum replik są przechwytywane w na replikację. Wszystkie pomocnicze repliki opóźniona serwera podstawowego.
2. Kworum zapisu przestanie działać z powodu replik, przechodząc w dół (ze względu na pakiet kodu lub węzeł przechodzi w dół).
3. Kworum zapisu nie przywracane ponieważ dane repliki są utracone (z powodu uszkodzenia dysku lub ponownej instalacji systemu komputera).

Te błędy skorelowane się tak zdarzyć w świecie rzeczywistym, ale nie jako błędy często indywidualnych. Bardzo ważne jest możliwość testowania dla tych scenariuszy, zanim wystąpią w środowisku produkcyjnym. Szczególnie ważne jest możliwość symulować tych scenariuszy z obciążeń produkcyjnych w warunkach kontrolowanych (na środku dnia z wszystkich inżynierów w talii). To znacznie lepszą niż w przypadku jego stanie się po raz pierwszy w środowisku produkcyjnym o 2:00

### <a name="unified-experience-across-different-environments"></a>Ujednolicone doświadczenie w różnych środowiskach
Praktyki tradycyjnie była utworzyć trzy różne zestawy środowiska, jeden dla środowiska programowania, jeden dla testów, a drugi do produkcji. Model został:

1. Środowisko deweloperskie do tworzenia przejścia stanu zezwala testów jednostkowych poszczególnych metod.
2. W środowisku testowym należy utworzyć nieudanych logowań end-to-end testy, które korzystają z różnych scenariuszy awarii.
3. Zachowaj nienaruszone, aby zapobiec błędom żadnych innych niż naturalnych i upewnij się, że jest bardzo szybki człowieka odpowiedzi na błąd środowiska produkcyjnego.

W sieci szkieletowej usług za pośrednictwem usługi analiza błędów możemy proponowania włączenie tej funkcji i użycie tej samej metodologii ze środowiska deweloperskiego do środowiska produkcyjnego. Istnieją dwa sposoby, w tym:

1. Aby wywołać kontrolowane błędów, przy użyciu błędów analizy usługi interfejsów API ze środowiska jednego pola aż do klastrów produkcyjnych.
2. Aby dać pomoru, która powoduje automatyczne wywoływanie awarii klastra, należy korzystać z usługi analizy błędów do generowania automatycznego błędów. Kontrolowanie współczynnik błędów za pomocą konfiguracji umożliwia tej samej usługi do sprawdzenia inaczej w różnych środowiskach.

Z usługi Service Fabric chociaż skali błędy będą różne w różnych środowiskach rzeczywistych mechanizmów będą identyczne. Dzięki temu wiele potoku przyspieszają-do wdrażanie kodu oraz możliwość testowania usług pod obciążeniem rzeczywistych.

## <a name="using-the-fault-analysis-service"></a>Przy użyciu usługi analiza błędów
**C#**

Funkcji usługi analiza błędów znajdują się w przestrzeni nazw System.Fabric w pakiecie Microsoft.ServiceFabric NuGet. Aby używać funkcji usługi analiza błędów, obejmują pakiet nuget jako odwołanie do projektu.

**Program PowerShell**

Przy użyciu programu PowerShell, należy zainstalować zestaw SDK sieci szkieletowej usług. Po zainstalowaniu zestawu SDK modułu ServiceFabric programu PowerShell jest załadowany można użyć automatycznego.

## <a name="next-steps"></a>Kolejne kroki
Aby utworzyć naprawdę skali chmury usługi, jest krytyczne, aby upewnić się, przed i po wdrożeniu, że usługi może wytrzymać rzeczywistych błędów. W świecie usługi obecnie możliwość innowacji szybko i Przenieś kod do środowiska produkcyjnego szybko jest bardzo ważne. Usługi analizy błędów ułatwia deweloperom usługi dokładnie w tym celu należy.

Rozpoczęcie testowania aplikacji i usług przy użyciu wbudowanych [przetestować scenariusze](service-fabric-testability-scenarios.md), lub utworzyć własne scenariuszy testowania przy użyciu [fault akcje](service-fabric-testability-actions.md) udostępniony przez usługę analiza błędów.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png
