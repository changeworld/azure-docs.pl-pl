---
title: Czynniki wpływające na wydajność programu Azure AD Connect
description: W tym dokumencie wyjaśniono, jak różne czynniki wpływają na aparat inicjowania obsługi administracyjnej usługi Azure AD Connect. Te czynniki pomogą organizacjom zaplanować wdrożenie usługi Azure AD Connect, aby upewnić się, że spełnia ich wymagania dotyczące synchronizacji.
services: active-directory
author: billmath
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: hybrid
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2018
ms.reviewer: martincoetzer
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5518d516848ba7c006827faa41ff76bbca35d0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897055"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>Czynniki wpływające na wydajność programu Azure AD Connect

Usługa Azure AD Connect synchronizuje usługę Active Directory z usługą Azure AD. Ten serwer jest kluczowym składnikiem przenoszenia tożsamości użytkowników do chmury. Podstawowe czynniki wpływające na wydajność usługi Azure AD Connect to:

| **Współczynnik projektu**| **Definicja** |
|:-|-|
| Topologia| Dystrybucja punktów końcowych i składników usługi Azure AD Connect musi zarządzać w sieci. |
| Skalowanie| Liczba obiektów, takich jak użytkownicy, grupy i jednostki organizacyjne, które mają być zarządzane przez usługę Azure AD Connect. |
| Sprzęt| Sprzęt (fizyczny lub wirtualny) dla usługi Azure AD Connect i zależna wydajność każdego składnika sprzętowego, w tym procesora, pamięci, sieci i konfiguracji dysku twardego. |
| Konfigurowanie| Jak usługa Azure AD Connect przetwarza katalogi i informacje. |
| Ładowanie| Częstotliwość zmian obiektu. Obciążenia mogą się różnić w ciągu godziny, dnia lub tygodnia. W zależności od komponentu może być zaprojektować obciążenie szczytowe lub średnie obciążenie. |

Celem tego dokumentu jest opisanie czynników wpływających na wydajność aparatu inicjowania obsługi administracyjnej usługi Azure AD Connect. Duże lub złożone organizacje (organizacje inicjujące inicjowanie obsługi administracyjnej ponad 100 000 obiektów) mogą używać zaleceń w celu optymalizacji ich implementacji usługi Azure AD Connect, jeśli wystąpią jakiekolwiek problemy z wydajnością opisane tutaj. Inne składniki usługi Azure AD Connect, takie jak [kondycja](how-to-connect-health-agent-install.md) usługi Azure AD Connect i agentów nie są omówione w tym miejscu.

> [!IMPORTANT]
> Firma Microsoft nie obsługuje modyfikowania ani obsługi usługi Azure AD Connect poza akcjami, które są formalnie udokumentowane. Każda z tych akcji może spowodować niespójny lub nieobsługiowany stan synchronizacji usługi Azure AD Connect. W rezultacie firma Microsoft nie może zapewnić pomocy technicznej dla takich wdrożeń.

## <a name="azure-ad-connect-component-factors"></a>Współczynniki składników usługi Azure AD Connect

Na poniższym diagramie przedstawiono architekturę wysokiego poziomu aparatu inicjowania obsługi administracyjnej łączącego się z jednym lasem, chociaż obsługiwanych jest wiele lasów. Ta architektura pokazuje, jak różne składniki współdziałają ze sobą.

![AzureADConnentInternal](media/plan-connect-performance-factors/AzureADConnentInternal.png)

Aparat inicjowania obsługi administracyjnej łączy się z każdym lasem usługi Active Directory i usługą Azure AD. Proces odczytywania informacji z każdego katalogu nosi nazwę Import. Eksport odnosi się do aktualizowania katalogów z aparatu inicjowania obsługi administracyjnej. Synchronizacja ocenia reguły przepływu obiektów wewnątrz aparatu inicjowania obsługi administracyjnej. Aby uzyskać głębsze nurkowanie, można zapoznać się z [synchronizacją usługi Azure AD Connect: Opis architektury](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture).

Usługa Azure AD Connect używa następujących obszarów przejściowych, reguł i procesów, aby umożliwić synchronizację z usługi Active Directory z usługą Azure AD:

* **Przestrzeń łącznika (CS)** — obiekty z każdego połączonego katalogu (CD), rzeczywiste katalogi, są przemieszczane w tym miejscu najpierw, zanim będą mogły być przetwarzane przez aparat inicjowania obsługi administracyjnej. Usługa Azure AD ma własny CS i każdy las, z którego łączysz, ma swój własny CS.
* **Metaverse (MV)** - Obiekty, które muszą być synchronizowane są tworzone w tym miejscu na podstawie reguł synchronizacji. Obiekty muszą istnieć w MV, zanim będą mogły wypełniać obiekty i atrybuty do innych połączonych katalogów. Jest tylko jeden MV.
* **Reguły synchronizacji** — decydują, które obiekty zostaną utworzone (rzutowane) lub połączone (połączone) z obiektami w MV. Reguły synchronizacji decydują również, które wartości atrybutów zostaną skopiowane lub przekształcone do i z katalogów.
* **Uruchamianie profili** — zestawia kroki procesu kopiowania obiektów i ich wartości atrybutów zgodnie z regułami synchronizacji między obszarami przejściowymi i połączonymi katalogami.

Istnieją różne profile uruchamiania w celu optymalizacji wydajności aparatu inicjowania obsługi administracyjnej. Większość organizacji będzie używać domyślnych harmonogramów i uruchamiać profile dla normalnych operacji, ale niektóre organizacje mogą być musiały [zmienić harmonogram](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) lub wyzwolić inne profile uruchamiania, aby zaspokoić nietypowe sytuacje. Dostępne są następujące profile uruchamiania:

### <a name="initial-sync-profile"></a>Początkowy profil synchronizacji

Profil synchronizacji początkowej to proces poczynienia połączonych katalogów, takich jak las usługi Active Directory, po raz pierwszy. Następnie wykonuje analizę wszystkich wpisów w bazie danych aparatu synchronizacji. Początkowy cykl utworzy nowe obiekty w usłudze Azure AD i zajmie więcej czasu, aby zakończyć, jeśli lasy usługi Active Directory są duże. Synchronizacja początkowa obejmuje następujące kroki:

1. Pełny import na wszystkich złączach
2. Pełna synchronizacja na wszystkich złączach
3. Eksport na wszystkich złączach

### <a name="delta-sync-profile"></a>Profil synchronizacji różnicowej

Aby zoptymalizować proces synchronizacji, ten profil uruchamiania przetwarza tylko zmiany (tworzy, usuwa i aktualizuje) obiektów w połączonych katalogach od czasu ostatniego procesu synchronizacji. Domyślnie profil synchronizacji różnicowej jest uruchamiany co 30 minut. Organizacje powinny dążyć do utrzymania czasu poniżej 30 minut, aby upewnić się, że usługa Azure AD jest aktualna. Aby monitorować kondycję usługi Azure AD Connect, użyj [agenta monitorowania kondycji,](how-to-connect-health-sync.md) aby wyświetlić wszelkie problemy z procesem. Profil synchronizacji różnicowej zawiera następujące kroki:

1. Import delta na wszystkich złączach
2. Synchronizacja delta na wszystkich złączach
3. Eksport na wszystkich złączach

Typowy scenariusz synchronizacji delta organizacji przedsiębiorstwa to:

- ~1% obiektów jest usuwanych
- ~1% obiektów jest tworzonych
- ~5% obiektów jest modyfikowanych

Szybkość zmian może się różnić w zależności od częstotliwości aktualizacji użytkowników w usłudze Active Directory. Na przykład wyższe wskaźniki zmian mogą wystąpić wraz z sezonowością zatrudniania i zmniejszania siły roboczej.

### <a name="full-sync-profile"></a>Pełny profil synchronizacji

Pełny cykl synchronizacji jest wymagany, jeśli wprowadzono dowolną z następujących zmian konfiguracji:



- Zwiększono zakres obiektów lub atrybutów, które mają być importowane z połączonych katalogów. Na przykład po dodaniu domeny lub operacji organizacyjnej do zakresu importu.
- Wprowadzono zmiany w regułach synchronizacji. Na przykład podczas tworzenia nowej reguły do wypełniania tytułu użytkownika w usłudze Azure AD z extension_attribute3 w usłudze Active Directory. Ta aktualizacja wymaga, aby aparat inicjowania obsługi administracyjnej ponownie zbadać wszystkich istniejących użytkowników, aby zaktualizować swoje tytuły, aby zastosować zmiany w przyszłości.

Następujące operacje są uwzględniane w cyklu pełnej synchronizacji:

1. Pełny import na wszystkich złączach
2. Pełna/delta synchronizacja na wszystkich złączach
3. Eksport na wszystkich złączach

> [!NOTE]
> Staranne planowanie jest wymagane podczas wykonywania zbiorczych aktualizacji wielu obiektów w usłudze Active Directory lub usłudze Azure AD. Aktualizacje zbiorcze spowoduje, że proces synchronizacji różnicowej potrwa dłużej podczas importowania, ponieważ wiele obiektów uległo zmianie. Długie importy mogą się zdarzyć, nawet jeśli aktualizacja zbiorcza nie ma wpływu na proces synchronizacji. Na przykład przypisanie licencji do wielu użytkowników w usłudze Azure AD spowoduje długi cykl importu z usługi Azure AD, ale nie spowoduje żadnych zmian atrybutów w usłudze Active Directory.

### <a name="synchronization"></a>Synchronizacja

Środowisko wykonawcze procesu synchronizacji ma następujące właściwości wydajności:

* Synchronizacja jest jednowątkowa, co oznacza, że aparat inicjowania obsługi administracyjnej nie wykonuje żadnego równoległego przetwarzania profilów uruchamiania połączonych katalogów, obiektów lub atrybutów.
* Czas importu rośnie liniowo wraz z liczbą synchronizowanych obiektów. Na przykład jeśli importowanie 10 000 obiektów trwa 10 minut, 20 000 obiektów zajmie około 20 minut na tym samym serwerze.
* Eksport jest również liniowy.
* Synchronizacja będzie rosnąć wykładniczo na podstawie liczby obiektów z odwołaniami do innych obiektów. Członkostwo w grupach i grupy zagnieżdżone mają główny wpływ na wydajność, ponieważ ich członkowie odnoszą się do obiektów użytkowników lub innych grup. Odwołania te należy znaleźć i odwołuje się do rzeczywistych obiektów w MV, aby zakończyć cykl synchronizacji.

### <a name="filtering"></a>Filtrowanie

Rozmiar topologii usługi Active Directory, którą chcesz zaimportować, jest czynnikiem numer jeden wpływającym na wydajność i całkowity czas, przez który wezmą udział wewnętrzne składniki aparatu inicjowania obsługi administracyjnej.

[Filtrowanie](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) powinno służyć do zmniejszenia obiektów do synchronizacji. Uniemożliwi to przetwarzanie i eksportowanie niepotrzebnych obiektów do usługi Azure AD. W kolejności preferencji dostępne są następujące techniki filtrowania:



- **Filtrowanie oparte na domenie** — użyj tej opcji, aby wybrać określone domeny do synchronizacji z usługą Azure AD. Po zainstalowaniu synchronizacji usługi Azure AD Connect należy dodać i usunąć domeny z konfiguracji aparatu synchronizacji po wprowadzeniu zmian w infrastrukturze lokalnej.
- **Filtrowanie jednostek organizacyjnych —** używa jednostek organizacyjnych do kierowania określonych obiektów w domenach usługi Active Directory do inicjowania obsługi administracyjnej usługi Azure AD. Filtrowanie naliczanie substancji organizacyjnych jest drugim zalecanym mechanizmem filtrowania, ponieważ używa prostych zapytań zakresu LDAP do importowania mniejszego podzbioru obiektów z usługi Active Directory.
- **Filtrowanie atrybutów na obiekt** — używa wartości atrybutów na obiektach, aby zdecydować, czy określony obiekt w usłudze Active Directory jest aprowidyzowany w usłudze Azure AD. Filtrowanie atrybutów doskonale nadaje się do dostrajania filtrów, gdy filtrowanie domeny i ou nie spełnia określonych wymagań dotyczących filtrowania. Filtrowanie atrybutów nie skraca czasu importu, ale może skrócić czas synchronizacji i eksportu.
- **Filtrowanie oparte na grupach** — używa członkostwa w grupie, aby zdecydować, czy obiekty powinny być aprowiowane w usłudze Azure AD. Filtrowanie oparte na grupach jest odpowiednie tylko do testowania sytuacji i nie jest zalecane do produkcji, ze względu na dodatkowe obciążenie wymagane do sprawdzenia członkostwa w grupie podczas cyklu synchronizacji.

Wiele trwałych [obiektów rozłącznika](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects) w programie Active Directory CS może spowodować dłuższy czas synchronizacji, ponieważ aparat inicjowania obsługi administracyjnej musi ponownie ocenić każdy obiekt rozłącznika w celu uzyskania możliwego połączenia w cyklu synchronizacji. Aby rozwiązać ten problem, należy wziąć pod uwagę jedną z następujących zaleceń:



- Umieść obiekty rozłącznika poza zakresem do importowania przy użyciu filtrowania domeny lub usługi organizacyjnej.
- Project/join the objects to the MV and set the [cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) attribute equal to True, aby zapobiec inicjowaniu obsługi administracyjnej tych obiektów w usłudze Azure AD CS.

> [!NOTE]
> Użytkownicy mogą się pomylić lub problemy z uprawnieniami aplikacji mogą wystąpić, gdy zbyt wiele obiektów są filtrowane. Na przykład w hybrydowej implementacji online programu Exchange użytkownicy z lokalnymi skrzynkami pocztowymi zobaczą więcej użytkowników na swojej globalnej liście adresów niż użytkownicy ze skrzynkami pocztowymi w usłudze Exchange online. W innych przypadkach użytkownik może chcieć udzielić dostępu w aplikacji w chmurze innemu użytkownikowi, który nie jest częścią zakresu filtrowanego zestawu obiektów.

### <a name="attribute-flows"></a>Przepływy atrybutów

Przepływy atrybutów to proces kopiowania lub przekształcania wartości atrybutów obiektów z jednego połączonego katalogu do innego połączonego katalogu. Są one definiowane jako część reguł synchronizacji. Na przykład po zmianie numeru telefonu użytkownika w usłudze Active Directory numer telefonu w usłudze Azure AD zostanie zaktualizowany. Organizacje mogą [modyfikować przepływy atrybutów](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-change-the-configuration) do różnych wymagań. Zaleca się skopiowanie istniejących przepływów atrybutów przed ich zmianą.

Proste przekierowania, takie jak przepływ wartości atrybutu do innego atrybutu, nie mają istotnego wpływu na wydajność. Przykładem przekierowania jest przepływ numeru telefonu komórkowego w usłudze Active Directory do numeru telefonu pakietu Office w usłudze Azure AD.

Przekształcanie wartości atrybutów może mieć wpływ na wydajność procesu synchronizacji. Przekształcanie wartości atrybutów obejmuje modyfikowanie, formatowanie, łączenie lub odejmowanie wartości atrybutów.

Organizacje mogą uniemożliwić przepływ niektórych atrybutów do usługi Azure AD, ale nie wpłynie to na wydajność aparatu inicjowania obsługi administracyjnej.

> [!NOTE]
> Nie usuwaj niechcianych przepływów atrybutów w regułach synchronizacji. Zaleca się, aby raczej je wyłączyć, ponieważ usunięte reguły są odtworzone podczas uaktualnień usługi Azure AD Connect.

## <a name="azure-ad-connect-dependency-factors"></a>Współczynniki zależności usługi Azure AD Connect

Wydajność usługi Azure AD Connect zależy od wydajności połączonych katalogów, które importuje i eksportuje do. Na przykład rozmiar usługi Active Directory, która musi zaimportować lub opóźnienie sieci do usługi Azure AD. Baza danych SQL, którego używa aparat inicjowania obsługi administracyjnej, wpływa również na ogólną wydajność cyklu synchronizacji.

### <a name="active-directory-factors"></a>Czynniki usługi Active Directory

Jak wspomniano wcześniej, liczba importowanych obiektów znacząco wpływa na wydajność. [Sprzęt i wymagania wstępne dla usługi Azure AD Connect](how-to-connect-install-prerequisites.md) przedstawiają określone warstwy sprzętu na podstawie rozmiaru wdrożenia. Usługa Azure AD Connect obsługuje tylko określone topologie opisane w [topologiach usługi Azure AD Connect.](plan-connect-topologies.md) Nie ma żadnych optymalizacji wydajności i zaleceń dla nieobsługiwał topologii.

Upewnij się, że serwer usługi Azure AD Connect spełnia wymagania sprzętowe na podstawie rozmiaru usługi Active Directory, który chcesz zaimportować. Zła lub niska łączność sieciowa między serwerem usługi Azure AD Connect a kontrolerami domeny usługi Active Directory może spowolnić importowanie.

### <a name="azure-ad-factors"></a>Czynniki usługi Azure AD

Usługa Azure AD używa ograniczania przepustowości w celu ochrony usługi w chmurze przed atakami typu "odmowa usługi" (DoS). Obecnie usługa Azure AD ma limit ograniczania przepustowości 7000 zapisów na 5 minut (84 000 na godzinę). Na przykład następujące operacje mogą być ograniczane:



- Eksport usługi Azure AD Connect do usługi Azure AD.
- Skrypty programu PowerShell lub aplikacje aktualizujące usługę Azure AD bezpośrednio nawet w tle, takie jak członkostwa w grupach dynamicznych.
- Użytkownicy aktualizujący własne rekordy tożsamości, takie jak rejestrowanie usługi MFA lub samoobsługowego resetowania haseł(self-service password reset).
- Operacje w graficznym interfejsie użytkownika.

Planowanie zadań wdrażania i konserwacji, aby upewnić się, że cykl synchronizacji usługi Azure AD Connect nie ma wpływu na limity ograniczania przepustowości. Na przykład jeśli masz dużą falę zatrudniania, w której tworzysz tysiące tożsamości użytkowników, może to spowodować aktualizacje dynamicznych członkostw w grupach, przypisań licencjonowania i samoobsługowych rejestracji resetowania haseł. Lepiej rozłożyć te zapisy na kilka godzin lub kilka dni.

### <a name="sql-database-factors"></a>Czynniki bazy danych SQL

Rozmiar źródła topologii usługi Active Directory wpłynie na wydajność bazy danych SQL. Postępuj zgodnie z [wymaganiami sprzętowymi](how-to-connect-install-prerequisites.md) bazy danych serwera SQL i rozważ następujące zalecenia:



- Organizacje z więcej niż 100 000 użytkowników można zmniejszyć opóźnienia sieci przez kolokację bazy danych SQL i aparatu inicjowania obsługi administracyjnej na tym samym serwerze.
- Ze względu na wysokie wymagania dotyczące wejścia i wyjścia dysku (We/Wy) procesu synchronizacji, użyj dysków SSD (SSD) dla bazy danych SQL aparatu inicjowania obsługi administracyjnej, aby uzyskać optymalne wyniki, jeśli nie jest to możliwe, rozważ konfiguracje RAID 0 lub RAID 1.
- Nie rób pełnej synchronizacji z wyprzedzeniem; powoduje niepotrzebne zmiany i wolniejsze czasy reakcji.

## <a name="conclusion"></a>Podsumowanie

Aby zoptymalizować wydajność implementacji usługi Azure AD Connect, należy wziąć pod uwagę następujące zalecenia:



- Użyj [zalecanej konfiguracji sprzętu](how-to-connect-install-prerequisites.md) na podstawie rozmiaru implementacji dla serwera Usługi Azure AD Connect.
- Podczas uaktualniania usługi Azure AD Connect w wdrożeniach na dużą skalę należy rozważyć użycie [metody migracji wahadłowej,](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version#swing-migration)aby upewnić się, że masz najmniej przestojów i najlepszą niezawodność. 
- Użyj SSD dla bazy danych SQL, aby uzyskać najlepszą wydajność zapisu.
- Filtruj zakres usługi Active Directory, aby uwzględnić tylko obiekty, które muszą być aprowidzone w usłudze Azure AD, przy użyciu domeny, ou lub filtrowania atrybutów.
- Jeśli chcesz zmienić domyślne reguły przepływu atrybutów, najpierw skopiuj regułę, a następnie zmień kopię i wyłącz oryginalną regułę. Pamiętaj, aby ponownie uruchomić pełną synchronizację.
- Zaplanuj odpowiedni czas dla początkowego profilu pełnego przebiegu synchronizacji.
- Staraj się zakończyć cykl synchronizacji delta w 30 minut. Jeśli profil synchronizacji różnicowej nie zostanie ukończony w ciągu 30 minut, zmodyfikuj domyślną częstotliwość synchronizacji, aby uwzględnić pełny cykl synchronizacji różnicowej.
- Monitorowanie [kondycji synchronizacji usługi Azure AD Connect](how-to-connect-health-agent-install.md) w usłudze Azure AD.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
