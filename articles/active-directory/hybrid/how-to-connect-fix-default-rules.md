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
ms.openlocfilehash: 761f3e6e72319a2e63d6b66f2893130ec5a82ebf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60353052"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Napraw modyfikacji domyślnych reguł w programie Azure AD Connect

Program Azure AD Connect jest dostarczany z domyślnych reguł synchronizacji.  Niestety nie mają zastosowanie następujące reguły powszechnie posiadać wszystkie organizacje i mogą zaistnieć sytuacje, w zależności od wymagań, gdy zajdzie potrzeba ich zmodyfikowania.

 Jeśli zmodyfikowano domyślne reguły lub planowane jest ich modyfikacji, następnie Poświęć chwilę na przeczytanie tego dokumentu.

W tym dokumencie przedstawiono przykłady 2 dostosowań najczęściej wykonywane przez użytkowników i wyjaśnia, prawidłowym sposobem, aby osiągnąć te dostosowania.

>[!NOTE] 
> Modyfikowanie istniejących reguł domyślne do osiągnięcia potrzebne dostosowania nie jest obsługiwana — dzięki temu uniemożliwi aktualizowanie tych reguł do najnowszej wersji w przyszłych wersjach. Uniemożliwi to użytkownik pobieranie niezbędnych poprawek usterek i nowe funkcje.  W tym dokumencie wyjaśniono, jak ten sam efekt bez modyfikowania istniejących reguł domyślnych. 

## <a name="how-to-identify-modified-default-rules"></a>Jak zidentyfikować zmodyfikowane domyślne reguły?
Począwszy od wersji 1.3.7.0 **program Azure AD Connect**, jest teraz łatwo identyfikować ich modyfikacji domyślną regułę. Można przejść do aplikacji na pulpicie i kliknąć **Synchronization Rules Editor**.

![Edytor](media/how-to-connect-fix-default-rules/default1.png)

W edytorze żadnych modyfikacji domyślnych reguł będą wyświetlane z ikoną przed nazwą, jak pokazano poniżej:

![Ikona](media/how-to-connect-fix-default-rules/default2.png)

 Również zostanie wyświetlony wyłączona reguła o tej samej nazwie obok niej, czyli standardowa domyślną regułę:

![Reguły domyślne](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Typowe dostosowania
Poniżej przedstawiono typowe dostosowania domyślnych reguł:

- [Zmiana przepływu atrybutów](#changing-attribute-flow)
- [Zmiana filtru określania zakresu](#changing-scoping-filter)
- [Zmiana warunek sprzężenia](#changing-join-condition)

## <a name="before-changing-any-rules"></a>Przed zmianą żadnych reguł
- Wyłącz harmonogram synchronizacji.  Harmonogram domyślnie jest uruchamiane co 30 minut. Upewnij się, że nie jest uruchomiona, podczas wprowadzania zmian i rozwiązywanie problemów z nowych zasad. Aby tymczasowo wyłączyć harmonogram, uruchom program PowerShell i uruchom `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 ![Reguły domyślne](media/how-to-connect-fix-default-rules/default3.png)

- Zmiana filtru określania zakresu może spowodować usunięcie obiektów w katalogu docelowym. Należy zachować ostrożność przed wprowadzeniem jakichkolwiek zmian z zakresu obiektów. Zaleca się zmiany serwer przejściowy, przed wprowadzeniem zmian na aktywnym serwerze.
- Uruchom Podgląd na pojedynczy obiekt zgodnie z opisem w [sprawdzania poprawności reguły synchronizacji](#validate-sync-rule) sekcji po dodaniu nowej reguły.
- Po dodaniu nowej reguły lub zmodyfikowanie reguły synchronizacji niestandardowego Uruchom pełną synchronizację. Tej synchronizacji będą dotyczyć nowe reguły wszystkie obiekty.

## <a name="changing-attribute-flow"></a>Zmiana przepływu atrybutów
Istnieją 3 różnych scenariuszy dla przepływu atrybutu, Przyjrzyjmy się jak osiągnąć je bez zmieniania domyślnych standardowych reguł.
- Dodawanie nowego atrybutu
- Zastępowanie wartości istniejący atrybut
- Nie są synchronizowane istniejący atrybut

### <a name="adding-new-attribute"></a>Dodawanie nowego atrybutu:
Jeśli okaże się, że atrybutu nie przesyła danych z katalogu źródłowego do katalogu docelowego, wówczas można użyć [synchronizacji programu Azure AD Connect: Rozszerzenia katalogów](how-to-connect-sync-feature-directory-extensions.md) przepływ nowych atrybutów.

Należy pamiętać, że Twój pierwszy wybór należy do użycia [synchronizacji programu Azure AD Connect: Rozszerzenia katalogów](how-to-connect-sync-feature-directory-extensions.md), poza pole funkcji dostarczonych przez program Azure AD Connect. Jednakże jeśli go nie będą dla Ciebie, a następnie wykonaj poniższe czynności, aby przepływ atrybutu bez modyfikowania istniejących standardowych domyślną regułę synchronizacji, można to zrobić, dodając dwa nowe reguły synchronizacji.


#### <a name="add-an-inbound-sync-rule"></a>Dodaj regułę synchronizacji ruchu przychodzącego:
Reguła synchronizacji ruchu przychodzącego oznacza, że źródło atrybutu jest obszaru łącznika i miejsce docelowe jest metaverse. Na przykład przepływ nowy atrybut z usługi Active Directory lokalnych z usługą Azure Active Directory, Utwórz nową regułę synchronizacji ruchu przychodzącego, uruchamiając **Synchronization Rule Editor**, następnie wybierz kierunek jako **ruchuprzychodzącego** i kliknij przycisk **Dodaj nową regułę**. 

 ![Reguły domyślne](media/how-to-connect-fix-default-rules/default3a.png)

Postępuj zgodnie z konwencji nazewnictwa do nazwę reguły, w tym miejscu użyliśmy **niestandardowych w witrynie AD — użytkownik**, oznacza to, że reguła jest niestandardową regułę i regułę ruchu przychodzącego z obszaru łącznika usługi AD do obiektu Metaverse.   

 ![Reguły domyślne](media/how-to-connect-fix-default-rules/default3b.png)

Przekazać własny opis reguły obsługi przyszłych reguły jest łatwe, takich jak co to jest celem tej reguły, i dlaczego jest konieczne.
Wybierz połączony System (las) — źródło atrybutu. Następnie wybierz typ obiektu systemu połączonych i typ obiektu Metaverse.

Określ wartość pierwszeństwa, od 0 – 99 (niższe numer wyższy priorytet). Zachowaj innych pól, takich jak "Tag", "Włączanie synchronizacji haseł" i "Wyłączone" jako domyślny.

Zachowaj "Scoping filtru" jest pusta, oznacza to, reguła będzie dotyczyć wszystkich obiektów, które są przyłączone do między systemem połączone usługi AD i Metaverse.

Zachować "Dołącz do reguły" puste, co oznacza, ta zasada zostanie ono wysłane na warunek sprzężenia, zdefiniowane w regule Standardowy domyślny. Jest to kolejny powód, aby nie zdezaktywuj lub usuń reguły Standardowy domyślny, ponieważ jeśli żaden warunek sprzężenia do potwierdzeń następnie ten atrybut nie będzie możliwy przepływ. 

Dodaj odpowiednie przemian z atrybutu można przypisać stała przepływ wartości stałej na atrybut docelowy, lub bezpośrednie mapowanie między atrybutem źródłowym lub docelowym lub wyrażenie dla atrybutu. Oto różnych [wyrażenia funkcji](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) można użyć.

#### <a name="add-an-outbound-sync-rule"></a>Dodaj regułę synchronizacji ruchu wychodzącego:
Do tej pory przez dodanie tylko reguły synchronizacji ruchu przychodzącego wykonaliśmy połowa utworu, ponieważ ten atrybut nie został jeszcze połączony z katalogu docelowego. Aby połączyć atrybut docelowy Dyrektor ds. musisz utworzyć regułę dla ruchu wychodzącego, co oznacza źródło jest metaverse, a element docelowy jest połączonego systemu. Aby utworzyć regułę dla ruchu wychodzącego, uruchom **Synchronization Rule Editor**, zmień **kierunek** do **ruchu wychodzącego** i kliknij przycisk **Dodaj nową regułę**. 

![Reguły domyślne](media/how-to-connect-fix-default-rules/default3c.png)

Jak reguły dla ruchu przychodzącego, można użyć konwencji nazewnictwa do **nazwa** reguły. Wybierz **połączony System** jako dzierżawy usługi Azure AD, wybierz obiekt połączonego systemu, do którego chcesz ustawić wartość atrybutu. Ustaw priorytet zakresu 0 – 99. 

![Reguły domyślne](media/how-to-connect-fix-default-rules/default3d.png)

Zachowaj **filtru Scoping** pusta, Zachowaj **Dołącz zasady** pusty, wypełnij przekształcenia jako stała, bezpośrednio lub wyrażenie. 

W tym przykładzie firma Microsoft wykazały jak przepływ nowy atrybut obiektu użytkownika z usługi Active Directory do usługi Azure Active Directory. Te kroki można użyć do mapowania dowolnego atrybutu z dowolnych obiektów źródłowych i docelowych.  Aby uzyskać więcej informacji, zobacz [tworzenia reguł niestandardowych synchronizacji](how-to-connect-create-custom-sync-rule.md) i [przygotowanie do świadczenia użytkownikom](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="overriding-value-of-existing-attribute"></a>Zastępowanie wartości istniejący atrybut
Istnieje możliwość, że chcesz zastąpić wartość atrybutu już zmapowany, na przykład chcesz zawsze ustawić wartości Null do atrybutu w usłudze Azure AD, można to zrobić, po prostu tworząc tylko Reguła ruchu przychodzącego, zgodnie z opisem w poprzednim kroku i flow  **AuthoritativeNull** wartości stałej dla elementu atrybut target. Należy pamiętać, że użyliśmy AuthoritativeNulll zamiast wartości Null w tym przypadku. Jest to spowodowane wartości innej niż null spowoduje zastąpienie wartości Null, nawet wtedy, gdy ma niższy priorytet (wyższa wartość liczbową w regule). Jednak AuthoritativeNull będą traktowane jako wartości Null i nie zostanie zastąpiona wartością inną niż null przez inne zasady. 

### <a name="dont-sync-existing-attribute"></a>Nie są synchronizowane istniejący atrybut
Jeśli chcesz wykluczyć atrybut synchronizowanie, można użyć atrybutu filtrowanie funkcji dostarczanej w programie Azure AD Connect. Uruchom **program Azure AD Connect** z ikony pulpitu, a następnie wybierz **Dostosowywanie opcji synchronizacji**.

![Reguły domyślne](media/how-to-connect-fix-default-rules/default4.png)

 Upewnij się, że **filtrowanie atrybutów i aplikacji usługi Azure AD** jest zaznaczone, a następnie kliknij przycisk **dalej**.

![Reguły domyślne](media/how-to-connect-fix-default-rules/default5.png)

Usuń zaznaczenie pola wyboru atrybutów, które chcesz wykluczyć z synchronizacji.

![Reguły domyślne](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="changing-scoping-filter"></a>Zmiana filtru określania zakresu
Synchronizacja programu Azure AD zajmuje się większość obiektów, można zmniejszyć zakres obiektów i zmniejszyć jego mniejszą liczbę obiektów do wyeksportowania w sposób obsługiwane bez konieczności zmieniania domyślnych standardowych reguł synchronizacji. W przypadku, gdy chcesz zwiększyć zakres obiektów, a następnie możesz **Edytuj** istniejącą regułę, sklonować ten projekt i Wyłącz istniejącą regułę. Firma Microsoft zaleca nie, aby zwiększyć zakres skonfigurowane przy użyciu usługi Azure AD Connect. Zwiększenie zakresu obiektów, spowoduje to trudne przez zespół pomocy technicznej zrozumieć, dostosowania i obsługa techniczna produktu.

Oto, jak można zmniejszyć zakres obiektów synchronizowana z usługą Azure AD. Należy pamiętać, że jeśli Zmniejsz zakres **użytkowników** synchronizowanego zatrzyma się synchronizowanie skrótów haseł również użytkownikom filtrowane w poziomie. Jeśli następnie obiekty już objęte synchronizacją po zmniejszeniu liczby zakresu, obiekty filtrowane poza zostanie usunięty z katalogu docelowego, można pracować nad zakresu bardzo ostrożnie.
Poniżej przedstawiono sposoby obsługiwanych, aby zawęzić zakres obiektów, które są synchronizowane.

- [Atrybut cloudFiltered](#cloudfiltered-attribute)
- [Filtrowanie jednostki Organizacyjnej](#ou-filtering)

### <a name="cloudfiltered-attribute"></a>Atrybut cloudFiltered
Należy pamiętać, że nie jest atrybutem, którego można ustawić w usłudze Active Directory. Należy ustawić wartość tego atrybutu, dodając nową regułę dla ruchu przychodzącego, jak wspomniano w **zastępowanie wartości istniejący atrybut** sekcji. Można następnie użyć **przekształcania** i użyj **wyrażenie** można ustawić tego atrybutu w magazynie Metaverse. Oto przykład, w którym nie chcesz synchronizować wszystkich użytkownika, którego nazwa działu zaczyna się od liter **HRD**:

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Firma Microsoft ma najpierw konwertowany dział ze źródła (Active Directory) na małe litery. A następnie użycie funkcji po lewej stronie, firma Microsoft miała tylko pierwszych 3 znaków i go w porównaniu z odnajdowanie obszaru macierzystego. Jeśli dopasowane, ustaw wartość na wartość True, w przeciwnym razie wartość NULL. Należy pamiętać, że firma Microsoft ustawienie wartości NULL, tak, aby inna reguła o niższym priorytecie (wyższa wartość liczbowa) może zapisywać do niego przy użyciu różnych warunków. Uruchom Podgląd w jeden obiekt do sprawdzania poprawności reguły synchronizacji, zgodnie z opisem w [sprawdzania poprawności reguły synchronizacji](#validate-sync-rule) sekcji.

![Reguły domyślne](media/how-to-connect-fix-default-rules/default7a.png)



### <a name="ou-filtering"></a>Filtrowanie jednostki Organizacyjnej
Można utworzyć jeden lub więcej jednostek organizacyjnych i przenieść obiekty, których nie chcesz, aby zsynchronizować do tych jednostek organizacyjnych. Następnie skonfiguruj jednostki Organizacyjnej, filtrowanie w usłudze Azure AD Connect, uruchamiając **program Azure AD Connect** z ikony pulpitu i wybierz przycisk opcji, jak pokazano poniżej. Można również skonfigurować jednostki Organizacyjnej, filtrowania w czasie instalacji programu Azure AD Connect. 

![Reguły domyślne](media/how-to-connect-fix-default-rules/default8.png)

Wykonaj instrukcje kreatora, a następnie usuń zaznaczenie jednostki organizacyjne nie mają być synchronizowane.

![Reguły domyślne](media/how-to-connect-fix-default-rules/default9.png)

## <a name="changing-join-condition"></a>Zmiana warunek sprzężenia
Firma Microsoft zaleca się, że użytkownik korzysta z domyślnych dołączysz postanowienia skonfigurowane przez program Azure AD Connect. Zmiana domyślnego warunki sprzężenia, spowoduje to trudne przez zespół pomocy technicznej zrozumieć, dostosowania i obsługa techniczna produktu.

## <a name="validate-sync-rule"></a>Sprawdź poprawność reguły synchronizacji
Reguły synchronizacji nowo dodanych można sprawdzić za pomocą funkcji w wersji zapoznawczej bez konieczności uruchamiania cyklu pełną synchronizację. Uruchom **usługi synchronizacji** interfejsu użytkownika.

![Reguły domyślne](media/how-to-connect-fix-default-rules/default10.png)

Kliknij pozycję **wyszukiwanie magazynu Metaverse**, wybierz obiekt zakresu jako **osoby**, **Dodaj klauzulę** i wymienić kryteria wyszukiwania. Kliknij pozycję **wyszukiwania** przycisk, a następnie kliknij dwukrotnie na obiekcie w **wyniki wyszukiwania** Pamiętaj, że uruchomieniu importowania i synchronizacji w lesie przed wykonaniem tego kroku zapewnia to danych w programie Azure AD Connect Stała aktualność dla tego obiektu.

![Reguły domyślne](media/how-to-connect-fix-default-rules/default11.png)



Wybierz **łączników**, zaznacz obiekt w odpowiedniej connector(forest), kliknij przycisk **właściwości...** .

![Reguły domyślne](media/how-to-connect-fix-default-rules/default12.png)

Kliknij pozycję **podglądu...**

![Reguły domyślne](media/how-to-connect-fix-default-rules/default13a.png)

Kliknij pozycję **Generowanie podglądu** i **przepływu atrybutu importowania** w okienku po lewej stronie.

![Reguły domyślne](media/how-to-connect-fix-default-rules/default14.png)
 
W tym miejscu można zauważyć, że nowo dodane zasady jest uruchamiany na obiekt i ma atrybut cloudFiltered ustawiony na wartość True.

![Reguły domyślne](media/how-to-connect-fix-default-rules/default15a.png)
 
Jak porównać zmodyfikowano regułę za pomocą reguły domyślnej?
Obie reguły można eksportować oddzielnie jako pliki tekstowe. Te reguły zostaną wyeksportowane jako plik skryptu programu powershell. Możesz porównać je przy użyciu dowolnego narzędzia do porównywania plików, aby zobaczyć, jakie zmiany są wykonywane. W tym miejscu w tym przykładzie I umożliwia windiff Porównaj dwa pliki.
 
Można zauważyć, że w polu użytkownik zmodyfikował regułę, atrybut msExchMailboxGuid jest zmieniana na **wyrażenie** wpisz zamiast **bezpośrednie** o **NULL** i  **ExecuteOnce** opcji. Możesz zignorować Identified i pierwszeństwo różnice. 

![Reguły domyślne](media/how-to-connect-fix-default-rules/default17.png)
 
Jak naprawić zmodyfikowane domyślną regułę?
Aby rozwiązać reguły domyślne ustawienia, możesz usunąć zmodyfikowano regułę i Włącz regułę domyślną, jak pokazano poniżej i następnie uruchom **pełną synchronizację**. Przed takie działanie, które podejmuj akcje naprawcze, jak wspomniano powyżej, aby nie utracić dostosowania próbujesz osiągnąć ## następne kroki

## <a name="next-steps"></a>Następne kroki
- [Sprzęt i wymagania wstępne](how-to-connect-install-prerequisites.md) 
- [Ustawienia ekspresowe](how-to-connect-install-express.md)
- [Ustawienia dostosowane](how-to-connect-install-custom.md)

