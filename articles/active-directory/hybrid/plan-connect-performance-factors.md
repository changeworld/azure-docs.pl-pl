---
title: Czynniki wpływające na wydajność programu Azure AD Connect
description: W tym dokumencie wyjaśniono, jak różne czynniki mają wpływ na program Azure AD Connect, inicjowanie obsługi administracyjnej aparatu. Poniższe zagadnienia pomogą organizacji, aby zaplanować wdrożenie ich program Azure AD Connect, aby upewnić się, że spełnia on wymagania dotyczące ich synchronizacji.
services: active-directory
author: billmath
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2018
ms.reviewer: martincoetzer
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a3a57fbe5df690e4dbdba8cbab85e62648bb298
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60295380"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>Czynniki wpływające na wydajność programu Azure AD Connect

Program Azure AD Connect synchronizuje usługi Active Directory do usługi Azure AD. Ten serwer jest kluczowym elementem przenoszenie tożsamości użytkowników w chmurze. Podstawowe czynniki, które mają wpływ na wydajność programu Azure AD Connect są:

| **Współczynnik projektu**| **Definicja** |
|:-|-|
| Topologia| Rozkład punktów końcowych i składniki usługi Azure AD Connect muszą zarządzać w sieci. |
| Skalowanie| Liczba obiektów, takich jak użytkownicy, grupy i jednostki organizacyjne, które będą zarządzane przez program Azure AD Connect. |
| Sprzęt| Sprzęt (fizyczny lub wirtualny) dla usługi Azure AD Connect i wydajności zależnych pojemności poszczególnych składników sprzętowych, w tym procesora CPU, pamięci, sieci i konfiguracja dysku twardego. |
| Konfigurowanie| Jak usługa Azure AD Connect procesy katalogów i informacji. |
| Ładowanie| Częstotliwość zmian obiektu. Obciążenia mogą się różnić w ciągu godziny, dnia lub tygodnia. W zależności od tego składnika wiążące się projektowania pod kątem obciążenia szczytowego lub średniego. |

Ten dokument ma na celu opisania czynników wpływających na wydajność programu Azure AD Connect, inicjowanie obsługi administracyjnej aparatu. Organizacje dużych lub złożonych (ponad 100 000 obiektów inicjowania obsługi organizacje) mogą używać zaleceń do optymalizacji ich implementacji usługi Azure AD Connect, w przypadku wystąpienia problemów z wydajnością opisana w tym temacie. Inne składniki programu Azure AD Connect, takich jak [program Azure AD Connect health](how-to-connect-health-agent-install.md) i agenci nie są opisane tutaj.

> [!IMPORTANT]
> Firma Microsoft nie obsługuje modyfikowania ani działania usługi Azure AD Connect poza akcjami, które zostały formalnie udokumentowane. Każda z tych akcji może spowodować niespójny lub nieobsługiwany stan synchronizacji programu Azure AD Connect. W związku z tym firma Microsoft nie może świadczyć pomocy technicznej w przypadku takich wdrożeń.

## <a name="azure-ad-connect-component-factors"></a>Czynniki składników usługi Azure AD Connect

Na poniższym diagramie przedstawiono architekturę wysokiego poziomu inicjowania obsługi administracyjnej aparatu nawiązywania połączenia z pojedynczym lasem w wielu lasach są obsługiwane. W tej architekturze przedstawiono, jak różne składniki oddziałują na inne.

![AzureADConnentInternal](media/plan-connect-performance-factors/AzureADConnentInternal.png)

Aparat inicjowania obsługi administracyjnej łączy dla każdego lasu usługi Active Directory i usługą Azure AD. Proces odczytu informacji z każdego katalogu jest nazywany importu. Eksportowanie odnosi się do aktualizacji katalogi z aparatu obsługi administracyjnej. Synchronizacja ocenia reguły przepływ obiekty w aparacie inicjowania obsługi administracyjnej. Aby uzyskać bardziej zgłębić temat mogą odwoływać się do [synchronizacji programu Azure AD Connect: Omówienie architektury](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture).

Program Azure AD Connect używa następujących obszarów tymczasowych, reguł i procesów do Zezwalaj na synchronizację z usługą Active Directory do usługi Azure AD:

* **Łącznik miejsca (CS)** — obiekty z każdego połączonego katalogu (CD), rzeczywiste katalogi są umieszczane w tym miejscu najpierw przed mogą być przetwarzane przez aparat inicjowania obsługi administracyjnej. Usługa Azure AD ma swój własny CS i każdego lasu, z którymi są nawiązywane połączenia ma swój własny CS.
* **Metaverse (MV)** — obiekty, które muszą być synchronizowane są tworzone w tym miejscu oparciu o reguły synchronizacji. Obiekty muszą istnieć w MV, zanim można wypełnić obiektów i atrybutów, które mają inne katalogi połączonych. Istnieje tylko jeden MV.
* **Synchronizowanie zasad** — decyduje, obiekty, które zostaną utworzone (planowanych) lub połączone obiekty w MV (połączone). Reguły synchronizacji podjąć decyzję, wartości atrybutów, które mają zostać skopiowane lub przekształcane do i z katalogów.
* **Profile uruchamiania** — zawiera również kroki procesu kopiowania obiektów i ich wartości atrybutów zgodnie z regułami synchronizacji między — obszarów tymczasowych i połączonych katalogów.

Istnieją różne profile uruchamiania w celu zoptymalizowania wydajności aparatu inicjowania obsługi administracyjnej. Większość organizacji będzie używane domyślne harmonogramy i uruchomić profile dla normalnych operacji, ale w niektórych organizacjach może być konieczne [zmienić harmonogram](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) , lub uruchomić inne profile uruchamiania w celu zaspokojenia rzadkich sytuacji. Dostępne są następujące profile przebiegu:

### <a name="initial-sync-profile"></a>Profil początkowej synchronizacji

Profil synchronizacji początkowej polega na odczytywanie połączonych katalogi, takich jak lasu usługi Active Directory po raz pierwszy. Następnie robi analizę na wszystkie wpisy w bazie danych aparatu synchronizacji. Początkowa cyklu spowoduje utworzenie nowych obiektów w usłudze Azure AD i zajmie dodatkowy czas do wykonania w przypadku dużych lasów usługi Active Directory. Synchronizacja początkowa obejmuje następujące kroki:

1. Pełny import na wszystkich łączników
2. Pełna synchronizacja w wszystkie łączniki
3. Eksport na wszystkich łączników

### <a name="delta-sync-profile"></a>Profil synchronizacji różnicowej

Aby zoptymalizować proces synchronizacji to profilu uruchamiania tylko proces zmiany (tworzy, usuwa i aktualizuje) obiektów w połączonych katalogów, od momentu ostatniego procesu synchronizacji. Domyślnie profilu synchronizacja różnicowa jest uruchamiane co 30 minut. Organizacje powinny wszelkich starań, aby zachować czas potrzebny na poniżej 30 minut, aby upewnić się, że usługi Azure AD jest aktualny. Aby monitorować kondycję platformy Azure AD Connect, należy użyć [agenta monitorowania kondycji](how-to-connect-health-sync.md) aby zobaczyć wszystkie problemy z procesu. Profil synchronizacji różnicowej obejmuje następujące kroki:

1. Import zmian na wszystkich łączników
2. Synchronizacja różnicowa na wszystkich łączników
3. Eksport na wszystkich łączników

Scenariusz synchronizacji różnicowej organizacji typowego przedsiębiorstwa jest:

- około 1% obiektów są usuwane.
- około 1% obiektów są tworzone.
- ~ 5% obiektów są modyfikowane.

Stawka zmiany mogą się różnić w zależności od tego, jak często aktualizować użytkowników w usłudze Active Directory w Twojej organizacji. Na przykład większe zmiany może wystąpić z sezonowości zatrudniania i zmniejszania pracownikom.

### <a name="full-sync-profile"></a>Pełna synchronizacja profilu

Cykl Pełna synchronizacja jest wymagana, jeśli zostały wprowadzone następujące zmiany konfiguracji:



- Zwiększenie zakresu obiektów lub atrybutów, które mają zaimportowane z połączonych katalogów. Na przykład po dodaniu domeny lub jednostki Organizacyjnej do zakresu importu.
- Wprowadzone zmiany reguł synchronizacji. Na przykład podczas tworzenia nowej reguły, aby wypełnić zwrot grzecznościowy dla użytkownika w usłudze Azure AD z extension_attribute3 w usłudze Active Directory. Ta aktualizacja wymaga, że aparat inicjowania obsługi administracyjnej ponownej analizy wszyscy istniejący użytkownicy, aby zaktualizować ich tytułów, aby zastosować zmianę, w przyszłości.

Następujące operacje są uwzględnione w cyklu pełnej synchronizacji:

1. Pełny import na wszystkich łączników
2. Synchronizacja pełnej/różnicowej dla wszystkich łączników
3. Eksport na wszystkich łączników

> [!NOTE]
> Starannego planowania jest wymagana podczas wykonywania aktualizacji zbiorczych do wielu obiektów w usłudze Active Directory lub w usłudze Azure AD. Aktualizacje zbiorcze spowoduje, że proces synchronizacji różnicowej trwać dłużej, podczas importowania, ponieważ zmieniono wiele obiektów. Importy długo może się zdarzyć, nawet wtedy, gdy aktualizacja zbiorcza nie wpływają procesu synchronizacji. Na przykład przypisywanie licencji do wielu użytkowników w usłudze Azure AD spowoduje, że cyklu długie importowania z usługi Azure AD, ale nie powoduje zmiany atrybutów w usłudze Active Directory.

### <a name="synchronization"></a>Synchronizacja

W czasie wykonywania procesu synchronizacji ma następujące cechy wydajności:

* Synchronizacja jest pojedynczego wątku, co oznacza, że aparat inicjowania obsługi administracyjnej nie wszystkie przetwarzania równoległego profile uruchamiania połączonych katalogów, obiektów lub atrybutów.
* Czas importowania rośnie liniowo wraz ze liczbę obiektów, które są synchronizowane. Na przykład jeśli obiekty 10 000 10 minut, aby zaimportować, następnie 20 000 obiektów potrwa około 20 minut na tym samym serwerze.
* Eksport jest również liniowego.
* Synchronizacja będzie rosnąć wykładniczo na podstawie liczby obiektów z odwołaniami do innych obiektów. Członkostwa w grupie grup zagnieżdżonych się głównym ten wpływ na wydajność, ponieważ składowe odnoszą się do obiektów użytkowników lub innych grup. Te odwołania, należy znaleźć i w odwołaniu do rzeczywistych obiektów w MV, aby zakończyć cykl synchronizacji.

### <a name="filtering"></a>Filtering

Rozmiar topologii usługi Active Directory, który chcesz zaimportować jest najwyższym współczynnik wywieranie wpływu na wydajność i całkowity czas zajmie wewnętrznych składników silnika inicjowania obsługi administracyjnej.

[Filtrowanie](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) powinien być używany do zmniejszenia obiekty do synchronizowanych. Pozwoli uniknąć niepotrzebnego obiektów i wyeksportowane do usługi Azure AD. Według priorytetu dostępne są następujące techniki filtrowania:



- **Filtrowanie oparte na domenie** — Użyj tej opcji, aby wybrać konkretne domeny do synchronizacji z usługą Azure AD. Należy dodać i usuwanie domen z Konfiguracja aparatu synchronizacji, po wprowadzeniu zmian w infrastrukturze lokalnej po zainstalowaniu synchronizacji programu Azure AD Connect.
- **Filtrowanie jednostki Organizacyjnej organizacji** -używa jednostek organizacyjnych pod kątem konkretnych obiektów w domenach usługi Active Directory do obsługi administracyjnej do usługi Azure AD. Filtrowanie jednostki Organizacyjnej jest druga, zaleca się filtrowanie mechanizmu, ponieważ używa prostego zapytania dotyczące zakresu LDAP do importowania mniejszy podzbiór obiektów z usługi Active Directory.
- **Atrybut filtrowanie na obiekt** — używa wartości atrybutów obiektów, aby zdecydować, czy określonego obiektu w usłudze Active Directory są aprowizowane w usłudze Azure AD. Filtrowanie atrybutów nadaje się doskonale do precyzyjnego filtry, gdy jednostki Organizacyjnej filtrowanie domen i nie spełnia określonych wymagań filtrowania. Filtrowanie atrybutów nie skrócić czas importowania, ale można zmniejszyć synchronizacji i eksportowania razy.
- **Filtrowanie na podstawie grupy** -używa grupy członkostwa zdecydować, czy obiekty powinny zostać aprowizowane w usłudze Azure AD. Filtrowanie na podstawie grupy tylko odpowiednie do testowania sytuacjach i nie zaleca się w środowisku produkcyjnym, ze względu na dodatkowe obciążenie jest wymagane do sprawdzenia członkostwa w grupie cyklu synchronizacji.

Wiele trwałego [rozłączników](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects) w usługi Active CS katalogu może spowodować wydłużenie czasu synchronizacji, ponieważ aparat inicjowania obsługi administracyjnej musi ponownie Oceń każdy obiekt izolacyjny możliwe połączenia w cyklu synchronizacji. Aby rozwiązać ten problem, należy wziąć pod uwagę jedną z następujących zaleceń:



- Umieść rozłączników poza zakresem dla importu za pomocą domeny lub jednostki Organizacyjnej filtrowania.
- Projekt/scalania obiektów MV i zestaw [cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) atrybutu jest równa True, aby uniemożliwić udostępnianie tych obiektów w usłudze Azure AD CS.

> [!NOTE]
> Użytkownicy mogą uzyskać mylić lub problemy z uprawnieniami aplikacji może wystąpić, gdy za dużo obiektów są filtrowane. Na przykład w hybrydowe programu Exchange online implementacji programu ze skrzynkami pocztowymi w środowisku lokalnym będzie towarzyszyć większej liczbie użytkowników w ich globalnej liście adresowej niż użytkowników ze skrzynkami pocztowymi w programie Exchange online. W innych przypadkach użytkownik może być do udzielania dostępu w aplikacji w chmurze do innego użytkownika, który nie jest częścią zakresu przefiltrowany zestaw obiektów.

### <a name="attribute-flows"></a>Przepływy atrybutów

Przepływy atrybutów to proces kopiowania lub przekształcania wartości atrybutów obiektów z jednego połączonego katalogu do innego katalogu połączonych. Są one zdefiniowane jako część reguły synchronizacji. Na przykład po zmianie numeru telefonu użytkownika w usłudze Active Directory zostanie zaktualizowany numer telefonu w usłudze Azure AD. Organizacje mogą [zmodyfikować przepływy atrybutów](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-change-the-configuration) do zestawu różnych wymagań. Zalecane jest, że w celu skopiowanie istniejących przepływów atrybutów, przed ich zmieniania.

Przekierowuje proste, jak przepływających wartość atrybutu na inny atrybut nie ma wpływu na wydajność materiału. Przykład przekierowanie będą przepływać numer telefonu komórkowego w usłudze Active Directory numer telefonu w biurze w usłudze Azure AD.

Przekształcanie wartości atrybutów może mieć negatywny wpływ na wydajność na temat procesu synchronizacji. Przekształcanie wartości atrybutów obejmuje modyfikowanie, ponowne formatowanie, złączenie lub odjęcie wartości atrybutów.

Organizacje mogą uniemożliwić niektórych atrybutów, które będą przepływać do usługi Azure AD, ale nie będzie mieć wpływ na wydajność aparatu inicjowania obsługi administracyjnej.

> [!NOTE]
> Nie należy usuwać przepływy niechciane atrybutów w regułach synchronizacji. Zalecane jest, należy wyłączyć je, ponieważ usunięto zasady są ponownie tworzone podczas uaktualniania program Azure AD Connect.

## <a name="azure-ad-connect-dependency-factors"></a>Czynniki zależności w usłudze Azure AD Connect

Wydajność programu Azure AD Connect jest zależna od wydajności połączonych katalogów go importu i eksportu do. Na przykład rozmiar usługi Active Directory, należy go zaimportować lub opóźnienia sieci z usługą Azure AD. Bazy danych SQL, używanych przez aparat inicjowania obsługi administracyjnej ma również wpływ na ogólną wydajność cykl synchronizacji.

### <a name="active-directory-factors"></a>Active Directory czynniki

Jak wspomniano wcześniej, liczba obiektów do zaimportowania ma wpływ na wydajność znacznie. [Sprzęt i wymagania wstępne programu Azure AD Connect](how-to-connect-install-prerequisites.md) konturu warstwami sprzętu, w zależności od rozmiaru wdrożenia. Program Azure AD Connect obsługują tylko określonych topologii zgodnie z opisem w [topologie obsługiwane w programie Azure AD Connect](plan-connect-topologies.md). Istnieją, bez optymalizacji wydajności i zalecenia dotyczące topologii nieobsługiwany.

Upewnij się, że serwer usługi Azure AD Connect spełnia wymagania sprzętowe na podstawie rozmiaru usługi Active Directory, który chcesz zaimportować. Niepoprawna lub powolne łączności między serwerem usługi Azure AD Connect i kontrolery domeny usługi Active Directory może spowolnić importu.

### <a name="azure-ad-factors"></a>Czynniki usługi Azure AD

Usługa Azure AD wymaga ograniczenia przepustowości, aby chronić usługę w chmurze przed atakami typu "odmowa usługi" (DoS). Obecnie usługa Azure AD ma limit ograniczania 7000 zapisów na 5 minut (84,000 za godzinę). Na przykład mogą być ograniczone następujące operacje:



- Usługa Azure AD Connect eksportowanie do usługi Azure AD.
- Skrypty programu PowerShell lub aplikacji, aktualizacji usługi Azure AD bezpośrednio nawet w tle, takie jak członkostwa w grupie dynamicznej.
- Użytkownicy aktualizacji własnych rekordów tożsamości, takich jak rejestrowanie na potrzeby uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ (Samoobsługowe resetowanie haseł).
- Operacje w graficznym interfejsie użytkownika.

Planowanie zadań wdrożenia i konserwacji, aby upewnić się, że cykl synchronizacji usługi Azure AD Connect nie ma wpływu limity ograniczania przepływności. Na przykład w przypadku dużych wave zatrudniania, której możesz utworzyć tysiące tożsamości użytkowników do członkostwa w grupach dynamicznych, licencjonowanie przypisania, może to spowodować aktualizacji i Samoobsługowe resetowanie haseł rejestracji. Zaleca się rozłożyć te operacje zapisu za pośrednictwem kilku godzin lub kilka dni.

### <a name="sql-database-factors"></a>Czynniki bazy danych SQL

Rozmiar topologii usługi Active Directory źródła będzie miała wpływ na wydajność bazy danych SQL. Postępuj zgodnie z [wymagania sprzętowe](how-to-connect-install-prerequisites.md) dla programu SQL server bazy danych, a następnie należy wziąć pod uwagę następujące zalecenia:



- Organizacje z więcej niż 100 000 użytkowników, można zmniejszyć opóźnienia sieciowe kolokowanie bazy danych SQL, a aparat inicjowania obsługi administracyjnej na tym samym serwerze.
- Ze względu na wysoką dysku danych wejściowych i wyjściowych (We/Wy) wymagania dotyczące synchronizacji, użyj pełny stan dyski (SSD) dla bazy danych SQL aparatu inicjowania obsługi administracyjnej, aby uzyskać optymalne wyniki, jeśli nie jest to możliwe, należy wziąć pod uwagę konfiguracja RAID 1 lub RAID 0.
- Nie wykonuj pełną synchronizację uprzedzając zdarzenie; powoduje niepotrzebne współczynnika zmian i wolniejszych czasów odpowiedzi.

## <a name="conclusion"></a>Podsumowanie

Aby zoptymalizować wydajność wdrożenia usługi Azure AD Connect, należy wziąć pod uwagę następujące zalecenia:



- Użyj [zalecana konfiguracja sprzętu](how-to-connect-install-prerequisites.md) oparte na rozmiarze wdrożenia serwera usługi Azure AD Connect.
- Podczas uaktualniania program Azure AD Connect w przypadku wdrożeń na dużą skalę, należy wziąć pod uwagę przy użyciu [metody migracji swing](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version#swing-migration), aby upewnić się, że masz co najmniej przestojów i uzyskać większą niezawodność. 
- Na użytek bazy danych SQL, aby uzyskać najlepszą wydajność zapisu dysków SSD.
- Zakres usługi Active Directory, obejmujący tylko obiekty, które należy aprowizować w usłudze Azure AD, przy użyciu domeny, jednostki Organizacyjnej lub filtrowanie atrybutów filtru.
- Jeśli potrzebujesz zmienić domyślne reguły przepływu atrybutu, najpierw skopiować regułę, a następnie zmień kopii i Wyłącz istniejącą regułę. Pamiętaj, aby ponownie uruchomić pełną synchronizację.
- Zaplanuj czas wystarczający dla pełnej synchronizacji początkowej profilu uruchamiania.
- Dokładamy wszelkich starań zakończyć cykl synchronizacji różnicowej w ciągu 30 minut. Jeśli profil synchronizacja różnicowa nie wykona w ciągu 30 minut, zmodyfikuj domyślna częstotliwość synchronizacji obejmujący cykl synchronizacji różnicowej kompletny.
- Monitor usługi [program Azure AD Connect sync kondycji](how-to-connect-health-agent-install.md) w usłudze Azure AD.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
