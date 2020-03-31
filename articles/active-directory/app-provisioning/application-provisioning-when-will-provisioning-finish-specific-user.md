---
title: Dowiedz się, kiedy określony użytkownik będzie mógł uzyskać dostęp do aplikacji
description: Jak sprawdzić, kiedy krytycznie ważny użytkownik będzie mógł uzyskać dostęp do aplikacji skonfigurowanej do inicjowania obsługi administracyjnej za pomocą usługi Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48370e2806b70d550bce95ceff3857a79561f247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264143"
---
# <a name="check-the-status-of-user-provisioning"></a>Sprawdzanie stanu inicjowania obsługi administracyjnej przez użytkowników

Usługa inicjowania obsługi administracyjnej usługi Azure AD uruchamia początkowy cykl inicjowania obsługi administracyjnej względem systemu źródłowego i systemu docelowego, a następnie okresowe cykle przyrostowe. Podczas konfigurowania inicjowania obsługi administracyjnej dla aplikacji, można sprawdzić bieżący stan usługi inicjowania obsługi administracyjnej i zobaczyć, kiedy użytkownik będzie mógł uzyskać dostęp do aplikacji.

## <a name="view-the-provisioning-progress-bar"></a>Wyświetlanie paska postępu inicjowania obsługi administracyjnej

 Na stronie **inicjowania obsługi administracyjnej** aplikacji można wyświetlić stan usługi inicjowania obsługi administracyjnej usługi Azure AD. **Sekcja Bieżący stan** u dołu strony pokazuje, czy cykl inicjowania obsługi administracyjnej rozpoczął inicjowanie obsługi administracyjnej kont użytkowników. Możesz obserwować postęp cyklu, zobaczyć, ilu użytkowników i grup zostało aprowizowanych i zobacz, ile ról zostało utworzonych.

Podczas pierwszej konfiguracji automatycznego inicjowania obsługi **administracyjnej, bieżący stan** sekcji u dołu strony pokazuje stan początkowego cyklu inicjowania obsługi administracyjnej. Ta sekcja jest aktualizowana przy każdym uruchomieniu cyklu przyrostowego. Wyświetlane są następujące szczegóły:
- Typ cyklu inicjowania obsługi administracyjnej (początkowy lub przyrostowy), który jest aktualnie uruchomiony lub został ostatnio ukończony.
- Pasek **postępu** przedstawiający procent cyklu inicjowania obsługi administracyjnej, który został ukończony. Wartość procentowa odzwierciedla liczbę aprowizowanych stron. Należy zauważyć, że każda strona może zawierać wielu użytkowników lub grup, więc procent nie jest bezpośrednio skorelowany z liczbą użytkowników, grup lub ról aprowizowanych.
- Przycisk **Odśwież,** za pomocą którego można aktualizować widok.
- Liczba **użytkowników** i **grup** w magazynie danych łącznika. Liczba zwiększa się w dowolnym momencie obiekt jest dodawany do zakresu inicjowania obsługi administracyjnej. Liczba nie spadnie, jeśli użytkownik zostanie usunięty lub usunięty na czas, ponieważ nie spowoduje to usunięcia obiektu z magazynu danych łącznika. Liczba zostanie cofnięta po [zresetowaniu](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) usługi CDS 
- **Łącze Wyświetl dzienniki inspekcji,** które otwiera dzienniki inicjowania obsługi administracyjnej usługi Azure AD, aby uzyskać szczegółowe informacje na temat wszystkich operacji uruchamianych przez usługę inicjowania obsługi administracyjnej użytkowników, w tym stan inicjowania obsługi administracyjnej dla poszczególnych użytkowników (zobacz sekcję [Użyj obsługi inicjowania obsługi administracyjnej](#use-provisioning-logs-to-check-a-users-provisioning-status) poniżej).

Po zakończeniu cyklu inicjowania obsługi administracyjnej sekcja **Statystyki do daty** pokazuje skumulowaną liczbę użytkowników i grup, które zostały aprowidywne do tej pory, wraz z datą zakończenia i czas trwania ostatniego cyklu. Identyfikator **działania** jednoznacznie identyfikuje najnowszy cykl inicjowania obsługi administracyjnej. Identyfikator **zadania** jest unikatowym identyfikatorem zadania inicjowania obsługi administracyjnej i jest specyficzny dla aplikacji w dzierżawie.

Postęp inicjowania obsługi administracyjnej można wyświetlić w witrynie Azure portal, na karcie **Inicjowanie obsługi &gt; administracyjnej nazwy &gt; \[\] &gt; aplikacji usługi Azure Active Directory Enterprise Apps.**

![Pasek postępu strony inicjowania obsługi administracyjnej](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Używanie dzienników inicjowania obsługi administracyjnej w celu sprawdzenia stanu inicjowania obsługi administracyjnej użytkownika

Aby wyświetlić stan inicjowania obsługi administracyjnej dla wybranego użytkownika, zapoznaj się z [dziennikami inicjowania obsługi administracyjnej (w wersji zapoznawczej)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) w usłudze Azure AD. Wszystkie operacje uruchamiane przez usługę inicjowania obsługi administracyjnej użytkowników są rejestrowane w dziennikach inicjowania obsługi administracyjnej usługi Azure AD. Obejmuje to wszystkie operacje odczytu i zapisu wykonane w systemach źródłowych i docelowych oraz dane użytkownika, które zostały odczytane lub zapisane podczas każdej operacji.

Dzienniki inicjowania obsługi administracyjnej można uzyskać w witrynie Azure Portal, wybierając **dzienniki inicjowania obsługi administracyjnej** aplikacji **usługi Azure Active Directory** &gt; **Enterprise Apps** &gt; (wersja zapoznawcza) w sekcji **Działanie.** Można przeszukiwać dane inicjowania obsługi administracyjnej na podstawie nazwy użytkownika lub identyfikatora w systemie źródłowym lub systemie docelowym. Aby uzyskać szczegółowe informacje, zobacz [Inicjowanie obsługi administracyjnej dzienników (wersja zapoznawcza)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Dzienniki inicjowania obsługi administracyjnej rejestrują wszystkie operacje wykonywane przez usługę inicjowania obsługi administracyjnej, w tym:

* Wykonywanie zapytań o usługę Azure AD dla przypisanych użytkowników, którzy są w zakresie inicjowania obsługi administracyjnej
* Zapytanie do aplikacji docelowej o istnienie tych użytkowników
* Porównywanie obiektów użytkownika między systemem
* Dodawanie, aktualizowanie lub wyłączanie konta użytkownika w systemie docelowym na podstawie porównania

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej w witrynie Azure portal, zobacz [przewodnik raportowania inicjowania obsługi administracyjnej.](check-status-user-account-provisioning.md)

## <a name="how-long-will-it-take-to-provision-users"></a>Jak długo trwa aprowizacji użytkowników?
Podczas korzystania z automatycznego inicjowania obsługi administracyjnej użytkowników za pomocą aplikacji usługa Azure AD automatycznie aprowizuje i aktualizuje konta użytkowników w aplikacji na podstawie takich ów dziele, jak [przypisanie użytkowników i grup](../manage-apps/assign-user-or-group-access-portal.md) w regularnych odstępach czasu, zazwyczaj co 40 minut.

Czas potrzebny dla danego użytkownika, które mają być aprowizacji zależy głównie od tego, czy zadanie inicjowania obsługi administracyjnej jest uruchomiony początkowy cykl lub cykl przyrostowy.

- W **cyklu początkowym**czas zadania zależy od wielu czynników, w tym liczby użytkowników i grup w zakresie inicjowania obsługi administracyjnej oraz całkowitej liczby użytkowników i grup w systemie źródłowym. Pierwsza synchronizacja między usługą Azure AD a aplikacją może potrwać od 20 minut do kilku godzin, w zależności od rozmiaru katalogu usługi Azure AD i liczby użytkowników w zakresie inicjowania obsługi administracyjnej. Pełna lista czynników, które wpływają na wydajność cyklu początkowego są podsumowane w dalszej części tej sekcji.

- W przypadku **cykli przyrostowych** po początkowym cyklu czasy pracy są zwykle szybsze (np. w ciągu 10 minut), ponieważ usługa inicjowania obsługi administracyjnej przechowuje znaki wodne reprezentujące stan obu systemów po początkowym cyklu, poprawiając wydajność kolejnych synchronizacji. Czas zadania zależy od liczby zmian wykrytych w tym cyklu inicjowania obsługi administracyjnej. Jeśli istnieje mniej niż 5000 zmian członkostwa użytkownika lub grupy, zadanie można zakończyć w ramach jednego przyrostowego cyklu inicjowania obsługi administracyjnej. 

W poniższej tabeli podsumowano czasy synchronizacji typowych scenariuszy inicjowania obsługi administracyjnej. W tych scenariuszach system źródłowy jest Azure AD i system docelowy jest aplikacją SaaS. Czasy synchronizacji są uzyskiwane z analizy statystycznej zadań synchronizacji dla aplikacji SaaS ServiceNow, Workplace, Salesforce i G Suite.


| Konfiguracja zakresu | Użytkownicy, grupy i członkowie w zakresie | Początkowy czas cyklu | Przyrostowy czas cyklu |
| -------- | -------- | -------- | -------- |
| Synchronizowanie tylko przypisanych użytkowników i grup |  < 1000 |  < 30 minut | < 30 minut |
| Synchronizowanie tylko przypisanych użytkowników i grup |  1,000 - 10,000 | 142 - 708 minut | < 30 minut |
| Synchronizowanie tylko przypisanych użytkowników i grup |   10,000 - 100,000 | 1 170 - 2340 minut | < 30 minut |
| Synchronizowanie wszystkich użytkowników i grup w usłudze Azure AD |  < 1000 | < 30 minut  | < 30 minut |
| Synchronizowanie wszystkich użytkowników i grup w usłudze Azure AD |  1,000 - 10,000 | < 30 - 120 minut | < 30 minut |
| Synchronizowanie wszystkich użytkowników i grup w usłudze Azure AD |  10,000 - 100,000  | 713 - 1 425 minut | < 30 minut |
| Synchronizowanie wszystkich użytkowników w usłudze Azure AD|  < 1000  | < 30 minut | < 30 minut |
| Synchronizowanie wszystkich użytkowników w usłudze Azure AD | 1,000 - 10,000  | 43 - 86 minut | < 30 minut |


Tylko w przypadku **konfiguracji Synchronizacja przypisanego użytkownika i grup**umożliwia określenie przybliżonych minimalnych i **maksymalnych oczekiwanych początkowych czasów cyklu:**

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Podsumowanie czynników, które wpływają na czas potrzebny na ukończenie **początkowego cyklu:**

- Całkowita liczba użytkowników i grup w zakresie inicjowania obsługi administracyjnej.

- Całkowita liczba użytkowników, grup i członków grupy obecnych w systemie źródłowym (Azure AD).

- Czy użytkownicy w zakresie inicjowania obsługi administracyjnej są dopasowywać do istniejących użytkowników w aplikacji docelowej lub muszą być tworzone po raz pierwszy. Zadania synchronizacji, dla których wszyscy użytkownicy są tworzone po raz pierwszy, zajmują około *dwa razy więcej czasu niż* zadania synchronizacji, dla których wszyscy użytkownicy są dopasowywać do istniejących użytkowników.

- Liczba błędów w [dziennikach inicjowania obsługi administracyjnej](check-status-user-account-provisioning.md). Wydajność jest mniejsza, jeśli istnieje wiele błędów i usługa inicjowania obsługi administracyjnej została przesuń do stanu kwarantanny. 

- Limity szybkości żądania i ograniczania przepustowości zaimplementowane przez system docelowy. Niektóre systemy docelowe implementują limity szybkości żądań i ograniczanie przepustowości, co może mieć wpływ na wydajność podczas dużych operacji synchronizacji. W tych warunkach aplikacja, która odbiera zbyt wiele żądań zbyt szybko może spowolnić szybkość odpowiedzi lub zamknąć połączenie. Aby zwiększyć wydajność, łącznik musi dostosować, nie wysyłając żądania aplikacji szybciej niż aplikacja może je przetworzyć. Inicjowanie obsługi administracyjnej łączników utworzonych przez firmę Microsoft dokonać tej korekty. 

- Liczba i rozmiary przypisanych grup. Synchronizowanie przypisanych grup trwa dłużej niż synchronizowanie użytkowników. Wydajność ma wpływ zarówno na liczbę, jak i rozmiary przypisanych grup. Jeśli aplikacja ma [włączone mapowania dla synchronizacji obiektów grupy,](customize-application-attributes.md#editing-group-attribute-mappings)właściwości grupy, takie jak nazwy grup i członkostwa, są synchronizowane oprócz użytkowników. Te dodatkowe synchronizacje potrwa dłużej niż tylko synchronizowanie obiektów użytkownika.

- Jeśli wydajność staje się problemem i próbujesz aprowizować większość użytkowników i grup w dzierżawie, użyj filtrów zakresu. Filtry zakresu umożliwiają precyzyjne dostrojenie danych wyodrębnianych przez usługę inicjowania obsługi administracyjnej z usługi Azure AD przez odfiltrowanie użytkowników na podstawie określonych wartości atrybutów. Aby uzyskać więcej informacji na temat filtrów zakresu, zobacz [Inicjowanie obsługi administracyjnej aplikacji oparte na atrybutach za pomocą filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Następne kroki
[Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](user-provisioning.md)
