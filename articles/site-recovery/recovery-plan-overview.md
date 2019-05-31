---
title: Odzyskiwanie po awarii przy użyciu usługi Azure Site Recovery przy użyciu planów odzyskiwania | Dokumentacja firmy Microsoft
description: Więcej informacji na temat odzyskiwania po awarii przy użyciu usługi Azure Site Recovery przy użyciu planów odzyskiwania.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 32cad7005b2b4da830ac3febf6da847933967a3d
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66400026"
---
# <a name="about-recovery-plans"></a>Informacje o planach odzyskiwania

W tym artykule opisano planów odzyskiwania w [usługi Azure Site Recovery](site-recovery-overview.md).

Plan odzyskiwania zbiera maszyn w grupach odzyskiwania. Plan można dostosować, dodając do niej zamówienia, instrukcje i zadania. Po zdefiniowaniu planu przejścia w tryb failover można uruchomić na nim.



## <a name="why-use-a-recovery-plan"></a>Dlaczego warto używać planu odzyskiwania?

Plan odzyskiwania pomaga zdefiniować proces odzyskiwania systematyczne, tworząc małe niezależnych zespołów, które możesz przełączać awaryjnie. Jednostka reprezentuje zwykle aplikacji w danym środowisku. Plan odzyskiwania definiuje, jak komputerów w trybie Failover i sekwencji mogą uruchomić po włączeniu trybu failover. Użyj plany odzyskiwania, aby:

* Modelowanie aplikacji wokół jego zależności.
* Automatyzuj zadania odzyskiwania, aby zmniejszyć cel czasu odzyskiwania.
* Upewnij się, że masz wszystko gotowe do odzyskiwania po awarii ani migracji poprzez zapewnienie, że Twoje aplikacje są częścią planu odzyskiwania.
* Uruchom testowanie trybu failover na plany odzyskiwania, aby upewnić się, że odzyskiwanie po awarii ani migracji działa zgodnie z oczekiwaniami.


## <a name="model-apps"></a>Model aplikacji

Można zaplanować i Utwórz grupę odzyskiwania, aby przechwycić właściwości specyficzne dla aplikacji. Na przykład rozważmy Typowa trójwarstwowa aplikacja za pomocą programu SQL server, wewnętrznej bazy danych, oprogramowanie pośredniczące i frontonu sieci web. Zazwyczaj planu odzyskiwania można dostosować tak, aby komputery w każdej warstwie start we właściwej kolejności po włączeniu trybu failover.

    - Wewnętrznej bazy danych SQL powinna zaczynać się najpierw, oprogramowanie pośredniczące następnej, a na końcu frontonu sieci web.
    - Ta kolejność start gwarantuje, że aplikacja działa przy uruchamianiu ostatnia maszyna.
    - Ta kolejność gwarantuje, że jeśli oprogramowanie pośredniczące, które próbuje nawiązać połączenie z warstwy programu SQL Server, warstwy programu SQL Server jest już uruchomiony. 
    - To zamówienie pomaga również zapewnić, że serwer frontonu uruchamia ostatniego, tak, aby użytkownicy końcowi nie Podłączaj się do adresu URL aplikacji, zanim wszystkie składniki są włączone i uruchomione oraz aplikacji jest gotowy do akceptowania żądań.

Aby utworzyć to zamówienie, Dodaj grupy do grupy odzyskiwania i Dodaj maszyny do grup.
- W przypadku, gdy zamówienie zostanie określony, używany jest sekwencjonowania. Akcje wykonywane równolegle, gdzie jest to odpowiednie poprawić odzyskiwania aplikacji cel czasu odzyskiwania.
- Maszyny w pojedynczej grupy pracy awaryjnej w sposób równoległy.
- Maszyny w różnych grupach przełączyć w tryb failover w kolejności grupy tak, aby grupa 2 maszyny zaczynają ich pracy awaryjnej, tylko wtedy, gdy wszystkie maszyny w grupy 1 zostały przełączone w tryb failover i uruchomiona.

    ![Przykładowy plan odzyskiwania](./media/recovery-plan-overview/rp.png)

Za pomocą to dostosowanie w miejscu Oto, co się stanie po uruchomieniu trybu failover w planie odzyskiwania: 

1. Krok zamykania polegające na wyłączanie maszyn lokalnych. Wyjątkiem jest, jeśli możesz uruchomić test trybu failover, w którym to przypadku lokacja główna będzie nadal działać. 
2. Zamknięcie wyzwala równoległe trybu failover wszystkich maszyn w planie odzyskiwania.
3. Przełączenie w tryb failover przygotowuje dyski maszyny wirtualnej przy użyciu replikowanych danych.
4. Grupy startowe są uruchamiane w kolejności, a następnie uruchomić maszyny w każdej grupie. Po pierwsze Grupa 1 działa, następnie grupa 2, a na końcu grupy 3. Jeśli istnieje więcej niż jednym komputerze w dowolnej grupie, wszystkie maszyny Uruchom równolegle.


## <a name="automate-tasks"></a>Automatyzacja zadań

Odzyskiwanie dużych aplikacji może być złożonym zadaniem. Wymagane ręczne wykonanie czynności dzięki czemu proces wprowadzania podatne na błędy i uruchamiających przełączenie w tryb failover może nie być świadome wszystkich niewymagającego aplikacji. Umożliwia nakłada zamówienie planu odzyskiwania i zautomatyzować wymagane przeprowadzenie kolejnych czynności na każdym etapie, za pomocą elementów runbook usługi Azure Automation do trybu failover na platformie Azure lub skryptów. Zadania, które nie mogą być zautomatyzowane można wstawić przerw na ręczne akcje wykonywane do planów odzyskiwania. Istnieje kilka typów zadań, które można skonfigurować:

* **Zadania na maszynie Wirtualnej platformy Azure po włączeniu trybu failover**: Po użytkownik przechodzenia w tryb failover na platformie Azure, zazwyczaj należy wykonywać akcje, dzięki czemu można połączyć się z maszyną wirtualną po włączeniu trybu failover. Na przykład: 
    * Utwórz publiczny adres IP maszyny wirtualnej platformy Azure.
    * Przypisywanie sieciowej grupy zabezpieczeń z kartą sieciową z maszyny Wirtualnej platformy Azure.
    * Dodaj moduł równoważenia obciążenia do zestawu dostępności.
* **Zadania wewnątrz maszyny Wirtualnej po włączeniu trybu failover**: Te zadania zazwyczaj ponownie skonfigurować aplikację uruchomioną na maszynie, dzięki czemu będzie on nadal działać prawidłowo w nowym środowisku. Na przykład:
    * Zmodyfikuj parametry połączenia bazy danych na maszynie.
    * Zmiana konfiguracji serwera sieci web lub reguły.


## <a name="test-failover"></a>Testowanie pracy w trybie failover

Plan odzyskiwania służy do wyzwalania testowy tryb failover. Należy stosować następujące najlepsze rozwiązania:

- Przed uruchomieniem pełnego trybu failover zawsze wykonać test trybu failover w aplikacji. Testowych trybów Failover ułatwia sprawdzanie, czy aplikacja przejdzie do lokalizacji odzyskiwania.
- Jeśli okaże się, że zostały pominięte, coś, Wyzwól czystego zapasowej, a następnie ponownie uruchomić test trybu failover. 
- Uruchom testowanie trybu failover wielokrotnie, dopóki nie masz pewności, sprawnie odzyskuje aplikacji.
- Ponieważ każda aplikacja jest unikatowy, należy tworzyć plany odzyskiwania, które są dostosowane do poszczególnych aplikacji, a następnie uruchomić testowy tryb failover na każdym.
- Aplikacje i ich zależności zmieniają się często. Aby upewnić się, że plany odzyskiwania są aktualne, należy uruchomić test trybu failover dla każdej aplikacji co kwartał.

    ![Zrzut ekranu przykładu testowanie planu odzyskiwania w usłudze Site Recovery](./media/recovery-plan-overview/rptest.png)

## <a name="watch-the-video"></a>Obejrzyj film

Obejrzyj film prosty przykład przedstawiający na kliknięcia w trybie failover dwuwarstwowej aplikacji WordPress.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Kolejne kroki

- [Utwórz](site-recovery-create-recovery-plans.md) planu odzyskiwania.
- Dowiedz się więcej o [uruchamiania trybu failover](site-recovery-failover.md).  
