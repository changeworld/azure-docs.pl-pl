---
title: Dowiedz się, kiedy określony użytkownik będzie mógł uzyskać dostęp do aplikacji | Microsoft Docs
description: Jak dowiedzieć się, kiedy krytycznie ważny jest dostęp do aplikacji skonfigurowanej do aprowizacji użytkowników w usłudze Azure AD
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
ms.openlocfilehash: 3c6ad7e305958131c4f544dfa2022e7471e9adac
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147107"
---
# <a name="check-the-status-of-user-provisioning"></a>Sprawdź stan aprowizacji użytkowników

Usługa Azure AD Provisioning uruchamia początkowy cykl aprowizacji dla systemu źródłowego i systemu docelowego, a następnie okresowe cykle przyrostowe. Podczas konfigurowania aprowizacji dla aplikacji można sprawdzić bieżący stan usługi aprowizacji i zobaczyć, kiedy użytkownik będzie mógł uzyskać dostęp do aplikacji.

## <a name="view-the-provisioning-progress-bar"></a>Wyświetl pasek postępu aprowizacji

 Na stronie **aprowizacji** aplikacji można wyświetlić stan usługi Azure AD Provisioning. **Bieżąca sekcja stanu** w dolnej części strony pokazuje, czy cykl aprowizacji rozpoczął Inicjowanie obsługi kont użytkowników. Postęp cyklu można obejrzeć, sprawdzić, ilu użytkowników i grup została zainicjowana, i zobaczyć, ile ról zostało utworzonych.

Podczas pierwszej konfiguracji automatycznej aprowizacji, **Bieżąca sekcja stanu** w dolnej części strony pokazuje stan początkowego cyklu aprowizacji. Ta sekcja jest aktualizowana za każdym razem, gdy cykl zostanie uruchomiony przyrostowo. Przedstawiono następujące szczegóły:
- Typ cyklu aprowizacji (początkowy lub przyrostowy), który jest aktualnie uruchomiony lub ostatnio zakończony.
- **Pasek postępu** przedstawiający procent cyklu aprowizacji, który został ukończony. Wartość procentowa odzwierciedla liczbę zainicjowanych stron. Należy pamiętać, że każda strona może zawierać wielu użytkowników lub grup, więc wartość procentowa nie jest bezpośrednio skorelowana z liczbą użytkowników, grup lub ról, które są obsługiwane.
- Przycisk **odświeżania** , którego można użyć, aby zachować zaktualizowany widok.
- Liczba zainicjowanych **użytkowników** i **grup** oraz liczba utworzonych ról. Podczas pierwszego cyklu liczba **użytkowników** liczy się o 1, gdy użytkownik zostanie utworzony lub zaktualizowany i liczy w dół o 1, gdy użytkownik zostanie usunięty. Podczas przyrostowego cyklu aktualizacje użytkowników nie wpływają na liczbę **użytkowników** ; Liczba zmienia się tylko wtedy, gdy użytkownicy są tworzeniu lub usuwani.
- Łącze **Wyświetl dzienniki inspekcji** , co spowoduje otwarcie dzienników inspekcji usługi Azure AD w celu uzyskania szczegółowych informacji o wszystkich operacjach wykonywanych przez usługę aprowizacji użytkowników, w tym o stanie aprowizacji dla poszczególnych użytkowników (zobacz sekcję [use dzienników inspekcji](#use-audit-logs-to-check-a-users-provisioning-status) poniżej).

Po zakończeniu cyklu aprowizacji sekcja **statystyki do daty** pokazuje skumulowaną liczbę użytkowników i grup, których zainicjowano na dzień, wraz z datą ukończenia i czasem trwania ostatniego cyklu. **Identyfikator działania** jednoznacznie identyfikuje najnowszy cykl aprowizacji. **Identyfikator zadania** jest unikatowym identyfikatorem zadania aprowizacji i jest specyficzny dla aplikacji w dzierżawie.

Postęp aprowizacji można wyświetlić w Azure Portal z poziomu karty  **&gt; \] &gt; &gt; aplikacje \[Azure Active Directory Enterprise aplikacji** .

![Pasek postępu strony aprowizacji](media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-audit-logs-to-check-a-users-provisioning-status"></a>Sprawdzanie stanu aprowizacji użytkownika przy użyciu dzienników inspekcji

Aby sprawdzić stan aprowizacji wybranego użytkownika, zapoznaj się z dziennikami inspekcji w usłudze Azure AD. Wszystkie operacje wykonywane przez usługę aprowizacji użytkowników są rejestrowane w dziennikach inspekcji usługi Azure AD. Obejmuje to wszystkie operacje odczytu i zapisu dokonane w systemach źródłowych i docelowych oraz dane użytkownika, które zostały odczytane lub zapisane podczas każdej operacji.

Dostęp do dzienników inspekcji aprowizacji można uzyskać w Azure Portal na karcie **dzienniki &gt; &gt; inspekcji &gt; \[aplikacji\] Azure Active Directory Enterprise** . Przefiltruj dzienniki w kategorii **aprowizacji konta** , aby zobaczyć tylko zdarzenia aprowizacji dla tej aplikacji. Użytkowników można wyszukiwać na podstawie "zgodnego identyfikatora", który został skonfigurowany dla nich w mapowaniu atrybutów. 

Na przykład, jeśli skonfigurowano "główną nazwę użytkownika" lub "adres e-mail" jako pasujący atrybut w stronie usługi Azure AD, a użytkownik nie będzie aprowizacji ma wartość "audrey@contoso.com", Wyszukaj w dziennikach inspekcji "audrey@contoso.com" i Przejrzyj wpisy zwracać.

Dzienniki inspekcji aprowizacji rejestruje wszystkie operacje wykonywane przez usługę aprowizacji, w tym:

* Wykonywanie zapytania dotyczącego usługi Azure AD dla przypisanych użytkowników, którzy znajdują się w zakresie aprowizacji
* Wykonywanie zapytania dotyczącego aplikacji docelowej pod kątem istnienia tych użytkowników
* Porównywanie obiektów użytkownika między systemem
* Dodawanie, aktualizowanie lub wyłączanie konta użytkownika w systemie docelowym na podstawie porównania

Aby uzyskać więcej informacji na temat odczytywania dzienników inspekcji w Azure Portal, zobacz [Przewodnik po raportowaniu obsługi administracyjnej](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Jak długo trwa inicjowanie obsługi administracyjnej użytkowników?
W przypadku korzystania z automatycznej aprowizacji użytkowników w aplikacji usługa Azure AD automatycznie inicjuje i aktualizuje konta użytkowników w aplikacji na podstawie takich elementów, jak [przypisywanie użytkowników i grup](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) w regularnych zaplanowanych odstępach czasu, zazwyczaj co 40 minut.

Czas, w którym dany użytkownik ma zostać zainicjowany, zależy głównie od tego, czy zadanie aprowizacji korzysta z synchronizacji początkowej czy synchronizacji przyrostowej.

- W przypadku **synchronizacji początkowej**czas zadania zależy od wielu czynników, w tym liczby użytkowników i grup w zakresie aprowizacji oraz łącznej liczby użytkowników i grup w systemie źródłowym. Pierwsza synchronizacja między usługą Azure AD a aplikacją może zająć od 20 minut do kilku godzin, w zależności od rozmiaru katalogu usługi Azure AD i liczby użytkowników w zakresie aprowizacji. Kompleksowa lista czynników wpływających na wydajność synchronizacji początkowej znajduje się w dalszej części tej sekcji.

- W przypadku **synchronizacji przyrostowych** po początkowej synchronizacji czasy zadań są szybsze (np. w ciągu 10 minut), ponieważ usługa aprowizacji przechowuje znaki wodne, które reprezentują stan obu systemów po synchronizacji początkowej, poprawiając wydajność kolejnych synchronizacji. Czas zadania zależy od liczby zmian wykrytych w tym cyklu aprowizacji. Jeśli istnieje mniej niż 5 000 zmian członkostwa użytkowników lub grup, zadanie może zakończyć się w ramach pojedynczego przyrostowego cyklu udostępniania. 

Poniższa tabela zawiera podsumowanie czasów synchronizacji typowych scenariuszy aprowizacji. W tych scenariuszach system źródłowy jest usługą Azure AD, a system docelowy to aplikacja SaaS. Czasy synchronizacji są uzyskiwane z analizy statystycznej zadań synchronizacji dla aplikacji SaaS usługi ServiceNow, miejsc pracy, usług Salesforce i G Suite.


| Konfiguracja zakresu | Użytkownicy, grupy i członkowie w zakresie | Czas synchronizacji początkowej | Czas synchronizacji przyrostowej |
| -------- | -------- | -------- | -------- |
| Synchronizuj tylko przypisanych użytkowników i grupy |  < 1 000 |  < 30 minut | < 30 minut |
| Synchronizuj tylko przypisanych użytkowników i grupy |  1000–10 000 | 142 – 708 minut | < 30 minut |
| Synchronizuj tylko przypisanych użytkowników i grupy |   10 000 – 100 000 | 1 170 – 2 340 minut | < 30 minut |
| Synchronizuj wszystkich użytkowników i grupy w usłudze Azure AD |  < 1 000 | < 30 minut  | < 30 minut |
| Synchronizuj wszystkich użytkowników i grupy w usłudze Azure AD |  1000–10 000 | < 30-120 minut | < 30 minut |
| Synchronizuj wszystkich użytkowników i grupy w usłudze Azure AD |  10 000 – 100 000  | 713 – 1 425 minut | < 30 minut |
| Synchronizuj wszystkich użytkowników w usłudze Azure AD|  < 1 000  | < 30 minut | < 30 minut |
| Synchronizuj wszystkich użytkowników w usłudze Azure AD | 1000–10 000  | 43 – 86 minut | < 30 minut |


Aby określić przybliżoną minimalną i maksymalną oczekiwaną długość **początkową synchronizacji** , można użyć następujących formuł do **synchronizacji konfiguracji tylko przypisanego użytkownika i grupy**.

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Podsumowanie czynników wpływających na czas wykonywania **synchronizacji początkowej**:

- Łączna liczba użytkowników i grup w zakresie aprowizacji.

- Łączna liczba użytkowników, grup i członków grupy obecnych w systemie źródłowym (Azure AD).

- Określa, czy użytkownicy w zakresie aprowizacji są dopasowywani do istniejących użytkowników w aplikacji docelowej, czy też muszą być tworzone po raz pierwszy. Zadania synchronizacji, dla których wszyscy użytkownicy są tworzone po raz pierwszy, są wykonywane *dwa razy* , o ile zadania synchronizacji, dla których wszyscy użytkownicy są dopasowane do istniejących użytkowników.

- Liczba błędów w dziennikach [inspekcji](check-status-user-account-provisioning.md). Wydajność jest wolniejsza, jeśli wystąpi wiele błędów, a usługa aprowizacji przestała się na stan kwarantanny.    

- Limity szybkości żądań i ograniczanie wydajności zaimplementowane przez system docelowy. Niektóre systemy docelowe implementują limity szybkości żądań i ograniczania przepustowości, co może mieć wpływ na wydajność podczas dużych operacji synchronizacji. W tych warunkach aplikacja, która otrzymuje zbyt wiele żądań zbyt szybko, może spowalniać swoją szybkość reakcji lub zamknąć połączenie. Aby zwiększyć wydajność, łącznik musi zostać dostosowany przez niewysyłanie żądań aplikacji szybciej niż aplikacja może je przetworzyć. Dla łączników aprowizacji utworzonych przez firmę Microsoft należy wprowadzić tę korektę. 

- Liczba i rozmiary przypisanych grup. Synchronizowanie przypisanych grup trwa dłużej niż synchronizowanie użytkowników. Zarówno liczba, jak i rozmiary przypisanych grup wpływają na wydajność. Jeśli aplikacja ma [mapowania włączone dla synchronizacji obiektów grup](customize-application-attributes.md#editing-group-attribute-mappings), w dodatku do użytkowników są synchronizowane właściwości grupy, takie jak nazwy grup i członkostwa. Te dodatkowe synchronizacje będą trwać dłużej niż tylko synchronizowanie obiektów użytkownika.

## <a name="next-steps"></a>Następne kroki
[Automatyzacja aprowizacji i anulowania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)
