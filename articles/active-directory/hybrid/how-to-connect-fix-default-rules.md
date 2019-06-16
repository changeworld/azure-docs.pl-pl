---
title: Jak naprawić zmodyfikowane domyślne reguły — program Azure AD Connect | Dokumentacja firmy Microsoft
description: Dowiedz się, jak naprawić modyfikacji domyślnych reguł, które pochodzą z programem Azure AD Connect.
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
ms.openlocfilehash: d2f0956b44d6df64fb73e5eee7844574237d8755
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067632"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Napraw modyfikacji domyślnych reguł w programie Azure AD Connect

Usługa Azure Active Directory (Azure AD) Connect używa domyślnych reguł synchronizacji.  Niestety nie mają zastosowanie następujące reguły powszechnie posiadać wszystkie organizacje. Zgodnie z wymaganiami, konieczne może być ich modyfikować. W tym artykule omówiono dwa przykłady typowych dostosowań i wyjaśnia, prawidłowym sposobem, aby osiągnąć te dostosowania.

>[!NOTE] 
> Modyfikowanie istniejącej reguły domyślne do osiągnięcia wymaganych dostosowań nie jest obsługiwane. Jeśli to zrobisz, więc uniemożliwia, aktualizowanie tych reguł do najnowszej wersji w przyszłych wersjach. Nie będą otrzymywać poprawek, które są potrzebne i nowych funkcji. W tym dokumencie wyjaśniono, jak ten sam efekt bez modyfikowania istniejących reguł domyślnych. 

## <a name="how-to-identify-modified-default-rules"></a>Sposób identyfikacji zmodyfikowanego domyślnych reguł
Począwszy od wersji 1.3.7.0 programu Azure AD Connect jest łatwo identyfikować ich modyfikacji domyślną regułę. Przejdź do **aplikacji na pulpicie**i wybierz **Synchronization Rules Editor**.

![Program Azure AD Connect, Synchronization Rules Editor wyróżniony](media/how-to-connect-fix-default-rules/default1.png)

W edytorze żadnych modyfikacji domyślnych reguł są wyświetlane z ikoną ostrzeżenia przed nazwą.

![ikona ostrzeżenia](media/how-to-connect-fix-default-rules/default2.png)

 Wyłączone reguły o takiej samej nazwie obok jest także wyświetlany (jest to standardowy domyślna reguła).

![Synchronization Rules Editor, pokazujący standardowe, domyślne reguły i modyfikacji domyślną regułę](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Typowe dostosowania
Poniżej przedstawiono typowe dostosowania domyślnych reguł:

- Zmiana przepływu atrybutów
- Zmiana filtru określania zakresu
- Zmień warunek sprzężenia

Zanim będzie można zmienić żadnych reguł:

- Wyłącz harmonogram synchronizacji. Harmonogram domyślnie jest uruchamiane co 30 minut. Upewnij się, że nie jest uruchamiana, gdy one wprowadzania zmian i rozwiązywanie problemów z nowych zasad. Aby tymczasowo wyłączyć harmonogram, uruchom program PowerShell i uruchom `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 ![Polecenia programu PowerShell, aby wyłączyć harmonogram synchronizacji](media/how-to-connect-fix-default-rules/default3.png)

- Zmiana filtru określania zakresu może spowodować usunięcie obiektów w katalogu docelowym. Należy zachować ostrożność, przed wprowadzeniem jakichkolwiek zmian z zakresu obiektów. Firma Microsoft zaleca, wprowadzić zmiany na serwerze tymczasowym, przed wprowadzeniem zmian na aktywnym serwerze.
- Uruchom Podgląd na pojedynczy obiekt, zgodnie z opisem w [sprawdzania poprawności reguły synchronizacji](#validate-sync-rule) sekcji po dodaniu nowej reguły.
- Uruchom pełną synchronizację po dodaniu nowej reguły lub zmodyfikowanie reguły synchronizacji niestandardowych. Tej synchronizacji ma zastosowanie nowych zasad do wszystkich obiektów.

## <a name="change-attribute-flow"></a>Zmiana przepływu atrybutów
Istnieją trzy różne scenariusze zmiany przepływu atrybutów:
- Dodawanie nowego atrybutu.
- Zastępowanie wartości istniejący atrybut.
- Wybranie tej opcji nie można zsynchronizować istniejący atrybut.

Możesz tworzyć je bez zmieniania domyślnych standardowych reguł.

### <a name="add-a-new-attribute"></a>Dodaj nowy atrybut
Jeśli okaże się, że atrybutu nie przesyła danych z katalogu źródłowego do katalogu docelowego, użyj [synchronizacji programu Azure AD Connect: Rozszerzenia katalogów](how-to-connect-sync-feature-directory-extensions.md) Aby rozwiązać ten problem.

Jeśli rozszerzenia nie działa, spróbuj dodać dwa nowe reguły synchronizacji, opisane w poniższych sekcjach.


#### <a name="add-an-inbound-sync-rule"></a>Dodaj regułę synchronizacji ruchu przychodzącego
Regułę synchronizacji ruchu przychodzącego oznacza, że źródło atrybutu jest obszaru łącznika i miejsce docelowe jest metaverse. Na przykład być nowy atrybut przepływać z lokalnej usługi Active Directory do usługi Azure Active Directory, Utwórz nową regułę synchronizacji ruchu przychodzącego. Uruchom **Synchronization Rules Editor**, wybierz opcję **przychodzący** jako kierunku, a następnie wybierz **Dodaj nową regułę**. 

 ! Editor](media/how-to-connect-fix-default-rules/default3a.png) reguły synchronizacji

Postępuj zgodnie z konwencji nazewnictwa do nazwę reguły. W tym miejscu użyjemy **niestandardowe w AD — użytkownik**. Oznacza to, czy reguły niestandardowe reguły i jest Reguła ruchu przychodzącego z obszaru łącznika usługi Active Directory do obiektu metaverse.   

 ![Utwórz regułę synchronizacji ruchu przychodzącego](media/how-to-connect-fix-default-rules/default3b.png)

Podaj opis reguły, dzięki czemu przyszłych konserwacji reguły jest proste. Na przykład opis może opierać się na cel reguły jest i dlaczego jest potrzebny.

Wybierz odpowiednie opcje **połączony System**, **połączony System typu obiektu**, i **typ obiektu Metaverse** pola.

Określ wartość priorytetu z zakresu od 0 do 99 (niższy numer, tym wyższy priorytet). Dla **Tag**, **Włączanie synchronizacji haseł**, i **wyłączone** polach, będą używane wartości domyślne.

Zachowaj **filtru Scoping** puste. Oznacza to, że ta reguła ma zastosowanie do wszystkich obiektów przyłączone między połączone systemu usługi Active Directory i obiektu metaverse.

Zachowaj **Dołącz zasady** puste. Oznacza to, że ta zasada używa warunek sprzężenia, zdefiniowane w regule Standardowy domyślny. Jest to kolejny powód, nie można wyłączyć lub usunąć regułę Standardowy domyślny. Jeśli nie ma żadnego warunku join, nie będzie przepływu atrybutu. 

Dodaj odpowiednie przekształcenia swojej atrybutu. Możesz przypisać stałą, aby stała wartość przepływu do swojej atrybut target. Umożliwia bezpośrednie mapowanie między atrybutem źródłowym lub docelowym. Alternatywnie można użyć wyrażenia dla atrybutu. Oto różnych [wyrażenia funkcji](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) można użyć.

#### <a name="add-an-outbound-sync-rule"></a>Dodaj regułę synchronizacji ruchu wychodzącego
Aby połączyć ten atrybut do katalogu docelowego, musisz utworzyć regułę dla ruchu wychodzącego. Oznacza to, że źródłem jest metaverse, a element docelowy połączonego systemu. Aby utworzyć regułę dla ruchu wychodzącego, uruchom **Synchronization Rules Editor**, zmień **kierunek** do **ruchu wychodzącego**i wybierz **Dodaj nową regułę**. 

![Edytor reguł synchronizacji](media/how-to-connect-fix-default-rules/default3c.png)

Za pomocą reguły dla ruchu przychodzącego, można używać konwencji nazewnictwa do nazwę reguły. Wybierz **połączony System** jako dzierżawy usługi Azure AD, a następnie wybierz połączonego systemu obiektu, do którego chcesz ustawić wartość atrybutu. Ustaw priorytet z zakresu od 0 do 99. 

![Utwórz regułę synchronizacji ruchu wychodzącego](media/how-to-connect-fix-default-rules/default3d.png)

Zachowaj **filtru Scoping** i **Dołącz zasady** puste. Wypełnij przekształcenia jako stała, bezpośrednio lub wyrażenie. 

Teraz wiesz, jak utworzyć nowy atrybut przepływ obiektu użytkownika z usługi Active Directory do usługi Azure Active Directory. Te kroki można użyć do mapowania dowolnego atrybutu z dowolnych obiektów źródłowych i docelowych. Aby uzyskać więcej informacji, zobacz [tworzenia reguł niestandardowych synchronizacji](how-to-connect-create-custom-sync-rule.md) i [przygotowanie do świadczenia użytkownikom](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="override-the-value-of-an-existing-attribute"></a>Zastąp wartość istniejącego atrybutu
Możesz zastąpić wartość atrybutu, który został już zamapowany. Na przykład jeśli chcesz, zawsze można ustawić wartości null do atrybutu w usłudze Azure AD, po prostu utworzyć tylko reguły dla ruchu przychodzącego. Wprowadź wartość stała `AuthoritativeNull`, przepływ do atrybutu docelowego. 

>[!NOTE] 
> Użyj `AuthoritativeNull` zamiast `Null` w tym przypadku. Jest to spowodowane wartość inną niż null zastępuje wartość null, nawet wtedy, gdy ma niższy priorytet (wyższa wartość liczbową w regule). `AuthoritativeNull`, z drugiej strony, nie jest zastępowana wartością inną niż null przez inne zasady. 

### <a name="dont-sync-existing-attribute"></a>Nie są synchronizowane istniejący atrybut
Jeśli chcesz wykluczyć atrybut synchronizowanie, użyj atrybutu filtrowanie funkcji dostarczanej w programie Azure AD Connect. Uruchom **program Azure AD Connect** z ikony pulpitu, a następnie wybierz **Dostosowywanie opcji synchronizacji**.

![Opcje dodatkowe zadania w usłudze Azure AD Connect](media/how-to-connect-fix-default-rules/default4.png)

 Upewnij się, że **filtrowanie atrybutów i aplikacji usługi Azure AD** jest zaznaczone i wybierz **dalej**.

![Opcjonalne funkcje usługi Azure AD Connect](media/how-to-connect-fix-default-rules/default5.png)

Wyczyść atrybuty, które chcesz wykluczyć z synchronizacji.

![Atrybuty usługi Azure AD Connect](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Zmiana filtru określania zakresu
Synchronizacja programu Azure AD zajmuje się większość obiektów. Możesz ograniczyć zakres obiektów i zmniejszyć liczbę obiektów, które mają zostać wyeksportowane bez konieczności zmieniania domyślnych standardowych reguł synchronizacji. 

Aby zawęzić zakres obiektów, których synchronizowania, użyj jednej z następujących metod:

- Atrybut cloudFiltered
- Jednostka organizacyjna filtrowania

Zmniejszenie zakresu użytkowników są synchronizowane, synchronizowanie skrótów haseł również nie będzie możliwy dla użytkowników filtrowane w poziomie. Jeśli obiekty już objęte synchronizacją, to po można zmniejszyć zakres obiektów poza filtrowane są usuwane z katalogu docelowego. Z tego powodu upewnij się, zakres bardzo ostrożnie.

>[!IMPORTANT] 
> Zwiększyć zakres obiektów, które skonfigurowano przy użyciu usługi Azure AD Connect nie jest zalecane. Ten sposób utrudnia zespołem pomocy technicznej firmy Microsoft zrozumieć dostosowania. Jeśli musisz zwiększyć zakres obiektów, Edytuj istniejącą regułę, sklonować ten projekt i Wyłącz istniejącą regułę. 

### <a name="cloudfiltered-attribute"></a>Atrybut cloudFiltered
Nie można ustawić tego atrybutu w usłudze Active Directory. Ustaw wartość tego atrybutu, dodając nową regułę dla ruchu przychodzącego. Następnie można użyć **przekształcania** i **wyrażenie** można ustawić tego atrybutu w magazynie metaverse. W poniższym przykładzie pokazano, że nie chcesz synchronizować wszystkich użytkowników, których nazwa działu zaczyna się od **HRD** (bez uwzględniania wielkości liter):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Firma Microsoft najpierw konwertowany dział ze źródła (Active Directory) na małe litery. Następnie za pomocą `Left` funkcji, firma Microsoft miała tylko pierwsze trzy znaki i porównać ją z `hrd`. Jeśli był zgodny, wartość jest równa `True`, w przeciwnym razie `NULL`. Podczas ustawiania wartości null, inna reguła o niższym priorytecie (wyższa wartość liczbową) może zapisywać do niego przy użyciu różnych warunków. Uruchom Podgląd w jeden obiekt do sprawdzania poprawności reguły synchronizacji, zgodnie z opisem w [sprawdzania poprawności reguły synchronizacji](#validate-sync-rule) sekcji.

![Utwórz opcje reguły synchronizacji ruchu przychodzącego](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Jednostka organizacyjna, filtrowanie
Można utworzyć co najmniej jedną jednostkę organizacyjną (OU) i przenieść obiekty, których nie chcesz, aby zsynchronizować do tych jednostek organizacyjnych. Następnie należy skonfigurować jednostkę Organizacyjną, filtrowanie w usłudze Azure AD Connect. Uruchom **program Azure AD Connect** z ikony pulpitu, a następnie wybierz następujące opcje. Można również skonfigurować jednostki Organizacyjnej, filtrowania w czasie instalacji programu Azure AD Connect. 

![Dodatkowe zadania usługi Azure AD Connect](media/how-to-connect-fix-default-rules/default8.png)

Wykonaj instrukcje kreatora, a następnie wyczyść jednostki organizacyjne nie mają być synchronizowane.

![Usługa Azure AD Connect domeny i jednostki Organizacyjnej, opcje filtrowania](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Zmień warunek sprzężenia
Użyj warunki sprzężenia domyślne, które są skonfigurowane przy użyciu usługi Azure AD Connect. Zmiana domyślnego warunki sprzężenia utrudnia pomocy technicznej firmy Microsoft zrozumieć, dostosowania i obsługa techniczna produktu.

## <a name="validate-sync-rule"></a>Sprawdź poprawność reguły synchronizacji
Reguły synchronizacji nowo dodanych można sprawdzić za pomocą funkcji w wersji zapoznawczej, bez konieczności uruchamiania cyklu pełną synchronizację. W programie Azure AD Connect, należy wybrać **usługi synchronizacji**.

![Program Azure AD Connect, za pomocą usługi synchronizacji z wyróżnioną pozycją](media/how-to-connect-fix-default-rules/default10.png)

Wybierz **wyszukiwanie magazynu Metaverse**. Wybierz obiekt zakresu jako **osoby**, wybierz opcję **Dodaj klauzulę**i wymienić kryteria wyszukiwania. Następnie wybierz pozycję **wyszukiwania**i kliknij dwukrotnie obiekt w wynikach wyszukiwania. Upewnij się, że dane w programie Azure AD Connect jest aktualne dla tego obiektu, uruchamiając importowania i synchronizacji w lesie, przed wykonaniem tego kroku.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

Na **właściwości obiektu Metaverse**, wybierz opcję **łączników**, zaznacz obiekt w odpowiedni łącznik (las) i wybierz **właściwości...** .

![Właściwości obiektu Metaverse](media/how-to-connect-fix-default-rules/default12.png)

Wybierz **podglądu...**

![Właściwości obiektu obszaru łącznika](media/how-to-connect-fix-default-rules/default13a.png)

W oknie podglądu wybierz **Generowanie podglądu** i **przepływu atrybutu importowania** w okienku po lewej stronie.

![Wersja zapoznawcza](media/how-to-connect-fix-default-rules/default14.png)
 
Zauważ, nowo dodane zasady jest uruchamiany na obiekt i ustawił `cloudFiltered` atrybut na wartość true.

![Wersja zapoznawcza](media/how-to-connect-fix-default-rules/default15a.png)
 
Aby porównać zmodyfikowano regułę za pomocą reguły domyślnej, wyeksportuj obu reguł oddzielnie, jako pliki tekstowe. Te reguły są eksportowane jako plik skryptu programu PowerShell. Możesz porównać ich za pomocą dowolnego narzędzie do porównywania plików (na przykład windiff), aby zobaczyć zmiany. 
 
Należy zauważyć, że w zmodyfikowano regułę `msExchMailboxGuid` atrybutu jest zmieniana na **wyrażenie** typu, zamiast **bezpośredniego**. Ponadto wartość zostanie zmieniona na **NULL** i **ExecuteOnce** opcji. Możesz zignorować Identified i pierwszeństwo różnice. 

![dane wyjściowe narzędzia Windiff](media/how-to-connect-fix-default-rules/default17.png)
 
Aby rozwiązać problem z reguł, aby je zmienić przywrócone do domyślnych ustawień, usuwanie modyfikacji reguły i włączyć regułę domyślną. Upewnij się, że nie tracisz dostosowywania, które próbujesz osiągnąć. Gdy wszystko będzie gotowe, uruchamianie **pełną synchronizację**.

## <a name="next-steps"></a>Kolejne kroki
- [Sprzęt i wymagania wstępne](how-to-connect-install-prerequisites.md) 
- [Ustawienia ekspresowe](how-to-connect-install-express.md)
- [Ustawienia dostosowane](how-to-connect-install-custom.md)



