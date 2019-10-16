---
title: Korzystanie z planów odzyskiwania w ramach odzyskiwania po awarii z Azure Site Recovery
description: Dowiedz się więcej o korzystaniu z planów odzyskiwania na potrzeby odzyskiwania po awarii przy użyciu usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: raynew
ms.openlocfilehash: 9e45787e7ae39b62605f5d8a54afd4ad95c9cca7
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331915"
---
# <a name="about-recovery-plans"></a>Informacje o planach odzyskiwania

W tym artykule opisano plany odzyskiwania w programie [Azure Site Recovery](site-recovery-overview.md).

Plan odzyskiwania umożliwia zbieranie maszyn w grupach odzyskiwania. Możesz dostosować plan, dodając do niego kolejność, instrukcje i zadania. Po zdefiniowaniu planu można uruchomić na nim tryb failover.  Do maszyn można odwoływać się w wielu planach odzyskiwania, w których kolejne plany spowodują pominięcie wdrożenia/uruchomienia maszyny, jeśli zostały wcześniej wdrożone za pośrednictwem innego planu odzyskiwania.


## <a name="why-use-a-recovery-plan"></a>Dlaczego warto korzystać z planu odzyskiwania?

Plan odzyskiwania ułatwia zdefiniowanie systematycznego procesu odzyskiwania przez utworzenie małych niezależnych jednostek, które można przełączyć do trybu failover. Jednostka zazwyczaj reprezentuje aplikację w Twoim środowisku. Plan odzyskiwania definiuje sposób przełączenia maszyn w tryb failover oraz kolejność, w jakiej zostały one uruchomione po przejściu w tryb pracy awaryjnej. Użyj planów odzyskiwania, aby:

* Modelowanie aplikacji w pobliżu jej zależności.
* Automatyzuj zadania odzyskiwania, aby zmniejszyć RTO.
* Sprawdź, czy jesteś przygotowany do migracji lub odzyskiwania po awarii, upewniając się, że Twoje aplikacje są częścią planu odzyskiwania.
* Uruchom test pracy w trybie failover w planach odzyskiwania, aby upewnić się, że odzyskiwanie po awarii lub migracja działa zgodnie z oczekiwaniami.


## <a name="model-apps"></a>Aplikacje modelu

> [!NOTE]
> Do jednego planu odzyskiwania można dodać do 100 chronionych wystąpień.

Można zaplanować i utworzyć grupę odzyskiwania, aby przechwytywać właściwości specyficzne dla aplikacji. Rozważmy na przykład typową aplikację trójwarstwowej z zapleczem programu SQL Server, programem pośredniczącym i frontonem sieci Web. Zazwyczaj dostosowuje się plan odzyskiwania, tak aby maszyny w poszczególnych warstwach były uruchamiane w odpowiedniej kolejności po przejściu w tryb failover.

- Zaplecze SQL należy uruchomić najpierw, drugie oprogramowanie, a wreszcie fronton sieci Web.
- Ta kolejność uruchamiania zapewnia, że aplikacja działa w momencie uruchomienia ostatniego komputera.
- Ta kolejność zapewnia, że po uruchomieniu oprogramowania pośredniczącego i próbie nawiązania połączenia z warstwą SQL Server warstwa SQL Server jest już uruchomiona. 
- Ta kolejność pomaga również upewnić się, że serwer frontonu zostanie uruchomiony jako ostatni, dzięki czemu użytkownicy końcowi nie łączą się z adresem URL aplikacji, zanim wszystkie składniki zostaną uruchomione i aplikacja będzie gotowa do akceptowania żądań.

Aby utworzyć to zamówienie, należy dodać grupy do grupy odzyskiwania i dodać maszyny do grup.
- W przypadku określenia kolejności jest używana sekwencjonowanie. Akcje są uruchamiane równolegle, tam gdzie to konieczne, w celu usprawnienia RTO odzyskiwania aplikacji.
- Maszyny w pojedynczej grupie działają równolegle w trybie failover.
- Maszyny znajdujące się w różnych grupach są przełączane w tryb failover w kolejności grupy, tak aby komputery z grupą 2 uruchomiły przełączenia w tryb pracy awaryjnej, tylko po przełączeniu się i uruchomieniu wszystkich

    ![Przykładowy plan odzyskiwania](./media/recovery-plan-overview/rp.png)

Dzięki temu dostosowaniu, co się dzieje, gdy uruchomisz tryb failover w ramach planu odzyskiwania: 

1. Krok zamykania powoduje próbę wyłączenia maszyn lokalnych. Wyjątek jest uruchamiany w przypadku uruchomienia testowego trybu failover, w którym to przypadku lokacja główna nadal działa. 
2. Zamknięcie wyzwala równoległą pracę w trybie failover dla wszystkich maszyn w planie odzyskiwania.
3. Tryb failover przygotowuje dyski maszyny wirtualnej przy użyciu zreplikowanych danych.
4. Grupy uruchamiania działają w kolejności i uruchamiają maszyny w każdej grupie. Najpierw zostanie uruchomiona Grupa 1, a następnie grupa 2, a na koniec Grupa 3. Jeśli w dowolnej grupie znajduje się więcej niż jeden komputer, wszystkie maszyny są uruchamiane równolegle.


## <a name="automate-tasks"></a>Automatyzowanie zadań

Odzyskiwanie dużych aplikacji może być złożonym zadaniem. Ręczne kroki sprawiają, że proces podatny na błędy, a osoba pracująca w trybie failover może nie wiedzieć o wszystkich złożonego aplikacji. Można użyć planu odzyskiwania, aby wprowadzić zamówienie i zautomatyzować akcje potrzebne w każdym kroku, korzystając z Azure Automation elementów Runbook w celu przełączenia w tryb failover do platformy Azure lub skryptów. W przypadku zadań, które nie mogą być zautomatyzowane, można wstawiać pauzy dla akcji ręcznych w planach odzyskiwania. Istnieje kilka typów zadań, które można skonfigurować:

* **Zadania na maszynie wirtualnej platformy Azure po**przejściu w tryb failover: w przypadku przełączenia awaryjnego na platformę Azure zwykle trzeba wykonać akcje, aby można było nawiązać połączenie z maszyną wirtualną po zakończeniu pracy w trybie failover. Na przykład: 
    * Utwórz publiczny adres IP na maszynie wirtualnej platformy Azure.
    * Przypisz sieciową grupę zabezpieczeń do karty sieciowej maszyny wirtualnej platformy Azure.
    * Dodaj moduł równoważenia obciążenia do zestawu dostępności.
* **Zadania w ramach maszyny wirtualnej po**przejściu do trybu failover: te zadania zwykle ponownie konfigurują aplikację uruchomioną na komputerze, tak aby nadal działała poprawnie w nowym środowisku. Na przykład:
    * Zmodyfikuj parametry połączenia z bazą danych w ramach maszyny.
    * Zmień konfigurację lub reguły serwera sieci Web.


## <a name="test-failover"></a>Testowanie pracy w trybie failover

Można użyć planu odzyskiwania, aby wyzwolić test pracy w trybie failover. Należy stosować następujące najlepsze rozwiązania:

- Przed uruchomieniem pełnego trybu failover zawsze Ukończ test pracy w trybie failover w aplikacji. Testowanie pracy w trybie failover pomaga sprawdzić, czy aplikacja znajduje się w witrynie odzyskiwania.
- Jeśli okaże się, że pominięto coś, wyzwól oczyszczanie, a następnie ponownie uruchom test pracy w trybie failover. 
- Przeprowadź test pracy awaryjnej wiele razy, dopóki nie masz pewności, że aplikacja będzie bezproblemowo odzyskiwana.
- Ponieważ każda aplikacja jest unikatowa, należy utworzyć plany odzyskiwania, które są dostosowane dla każdej aplikacji, i uruchomić test pracy w trybie failover na każdym z nich.
- Aplikacje i ich zależności zmieniają się często. Aby zapewnić aktualność planów odzyskiwania, Uruchom test pracy w trybie failover dla każdej aplikacji co kwartał.

    ![Zrzut ekranu przykładowego planu odzyskiwania testowego w Site Recovery](./media/recovery-plan-overview/rptest.png)

## <a name="watch-the-video"></a>Obejrzyj wideo

Obejrzyj szybki przykład wideo pokazujący przejście w tryb failover na potrzeby dwuwarstwowej aplikacji WordPress.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Następne kroki

- [Utwórz](site-recovery-create-recovery-plans.md) plan odzyskiwania.
- Dowiedz się więcej o [uruchamianiu trybu failover](site-recovery-failover.md).  
