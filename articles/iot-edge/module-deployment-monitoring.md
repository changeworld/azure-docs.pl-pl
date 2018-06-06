---
title: Wdrażanie modułów Azure IoT Edge | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu modułów uzyskać wdrażania na urządzenia brzegowe
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 880a17b6029dafec9ed41e3a32802dc42b872e77
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34725330"
---
# <a name="understand-iot-edge-deployments-for-single-devices-or-at-scale---preview"></a>Zrozumienie wdrożeń IoT Edge dla urządzeń z jednego lub na dużą skalę - preview

Urządzenia brzegowe IoT Azure wykonaj [cykl życia urządzenia] [ lnk-lifecycle] przypomina na inne typy urządzeń IoT:

1. Urządzenia brzegowe IoT obsługi administracyjnej, która obejmuje tworzenie obrazu urządzenia z systemem operacyjnym i instalowania [środowiska uruchomieniowego krawędzi IoT][lnk-runtime].
1. Urządzenia są skonfigurowane do uruchomienia [modułów krawędzi IoT][lnk-modules]i następnie monitorować kondycję. 
1. Ponadto urządzenia można wycofać gdy są one zastąpione lub staną się nieaktualne.  

Azure IoT krawędź udostępnia dwa sposoby konfigurowania modułów do uruchamiania na urządzeniach krawędzi IoT: jeden dla rozwoju i szybkie iteracji na jednym urządzeniu (który został użyty w samouczkach Azure IoT krawędzi), a drugi do zarządzania dużą floty urządzeń IoT krawędzi. Obie metody są dostępne, w portalu Azure i programowo.

Ten artykuł skupia się na konfiguracji i monitorowania etapów floty urządzeń, nazywane zbiorczo krawędzi IoT wdrożeń automatycznych. Ogólne kroki wdrożenia są następujące:   

1. Operator definiuje wdrożenia, które opisano zestaw modułów, a także urządzeń docelowych. Dla każdego wdrożenia ma manifest wdrażania, która odzwierciedla te informacje. 
1. Usługa Centrum IoT komunikuje się z wszystkie objęte nimi urządzenia ich konfiguracji z żądaną modułów. 
1. Usługa Centrum IoT pobiera stan z urządzeń IoT Edge i udostępnia te operator monitorowania.  Na przykład operator widoczny, gdy urządzenia nie skonfigurowano pomyślnie lub moduł nie powiedzie się w czasie wykonywania. 
1. W dowolnym momencie nowych urządzeń IoT krawędzi, które spełniają warunki określania wartości docelowej są skonfigurowane do wdrożenia. Na przykład wdrożenia, którego celem jest automatycznie wszystkie urządzenia IoT krawędzi w stanie Waszyngton konfiguruje nowe urządzenie brzegowe IoT po elastycznie i dodane do grupy urządzeń w stanie Waszyngton. 
 
Ten artykuł przeprowadzi Cię przez poszczególne składniki zaangażowane w Konfigurowanie i monitorowanie wdrożenia. Aby uzyskać wskazówki dotyczące tworzenia i aktualizowania wdrożenia, zobacz [wdrażanie i monitorowanie krawędzi IoT modułów na dużą skalę][lnk-howto].

## <a name="deployment"></a>Wdrożenie

Automatyczne wdrożenie krawędzi IoT przypisuje krawędzi IoT obrazów modułu do uruchamiania jako wystąpień w zestawie docelowym urządzenia brzegowe IoT. Działa on przez skonfigurowanie manifest rozmieszczenia krawędzi IoT Aby dołączyć listę modułów z odpowiednie parametry inicjacji. Wdrożenia można przypisać do jednego urządzenia (zazwyczaj na podstawie identyfikatora urządzenia) lub do grupy urządzeń (w oparciu tagów). Po urządzenia IoT odbiera manifest wdrażania, pobiera i instaluje obrazy kontener modułu z repozytoriami odpowiedniego kontenera i konfiguruje je odpowiednio. Po utworzeniu wdrożenia operator można monitorować stan wdrożenia, aby zobaczyć, czy urządzeń docelowych są poprawnie skonfigurowane.   

Urządzenia muszą być udostępniane jako urządzenia IoT brzegowe można skonfigurować z wdrożeniem. Poniżej są wymagania wstępne i nie są uwzględnione we wdrożeniu:
* Podstawowy system operacyjny
* Docker 
* Inicjowanie obsługi środowiska uruchomieniowego krawędzi IoT 

### <a name="deployment-manifest"></a>Manifest rozmieszczenia

Manifest rozmieszczenia jest dokument JSON, który opisuje moduły można skonfigurować na urządzeniach docelowych IoT krawędzi. Zawiera on konfiguracji metadane dla wszystkich modułów moduły wymagany system (w szczególności agenta krawędzi IoT i Centrum IoT krawędzi).  

Metadane konfiguracji dla każdego modułu obejmują: 
* Wersja 
* Typ 
* Stan (np. uruchomiona lub zatrzymana) 
* Ponowne uruchomienie zasad 
* Repozytorium obrazów i kontener 
* Trasy dla danych wejściowych i wyjściowych 

### <a name="target-condition"></a>Warunek docelowy

Warunek docelowy jest stale obliczenia obejmują nowe urządzenia, które spełniają wymagania lub usuń urządzenia, które nie może wykonywać za pomocą czasu życia wdrożenia. Wdrożenie zostanie ponownie uaktywnić, jeśli usługa wykrywa zmiany stanu docelowego. Na przykład masz wdrożenie A mającej tags.environment warunek docelowy = "produkcyjnego". Gdy należy rozpocząć poza wdrożenia istnieją 10 urządzeń produkcyjną. Moduły pomyślnie zostały zainstalowane w tych 10 urządzeń. Stan agenta krawędzi IoT jest wyświetlany jako 10 łączna liczba urządzeń, 10 pomyślnie odpowiedzi, 0 odpowiedzi i 0 oczekujące odpowiedzi. Teraz Dodaj 5 więcej urządzeń z tags.environment = "produkcyjnego". Usługa wykryje zmianę i stan agenta krawędzi IoT pomyślnie staje się 15 łączna liczba urządzeń, 10 odpowiedzi, 0 odpowiedzi i 5 oczekujące odpowiedzi przy próbie wdrożyć pięć nowych urządzeń.

Wszelkie warunek typu Boolean na urządzeniu twins znaczników lub deviceId można użyć do wybrania urządzeń docelowych. Jeśli chcesz użyć warunku tagów, musisz dodać "tagi":{} części dwie urządzenia, w tym samym poziomie jako właściwości. [Dowiedz się więcej na temat tagów w dwie urządzenia](../iot-hub/iot-hub-devguide-device-twins.md)

Przykłady warunków docelowych:
* deviceId = "linuxprod1"
* Tags.Environment = "produkcyjnego"
* Tags.Environment = prod tags.location i = "westus"
* Tags.Environment = prod tags.location lub = "westus"
* Tags.operator = 'John' i tags.environment = prod deviceId nie = "linuxprod1"

Poniżej przedstawiono niektóre ogranicza podczas tworzenia warunku docelowych:

* W dwie urządzenia można utworzyć tylko przy użyciu znaczników lub deviceId warunek docelowy.
* Podwójny cudzysłów nie jest dozwolone w jakiejkolwiek jego części warunek docelowy. Użyj pojedynczych cudzysłowów.
* Apostrofy reprezentują wartości stanu docelowego. W związku z tym musi escape z innego pojedynczy cudzysłów pojedynczy cudzysłów, jeśli jest ona częścią nazwy urządzenia. Na przykład warunek docelowy: operator'sDevice musi mieć postać deviceId = "operator" sDevice ".
* Cyfry, litery i następujące znaki są dozwolone w docelowej values:-:.+%_#* warunek? (),=@;$

### <a name="priority"></a>Priorytet

Priorytet określa, czy wdrożenie powinien być zastosowany względem innych wdrożeń urządzenia docelowego. Priorytet wdrożenia jest dodatnią liczbą całkowitą z większą liczbą określające wyższy priorytet. Jeśli urządzenia IoT jest przeznaczona przez więcej niż jedno wdrożenie, dotyczą wdrożenia o najwyższym priorytecie.  Wdrożenia z niższym priorytetów nie są stosowane, nie są one łączone.  Jeśli urządzenie jest przeznaczona z co najmniej dwa wdrożenia z taki sam priorytet, stosuje się ostatnio utworzonego wdrożenia (określoną przez utworzenie sygnatury czasowej).

### <a name="labels"></a>Etykiety 

Etykiety są pary klucz wartość ciągu, których można użyć do filtrowania i grupy wdrożeń. Wdrożenie może mieć wiele etykiet. Etykiety są opcjonalne i nie wpływu rzeczywista konfiguracja urządzenia brzegowe IoT. 

### <a name="deployment-status"></a>Stan wdrożenia

Wdrożenia można monitorować w celu określenia, czy pomyślnie zastosowane dla wszystkich docelowych urządzeń IoT krawędzi.  Docelowe urządzenie brzegowe pojawi się w co najmniej jednej z następujących kategorii stanu: 
* **Docelowy** wyświetlane krawędzi IoT urządzenia, które odpowiada wdrożenia przeznaczonych dla warunku.
* **Rzeczywiste** pokazuje docelowym krawędzi IoT urządzenia, które nie są objęte przez inne wdrożenie o wyższym priorytecie.
* **Dobra** wyświetlane krawędzi IoT urządzenia, które mają zgłaszana z powrotem do usługi czy moduły zostały pomyślnie wdrożone. 
* **Zła** pokazuje krawędzi IoT urządzenia mają zgłaszana z powrotem do usługi, że jedna lub moduły nie zostały pomyślnie wdrożone. W celu dalszego badania błąd, zdalne nawiązywanie połączenia tego urządzenia, a następnie Wyświetl pliki dziennika.
* **Nieznany** wyświetlane krawędzi IoT urządzenia, które nie zgłosiły żadnych stanu dotyczące tego wdrożenia. W celu dalszego badania, Wyświetl pliki dziennika i informacje o usługi.

## <a name="phased-rollout"></a>Etapowego wdrażania 

Etapowego wdrażania jest ogólny proces, zgodnie z którymi operator wdraża zmiany rozszerzanie zbiór urządzeń IoT krawędzi. Celem jest zmiany stopniowo, aby zmniejszyć ryzyko dokonywania szerokości skali fundamentalne zmiany.  

Etapowe wdrażanie jest wykonywane w następujących faz i kroki: 
1. Ustanowienie środowiska testowego z urządzeń IoT krawędzi przez ustawienie tag dwie urządzenia, takie jak i udostępniania ich `tag.environment='test'`. Środowisko testowe duplikowany wdrażania po pewnym czasie będzie obowiązywać w środowisku produkcyjnym. 
1. Tworzenie wdrożenia, łącznie z modułami żądaną i konfiguracji. Warunek docelowy powinien wskazywać testu środowisko urządzenia IoT krawędzi.   
1. Sprawdź poprawność nowej konfiguracji modułu w środowisku testowym.
1. Zaktualizuj wdrożenie, aby uwzględnić podzbiór urządzenia IoT Edge produkcyjnych przez dodanie nowego znacznika do określania wartości docelowej stanu. Upewnij się również, że priorytet dla wdrożenia jest wyższy niż pozostałe obecnie wskazywane na tych urządzeniach wdrożenia 
1. Sprawdź, czy wdrożenie powiodło się na urządzeniach docelowych IoT wyświetlając stan wdrożenia.
1. Aktualizacja środowiska pod kątem wszystkich pozostałych urządzeń IoT Edge produkcyjnych.

## <a name="rollback"></a>Wycofywanie

Wdrożeń można wycofać w przypadku błędów lub błędy konfiguracji.  Ponieważ wdrożenia definiuje konfigurację modułu bezwzględny dla urządzenia IoT, dodatkowe wdrożenia może musi być wskazywane na tym samym urządzeniu z niższym priorytetem, nawet jeśli celem jest, aby usunąć wszystkie moduły.  

Wycofywanie zmian należy wykonać w następującej kolejności: 
1. Upewnij się, że drugie wdrożenie jest również wskazywane na ten sam zestaw urządzenia. Jeśli celem wycofywanie usunąć wszystkie moduły, drugie wdrożenie nie może zawierać żadnych modułów. 
1. Zmodyfikuj lub usuń wyrażenie warunku docelowej wdrożenia, które chcesz przywracać tak, aby urządzenia nie spełniają warunek określania wartości docelowej.
1. Sprawdź, czy wycofywanie zakończyło się pomyślnie, sprawdzając stan wdrożenia.
   * Wdrożenie zwrotnego wycofana powinien być już wyświetlony stan dla urządzeń, które zostały wycofane.
   * Drugie wdrożenie obejmuje teraz stan wdrożenia dla urządzeń, które zostały wycofane.


## <a name="next-steps"></a>Kolejne kroki

* Szczegółowe kroki umożliwiające tworzenie, aktualizowanie lub usuwanie wdrożenia w [wdrażanie i monitorowanie krawędzi IoT modułów na dużą skalę][lnk-howto].
* Dowiedz się więcej o innych pojęcia krawędzi IoT, takich jak [środowiska uruchomieniowego krawędzi IoT] [ lnk-runtime] i [modułów krawędzi IoT][lnk-modules].

<!-- Links -->
[lnk-lifecycle]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-runtime]: iot-edge-runtime.md
[lnk-modules]: iot-edge-modules.md
[lnk-howto]: how-to-deploy-monitor.md
