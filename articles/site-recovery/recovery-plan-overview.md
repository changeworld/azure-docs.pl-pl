---
title: W usłudze Azure Site Recovery przy użyciu planów odzyskiwania | Dokumentacja firmy Microsoft
description: Więcej informacji na temat planów odzyskiwania w usłudze Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: raynew
ms.openlocfilehash: 871c9e8404438f966cab2fc5ab782e254295569e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30181983"
---
# <a name="about-recovery-plans"></a>Plany odzyskiwania — informacje

W tym artykule opisano planów odzyskiwania w [usługi Azure Site Recovery](site-recovery-overview.md).

Plan odzyskiwania zbiera maszyn w grupach odzyskiwania. Plan można dostosować, dodając do niego kolejności, instrukcje i zadania. Po zdefiniowaniu planu można uruchomić na nim trybu failover.





## <a name="why-use-a-recovery-plan"></a>Dlaczego warto używać planu odzyskiwania?

Plan odzyskiwania pomaga zdefiniować proces systematyczne odzyskiwania, tworząc małych jednostek niezależne, które możesz w trybie Failover. Jednostka zazwyczaj reprezentuje aplikacji w danym środowisku. Plan odzyskiwania definiuje sposób maszyn w tryb failover i sekwencji uruchamiają po w tryb failover. Użyj planów odzyskiwania:

* Model aplikacji wokół jego zależności.
* Automatyzowanie zadań odzyskiwania, aby zmniejszyć RTO.
- Sprawdź, czy jest przygotowane do migracji oraz odzyskiwanie awaryjne, zapewniając, że aplikacje są częścią planu odzyskiwania.
* Uruchom test trybu failover w planie odzyskiwania, aby upewnić się, że odzyskiwania po awarii lub migracji działa zgodnie z oczekiwaniami.


## <a name="model-apps"></a>Model aplikacji

Można zaplanować i utworzyć grupę odzyskiwania do przechwytywania właściwości specyficzne dla aplikacji. Na przykład zastanówmy Typowa trójwarstwowa aplikacja z programem SQL server wewnętrznej bazy danych, oprogramowanie pośredniczące i frontonu sieci web. Zazwyczaj planu odzyskiwania można dostosować tak, aby w każdej warstwie uruchomić we właściwej kolejności po pracy awaryjnej.

    - Wewnętrznej bazy danych SQL powinna zaczynać się najpierw, oprogramowanie pośredniczące następnej, a na końcu frontonu sieci web.
    - To zamówienie start gwarantuje, że aplikacja działa w czasie uruchamiania Ostatnia maszyna.
    - To zamówienie gwarantuje, że gdy oprogramowanie pośredniczące, które próbuje nawiązać połączenia z warstwy programu SQL Server, warstwy programu SQL Server jest już uruchomiony. 
    - Upewnij się, że serwer frontonu rozpoczyna ostatnich, dzięki czemu użytkownicy końcowi nie łączyć się adres URL aplikacji, zanim wszystkie składniki są uruchomione i uruchomiona, a aplikacja jest gotowa do akceptowania żądań pomaga również w tej kolejności.

Aby utworzyć to zamówienie, dodać grupy do grupy odzyskiwania i Dodaj maszyny do grup. 
    - W przypadku, gdy określono kolejności, sekwencjonowania jest używany. Akcje uruchamiane równolegle odpowiednio zwiększyć odzyskiwania aplikacji RTO.
    - Maszyny w jednej grupie w trybie Failover równolegle.
    - Maszyny w różnych grupach przełączyć według grupy, grupa 2 maszyn rozpocząć ich pracy awaryjnej tylko wtedy, gdy wszystkie komputery z grupy 1 zostały przełączone do trybu failover i uruchomiona.

    ![Przykład planu odzyskiwania](./media/recovery-plan-overview/rp.png)

Z tym dostosowanie w miejscu Oto, co się stanie po uruchomieniu trybu failover planu odzyskiwania: 

1. Krok zamknięcia próbuje Wyłącz maszyny lokalnej. Wyjątek stanowi, jeżeli możesz uruchomić test trybu failover, w którym to przypadku lokacji głównej kontynuuje działanie. 
2. Zamknięcie wyzwala równoległych trybu failover wszystkich maszyn w planie odzyskiwania.
3. Tryb failover przygotowuje dysków maszyny wirtualnej przy użyciu zreplikowanych danych.
4. Grupy uruchamiania są uruchamiane w kolejności i uruchom maszyny w każdej grupie. Po pierwsze Grupa 1 wykonywany, następnie grupa 2, a na końcu grupy 3. Jeśli istnieje więcej niż jedna maszyna w dowolnej grupie, a następnie ją uruchom wszystkie maszyny równolegle.


## <a name="automate-tasks"></a>Automatyzowania zadań

Odzyskiwanie dużych aplikacji może być złożonym zadaniem. Ręczne uproszczenia procesu podatne na błędy i uruchamiających przejście w tryb failover może nie być świadomy wszystkich aplikacji mogli dokładnie zapoznać. Umożliwia nałożyć kolejności planu odzyskiwania i zautomatyzować wymagane w każdym kroku czynności przy użyciu elementu runbook usługi Automatyzacja Azure w trybie failover do platformy Azure lub skryptów. Zadania, które nie mogły zostać zautomatyzowane można wstawić wstrzymuje działanie do działania ręczne do planów odzyskiwania. Istnieje kilka typów zadań, które można skonfigurować:

* **Zadania na maszynie Wirtualnej Azure po pracy awaryjnej**: podczas powrotu po awarii za pośrednictwem na platformie Azure, zazwyczaj należy wykonać akcje, dzięki czemu można łączyć się do maszyny Wirtualnej po pracy awaryjnej. Na przykład: 
    * Utwórz publiczny adres IP na maszynie Wirtualnej Azure.
    * Przypisywanie sieciowej grupy zabezpieczeń do karty sieciowej maszyny wirtualnej Azure.
    * Dodaj usługi równoważenia obciążenia do zestawu dostępności.
* **Zadania wewnątrz maszyn wirtualnych po pracy awaryjnej**: te zadania zazwyczaj ponownie skonfigurować aplikacji uruchomionych na komputerze, dzięki czemu nadal działają prawidłowo w nowym środowisku. Na przykład:
    * Zmodyfikuj parametry połączenia bazy danych na maszynie.
    * Zmień konfigurację serwera sieci web lub reguły.


## <a name="test-failover"></a>Testowanie trybu failover

Plan odzyskiwania służy do wyzwalania test trybu failover. Należy stosować następujące najlepsze rozwiązania:

- Przed uruchomieniem pełne trybu failover zawsze wykonać test trybu failover w aplikacji. Testowy tryb failover ułatwiają Sprawdź, czy aplikacja pojawia się w lokacji odzyskiwania.
- Jeśli okaże się, że element nie został pominięty, wyzwolenia czystej w górę, a następnie uruchom ponownie testowania trybu failover. 
- Uruchom test trybu failover wielokrotnie, dopóki nie masz pewności, sprawnie odzyskuje aplikację.
- Ponieważ każda aplikacja jest unikatowa, należy utworzyć plany odzyskiwania, które są dostosowane dla każdej aplikacji i uruchom test trybu failover na każdym.
- Aplikacje i ich zależności często zmieniana. Aby upewnić się, że planów odzyskiwania są aktualne, uruchom test trybu failover dla każdej aplikacji, co kwartał.

    ![Zrzut ekranu przedstawiający przykładowy plan odzyskiwania testowy w usłudze Site Recovery](./media/recovery-plan-overview/rptest.png)

## <a name="watch-the-video"></a>Obejrzyj film

Obejrzyj film prosty przykład przedstawiający awarię i kliknij dwuwarstwowej aplikacji WordPress.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Kolejne kroki

- [Utwórz](site-recovery-create-recovery-plans.md) planu odzyskiwania.
* Dowiedz się więcej o [uruchamiania trybu failover](site-recovery-failover.md).  
