---
title: Dowiedz się, gdy określony użytkownik będzie można uzyskać dostęp do aplikacji | Dokumentacja firmy Microsoft
description: Jak ustalić, kiedy znaczenie użytkownik będzie mógł uzyskać dostęp do aplikacji skonfigurowanych do aprowizacji użytkowników z usługą Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd6b70e7a4542a4cad2ee95fa280ddf8fbe6553
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310031"
---
# <a name="check-the-status-of-user-provisioning"></a>Sprawdź stan aprowizacji użytkowników

Usługi aprowizacji usługi Azure AD działa początkowa cyklu inicjowania obsługi administracyjnej z systemu źródłowego i systemu docelowego, następuje okresowe przyrostowe cykle. Po skonfigurowaniu udostępniania dla aplikacji, można sprawdzić bieżący stan usługi aprowizacji i zobaczyć, kiedy użytkownik będzie mógł uzyskać dostęp do aplikacji.

## <a name="view-the-provisioning-progress-bar-preview"></a>Wyświetl aprowizacji (wersja zapoznawcza) — pasek postępu

 Na **aprowizacji** strony dla aplikacji, można wyświetlić stan inicjowania obsługi administracyjnej usługi AD platformy Azure. **Bieżący stan** sekcji w dolnej części strony pokazuje, czy cyklu inicjowania obsługi administracyjnej rozpoczął świadczenie kont użytkowników. Możesz obserwować postęp cyklu, zobacz, jak wielu użytkowników i grup Obsługa została zainicjowana i zobaczyć, jak wiele ról zostały utworzone.

Podczas pierwszej konfiguracji automatycznej aprowizacji **bieżący stan** sekcji w dolnej części strony wyświetla stan początkowy cyklu inicjowania obsługi administracyjnej. W tej sekcji aktualizuje każdym razem, gdy przyrostowych cyklu. Są wyświetlane następujące informacje:
- Typ cyklu inicjowania obsługi administracyjnej (początkowa lub przyrostowa), który jest aktualnie uruchomiona lub ostatnio zostało ukończone.
- A **pasek postępu** przedstawiający wartość procentową cyklu inicjowania obsługi administracyjnej, która została zakończona. Wartość procentowa odzwierciedla liczbę stron aprowizowane. Należy pamiętać, że każda strona może zawierać wielu użytkowników lub grup, dlatego wartość procentową bezpośrednio nie odnoszą się do liczby użytkowników, grup lub ról aprowizowane.
- A **Odśwież** przycisku można użyć, aby zachować wyświetlanie aktualizacji.
- Liczba **użytkowników** i **grup** zaaprowizowana oraz liczbę role utworzone. Podczas początkowego cyklu **użytkowników** numer liczy w górę o 1 po użytkownik jest tworzony lub aktualizowany i liczy w dół o 1 po użytkownik zostanie usunięty. Przyrostowych cyklu nie wpływają na aktualizacje użytkownika **użytkowników** liczba; Liczba zmian tylko wtedy, gdy użytkownicy są tworzone lub usuwane.
- A **wyświetlanie dzienników inspekcji** połączyć, która otwiera inspekcji usługi Azure AD dzienniki dla szczegółowe informacje o wszystkich operacji uruchomienia przez użytkownika inicjowania obsługi usługi, w tym aprowizacji stanu dla poszczególnych użytkowników (zobacz [Dziennikiinspekcjiużycia](#use-audit-logs-to-check-a-users-provisioning-status)sekcji poniżej).

Po ukończeniu inicjowania obsługi administracyjnej cyklu **statystyki do daty** sekcji przedstawiono zbiorczą liczby użytkowników i grup, które zostały udostępnione do daty, oraz Data zakończenia i czas trwania ostatniego cyklu. **Identyfikator działania** unikatowo identyfikuje najbardziej aktualną cyklu inicjowania obsługi administracyjnej. **Identyfikator zadania** jest unikatowym identyfikatorem dla zadania obsługi administracyjnej i specyficzne dla aplikacji w dzierżawie.

Postęp aprowizowania można wyświetlić w witrynie Azure portal w **usługi Azure Active Directory &gt; aplikacje dla przedsiębiorstw &gt; \[Nazwa aplikacji\] &gt; aprowizacji** kartę.

![Inicjowanie obsługi administracyjnej pasek postępu strony](media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-audit-logs-to-check-a-users-provisioning-status"></a>Korzystaj z dzienników inspekcji można sprawdzić stanu aprowizacji użytkownika

Aby wyświetlić stan inicjowania obsługi administracyjnej dla wybranego użytkownika, sprawdź w dziennikach inspekcji w usłudze Azure AD. Wszystkie operacje uruchomione przez użytkownika usługi aprowizacji są rejestrowane w usłudze Azure AD dzienniki inspekcji. Dotyczy to wszystkich operacji wprowadzone do systemów źródłowych i docelowych i danych użytkownika, który został odczytać lub zapisywane podczas każdej operacji odczytu i zapisu.

Inicjowania obsługi dzienników inspekcji można uzyskać w witrynie Azure portal w **usługi Azure Active Directory &gt; aplikacje dla przedsiębiorstw &gt; \[Nazwa aplikacji\] &gt; dzienników inspekcji** kartę. Filtruj dzienniki na **Inicjowanie obsługi administracyjnej konta** kategorię, aby zobaczyć tylko inicjowania obsługi zdarzeń dla tej aplikacji. Możesz wyszukać użytkowników na podstawie "Pasującego Identyfikatora" który został skonfigurowany dla nich w mapowania atrybutów. 

Na przykład, jeśli skonfigurowano "główna nazwa użytkownika" lub "adres e-mail" jako atrybutu zgodnego po stronie usługi Azure AD, a użytkownik nie aprowizacji ma wartość "audrey@contoso.com", następnie przeszukiwanie dzienników inspekcji dla "audrey@contoso.com", a następnie przejrzyj wpisy zwracane.

Inicjowania obsługi dzienników inspekcji rejestrowania wszystkich operacji wykonywanych przez usługę aprowizacji, w tym:

* Tworzenie zapytań usługi Azure AD dla przypisanych użytkowników, którzy znajdują się w zakresie do inicjowania obsługi
* Podczas badania aplikacji docelowej, aby istnienie tych użytkowników
* Porównywanie obiektów użytkownika między systemem
* Dodawanie, aktualizowanie lub wyłączenie konta użytkownika w systemie docelowym na podstawie porównania

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników inspekcji w witrynie Azure portal, zobacz [inicjowania obsługi administracyjnej Przewodnik po raportowaniu](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Jak długo potrwa inicjowania obsługi użytkowników?
Korzystając z automatycznej aprowizacji użytkowników z aplikacją, usługi Azure AD automatycznie aprowizuje i aktualizuje kont użytkowników w aplikacji, w oparciu o elementów, takich jak [przypisania użytkowników i grup](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) w zaplanowany czas interwału, zwykle co 10 minut.

Czas potrzebnego dla danego użytkownika do udostępnienia zależy od głównie tego, czy zadanie inicjowania obsługi administracyjnej jest uruchomione początkowej synchronizacji lub synchronizacji przyrostowej.

- Aby uzyskać **początkowej synchronizacje**, czas zadania zależy od wielu czynników, w tym liczbę użytkowników i grup w zakresie udostępniania, a łączna liczba użytkowników i grup w systemie źródłowym. Pierwsza synchronizacja między usługą Azure AD i aplikacji może potrwać od 20 minut do kilku godzin, w zależności od rozmiaru katalogu usługi Azure AD i liczbę użytkowników w zakresie udostępniania. Wyczerpujący wykaz czynniki mające wpływ na wydajność synchronizacji początkowej są podsumowywane w dalszej części w tej sekcji.

- Aby uzyskać **synchronizacje przyrostowe** po synchronizacji początkowej czasy zadań zwykle staje się szybsze (np. w ciągu 10 minut), ponieważ usługa aprowizowania przechowuje znaki wodne, które reprezentują stanu obu systemów po początkowej synchronizacji, zwiększanie wydajności o kolejne synchronizacje. Czas zadania zależy od liczby zmian wykrytych w tym cyklu inicjowania obsługi administracyjnej. W przypadku mniej niż 5000 użytkownika lub zmiany członkostwa w grupie, zadania można zakończyć w ciągu jednego cyklu inicjowania obsługi administracyjnej przyrostowe. 

Poniższa tabela podsumowuje godziny synchronizacji dla typowych scenariuszy inicjowania obsługi administracyjnej. W tych scenariuszach w systemie źródłowym jest usługa Azure AD i system docelowy jest aplikacją SaaS. Czas synchronizacji są uzyskiwane z analizy statystycznej zadania synchronizacji pod kątem aplikacji SaaS usługi ServiceNow, obszar roboczy, Salesforce i usługi G Suite.


| Konfiguracja zakresu | Użytkownicy, grupy i elementy członkowskie w zakresie | Czas synchronizacji początkowej | Czas synchronizacji przyrostowej |
| -------- | -------- | -------- | -------- |
| Synchronizuj przypisanych użytkowników i tylko grupy |  < 1,000 |  < 30 minut | < 30 minut |
| Synchronizuj przypisanych użytkowników i tylko grupy |  1000–10 000 | 142 - minut 708 | < 30 minut |
| Synchronizuj przypisanych użytkowników i tylko grupy |   10,000 - 100,000 | 1,170 - 2,340 minut | < 30 minut |
| Synchronizuj wszystkich użytkowników i grup w usłudze Azure AD |  < 1,000 | < 30 minut  | < 30 minut |
| Synchronizuj wszystkich użytkowników i grup w usłudze Azure AD |  1000–10 000 | < 30 – 120 minut | < 30 minut |
| Synchronizuj wszystkich użytkowników i grup w usłudze Azure AD |  10,000 - 100,000  | 713 - 1,425 minut | < 30 minut |
| Synchronizuj wszystkich użytkowników w usłudze Azure AD|  < 1,000  | < 30 minut | < 30 minut |
| Synchronizuj wszystkich użytkowników w usłudze Azure AD | 1000–10 000  | 43 - 86 minut | < 30 minut |


Dla konfiguracji **synchronizacji przypisane tylko grupy użytkowników i**, następujące formuły można użyć do określenia przybliżony minimalne i maksymalne, oczekiwano **początkowej synchronizacji** razy:

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Podsumowanie czynniki wpływające na czas potrzebny do ukończenia **początkowej synchronizacji**:

- Całkowita liczba użytkowników i grup w zakresie udostępniania.

- Całkowita liczba użytkowników, grup i członków grupy obecne w systemie źródłowym (Azure AD).

- Użytkownicy w zakresie udostępniania są dopasowywane do istniejących użytkowników w aplikacji docelowej, czy konieczne będzie utworzenie po raz pierwszy. Zadania synchronizacji, dla których wszyscy użytkownicy są tworzone po raz pierwszy to potrwać około *dwa razy dłużej* synchronizacji jako zadania, dla których wszyscy użytkownicy są dopasowywane do istniejących użytkowników.

- Liczba błędów w [dzienniki inspekcji](check-status-user-account-provisioning.md). Wydajność jest niższa, jeśli ma wiele błędów i usługi aprowizacji włożono w stan kwarantanny.    

- Żądanie, limitów szybkości i ograniczania przepustowości zaimplementowana przez system docelowy. Niektóre systemy docelowe zaimplementować żądania limitów szybkości i ograniczania przepustowości, które mogą wpłynąć na wydajność podczas operacji synchronizacji dużej. W tych warunkach aplikację, która odbiera zbyt wiele żądań zbyt szybko może spowolnić jej wskaźnika odpowiedzi lub zamknąć połączenie. Aby zwiększyć wydajność, łącznik wymaga dostosowania przez nie wysyła szybciej niż aplikacji mogły je przetwarzać żądań aplikacji. Inicjowania obsługi administracyjnej łączniki utworzone przez firmę Microsoft, należy to dostosowanie. 

- Liczby i rozmiarów przypisanych grup. Trwa synchronizowanie przydzielonych grup trwa dłużej niż synchronizowaniem użytkowników. Liczba i rozmiary przypisanych grup obniżenie wydajności. Jeśli aplikacja ma [mapowania włączone do celów synchronizacji obiektu grupy](customize-application-attributes.md#editing-group-attribute-mappings)właściwości grupy, takie jak nazwy grup i członkostw są synchronizowane oprócz użytkowników. Te dodatkowe synchronizacje będzie trwać dłużej niż tylko synchronizacja obiektów użytkowników.

## <a name="next-steps"></a>Kolejne kroki
[Automatyzacja aprowizacji i anulowania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)
