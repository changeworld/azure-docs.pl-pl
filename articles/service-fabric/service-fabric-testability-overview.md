---
title: Omówienie usługi analizy błędów
description: W tym artykule opisano usługę analizy błędów w sieci szkieletowej usług do wywoływania błędów i uruchamiania scenariuszy testowych względem usług.
author: anmolah
ms.topic: conceptual
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: d5c770a4d823ebe9b2700b081c407c54dd1d18a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465573"
---
# <a name="introduction-to-the-fault-analysis-service"></a>Wprowadzenie do usługi analizy usterek
Usługa analizy błędów jest przeznaczona do testowania usług, które są oparte na sieci szkieletowej usługi Microsoft Azure. Za pomocą usługi analizy błędów można wywoływać znaczące błędy i uruchamiać kompletne scenariusze testów dla aplikacji. Te błędy i scenariusze wykonywania i sprawdzania poprawności wielu stanów i przejść, które usługa będzie doświadczenie przez cały okres jej istnienia, wszystko w sposób kontrolowany, bezpieczny i spójny.

Akcje są poszczególne błędy kierowania usługi do testowania go. Deweloper usługi można użyć tych jako bloków konstrukcyjnych do pisania skomplikowanych scenariuszy. Przykład:

* Uruchom ponownie węzeł, aby symulować dowolną liczbę sytuacji, w których komputer lub maszyna wirtualna jest ponownie uruchamiana.
* Przenieś replikę usługi stanowej, aby symulować równoważenie obciążenia, przewijanie w błąd lub uaktualnianie aplikacji.
* Wywołaj utratę kworum w usłudze stanowej, aby utworzyć sytuację, w której operacje zapisu nie mogą być kontynuowane, ponieważ nie ma wystarczającej liczby replik "kopii zapasowej" lub "pomocniczej", aby zaakceptować nowe dane.
* Wywołaj utratę danych w usłudze stanowej, aby utworzyć sytuację, w której cały stan w pamięci jest całkowicie wymazany.

Scenariusze są złożone operacje składające się z jednej lub więcej akcji. Usługa analizy błędów udostępnia dwa wbudowane pełne scenariusze:

* Scenariusz chaosu
* Scenariusz trybu failover

## <a name="testing-as-a-service"></a>Testowanie jako usługa
Usługa analizy błędów to usługa systemu sieci szkieletowej usług, która jest automatycznie uruchamiana z klastrem sieci szkieletowej usług. Ta usługa działa jako host do iniekcji błędów, wykonywania scenariusza testu i analizy kondycji. 

![Usługa analizy usterek][0]

Po zainicjowaniu akcji błędu lub scenariusza testowego, polecenie jest wysyłane do usługi analizy błędów, aby uruchomić akcję błędu lub scenariusz testowy. Usługa analizy błędów jest stanowa, dzięki czemu można niezawodnie uruchamiać błędy i scenariusze oraz sprawdzać poprawność wyników. Na przykład scenariusz testu długotrwałego można niezawodnie wykonać przez usługę analizy błędów. A ponieważ testy są wykonywane wewnątrz klastra, usługa może sprawdzić stan klastra i usług, aby zapewnić bardziej szczegółowe informacje o awariach.

## <a name="testing-distributed-systems"></a>Testowanie systemów rozproszonych
Sieci szkieletowej usług znacznie ułatwia pisanie i zarządzanie rozproszonymi skalowalnymi aplikacjami. Usługa analizy błędów ułatwia testowanie aplikacji rozproszonej podobnie. Istnieją trzy główne problemy, które należy rozwiązać podczas testowania:

1. Symulowanie/generowanie błędów, które mogą wystąpić w rzeczywistych scenariuszach: Jednym z ważnych aspektów sieci szkieletowej usług jest to, że umożliwia rozproszonym aplikacjom odzyskiwanie po różnych błędach. Jednak aby przetestować, czy aplikacja jest w stanie odzyskać od tych błędów, potrzebujemy mechanizmu do symulacji/generowania tych rzeczywistych błędów w kontrolowanym środowisku testowym.
1. Możliwość generowania skorelowanych awarii: Podstawowe awarie w systemie, takie jak awarie sieci i awarie maszyn, są łatwe do wyprodukowania indywidualnie. Generowanie znacznej liczby scenariuszy, które mogą się zdarzyć w świecie rzeczywistym w wyniku interakcji tych poszczególnych błędów jest nietrywialne.
1. Ujednolicone środowisko na różnych poziomach programowania i wdrażania: Istnieje wiele systemów wtrysku usterek, które mogą wykonywać różne typy awarii. Jednak doświadczenie we wszystkich tych jest słabe podczas przechodzenia od scenariuszy deweloperskich jednorodznych, do uruchamiania tych samych testów w dużych środowiskach testowych, do korzystania z nich do testów w środowisku produkcyjnym.

Chociaż istnieje wiele mechanizmów, aby rozwiązać te problemy, brakuje systemu, który robi to samo z wymaganymi gwarancjami — aż z jedno pole środowiska deweloperskiego, aby przetestować w klastrach produkcyjnych. Usługa analizy błędów pomaga deweloperom aplikacji skoncentrować się na testowaniu ich logiki biznesowej. Usługa analizy błędów zapewnia wszystkie możliwości potrzebne do testowania interakcji usługi z podstawowym systemem rozproszonym.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Symulowanie/generowanie rzeczywistych scenariuszy awarii
Aby przetestować odporność systemu rozproszonego na awarie, potrzebujemy mechanizmu do generowania awarii. Podczas gdy w teorii generowanie awarii, takie jak węzeł w dół, wydaje się łatwe, zaczyna uderzać w ten sam zestaw problemów spójności, które sieci szkieletowej usług próbuje rozwiązać. Na przykład, jeśli chcemy zamknąć węzeł, wymagany przepływ pracy jest następujący:

1. Od klienta wystaw żądanie węzła zamknięcia.
1. Wyślij żądanie do odpowiedniego węzła.
   
    a. Jeśli węzeł nie zostanie znaleziony, powinien zakończyć się niepowodzeniem.
   
    b. Jeśli węzeł zostanie znaleziony, należy zwrócić tylko wtedy, gdy węzeł jest zamknięty.

Aby sprawdzić błąd z punktu widzenia testu, test musi wiedzieć, że gdy ten błąd jest wywołany, błąd faktycznie się dzieje. Gwarancja, że usługa Service Fabric zapewnia jest to, że węzeł będzie w dół lub był już w dół, gdy polecenie dotarło do węzła. W obu przypadkach test powinien być w stanie poprawnie przyczyny o stanie i zakończyć się pomyślnie lub nie poprawnie w jego sprawdzania poprawności. System zaimplementowany poza sieci szkieletowej usług, aby wykonać ten sam zestaw błędów może trafić wiele problemów z siecią, sprzętem i oprogramowaniem, co uniemożliwiłoby mu zapewnienie poprzednich gwarancji. W obecności problemów wymienionych wcześniej sieci szkieletowej usług będzie ponownie skonfigurować stan klastra do obejść problemy, a tym samym usługa analizy błędów nadal będzie w stanie dać odpowiedni zestaw gwarancji.

### <a name="generating-required-events-and-scenarios"></a>Generowanie wymaganych zdarzeń i scenariuszy
Podczas symulacji rzeczywistych awarii konsekwentnie jest trudne do rozpoczęcia, zdolność do generowania skorelowanych awarii jest jeszcze trudniejsze. Na przykład utrata danych dzieje się w usługi stanowej utrwalone, gdy dzieją się następujące rzeczy:

1. Tylko kworum zapisu replik są przechwytywane w replikacji. Wszystkie repliki pomocnicze pozostają w tyle za podstawowym.
1. Kworum zapisu ulegnie ulegnie spowolnić z powodu replik w dół (ze względu na pakiet kodu lub węzeł w dół).
1. Kworum zapisu nie może wrócić, ponieważ dane dla replik są tracone (z powodu uszkodzenia dysku lub ponownego zaimkowania komputera).

Te skorelowane awarie zdarzają się w świecie rzeczywistym, ale nie tak często, jak poszczególne awarie. Możliwość testowania tych scenariuszy, zanim się one zdarzyć w produkcji jest krytyczna. Jeszcze ważniejsza jest możliwość symulowania tych scenariuszy z obciążeniami produkcyjnymi w kontrolowanych okolicznościach (w środku dnia ze wszystkimi inżynierami na pokładzie). To znacznie lepsze niż to, że stało się to po raz pierwszy w produkcji o godzinie 2:00.

### <a name="unified-experience-across-different-environments"></a>Ujednolicone środowisko w różnych środowiskach
Praktyką tradycyjnie było stworzenie trzech różnych zestawów doświadczeń, jednego dla środowiska programistycznego, jednego dla testów i jednego do produkcji. Model był:

1. W środowisku programistycznym należy utworzyć przejścia stanu, które umożliwiają testy jednostkowe poszczególnych metod.
1. W środowisku testowym wygeneruj błędy, aby umożliwić kompleksowe testy, które wykonują różne scenariusze awarii.
1. Zachowaj nieskazitelną niedoskonałość środowiska produkcyjnego, aby zapobiec wszelkim awariom nienaturalnych i zapewnić bardzo szybką reakcję człowieka na awarie.

W sieci szkieletowej usług, za pośrednictwem usługi analizy błędów, proponujemy, aby włączyć to wokół i używać tej samej metodologii ze środowiska dewelopera do produkcji. Istnieją dwa sposoby osiągnięcia tego celu:

1. Aby wywołać kontrolowane awarie, należy użyć interfejsów API usługi analizy błędów ze środowiska jednopokojowego do klastrów produkcyjnych.
1. Aby nadać klastrowi gorączkę, która powoduje automatyczną indukcję błędów, użyj usługi analizy błędów do generowania automatycznych błędów. Kontrolowanie szybkości błędów za pomocą konfiguracji umożliwia tę samą usługę, która ma być testowana inaczej w różnych środowiskach.

Z sieci szkieletowej usług, chociaż skala błędów będzie inna w różnych środowiskach, rzeczywiste mechanizmy będą identyczne. Pozwala to na znacznie szybszy potok kodu do wdrożenia i możliwość testowania usług w rzeczywistych obciążeniach.

## <a name="using-the-fault-analysis-service"></a>Korzystanie z usługi analizy błędów
**C #**

Funkcje usługi analizy błędów znajdują się w obszarze nazw System.Fabric w pakiecie Microsoft.ServiceFabric NuGet. Aby użyć funkcji usługi analizy błędów, należy dołączyć pakiet nuget jako odwołanie w projekcie.

**Powershell**

Aby korzystać z programu PowerShell, należy zainstalować pakiet SDK sieci szkieletowej usług. Po zainstalowaniu SDK moduł ServiceFabric PowerShell jest automatycznie ładowany do użycia.

## <a name="next-steps"></a>Następne kroki
Aby stworzyć usługi w prawdziwie chmurowej skali, ważne jest, aby zapewnić, zarówno przed i po wdrożeniu, że usługi mogą wytrzymać rzeczywiste awarie. W dzisiejszym świecie usług bardzo ważna jest możliwość szybkiego wprowadzania innowacji i szybkiego przenoszenia kodu do produkcji. Usługa analizy błędów pomaga deweloperom usług, aby dokładnie to zrobić.

Rozpocznij testowanie aplikacji i usług przy użyciu [wbudowanych scenariuszy testowych](service-fabric-testability-scenarios.md)lub autor własnych scenariuszy testowych przy użyciu [akcji błędów dostarczonych](service-fabric-testability-actions.md) przez usługę analizy błędów.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png
