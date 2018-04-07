---
title: Analiza przypadku Azure - Umbraco bazy danych Azure SQL | Dokumentacja firmy Microsoft
description: Więcej informacji na temat skalowania usługi tysięcy dzierżawcy w chmurze i jak Umbraco używana baza danych SQL, aby szybko ustanowić
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 1079adb6ef8a206506823fdee6721aabbf857b4d
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="umbraco-uses-azure-sql-database-to-quickly-provision-and-scale-services-for-thousands-of-tenants-in-the-cloud"></a>Program Umbraco używa bazy danych SQL Azure szybko udostępnić skali usługi i tysiące dzierżawcy w chmurze
![Umbraco Logo](./media/sql-database-implementation-umbraco/umbracologo.png)

Program Umbraco jest popularnych open source zawartości — system zarządzania (CMS) można uruchomić niczego z małych kampanii lub broszura lokacji do złożone aplikacje dla firm z listy Fortune 500 i globalnych nośnika witryn sieci Web. 

> "Istnieje bardzo duże społeczności deweloperów, którzy z deweloperami więcej niż 100 000 na naszych forach i więcej niż 350,000 witryn, które są aktywne, systemem Umbraco na korzystanie z systemu".
> 
> — Umbraco Witold Christensen, realizacji techniczne
> 
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-Case-Study-Umbraco/player]
> 
> 

Aby uprościć wdrożenia klienta, Umbraco dodane Umbraco jako — usługa (UaaS): oferty oprogramowania — jako — usługa (SaaS), która eliminuje potrzebę stosowania wdrożeń lokalnych zawiera wbudowane skalowanie i usuwa zarządzania narzut umożliwia deweloperom skoncentrować się na produktu innowacji zamiast rozwiązania zarządzania. Program Umbraco jest zapewnienie świadczenia te polegając na elastyczny model (PaaS) platformy jako usługi udostępniane przez Microsoft Azure.

UaaS umożliwia klientom SaaS, użyj funkcji Umbraco CMS, które zostały wcześniej poza ich zasięgu. Ci klienci są udostępniane z środowiska CMS pracy, który zawiera produkcyjną bazę danych. Klientów można dodać do dwóch dodatkowych baz danych do prac deweloperskich i środowisk przemieszczania, w zależności od wymagań. Po zażądaniu nowego środowiska zautomatyzowany proces przypisuje klienta bazy danych automatycznie. Nowa baza danych jest gotowy w sekundach, ponieważ bazy danych już zostały wstępnie udostępnione przez program Umbraco z puli elastycznej Azure dostępnych baz danych (zobacz rysunek 1).

![Cykl życia inicjowania obsługi administracyjnej Umbraco](./media/sql-database-implementation-umbraco/figure1.png)

Rysunek 1. Inicjowanie obsługi cyklu życia Umbraco jako usługa (UaaS)

## <a name="azure-elastic-pools-and-automation-simplify-deployments"></a>Pule elastyczne Azure i automatyzację uprościć wdrożenia
Z bazy danych SQL Azure i innymi usługami Azure Umbraco klienci mogą samodzielnie inicjować środowiskami i Umbraco łatwo monitorowanie i zarządzanie bazami danych w ramach intuicyjne przepływu pracy:

1. Provision
   
   Program Umbraco przechowuje pojemności 200 dostępnych wstępnie przygotowany baz danych z puli elastycznej. Po zarejestrowaniu się nowego nabywcy dla UaaS Umbraco zapewnia klienta w nowym środowisku SMF w najbliższym czasie rzeczywistym przez przypisywanie ich bazy danych z puli dostępności.
   
   Gdy puli dostępności osiągnie progu, tworzona jest nowa pula elastycznej i nowych baz danych są wstępnie przygotowany do przypisania do klientów zgodnie z potrzebami.
   
   Implementacja jest pełni zautomatyzowany przy użyciu biblioteki zarządzania C# i kolejek usługi Azure Service Bus.
2. Utilize
   
   Klienci użyć jednej do trzech środowiskach (do produkcji, tymczasowego, i/lub Programowanie), każdy z własną bazę danych. Odbiorcy są bazy danych w pule elastyczne, dzięki czemu program Umbraco umożliwia wydajne skalowanie bez konieczności nadmiernego udostępniania.
   
   ![Przegląd projektu Umbraco](./media/sql-database-implementation-umbraco/figure2.png)
   
   ![Szczegóły projektu Umbraco](./media/sql-database-implementation-umbraco/figure3.png)
   
   Rysunek 2. Program Umbraco jako — usługa (UaaS) klienta witryny sieci Web, przedstawiający Przegląd projektu i szczegóły
   
   Baza danych SQL Azure korzysta jednostki transakcji bazy danych (Dtu), aby reprezentować względną wydajność wymagane dla transakcji bazy danych rzeczywistych. Dla klientów UaaS baz danych jest zwykle używana w Dtu około 10, ale każda ma elastyczność skalowania na żądanie. Oznacza to, że UaaS można zapewnić, że klienci zawsze są niezbędne zasoby, nawet w godzinach szczytu. Na przykład podczas ostatniego zdarzenia sportowych niedzielę wieczorem jeden UaaS doświadczonym bazy danych klienta pików maksymalnie 100 jednostek Dtu w czasie trwania gry. Azure pule elastyczne możliwe Umbraco do obsługi tego wysokie zapotrzebowanie, bez spadku wydajności.
3. Monitorowanie
   
   Monitory Umbraco bazy danych za pomocą pulpitów nawigacyjnych w portalu Azure, wraz z alertów e-mail niestandardowe działania.
4. Odzyskiwanie po awarii
   
   Platforma Azure oferuje dwie opcje odzyskiwania awaryjnego (DR): aktywna replikacja geograficzna i przywracaniem geograficznym. Firmy, należy wybrać opcję odzyskiwania po awarii zależy od jego [cele ciągłość prowadzenia działalności biznesowej](sql-database-business-continuity.md).
   
   aktywna replikacja geograficzna udostępnia najszybszą poziom odpowiedzi w przypadku przestoju. Przy użyciu aktywna replikacja geograficzna, można utworzyć maksymalnie cztery elementów dodatkowych do odczytu na serwerach w różnych regionach, a następnie można zainicjować trybu failover do dowolnego pomocnicze bazy danych w przypadku awarii.
   
   Umbraco nie wymaga — replikacja geograficzna, ale jej zalet Azure przywracaniem geograficznym do zapewnienia minimalnej przestoju w przypadku awarii. geograficzne polega na kopie zapasowe bazy danych w geograficznie nadmiarowego magazynu Azure. Która umożliwia przywracanie z kopii zapasowej w przypadku wystąpienia awarii w regionie podstawowym.
5. Usuwanie
   
   Po usunięciu środowiska projektu, wszystkie skojarzone bazy danych (Programowanie, przemieszczania lub na żywo) zostaną usunięte podczas oczyszczania kolejki usługi Azure Service Bus. Ten proces automatycznego przywraca nieużywane baz danych do puli elastycznej bazy danych dostępności w Umbraco, udostępnia je do przyszłych udostępniania przy zachowaniu maksymalne wykorzystanie.

## <a name="elastic-pools-allow-uaas-to-scale-with-ease"></a>Zezwalaj na elastyczne pule UaaS do skalowania, łatwości
Dzięki wykorzystaniu Azure pule elastyczne, Umbraco zoptymalizować wydajność w przypadku klientów bez konieczności over lub niepełnej udostępniania. Program Umbraco ma obecnie niemal 3000 baz danych między 19 pule elastyczne, możliwość łatwego skalowania zgodnie z potrzebami, aby zmieścił się w ich istniejących klientów 325,000 lub nowych klientów, którzy są gotowe do wdrożenia SMF w chmurze.

W rzeczywistości zgodnie z Christensen Witold, techniczne prowadzić na Umbraco, "UaaS teraz występują wzrostu około 30 nowych klientów na dzień. Naszym klientom są szczególną przyjemność z elastycznością oprogramowania możliwość obsługi administracyjnej nowych projektów w sekundach, natychmiast publikowania aktualizacji witryn na żywo ze środowiska programowania przy użyciu "jednym kliknięciem wdrożenia" i wprowadź zmiany, podobnie jak szybko, jeśli znajduje się błędy."

Jeśli klient nie wymaga już środowisku drugi i/lub trzeci, wystarczy po prostu usunąć tych środowisk. Który zwalnia zasoby, które mogą służyć do innych klientów jako część puli elastycznej bazy danych dostępności Umbraco.

![Architektura wdrażania usługi program Umbraco](./media/sql-database-implementation-umbraco/figure4.png)

Rysunek 3. UaaS architektura wdrażania usługi Microsoft Azure

## <a name="the-path-from-datacenter-to-cloud"></a>Ścieżka z centrum danych do chmury
Gdy deweloperzy Umbraco początkowo decyzji o przeniesieniu do modelu SaaS, znał czy powinny ekonomiczne i skalowalne sposobem tworzenia usługi.

> "elastyczne pule są dokładne dopasowanie dla naszych SaaS oferty, ponieważ firma Microsoft może wybrać pojemności górę i w dół zgodnie z potrzebami. Inicjowanie obsługi administracyjnej jest łatwe, a z naszych Instalatora możemy zapewnić wykorzystania maksimum."
> 
> — Umbraco Witold Christensen, realizacji techniczne
> 
> 

"Trzeba poświęcić czas na rozwiązywanie problemów klientów nie zarządzanie infrastrukturą. "Aby ułatwić naszym klientom osiągnięcie jak największych korzyści możemy mówi Niels Hartvig, twórcę Umbraco. "Początkowo uznane serwerów, nad hosta, ale planowania pojemności byłby nightmare". Przypadkowo Umbraco nie zawiera żadnych administratorów bazy danych, które podkreślenia propozycji wartość klucza dla przy użyciu UaaS.

Jeden cel ważne dla deweloperów Umbraco było umożliwiają klientom UaaS udostępnić środowisk szybko i bez ograniczeń pojemności. Jednak dostarczanie dedykowanych usługi hostowanej w centrach danych Umbraco wymagałyby wiele nadmiarowej pojemności do obsługi seria podczas przetwarzania. Który będzie mieć przeznaczone Dodawanie infrastruktury obliczeniowej znaczne, który będzie regularnie wykorzystany.

Ponadto zespół deweloperów Umbraco chciał rozwiązania, które umożliwiałyby je do ponownego użycia, jaka ich istniejący kod, jak to możliwe. Deweloper Umbraco Mikkel Madsen stany, "możemy wszystkiego z narzędzi programistycznych firmy Microsoft, które możemy już znasz, takich jak Microsoft SQL Server, bazy danych SQL Microsoft Azure, platformy ASP.net i internetowe usługi informacyjne (IIS). Przed inwestycją w IaaS i PaaS w chmurze rozwiązania, trzeba upewnić się, że będzie obsługuje on naszych narzędzi firmy Microsoft i platformy, dlatego firma Microsoft nie trzeba wprowadzać masowych zmian naszego kodu podstawowej. "

Aby spełnić wszystkie jego kryteria, Umbraco wyszukiwanego partnera chmury z kwalifikacji następujące:

* Wystarczająca pojemność i niezawodności
* Obsługę narzędzi programistycznych firmy Microsoft, tak aby inżynierowie Umbraco czy unika się całkowicie reinvent swoje Środowisko deweloperskie
* Obecność we wszystkich rynkach geograficznych, w których UaaS konkuruje (firmy trzeba upewnij się, że ich szybko dostęp do danych i czy dane są przechowywane w lokalizacji, która spełnia wymogi prawne regionalne)

## <a name="why-umbraco-chose-azure-for-uaas"></a>Dlaczego Umbraco wybrany Azure dla UaaS
Zgodnie z Christensen Witold "po uwzględnieniu wszystkich opcji, wybraliśmy Azure ponieważ spełniał wszystkie nasze kryteria z możliwości zarządzania i skalowalność w znajomości i efektywności. Skonfigurowanie środowiska na maszynach wirtualnych Azure, a każde środowisko ma własne wystąpienie bazy danych SQL Azure z wszystkich wystąpień w pule elastyczne. Oddzielenie baz danych między programowanie, przemieszczania i środowisk na żywo, można oferujemy klientów izolację wydajności niezawodne dopasowane do skalowania — Windows ogromnych. "

Witold będzie nadal występował, "przed było ręcznie zainicjować serwery baz danych w sieci web. Obecnie nie mamy można traktować go. Wszystko jest zautomatyzowany — od zainicjowania obsługi do oczyszczania. "

Witold jest również wszystkiego o możliwości skalowania dostarczany przez platformę Azure. "elastyczne pule są dokładne dopasowanie dla naszych SaaS oferty, ponieważ firma Microsoft może wybrać pojemności górę i w dół zgodnie z potrzebami. Inicjowanie obsługi administracyjnej jest łatwe, a z naszych Instalatora możemy zapewnić wykorzystania maksimum." Stany Witold, "prostotę pule elastyczne, oraz zapewnienie na podstawie warstwy usługi Dtu daje możliwość obsługi administracyjnej nowych pul zasobów na żądanie. Ostatnio jeden z naszych klientów większych daszenie kalenicowe do 100 jednostek Dtu w jego środowisku produkcyjnym. Nasze pule elastyczne korzysta z platformy Azure, pod warunkiem baz danych klienta z zasobami, które są wymagane w czasie rzeczywistym bez konieczności prognozować wymagania jednostek dtu w warstwie. Krótko mówiąc, naszym klientom uzyskać Włącz wokół czasu, który oczekiwane i możemy spełnić wydajności umów poziomu usług".

Go podsumowuje przez Mikkel Madsen: "Firma Microsoft już przyjętych zaawansowanego algorytmu Azure łączącego typowy scenariusz SaaS (dołączania nowych klientów w czasie rzeczywistym na dużą skalę) do naszej wzorzec aplikacji (wstępnie udostępniania bazy danych, zarówno programowanie i na żywo) u góry podstawową technologią (przy użyciu kolejek usługi Azure Service Bus w połączeniu z bazą danych SQL Azure)."

## <a name="with-azure-uaas-is-exceeding-customer-expectations"></a>Przy użyciu platformy Azure UaaS jest przekroczenie oczekiwań klientów
Po wybraniu Azure jako partnera chmury, Umbraco mógł zapewnić klientom UaaS optymalizacji wydajności zarządzanie zawartością, bez inwestycyjnych zasobów informatycznych wymagane od siebie rozwiązanie. Witold jest wyświetlany komunikat "przyłączyć wygody deweloperów i skalowalność, który daje Azure i klientów są thrilled z funkcjami i niezawodności. Ogólnie był dużą win firmie Microsoft!"

## <a name="more-information"></a>Więcej informacji
* Aby uzyskać więcej informacji na temat usługi Azure pule elastyczne, zobacz [pule elastyczne](sql-database-elastic-pool.md).
* Aby dowiedzieć się więcej na temat usługi Azure Service Bus, zobacz [Azure Service Bus](https://azure.microsoft.com/services/service-bus/).
* Aby dowiedzieć się więcej o wirtualnej sieci, zobacz [sieci wirtualne](https://azure.microsoft.com/documentation/services/virtual-network/).    
* Aby dowiedzieć się więcej na temat tworzenia kopii zapasowych i odzyskiwania, zobacz [ciągłość prowadzenia działalności biznesowej](sql-database-business-continuity.md).    
* Aby dowiedzieć się więcej na temat monitorowania ppols, zobacz [monitorowania pule](sql-database-elastic-pool-manage-portal.md).    
* Aby dowiedzieć się więcej na temat Umbraco jako usługa, zobacz [Umbraco](https://umbraco.com/cloud).

