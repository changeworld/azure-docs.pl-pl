---
title: 'Usługa Azure AD Connect: opis deklaratywnej inicjowania obsługi administracyjnej | Dokumenty firmy Microsoft'
description: W tym artykule wyjaśniono modelu konfiguracji inicjowania obsługi administracyjnej deklaratywnej w usłudze Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cfbb870d-be7d-47b3-ba01-9e78121f0067
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 543c1a6706f794b81c4f93fc6fff3a61ed3fb9e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60246334"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Synchronizacja usługi Azure AD Connect: opis aprowizacji deklaratywnej
W tym temacie opisano model konfiguracji w usłudze Azure AD Connect. Model jest nazywany deklaratywne inicjowania obsługi administracyjnej i pozwala na dokonanie zmiany konfiguracji z łatwością. Wiele rzeczy opisanych w tym temacie są zaawansowane i nie są wymagane dla większości scenariuszy klienta.

## <a name="overview"></a>Omówienie
Deklaratywny inicjowania obsługi administracyjnej jest przetwarzanie obiektów pochodzących ze źródła połączonego katalogu i określa, jak obiekt i atrybuty powinny być przekształcane ze źródła do obiektu docelowego. Obiekt jest przetwarzany w potoku synchronizacji i potoku jest taka sama dla reguł przychodzących i wychodzących. Reguła ruchu przychodzącego jest z przestrzeni łącznika do metaverse i reguły wychodzącej jest od metaverse do przestrzeni łącznika.

![Potok synchronizacji](./media/concept-azure-ad-connect-sync-declarative-provisioning/sync1.png)  

Potok ma kilka różnych modułów. Każdy z nich jest odpowiedzialny za jedną koncepcję w synchronizacji obiektów.

![Potok synchronizacji](./media/concept-azure-ad-connect-sync-declarative-provisioning/pipeline.png)  

* Źródło, Obiekt źródłowy
* [Zakres](#scope), Wyszukuje wszystkie reguły synchronizacji, które znajdują się w zakresie
* [Sprzężenie](#join), Określa relację między przestrzenią łącznika a metaverse
* Przekształcanie, oblicza, jak atrybuty powinny być przekształcane i przepływowe
* [Pierwszeństwo](#precedence), Rozwiązuje sprzeczne składy atrybutów
* Cel, Obiekt docelowy

## <a name="scope"></a>Zakres
Moduł zakresu jest ocena obiektu i określa reguły, które są w zakresie i powinny być uwzględnione w przetwarzaniu. W zależności od wartości atrybutów obiektu, różne reguły synchronizacji są oceniane w zakresie. Na przykład wyłączony użytkownik bez skrzynki pocztowej programu Exchange ma inne reguły niż włączony użytkownik ze skrzynką pocztową.  
![Zakres](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope1.png)  

Zakres jest zdefiniowany jako grupy i klauzule. Klauzule znajdują się wewnątrz grupy. Logiczne i jest używany między wszystkie klauzule w grupie. Na przykład (dział =IT I kraj = Dania). Logiczne OR jest używany między grupami.

![Zakres](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope2.png)  
Zakres na tym zdjęciu należy odczytywać jako (dział = IT i kraj = Dania) LUB (kraj = Szwecja). Jeśli grupa 1 lub grupa 2 jest oceniana na wartość true, reguła jest w zakresie.

Moduł zakresu obsługuje następujące operacje.

| Operacja | Opis |
| --- | --- |
| RÓWNA, NOTEQUAL |Porównanie ciągu, który ocenia, czy wartość jest równa wartości w atrybucie. Aby uzyskać atrybuty o wielu wartościach, zobacz ISIN i ISNOTIN. |
| LESSTHAN, LESSTHAN_OR_EQUAL |Porównanie ciągu, który ocenia, jeśli wartość jest mniejsza niż wartości w atrybucie. |
| ZAWIERA, NOTCONTAINS |Porównanie ciągu, który ocenia, czy wartość można znaleźć gdzieś wewnątrz wartości w atrybucie. |
| STARTSWITH, NOTSTARTSWITH |Porównanie ciągu, który ocenia, jeśli wartość znajduje się na początku wartości w atrybucie. |
| ENDSWITH, NOTENDSWITH |Porównanie ciągu, który ocenia, jeśli wartość znajduje się na końcu wartości w atrybucie. |
| GREATERTHAN, GREATERTHAN_OR_EQUAL |Porównanie ciągów, który ocenia, jeśli wartość jest większa niż wartości w atrybucie. |
| ISULL, ISNOTNULL |Ocenia, czy atrybut jest nieobecny w obiekcie. Jeśli atrybut nie jest obecny i w związku z tym null, a następnie reguła jest w zakresie. |
| ISIN, ISNOTIN |Ocenia, czy wartość jest obecna w zdefiniowanym atrybucie. Ta operacja jest wielowartościową odmianą EQUAL i NOTEQUAL. Atrybut ma być atrybutem wielowartościowym i jeśli wartość można znaleźć w dowolnej wartości atrybutu, a następnie reguła jest w zakresie. |
| ISBITSET, ISNOTBITSET |Ocenia, czy ustawiony jest określony bit. Na przykład może służyć do oceny bitów w userAccountControl, aby sprawdzić, czy użytkownik jest włączony lub wyłączony. |
| ISMEMBEROF, ISNOTMEMBEROF |Wartość powinna zawierać numer DN do grupy w przestrzeni łącznika. Jeśli obiekt jest członkiem określonej grupy, reguła jest w zakresie. |

## <a name="join"></a>Join
Moduł sprzężenia w potoku synchronizacji jest odpowiedzialny za znalezienie relacji między obiektem w źródle a obiektem w obiekcie docelowym. W przypadku reguły przychodzącej ta relacja będzie obiektem w przestrzeni łącznika, znajdując relację z obiektem w metaverse.  
![Łączenie między cs i mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/join1.png)  
Celem jest sprawdzenie, czy w metaverse znajduje się obiekt, utworzony przez inny łącznik, z który powinien być skojarzony. Na przykład w lesie zasobów konta użytkownik z lasu kont powinien być połączony z użytkownikiem z lasu zasobów.

Sprzężenia są używane głównie na regułach ruchu przychodzącego do łączenia obiektów przestrzeni łącznika razem z tym samym obiektem metaverse.

Sprzężenia są definiowane jako jedna lub więcej grup. Wewnątrz grupy masz klauzule. Logiczne i jest używany między wszystkie klauzule w grupie. Logiczne OR jest używany między grupami. Grupy są przetwarzane w kolejności od góry do dołu. Gdy jedna grupa znajdzie dokładnie jedno dopasowanie z obiektem w obiekcie docelowym, żadne inne reguły sprzężenia nie są oceniane. Jeśli zostanie znaleziony zero lub więcej niż jeden obiekt, przetwarzanie będzie kontynuowane do następnej grupy reguł. Z tego powodu reguły powinny być tworzone w kolejności najbardziej wyraźne pierwszy i bardziej rozmyte na końcu.  
![Definicja sprzężenia](./media/concept-azure-ad-connect-sync-declarative-provisioning/join2.png)  
Sprzężenia na tym zdjęciu są przetwarzane od góry do dołu. Najpierw potok synchronizacji widzi, czy istnieje dopasowanie na employeeID. Jeśli nie, druga reguła widzi, czy nazwa konta może służyć do łączenia obiektów razem. Jeśli nie jest to zgodne albo, trzecia i ostatnia reguła jest bardziej rozmyte dopasowania przy użyciu nazwy użytkownika.

Jeśli wszystkie reguły sprzężenia zostały ocenione i nie ma dokładnie jednego dopasowania, używany jest **typ łącza** na stronie **Opis.** Jeśli ta opcja jest **ustawiona**na Provision , tworzony jest nowy obiekt w miejscu docelowym.  
![Udostępnianie lub dołączanie](./media/concept-azure-ad-connect-sync-declarative-provisioning/join3.png)  

Obiekt powinien mieć tylko jedną regułę synchronizacji z regułami sprzężenia w zakresie. Jeśli istnieje wiele reguł synchronizacji, w których jest zdefiniowane sprzężenie, występuje błąd. Pierwszeństwo nie jest używany do rozwiązywania konfliktów sprzężenia. Obiekt musi mieć regułę sprzężenia w zakresie, aby atrybuty mogły przepływać z tym samym kierunkiem przychodzącym/wychodzącym. Jeśli chcesz przepływ atrybutów zarówno przychodzące i wychodzące do tego samego obiektu, musi mieć zarówno przychodzące i wychodzące reguły synchronizacji z sprzężenia.

Sprzężenie wychodzące ma specjalne zachowanie podczas próby aprowizowania obiektu do docelowego obszaru łącznika. Atrybut DN jest używany do pierwszej próby sprzężenia wstecznego. Jeśli w docelowej przestrzeni łącznika znajduje się już obiekt o tej samej niuacie docelowych, obiekty są łączone.

Moduł sprzężenia jest oceniany tylko raz, gdy nowa reguła synchronizacji wchodzi w zakres. Po dołączeniu obiektu nie jest rozłączanie, nawet jeśli kryteria sprzężenia nie są już spełnione. Jeśli chcesz odłączyć obiekt, reguła synchronizacji, która przyłączyła się do obiektów, musi wykraczać poza zakres.

### <a name="metaverse-delete"></a>Usuwanie metaverse
Obiekt metaverse pozostaje tak długo, jak istnieje jedna reguła synchronizacji w zakresie z **Typem łącza** **ustawionym** na Provision lub **StickyJoin**. StickyJoin jest używany, gdy łącznik nie może aprowizować nowy obiekt do metaverse, ale po jego połączeniu, musi zostać usunięty w źródle przed metaverse obiektu jest usuwany.

Po usunięciu obiektu metaverse wszystkie obiekty skojarzone z regułą synchronizacji wychodzącej oznaczoną do **udostępnienia** są oznaczone do usunięcia.

## <a name="transformations"></a>Przekształcenia
Przekształcenia są używane do definiowania, jak atrybuty powinny przepływać ze źródła do obiektu docelowego. Przepływy mogą mieć jeden z następujących **typów przepływu:** Bezpośredni, Stały lub Wyrażenie. Przepływ bezpośredni, przepływa wartość atrybutu, jak jest bez dodatkowych przekształceń. Wartość stała ustawia określoną wartość. Wyrażenie używa deklaratywnego języka wyrażenia inicjowania obsługi administracyjnej do wyrażania, jak powinna być transformacja. Szczegóły dotyczące języka wyrażeń można znaleźć w [zrozumieniu deklaratywne inicjowania obsługi administracyjnej tematu języka wyrażenia.](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)

![Udostępnianie lub dołączanie](./media/concept-azure-ad-connect-sync-declarative-provisioning/transformations1.png)  

Pole wyboru **Zastosuj raz** definiuje, że atrybut powinien być ustawiany tylko wtedy, gdy obiekt jest początkowo tworzony. Na przykład tej konfiguracji można użyć do ustawienia początkowego hasła dla nowego obiektu użytkownika.

### <a name="merging-attribute-values"></a>Scalanie wartości atrybutów
W przepływach atrybutów istnieje ustawienie, aby określić, czy atrybuty wielowartościowe powinny być scalane z kilku różnych łączników. Wartością domyślną jest **Update**, co oznacza, że reguła synchronizacji o najwyższym priorytecie powinna zostać wygrana.

![Typy scalania](./media/concept-azure-ad-connect-sync-declarative-provisioning/mergetype.png)  

Istnieje również **Merge** and **MergeCaseInsensitive**. Te opcje umożliwiają scalanie wartości z różnych źródeł. Na przykład może służyć do scalania elementu członkowskiego lub proxyAddresses atrybut z kilku różnych lasów. Korzystając z tej opcji, wszystkie reguły synchronizacji w zakresie obiektu muszą używać tego samego typu korespondencji seryjnej. Nie można **zdefiniować aktualizacji** z jednego łącznika i **scal** z innego. Jeśli spróbujesz, zostanie wyświetlony błąd.

Różnica między **Merge** i **MergeCaseInsensitive** jest jak przetwarzać zduplikowane wartości atrybutów. Aparat synchronizacji zapewnia, że zduplikowane wartości nie są wstawiane do atrybutu docelowego. Z **MergeCaseInsensitive**, zduplikowane wartości z tylko różnicy w przypadku nie będą obecne. Na przykład nie powinno byćSMTP:bob@contoso.comwidocznesmtp:bob@contoso.comzarówno " " i " w atrybucie docelowym. **Scalanie** jest tylko patrząc na dokładne wartości i wiele wartości, gdzie istnieje tylko różnica w przypadku może być obecny.

Opcja **Zamień** jest taka sama jak **Aktualizacja**, ale nie jest używana.

### <a name="control-the-attribute-flow-process"></a>Sterowanie procesem przepływu atrybutów
Gdy wiele reguł synchronizacji przychodzącej są skonfigurowane w celu przyczynienia się do tego samego atrybutu metaverse, a następnie pierwszeństwo jest używany do określenia zwycięzcy. Reguła synchronizacji o najwyższym priorytecie (najniższa wartość liczbowa) będzie współtworzyć tę wartość. To samo dotyczy reguł ruchu wychodzącego. Reguła synchronizacji o najwyższym priorytecie wygrywa i wnosi wartość do połączonego katalogu.

W niektórych przypadkach, zamiast przyczyniać się do wartości, reguła synchronizacji powinna określać, jak powinny zachowywać się inne reguły. Istnieje kilka specjalnych literałów używanych w tym przypadku.

W przypadku reguł synchronizacji przychodzącej litera **NULL** może służyć do wskazywania, że przepływ nie ma żadnej wartości do współtworzenia. Inna reguła o niższym priorytecie może przyczynić się do wartości. Jeśli żadna reguła nie przyczyniła się do wartości, atrybut metaverse zostanie usunięty. W przypadku reguły ruchu wychodzącego jeśli **wartość NULL** jest wartością końcową po przetworzeniu wszystkich reguł synchronizacji, wartość jest usuwana w połączonym katalogu.

Dosłowny **AuthoritativeNull** jest podobny do **NULL,** ale z tą różnicą, że nie niższe reguły pierwszeństwa może przyczynić się do wartości.

Przepływ atrybutów może również używać **IgnoreThisFlow**. Jest podobny do NULL w tym sensie, że wskazuje, że nie ma nic do wniesienia. Różnica polega na tym, że nie usuwa już istniejącej wartości w docelowych. To jest jak przepływ atrybutu nigdy nie było tam.

Oto przykład:

W *Out to AD — hybryda programu User Exchange* można znaleźć następujący przepływ:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
To wyrażenie należy odczytać tak: jeśli skrzynka pocztowa użytkownika znajduje się w usłudze Azure AD, a następnie przepływ atrybutu z usługi Azure AD do usługi AD. Jeśli nie, nie należy niczego przesyłać z powrotem do usługi Active Directory. W takim przypadku zachowa istniejącą wartość w u!

### <a name="importedvalue"></a>ImportowaneValue
Funkcja ImportowanaValue różni się od wszystkich innych funkcji, ponieważ nazwa atrybutu musi być ujęta w cudzysłowie, a nie nawiasy kwadratowe:  
`ImportedValue("proxyAddresses")`.

Zwykle podczas synchronizacji atrybut używa oczekiwanej wartości, nawet jeśli nie został jeszcze wyeksportowany lub odebrano błąd podczas eksportowania ("góra wieży"). Synchronizacja przychodząca zakłada, że atrybut, który nie osiągnął jeszcze połączonego katalogu, po pewnym czasie do niego dociera. W niektórych przypadkach ważne jest, aby synchronizować tylko wartość, która została potwierdzona przez połączony katalog ("hologram i wieża importu delta").

Przykład tej funkcji można znaleźć w out-of-box Reguła synchronizacji *w z usługi AD — Użytkownik wspólny z programu Exchange*. W programie Hybrid Exchange wartość dodana programu Exchange online powinna być synchronizowana tylko wtedy, gdy zostanie potwierdzone, że wartość została pomyślnie wyeksportowana:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Pierwszeństwo
Gdy kilka reguł synchronizacji próbuje przyczynić się do tej samej wartości atrybutu do obiektu docelowego, wartość pierwszeństwa jest używana do określenia zwycięzcy. Reguła o najwyższym priorytecie, najniższej wartości liczbowej, przyczyni się do atrybutu w konflikcie.

![Typy scalania](./media/concept-azure-ad-connect-sync-declarative-provisioning/precedence1.png)  

Ta kolejność może służyć do definiowania bardziej precyzyjnych przepływów atrybutów dla małego podzbioru obiektów. Na przykład reguły out-of-box upewnij się, że atrybuty z włączonego konta **(Konto użytkownika)** mają pierwszeństwo od innych kont.

Pierwszeństwo można zdefiniować między łącznikami. Dzięki temu łączniki z lepszymi danymi w pierwszej kolejności współtworzyć wartości.

### <a name="multiple-objects-from-the-same-connector-space"></a>Wiele obiektów z tego samego obszaru łącznika
Jeśli masz kilka obiektów w tej samej przestrzeni łącznika połączone do tego samego obiektu metaverse, pierwszeństwo musi być dostosowane. Jeśli kilka obiektów znajduje się w zakresie tej samej reguły synchronizacji, aparat synchronizacji nie jest w stanie określić pierwszeństwa. Jest niejednoznaczne, który obiekt źródłowy powinien przyczynić się wartość do metaverse. Ta konfiguracja jest zgłaszana jako niejednoznaczna, nawet jeśli atrybuty w źródle mają taką samą wartość.  
![Wiele obiektów połączonych z tym samym obiektem mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple1.png)  

W tym scenariuszu należy zmienić zakres reguł synchronizacji, aby obiekty źródłowe miały różne reguły synchronizacji w zakresie. To pozwala zdefiniować różne pierwszeństwo.  
![Wiele obiektów połączonych z tym samym obiektem mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o języku wyrażeń w [opisie wyrażeń deklaratywnej inicjowania obsługi administracyjnej](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Zobacz, jak deklaratywny inicjowania obsługi administracyjnej jest używany po wyjęciu z pudełka [w opisie konfiguracji domyślnej](concept-azure-ad-connect-sync-default-configuration.md).
* Zobacz, jak wprowadzić praktyczną zmianę za pomocą deklaratywnych inicjowania obsługi administracyjnej w [jak wprowadzić zmiany w konfiguracji domyślnej](how-to-connect-sync-change-the-configuration.md).
* Kontynuuj czytanie, jak użytkownicy i kontakty współpracują ze sobą w [opisie użytkowników i kontaktów](concept-azure-ad-connect-sync-user-and-contacts.md).

**Tematy omówienie**

* [Synchronizacja usługi Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)

**Tematy informacyjne**

* [Synchronizacja usługi Azure AD Connect: odwołanie do funkcji](reference-connect-sync-functions-reference.md)
