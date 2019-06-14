---
title: Omówienie usługi analizy błędów | Dokumentacja firmy Microsoft
description: W tym artykule opisano usługa analizy błędów w usłudze Service Fabric wykonuje błędów i uruchamianiu scenariuszy testowania usługi.
services: service-fabric
documentationcenter: .net
author: anmolah
manager: chackdan
editor: vturecek
ms.assetid: 1f064276-293a-4989-a513-e0d0b9fdf703
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: 3581550779b2387515b4f300d211b4e0a894edc7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60544830"
---
# <a name="introduction-to-the-fault-analysis-service"></a>Wprowadzenie do usługa analizy błędów
Usługa analizy błędów jest przeznaczony do testowania usług, które są wbudowane w usłudze Microsoft Azure Service Fabric. Usługa analizy błędów możesz wywołać istotne błędy i uruchamianie scenariuszy ukończenia testowej względem aplikacji. Te błędy i scenariuszy wykonywania i zweryfikować liczne Stany i przejścia, które usługa będzie wystąpić w okresie swojego istnienia, wszystko to w sposób kontrolowany, bezpieczne i zgodne.

Akcje są poszczególne błędy usługi do testowania. Deweloper usługi umożliwia je jako bloki konstrukcyjne pisania złożonych zadań. Na przykład:

* Uruchom ponownie węzeł, aby zasymulować dowolną liczbę sytuacji, w którym maszyna lub maszyna wirtualna jest uruchamiana ponownie.
* Przenieś repliki usługi stanowej w celu symulowania równoważenia obciążenia i trybu failover lub uaktualnienie aplikacji.
* Wywoływanie utraty kworum na usługi stanowej, aby uniknąć sytuacji, w których nie można kontynuować operacje zapisu, ponieważ nie ma wystarczającej liczby replik "wykonaj kopię zapasową" i "secondary" nowe dane.
* Wywoływanie utraty danych w usługi stanowej, aby uniknąć sytuacji, gdy wszystkie stany w pamięci całkowicie zostaną wyczyszczone.

Scenariusze są złożonych operacji składa się z co najmniej jednej akcji. Usługa analizy błędów zapewnia dwie wbudowane pełne scenariusze:

* Scenariusz chaosu
* Scenariusz w trybie Failover

## <a name="testing-as-a-service"></a>Testowanie jako usługa
Usługa analizy błędów to usługa systemu usługi Service Fabric, która zostanie automatycznie uruchomiony przy użyciu klastra usługi Service Fabric. Ta usługa działa jako host technika wstrzykiwania błędów, wykonanie scenariusza testu i analizy kondycji. 

![Usługa analizy błędów][0]

Po zainicjowaniu scenariusz akcji lub testów odporności, polecenia są wysyłane do usługa analizy błędów, co można uruchomić scenariusza akcji lub testów odporności danych. Usługa analizy błędów jest stanowe, dzięki czemu mogą niezawodne uruchamianie scenariuszy i usterek i Zweryfikuj wyniki. Na przykład do scenariusza testów długotrwałych może być niezawodnie wykonywane przez usługa analizy błędów. A ponieważ testy są wykonywane w klastrze, usługę można sprawdzić stan klastra i usługi w celu zapewnienia bardziej szczegółowe informacje o błędach.

## <a name="testing-distributed-systems"></a>Testowanie systemów rozproszonych
Usługa Service Fabric sprawia, że pisanie aplikacji i zarządzaniem nimi rozproszonych skalowalne znacznie łatwiejsze. Usługa analizy błędów sprawia, że testowanie aplikacji rozproszonej podobnie łatwiejsze. Istnieją trzy główne kwestie, które muszą zostać rozwiązane podczas testowania:

1. Symulowanie generowanie błędów, które mogą wystąpić w rzeczywistych scenariuszach: Jedną z ważnymi aspektami usługi Service Fabric jest umożliwienie aplikacji rozproszonych odzyskać sprawność po awarii w różnych. Aby sprawdzić, czy aplikacja jest w stanie odzyskać z tych błędów, Potrzebujemy jednak mechanizm do symulowania/generowanie tych błędów rzeczywistych, w środowisku testowym kontrolowany.
1. Możliwość generowania skorelowany błędów: Podstawowe błędy systemu, takie jak awarie sieci i błędy maszyny są łatwe do utworzenia osobno. Generowanie znacznej liczby scenariuszy, które mogą wystąpić w rzeczywistych warunkach w wyniku interakcji z tych błędów pojedynczych jest trywialny.
1. Ujednolicone środowisko na różnych poziomach programowania i wdrażania: Istnieje wiele systemów iniekcji błędów, które mogą wykonywać różnego rodzaju błędów. Jednak środowisko we wszystkich z nich jest niska, przy przechodzeniu scenariusze dla deweloperów jednopunktowe do tego samego testów w środowiskach testowych duże, aby z nich korzystać w przypadku testów w środowisku produkcyjnym.

Brak dostępnych jest wiele mechanizmów rozwiązywania tych problemów, system, który działa tak samo, wymagane gwarancje — od jednopunktowe deweloperów środowisku, aby przetestować w klastrach produkcyjnych —. Usługa analizy błędów pomaga programistom koncentrować się na testowanie ich logiki biznesowej. Usługa analizy błędów zapewnia wszystkie funkcje potrzebne do testowania interakcję usługi z bazowego systemu rozproszonego.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Symulowanie generowania scenariuszy awarii rzeczywistych
Aby przetestować niezawodności systemu rozproszonego przed awariami, musimy mechanizm generowanie błędów. Gdy teoretycznie generowanie błędu, takie jak węzeł w dół jest łatwe, rozpoczyna się osiągnięcia tego samego zestawu problemy spójności, które próbuje rozwiązać usługi Service Fabric. Na przykład jeśli chcemy, aby zamknąć węzły wymagane przepływu pracy jest następująca:

1. Z poziomu klienta należy wysłać żądanie zamknięcia węzła.
1. Wyślij żądanie do prawidłowego węzła.
   
    a. Jeśli węzeł nie zostanie znaleziony, jeżeli nie.
   
    b. Jeśli węzeł zostanie znaleziony, powinna zwrócić tylko jeśli węzeł zostanie zamknięty.

Aby sprawdzić, czy błąd z punktu widzenia testu, test musi wiedzieć, że jeśli ten błąd jest wywołane, awarii rzeczywiście się dzieje. Gwarancji, że Usługa Service Fabric zapewnia, jest to, że albo węzeł zostanie umieszczona w dół lub już nie działał, gdy polecenie węzła. W obu przypadkach testu powinien móc poprawnie przyczyny o stanie i powiedzie się lub nie działać poprawnie w jego poprawności. Zaimplementowana poza Service Fabric, aby wykonać ten sam zestaw awarii systemu można trafień wiele sieci, sprzętu i problemów z oprogramowaniem, które uniemożliwiałyby jej zapewnienia poprzedniego gwarancji. Obecności problemy, o których wspomniano wcześniej, Usługa Service Fabric umożliwia ponowną konfigurację stanu klastra w celu obejścia problemów i dlatego usługa analizy błędów nadal będzie można nadać właściwy zestaw gwarancji.

### <a name="generating-required-events-and-scenarios"></a>Generowanie wymaganych zdarzeń i scenariusze
Stale symulowania błędu w rzeczywistych warunkach jest rozpoczęcie od trudne, nawet silniejsze jest możliwość generowania skorelowany błędów. Na przykład utraty danych odbywa się w stanowej usłudze utrwalonych gdy wystąpi następujących czynności:

1. Tylko kworum zapisu repliki są przechwytywane w w replikacji. Repliki pomocnicze opóźniona podstawowego.
1. Kworum zapisu ulegnie awarii z powodu repliki zostanie wyłączona (pakiet kodu lub węzeł, który przejdzie w dół).
1. Kworum zapisu nie przywracane ponieważ dane dla replik są tracone (z powodu uszkodzenia dysku lub odtwarzanie z obrazu maszynę).

Te błędy skorelowany się tak zdarzyć w rzeczywistych warunkach, ale nie jako często, jak poszczególne błędy. Ważne jest możliwość testowania dla tych scenariuszy, zanim wystąpią w środowisku produkcyjnym. Szczególnie ważna jest możliwość symulować te scenariusze obciążeń produkcyjnych w kontrolowanym środowisku (na środku dnia inżynierów wszystkie na pokładzie). To znacznie lepsze niż w przypadku to wykonać po raz pierwszy w środowisku produkcyjnym o 2:00

### <a name="unified-experience-across-different-environments"></a>Ujednolicone środowisko, w różnych środowiskach
Praktyka tradycyjnie była utworzyć trzy różne zestawy środowiska, jeden dla środowiska programistycznego, jeden dla testów i jeden dla środowiska produkcyjnego. Model został:

1. W środowisku deweloperskim należy utworzyć stanami, zezwalających na testy jednostkowe poszczególnych metod.
1. W środowisku testowym generuje błędy, aby umożliwić testy end-to-end, które wykonuje różne scenariusze awarii.
1. W środowisku produkcyjnym należy idealnego, aby uniknąć błędów — do naturalnym i upewnij się, że istnieje bardzo szybkich odpowiedzi ludzi na awarie.

W usłudze Service Fabric za pomocą usługa analizy błędów możemy proponowania to przetwarzanie i używać tej samej metodologii ze środowiska deweloperskiego do środowiska produkcyjnego. Istnieją dwa sposoby osiągnięcia tego:

1. Aby wywołać błędy kontrolowanego, przy użyciu błędów analizy usługi interfejsów API ze środowiska jednopunktowego aż do klastrów produkcyjnych.
1. Aby dać klastra można, która powoduje automatyczne indukowana niepowodzeń, umożliwia generowanie błędów automatyczne usługa analizy błędów. Kontrolowanie współczynnik błędów za pomocą konfiguracji umożliwia tej samej usługi, która ma zostać przetestowana inaczej w różnych środowiskach.

Z usługą Service Fabric chociaż skali błędy będą różne w różnych środowiskach rzeczywistych mechanizmów będą identyczne. Umożliwia to znacznie szybszy kod wdrożenia potoku i możliwość testowania usługi w rzeczywistych warunkach.

## <a name="using-the-fault-analysis-service"></a>Usługa analizy błędów
**C#**

Funkcje analizy błędów są w przestrzeni nazw System.Fabric w pakiecie Microsoft.ServiceFabric NuGet. Korzystanie z funkcji Usługa analizy błędów obejmują pakiet nuget jako odwołania w projekcie.

**Program PowerShell**

Przy użyciu programu PowerShell, należy zainstalować zestaw SDK usługi Service Fabric. Po zainstalowaniu zestawu SDK moduł ServiceFabric programu PowerShell jest ustalana automatycznie ładowane do użycia.

## <a name="next-steps"></a>Kolejne kroki
Tworzenie prawdziwie usług skali chmury, koniecznie upewnij się, zarówno przed i po wdrożeniu, czy usługi może wytrzymać rzeczywistych błędów. W świecie usług obecnie możliwości szybkiej innowacji i szybkie przenoszenie kodu do środowiska produkcyjnego jest bardzo ważne. Usługa analizy błędów pomaga deweloperom usługi, w tym dokładnie.

Rozpoczęcie testowania aplikacji i usług przy użyciu wbudowanych [przetestować scenariusze](service-fabric-testability-scenarios.md), lub tworzenie własnych scenariuszy testowania przy użyciu [błędów akcje](service-fabric-testability-actions.md) usługa analizy błędów.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png
