---
title: Jak naprawić zmodyfikowane reguły domyślne — Usługa Azure AD Connect | Dokumenty firmy Microsoft
description: Dowiedz się, jak naprawić zmodyfikowane reguły domyślne pochodzące z usługi Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4626e0149028a140d143fb8d0969a03b732201fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79036979"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Naprawianie zmodyfikowanych reguł domyślnych w usłudze Azure AD Connect

Usługa Azure Active Directory (Azure AD) Connect używa domyślnych reguł synchronizacji.  Niestety, zasady te nie mają zastosowania powszechnie do wszystkich organizacji. Na podstawie wymagań może być konieczne ich zmodyfikowanie. W tym artykule omówiono dwa przykłady najbardziej typowych dostosowań i wyjaśniono prawidłowy sposób osiągnięcia tych dostosowań.

>[!NOTE] 
> Modyfikowanie istniejących reguł domyślnych w celu osiągnięcia wymaganego dostosowania nie jest obsługiwane. Jeśli to zrobisz, uniemożliwia aktualizację tych reguł do najnowszej wersji w przyszłych wersjach. Nie dostaniesz potrzebnych poprawek błędów ani nowych funkcji. W tym dokumencie wyjaśniono, jak osiągnąć ten sam wynik bez modyfikowania istniejących reguł domyślnych. 

## <a name="how-to-identify-modified-default-rules"></a>Jak zidentyfikować zmodyfikowane reguły domyślne
Począwszy od wersji 1.3.7.0 usługi Azure AD Connect, łatwo zidentyfikować zmodyfikowaną regułę domyślną. Przejdź do **aplikacji na pulpicie**i wybierz pozycję **Edytor reguł synchronizacji**.

![Usługa Azure AD Connect z wyróżnionym edytorem reguł synchronizacji](media/how-to-connect-fix-default-rules/default1.png)

W edytorze wszystkie zmodyfikowane reguły domyślne są wyświetlane z ikoną ostrzeżenia przed nazwą.

![Ikona ostrzeżenia](media/how-to-connect-fix-default-rules/default2.png)

 Pojawia się również wyłączona reguła o tej samej nazwie obok (jest to standardowa reguła domyślna).

![Edytor reguł synchronizacji z regułami domyślnymi ze standardową regułą i zmodyfikowaną regułą domyślną](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Typowe dostosowania
Poniżej przedstawiono typowe dostosowania reguł domyślnych:

- Zmienianie przepływu atrybutów
- Zmień filtr zakresu
- Zmień warunek sprzężenia

Przed zmianą jakichkolwiek reguł:

- Wyłącz harmonogram synchronizacji. Harmonogram jest domyślnie uruchamiany co 30 minut. Upewnij się, że nie zaczyna się podczas wprowadzania zmian i rozwiązywania problemów z nowymi regułami. Aby tymczasowo wyłączyć harmonogram, uruchom program PowerShell i uruchom program `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 ![Polecenia programu PowerShell wyłączają harmonogram synchronizacji](media/how-to-connect-fix-default-rules/default3.png)

- Zmiana filtru zakresu może spowodować usunięcie obiektów w katalogu docelowym. Należy zachować ostrożność przed wprowadzeniem jakichkolwiek zmian w określaniu zakresu obiektów. Zaleca się wprowadzenie zmian na serwerze przejściowym przed wprowadzeniem zmian na aktywnym serwerze.
- Uruchom podgląd na jednym obiekcie, jak wspomniano w sekcji [Sprawdź poprawność reguły synchronizacji,](#validate-sync-rule) po dodaniu nowej reguły.
- Uruchom pełną synchronizację po dodaniu nowej reguły lub zmodyfikowaniu dowolnej reguły synchronizacji niestandardowej. Ta synchronizacja stosuje nowe reguły do wszystkich obiektów.

## <a name="change-attribute-flow"></a>Zmienianie przepływu atrybutów
Istnieją trzy różne scenariusze zmiany przepływu atrybutów:
- Dodawanie nowego atrybutu.
- Zastępowanie wartości istniejącego atrybutu.
- Nie można synchronizować istniejącego atrybutu.

Można to zrobić bez zmiany standardowych reguł domyślnych.

### <a name="add-a-new-attribute"></a>Dodawanie nowego atrybutu
Jeśli okaże się, że atrybut nie przepływa z katalogu źródłowego do katalogu docelowego, użyj [synchronizacji usługi Azure AD Connect: rozszerzenia katalogów,](how-to-connect-sync-feature-directory-extensions.md) aby rozwiązać ten problem.

Jeśli rozszerzenia nie działają dla Ciebie, spróbuj dodać dwie nowe reguły synchronizacji, opisane w poniższych sekcjach.


#### <a name="add-an-inbound-sync-rule"></a>Dodawanie reguły synchronizacji przychodzącej
Reguła synchronizacji przychodzącej oznacza, że źródłem atrybutu jest przestrzeń łącznika, a obiekt docelowy jest metaverse. Na przykład, aby nowy przepływ atrybutów z lokalnej usługi Active Directory do usługi Azure Active Directory, należy utworzyć nową regułę synchronizacji przychodzącej. Uruchom **Edytor reguł synchronizacji**, wybierz pozycję **Ruch przychodzący** jako kierunek i wybierz pozycję **Dodaj nową regułę**. 

 ![Edytor reguł synchronizacji](media/how-to-connect-fix-default-rules/default3a.png)

Postępuj zgodnie z własną konwencją nazewnictwa, aby nadać nazwę regule. Tutaj używamy **Custom In z AD - User**. Oznacza to, że reguła jest regułą niestandardową i jest regułą przychodzącą z obszaru łącznika usługi Active Directory do metaverse.   

 ![Tworzenie reguły synchronizacji przychodzącej](media/how-to-connect-fix-default-rules/default3b.png)

Podaj własny opis reguły, aby przyszłe utrzymanie reguły było łatwe. Na przykład opis może być oparty na tym, jaki jest cel reguły i dlaczego jest potrzebna.

Dokonaj wyboru pól **Połączony system**, Połączony typ obiektu **systemowego**i **Typ obiektu Metaverse.**

Określ wartość pierwszeństwa od 0 do 99 (im niższa liczba, tym wyższy priorytet). W polach **Znacznik**, **Włącz synchronizację haseł**i **Wyłączone** użyj domyślnych zaznaczeń.

Filtr **zakresu należy** zachować w stanie pustym. Oznacza to, że reguła ma zastosowanie do wszystkich obiektów połączonych między systemem połączonym z usługą Active Directory a metaverse.

Zachowaj **reguły sprzężenia** puste. Oznacza to, że ta reguła używa warunku sprzężenia zdefiniowanego w standardowej regule domyślnej. Jest to kolejny powód, aby nie wyłączać ani nie usuwać standardowej reguły domyślnej. Jeśli nie ma warunku sprzężenia, atrybut nie będzie przepływać. 

Dodaj odpowiednie przekształcenia dla swojego atrybutu. Można przypisać stałą, aby stały przepływ wartości do atrybutu docelowego. Można użyć bezpośredniego mapowania między atrybutem źródłowym lub docelowym. Można też użyć wyrażenia dla atrybutu. Oto różne [funkcje ekspresji,](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) których można użyć.

#### <a name="add-an-outbound-sync-rule"></a>Dodawanie reguły synchronizacji wychodzącej
Aby połączyć atrybut z katalogiem docelowym, należy utworzyć regułę wychodzącą. Oznacza to, że źródłem jest metaverse, a celem jest połączony system. Aby utworzyć regułę ruchu wychodzącego, uruchom **Edytor reguł synchronizacji**, zmień **kierunek** na **Wychodzący**i wybierz pozycję **Dodaj nową regułę**. 

![Edytor reguł synchronizacji](media/how-to-connect-fix-default-rules/default3c.png)

Podobnie jak w przypadku reguły przychodzącej, można użyć własnej konwencji nazewnictwa, aby nazwać regułę. Wybierz **połączony system** jako dzierżawcę usługi Azure AD i wybierz połączony obiekt systemowy, na który chcesz ustawić wartość atrybutu. Ustaw pierwszeństwo od 0 do 99. 

![Tworzenie reguły synchronizacji ruchu wychodzącego](media/how-to-connect-fix-default-rules/default3d.png)

Zachowaj puste **reguły** zakresu i **dołączania.** Wypełnij transformację jako stałą, bezpośrednią lub wyrażeniem. 

Teraz wiesz, jak zrobić nowy atrybut przepływu obiektu użytkownika z usługi Active Directory do usługi Azure Active Directory. Za pomocą tych kroków można mapować dowolny atrybut z dowolnego obiektu do źródła i obiektu docelowego. Aby uzyskać więcej informacji, zobacz [Tworzenie reguł synchronizacji niestandardowej](how-to-connect-create-custom-sync-rule.md) i [Przygotowywanie do aprowizowania użytkowników](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="override-the-value-of-an-existing-attribute"></a>Zastępowanie wartości istniejącego atrybutu
Można zastąpić wartość atrybutu, który został już zamapowany. Na przykład jeśli zawsze chcesz ustawić wartość null atrybutu w usłudze Azure AD, po prostu utwórz tylko regułę przychodzącą. Należy wykonać stałą wartość , `AuthoritativeNull`przepływ do atrybutu docelowego. 

>[!NOTE] 
> Użyj `AuthoritativeNull` zamiast `Null` w tym przypadku. Dzieje się tak, ponieważ wartość nienastępna wartości null zastępuje wartość null, nawet jeśli ma niższy priorytet (wyższa wartość liczbowa w regule). `AuthoritativeNull`, z drugiej strony, nie jest zastępowany wartością inną niż null przez inne reguły. 

### <a name="dont-sync-existing-attribute"></a>Nie synchronizuj istniejącego atrybutu
Jeśli chcesz wykluczyć atrybut z synchronizacji, użyj funkcji filtrowania atrybutów dostępnej w usłudze Azure AD Connect. Uruchom **usługę Azure AD Connect** z ikony pulpitu, a następnie wybierz pozycję Dostosuj opcje **synchronizacji**.

![Opcje dodatkowych zadań usługi Azure AD Connect](media/how-to-connect-fix-default-rules/default4.png)

 Upewnij się, że wybrano **aplikację i atrybuty usługi Azure AD,** a następnie wybierz pozycję **Dalej**.

![Opcjonalne funkcje usługi Azure AD Connect](media/how-to-connect-fix-default-rules/default5.png)

Wyczyść atrybuty, które chcesz wykluczyć z synchronizacji.

![Atrybuty usługi Azure AD Connect](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Zmień filtr zakresu
Usługa Azure AD Sync zajmuje się większością obiektów. Można zmniejszyć zakres obiektów i zmniejszyć liczbę obiektów do wyeksportowania, bez zmiany standardowych reguł synchronizacji domyślnej. 

Użyj jednej z następujących metod, aby zmniejszyć zakres synchronizowanych obiektów:

- cloudFiltered atrybut
- Filtrowanie jednostek organizacyjnych

Jeśli zmniejszysz zakres synchronizacji użytkowników, synchronizacja skrótów haseł zostanie również zatrzymana dla odfiltrowanych użytkowników. Jeśli obiekty są już synchronizowane, po zmniejszeniu zakresu odfiltrowane obiekty są usuwane z katalogu docelowego. Z tego powodu należy zapewnić bardzo ostrożny zakres.

>[!IMPORTANT] 
> Zwiększenie zakresu obiektów skonfigurowanych przez usługę Azure AD Connect nie jest zalecane. W ten sposób utrudnia zespołowi pomocy technicznej firmy Microsoft zrozumienie dostosowań. Jeśli musisz zwiększyć zakres obiektów, edytuj istniejącą regułę, sklonuj ją i wyłącz oryginalną regułę. 

### <a name="cloudfiltered-attribute"></a>cloudFiltered atrybut
Tego atrybutu nie można ustawić w usłudze Active Directory. Ustaw wartość tego atrybutu, dodając nową regułę przychodzącą. Następnie można użyć **transformacji** i **wyrażenia,** aby ustawić ten atrybut w metaverse. W poniższym przykładzie pokazano, że nie chcesz synchronizować wszystkich użytkowników, których nazwa działu zaczyna się od **HRD** (bez uwzględniania wielkości liter):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Najpierw przekonwertowaliśmy dział ze źródła (Active Directory) na małe litery. Następnie, korzystając `Left` z tej funkcji, wzięliśmy tylko `hrd`pierwsze trzy znaki i porównaliśmy je z . Jeśli jest dopasowana, wartość `True`jest `NULL`ustawiona na , w przeciwnym razie . Przy ustawianiu wartości null, niektóre inne reguły o niższym priorytecie (wyższa wartość liczbowa) można zapisać do niego z innym warunkiem. Uruchom podgląd na jednym obiekcie, aby sprawdzić poprawność reguły synchronizacji, jak wspomniano w sekcji [Sprawdzanie poprawności reguły synchronizacji.](#validate-sync-rule)

![Tworzenie opcji reguły synchronizacji przychodzącej](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Filtrowanie jednostek organizacyjnych
Można utworzyć jedną lub więcej jednostek organizacyjnych (UA) i przenieść obiekty, których nie chcesz synchronizować z tymi jednostkami organizacyjnymi. Następnie skonfiguruj filtrowanie ou w usłudze Azure AD Connect. Uruchom **usługę Azure AD Connect** z ikony pulpitu i wybierz następujące opcje. Można również skonfigurować filtrowanie instalacji organizacyjnej w czasie instalacji usługi Azure AD Connect. 

![Dodatkowe zadania usługi Azure AD Connect](media/how-to-connect-fix-default-rules/default8.png)

Postępuj zgodnie z instrukcjami kreatora i wyczyść procesory organizacyjne, których nie chcesz synchronizować.

![Opcje filtrowania domeny i usługi Organizacyjnej usługi Azure AD Connect](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Zmień warunek sprzężenia
Użyj domyślnych warunków sprzężenia skonfigurowanych przez usługę Azure AD Connect. Zmiana domyślnych warunków sprzężenia utrudnia pomocy technicznej firmy Microsoft zrozumienie dostosowań i obsługę produktu.

## <a name="validate-sync-rule"></a>Sprawdzanie poprawności reguły synchronizacji
Nowo dodana reguła synchronizacji można sprawdzić poprawność przy użyciu funkcji podglądu bez uruchamiania pełnego cyklu synchronizacji. W usłudze Azure AD Connect wybierz pozycję **Usługa synchronizacji**.

![Usługa Azure AD Connect z wyróżnioną usługą synchronizacji](media/how-to-connect-fix-default-rules/default10.png)

Wybierz **metaverse search**. Wybierz obiekt zakresu jako **osobę**, wybierz **dodaj klauzulę**i wymiewaj kryteria wyszukiwania. Następnie wybierz pozycję **Wyszukaj**i kliknij dwukrotnie obiekt w wynikach wyszukiwania. Upewnij się, że dane w usłudze Azure AD Connect są aktualne dla tego obiektu, uruchamiając import i synchronizację w lesie przed uruchomieniem tego kroku.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

W **polu Właściwości obiektu Metaverse**wybierz pozycję **Łączniki**, zaznacz obiekt w odpowiednim łączniku (lesie) i wybierz **polecenie Właściwości...**.

![Właściwości obiektu Metaverse](media/how-to-connect-fix-default-rules/default12.png)

Wybierz **podgląd...**

![Właściwości obiektu spacji łącznika](media/how-to-connect-fix-default-rules/default13a.png)

W oknie Podgląd wybierz pozycję **Generuj podgląd** i **Przepływ atrybutów importuj** w lewym okienku.

![Wersja zapoznawcza](media/how-to-connect-fix-default-rules/default14.png)
 
W tym miejscu należy zauważyć, że nowo dodana `cloudFiltered` reguła jest uruchamiana na obiekcie i ustawiła atrybut na true.

![Wersja zapoznawcza](media/how-to-connect-fix-default-rules/default15a.png)
 
Aby porównać zmodyfikowaną regułę z regułą domyślną, wyeksportuj obie reguły oddzielnie jako pliki tekstowe. Reguły te są eksportowane jako plik skryptu programu PowerShell. Można je porównać za pomocą dowolnego narzędzia do porównywania plików (na przykład windiff), aby zobaczyć zmiany. 
 
Należy zauważyć, że w `msExchMailboxGuid` zmodyfikowanej regule atrybut jest zmieniany na typ **wyrażenia,** a nie **direct**. Ponadto wartość zostanie zmieniona na **NULL** i **ExecuteOnce** opcji. Można zignorować różnice zidentyfikowane i pierwszeństwo. 

![moc narzędzia windiff](media/how-to-connect-fix-default-rules/default17.png)
 
Aby naprawić reguły, aby zmienić je z powrotem na ustawienia domyślne, usuń zmodyfikowaną regułę i włącz regułę domyślną. Upewnij się, że nie utracisz dostosowania, które próbujesz osiągnąć. Gdy będziesz gotowy, uruchom **pełną synchronizację**.

## <a name="next-steps"></a>Następne kroki
- [Sprzęt i wymagania wstępne](how-to-connect-install-prerequisites.md) 
- [Ustawienia ekspresowe](how-to-connect-install-express.md)
- [Ustawienia dostosowane](how-to-connect-install-custom.md)



