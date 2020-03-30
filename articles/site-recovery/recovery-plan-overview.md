---
title: Plany odzyskiwania w usłudze Azure Site Recovery – informacje
description: Dowiedz się więcej o planach odzyskiwania w usłudze Azure Site Recovery.
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: beb92bd62d011ef8aaf304dbb769e7694e6d7e60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257773"
---
# <a name="about-recovery-plans"></a>Informacje o planach odzyskiwania

Ten artykuł zawiera omówienie planów odzyskiwania w [usłudze Azure Site Recovery](site-recovery-overview.md).

Plan odzyskiwania gromadzi maszyny do grup odzyskiwania w celu pracy awaryjnej. Plan odzyskiwania pomaga zdefiniować proces systematycznego odzyskiwania, tworząc małe niezależne jednostki, które można awaryjnie. Jednostka zazwyczaj reprezentuje aplikację w twoim środowisku.

- Plan odzyskiwania definiuje sposób pracy maszyn w pracy awaryjnej i sekwencję, w której są uruchamiane po przewijaniu awaryjnym.
- Plany odzyskiwania są używane do pracy awaryjnej na platformie Azure, ale nie można ich używać do powrotu po awarii z platformy Azure.
- Do jednego planu odzyskiwania można dodać do 100 chronionych wystąpień.
- Plan można dostosować, dodając do niego kolejność, instrukcje i zadania.
- Po zdefiniowaniu planu można uruchomić na nim funkcję failover.
- Do maszyn można odwoływać się w wielu planach odzyskiwania, w których kolejne plany pomijają wdrażanie/uruchamianie komputera, jeśli został wcześniej wdrożony przy użyciu innego planu odzyskiwania.



### <a name="why-use-a-recovery-plan"></a>Dlaczego warto skorzystać z planu naprawczego?

Użyj planów odzyskiwania, aby:

* Modelowanie aplikacji wokół jej zależności.
* Zautomatyzuj zadania odzyskiwania, aby skrócić cel czasu odzyskiwania (RTO).
* Sprawdź, czy jesteś przygotowany do migracji lub odzyskiwania po awarii, upewniając się, że aplikacje są częścią planu odzyskiwania.
* Uruchom testy awaryjne w planach odzyskiwania, aby upewnić się, że odzyskiwanie po awarii lub migracja działa zgodnie z oczekiwaniami.


## <a name="model-apps"></a>Modelowanie aplikacji 
Można zaplanować i utworzyć grupę odzyskiwania do przechwytywania właściwości specyficznych dla aplikacji. Na przykład rozważmy typową aplikację trójwarstwową z zapleczem serwera SQL, oprogramowaniem pośredniczącym i frontendem sieci web. Zazwyczaj można dostosować plan odzyskiwania tak, aby maszyny w każdej warstwie rozpocząć w odpowiedniej kolejności po przewijeniu w pracę awaryjną.

- Wewnętrznej bazy danych SQL należy uruchomić najpierw, oprogramowanie pośredniczące obok i wreszcie wewnętrznej wewnętrznej bazy danych sieci web.
- Ta kolejność uruchamiania zapewnia, że aplikacja działa do czasu uruchomienia ostatniego komputera.
- Ta kolejność zapewnia, że gdy oprogramowanie pośredniczące uruchamia się i próbuje połączyć się z warstwą PROGRAMU SQL Server, warstwa programu SQL Server jest już uruchomiona. 
- Ta kolejność pomaga również upewnić się, że serwer frontonu uruchamia się jako ostatni, dzięki czemu użytkownicy końcowi nie łączą się z adresem URL aplikacji, zanim wszystkie składniki będą uruchomione, a aplikacja będzie gotowa do akceptowania żądań.

Aby utworzyć tę kolejność, należy dodać grupy do grupy odzyskiwania i dodać maszyny do grup.
- W przypadku gdy określono kolejność, używane jest sekwencjonowanie. Akcje uruchamiane równolegle, w stosownych przypadkach, w celu poprawy odzyskiwania aplikacji RTO.
- Maszyny w jednej grupie pracy awaryjnej równolegle.
- Maszyny w różnych grupach są awaryjne w kolejności grupowej, dzięki czemu maszyny grupy 2 rozpoczynają pracę awaryjną dopiero po zakończeniu pracy i uruchomieniu wszystkich maszyn w grupie 1.

    ![Przykładowy plan odzyskiwania](./media/recovery-plan-overview/rp.png)

Po wprowadzeniu tego dostosowania w miejscu, oto co się dzieje po uruchomieniu pracy awaryjnej w planie odzyskiwania: 

1. Krok zamknięcia próbuje wyłączyć maszyny lokalne. Wyjątkiem jest uruchomienie testu pracy awaryjnej, w którym to przypadku lokacja główna kontynuuje uruchamianie. 
2. Zamknięcie wyzwala równoległe pracy awaryjnej wszystkich komputerów w planie odzyskiwania.
3. Funkcja failover przygotowuje dyski maszyny wirtualnej przy użyciu replikowanych danych.
4. Grupy startowe działają w kolejności i uruchamiają maszyny w każdej grupie. Najpierw działa grupa 1, następnie grupa 2, a na koniec grupa 3. Jeśli w dowolnej grupie znajduje się więcej niż jedna maszyna, wszystkie maszyny uruchamiają się równolegle.


## <a name="automate-tasks-in-recovery-plans"></a>Automatyzacja zadań w planach odzyskiwania

Odzyskiwanie dużych aplikacji może być złożonym zadaniem. Ręczne kroki sprawiają, że proces jest podatny na błędy, a osoba uruchamiana w trybie failover może nie być świadoma wszystkich zawiłości aplikacji. Za pomocą planu odzyskiwania można narzucić zamówienie i zautomatyzować akcje wymagane na każdym kroku przy użyciu śreżystek systemu Azure Automation do pracy awaryjnej na platformie Azure lub skryptów. W przypadku zadań, których nie można zautomatyzować, można wstawić pauzy dla akcji ręcznych do planów odzyskiwania. Istnieje kilka typów zadań, które można skonfigurować:

* **Zadania na maszynie Wirtualnej platformy Azure po przełączeniu w stan failover:** W przypadku awarii na platformie Azure, zazwyczaj należy wykonać akcje, dzięki czemu można połączyć się z maszyną wirtualną po przełączeniu awaryjnym. Przykład: 
    * Utwórz publiczny adres IP na maszynie Wirtualnej platformy Azure.
    * Przypisywanie sieciowej grupy zabezpieczeń do karty sieciowej maszyny Wirtualnej platformy Azure.
    * Dodaj moduł równoważenia obciążenia do zestawu dostępności.
* **Zadania wewnątrz maszyny Wirtualnej po pracy awaryjnej:** Te zadania zazwyczaj ponownie skonfigurować aplikację uruchomiono na komputerze, tak aby nadal działać poprawnie w nowym środowisku. Przykład:
    * Zmodyfikuj parametry połączenia bazy danych wewnątrz urządzenia.
    * Zmień konfigurację lub reguły serwera sieci web.


### <a name="run-a-test-failover-on-recovery-plans"></a>Uruchamianie testowej pracy awaryjnej w planach odzyskiwania

Można użyć planu odzyskiwania, aby wyzwolić test pracy awaryjnej. Skorzystaj z następujących najlepszych rozwiązań:

- Zawsze należy ukończyć test pracy awaryjnej w aplikacji, przed uruchomieniem pełnej pracy awaryjnej. Testowanie pracy awaryjnej pomaga sprawdzić, czy aplikacja pojawia się w witrynie odzyskiwania.
- Jeśli okaże się, że coś przegapiłeś, wyzwuś oczyszczanie, a następnie uruchom ponownie test w pracy awaryjnej. 
- Uruchom test pracy awaryjnej wiele razy, dopóki nie masz pewności, że aplikacja odzyskuje płynnie.
- Ponieważ każda aplikacja jest unikatowa, należy utworzyć plany odzyskiwania, które są dostosowane dla każdej aplikacji i uruchomić test pracy awaryjnej na każdym.
- Aplikacje i ich zależności często się zmieniają. Aby upewnić się, że plany odzyskiwania są aktualne, uruchom test pracy awaryjnej dla każdej aplikacji co kwartał.

    ![Zrzut ekranu przedstawiający przykładowy plan odzyskiwania testów w układzie Site Recovery](./media/recovery-plan-overview/rptest.png)

## <a name="watch-a-recovery-plan-video"></a>Obejrzyj film o planie odzyskiwania

Obejrzyj krótki przykładowy film pokazujący tryb failover po kliknięciu dla planu odzyskiwania dla dwuwarstwowej aplikacji WordPress.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Następne kroki

- [Tworzenie](site-recovery-create-recovery-plans.md) planu odzyskiwania.
- [Uruchom trybu](site-recovery-failover.md) failover. 
