---
title: 'Azure AD Connect: Opis Aprowizacją deklaratywną | Dokumentacja firmy Microsoft'
description: W tym artykule wyjaśniono deklaratywne inicjowania obsługi administracyjnej model konfiguracji w programie Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: cfbb870d-be7d-47b3-ba01-9e78121f0067
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: bb6a0c16322884afba3d306c491c3cd592fc8595
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593195"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Synchronizacja programu Azure AD Connect: opis Aprowizacją deklaratywną
W tym temacie opisano model konfiguracji w programie Azure AD Connect. Model jest nazywany Aprowizacją deklaratywną i pozwala na zapewnienie zmiany z łatwością konfiguracji. Wiele czynności opisanych w tym temacie są zaawansowane i nie jest wymagane dla większości scenariuszy.

## <a name="overview"></a>Przegląd
Aprowizacja deklaratywna jest przetwarzanie obiektów odbierane z połączonego katalogu źródłowego i określa, jak obiekt i atrybuty transformacji ze źródła do miejsca docelowego. Obiekt jest przetwarzane w potoku synchronizacji i potoku jest taki sam dla reguł ruchu przychodzącego i wychodzącego. Reguła ruchu przychodzącego jest z przestrzeni łącznika do środowiska metaverse, a Reguła ruchu wychodzącego jest metaverse do przestrzeni łącznika.

![Potok synchronizacji](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/sync1.png)  

Potok ma kilka różnych modułach. Każdy z nich jest odpowiedzialny za jednej koncepcji w obiekt synchronizacji.

![Potok synchronizacji](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/pipeline.png)  

* Źródło, obiektu źródłowego
* [Zakres](#scope), odnajduje wszystkie reguły synchronizacji, które znajdują się w zakresie
* [Dołącz](#join), określa relację między obszar łączników i metaverse
* [Przekształć](#transform), jak atrybuty powinny zostać przekształcone powoduje obliczenie i przepływu
* [Pierwszeństwo](#precedence), rozwiązuje konflikt wkładów atrybutu
* Obiekt docelowy, obiekt docelowy

## <a name="scope"></a>Zakres
Moduł zakresu dokonuje oceny obiektu i określa reguły, które znajdują się w zakresie, a powinien być uwzględniany w przetwarzaniu. W zależności od wartości atrybutów dla obiektu reguły synchronizacji różnych są oceniane znajdował się w zakresie. Na przykład wyłączony użytkownik z nie skrzynek pocztowych programu Exchange mają inne reguły niż włączonego użytkownika z skrzynki pocztowej.  
![Zakres](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope1.png)  

Zakres jest zdefiniowany jako grupy i klauzul. Klauzule znajdują się wewnątrz grupy. Logiczne i jest używany podczas komunikacji między wszystkie klauzule w grupie. Na przykład (działu IT i kraju = = Dania). Logiczne lub jest używana między grupami.

![Zakres](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope2.png)  
Zakres na tej ilustracji są odczytywane jako (działu IT i kraju = = Dania) lub (kraju = Szwecja). Jeśli zarówno grupy 1 lub 2, które jest obliczane na true, reguła znajduje się w zakresie.

Moduł zakresu obsługuje następujące operacje.

| Operacja | Opis |
| --- | --- |
| RÓWNOŚCI, NOTEQUAL |Porównaj ciąg, który ocenia, czy wartość jest równa wartości atrybutu. W przypadku atrybutów wielowartościowych Zobacz ISIN i ISNOTIN. |
| LESSTHAN, LESSTHAN_OR_EQUAL |Porównaj ciąg, który ocenia, czy wartość jest poniżej wartości w atrybucie. |
| ZAWIERA, NOTCONTAINS |Porównaj ciąg, obliczane, jeśli wartość znajduje się gdzieś w wartości atrybutu. |
| STARTSWITH, NOTSTARTSWITH |Porównaj ciąg, który ocenia, czy wartość jest na początku wartości w atrybucie. |
| ENDSWITH, NOTENDSWITH |Porównaj ciąg, który ocenia, czy wartość jest na końcu wartości w atrybucie. |
| GREATERTHAN, GREATERTHAN_OR_EQUAL |Porównaj ciąg, który ocenia, czy wartość jest większa od wartości w atrybucie. |
| ISNULL, ISNOTNULL |Ocenia, czy ten atrybut jest nieobecny z obiektu. Jeśli ten atrybut nie jest obecny i dlatego wartość null, reguła jest w zakresie. |
| ISIN, ISNOTIN |Ocenia, czy wartość jest obecny w atrybucie zdefiniowane. Ta operacja jest odmianą wielowartościowe równości i NOTEQUAL. Ten atrybut powinien być atrybutu wielowartościowego, a jeśli wartość znajduje się w jednym z wartości atrybutów, następnie reguła jest w zakresie. |
| ISBITSET, ISNOTBITSET |Ocenia, jeśli ustawiono bit konkretnego. Na przykład może być użyta do oceny usługi bits w kontroli konta użytkownika, aby sprawdzić, czy użytkownik jest włączona lub wyłączona. |
| ISMEMBEROF, ISNOTMEMBEROF |Wartość powinna zawierać nazwy domeny do grupy w przestrzeni łącznika. Jeśli obiekt jest członkiem określonej grupy, reguła jest w zakresie. |

## <a name="join"></a>Join
W module sprzężenia w potoku synchronizacji jest odpowiedzialny za znajdowania relacji między obiektem w źródle i obiektu w miejscu docelowym. W przychodzącej regule ta relacja będzie obiektu w przestrzeni łącznika, znajdowanie relacji do obiektu w magazynie metaverse.  
![Dołącz między cs i mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join1.png)  
Celem jest, aby zobaczyć, jeśli istnieje obiekt już w magazynie metaverse, utworzone przez innego łącznika powinna być skojarzona z. Na przykład w lesie zasobów konta użytkownika z lasu kont powinien być połączony z użytkownikiem w lesie zasobów.

Sprzężenia są używane przede wszystkim od reguł ruchu przychodzącego sprzęgać ze sobą obiektów przestrzeni łącznika do tego samego obiektu metaverse.

Złączenia są definiowane jako co najmniej jedną grupę. W grupie masz klauzul. Logiczne i jest używany podczas komunikacji między wszystkie klauzule w grupie. Logiczne lub jest używana między grupami. Grupy są przetwarzane w kolejności od góry do dołu. Czy po jednej grupie ma dokładnie jedno dopasowanie z obiektem w celu są oceniane żadnych reguł sprzężenia. Zero lub więcej niż jeden obiekt zostanie znaleziony, przetwarzanie będzie kontynuowane do następnej grupy reguł. Z tego powodu reguły powinna być utworzony kolejności najbardziej jawne pierwszy i bardziej rozmytego na końcu.  
![Dołącz do definicji](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join2.png)  
Sprzężenia na tej ilustracji są przetwarzane od góry do dołu. Najpierw potoku synchronizacji widzi, jeśli istnieje dopasowanie na identyfikator pracownika. Jeśli nie widzi drugą regułę, jeśli nazwa konta może służyć do łączenie obiektów. Jeśli nie jest dopasowanie albo, trzeci i końcowa reguła jest więcej rozmytego dopasowanie przy użyciu nazwy użytkownika.

Zostały ocenione wszystkie reguły sprzężenia, jeśli ma dokładnie jedno dopasowanie **typu łącza** na **opis** strona jest używana. Jeśli ta opcja jest ustawiona na **udostępniania**, a następnie tworzony jest nowy obiekt w obiekcie docelowym.  
![Zapewnianie lub sprzężenia](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join3.png)  

Obiekt powinien mieć tylko jedną regułę synchronizacji jednego z regułami sprzężenia w zakresie. Jeśli istnieje wiele reguł synchronizacji których sprzężenie jest zdefiniowana, występuje błąd. Pierwszeństwo nie jest używana do rozwiązywania konfliktów sprzężenia. Obiekt musi mieć reguły sprzężenia w zakresie dla atrybutów, które będą przepływać z tym samym kierunku ruchu przychodzącego/wychodzącego. Jeśli potrzebujesz przepływ atrybutów ruchu przychodzącego i wychodzącego z tym samym obiektem, musi mieć ruchu przychodzącego i regułę synchronizacji ruchu wychodzącego z sprzężenia.

Wychodzące sprzężenia ma specjalnego zachowania podczas próby udostępnienia obiektu do miejsca docelowego łącznika. Atrybut nazwy domeny jest używany do najpierw spróbuj użyć wstecznego sprzężenia. Jeśli do przestrzeni łącznika docelowego o tej samej nazwie Wyróżniającej istnieje już obiekt, obiekty są łączone.

Modułu sprzężenia jest oceniana tylko wtedy, gdy podczas nową regułę synchronizacji wchodzi w zakres. Jeśli została przyłączona do obiektu, nie jest odłączania nawet wtedy, gdy kryteria nie jest spełniony. Jeśli chcesz odłączyć obiektu reguły synchronizacji, który dołączył obiekty musi się znaleźć poza zakresem.

### <a name="metaverse-delete"></a>Usuń metaverse
Obiektu metaverse pozostaje tak długo, jak istnieje jedna reguła synchronizacji w zakresie z **typu łącza** ustawioną **udostępniania** lub **StickyJoin**. StickyJoin jest używany, gdy łącznik nie może udostępnić nowy obiekt do środowiska metaverse, a została przyłączona do, musi ono zostać usunięte w źródle przed usunięciem obiektu metaverse.

Po usunięciu obiektu metaverse wszystkie obiekty skojarzone z reguła synchronizacji ruchu wychodzącego oznaczone do **udostępniania** są oznaczone do usunięcia.

## <a name="transformations"></a>Przekształcenia
Przekształceń są używane do definiowania, jak atrybuty powinny przepływać ze źródła do obiektu docelowego. Strumienie może mieć jedną z następujących **przepływ typów**: bezpośrednie, stałej lub wyrażenia. Bezpośrednie przepływu, przepływy jako wartość atrybutu-jest nie dodatkowe przekształceń. Stała wartość Ustawia określoną wartość. Wyrażenie używa deklaratywne inicjowania obsługi language wyrażenie Express, jak powinna być transformacja. Szczegóły język wyrażeń znajduje się w [opis deklaratywne inicjowania obsługi language wyrażenie](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) tematu.

![Zapewnianie lub sprzężenia](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/transformations1.png)  

**Zastosuj raz** wyboru określa, że ten atrybut powinien można ustawić tylko podczas tworzenia obiektu. Tę konfigurację można na przykład można ustawić początkowe hasło dla nowego obiektu użytkownika.

### <a name="merging-attribute-values"></a>Scalanie wartości atrybutów
Przepływy atrybutów ma ustawienie, aby określić, czy atrybuty wielowartościowe powinny zostać scalone z kilku różnych łączników. Wartość domyślna to **aktualizacji**, co oznacza, że powinien win reguła synchronizacji o najwyższym priorytecie.

![Scal typów](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/mergetype.png)  

Istnieje również **scalania** i **MergeCaseInsensitive**. Te opcje umożliwiają scalania wartości z różnych źródeł. Na przykład może służyć do scalenia atrybut elementu członkowskiego lub proxyAddresses z kilku różnych lasach. Tej opcji wszystkie reguły synchronizacji w zakresie dla obiekt muszą używać tego samego typu scalania. Nie można zdefiniować **aktualizacji** z jeden łącznik i **scalania** z innej. Jeśli spróbujesz, wystąpi błąd.

Różnica między **scalania** i **MergeCaseInsensitive** jest sposób przetwarzania wartości zduplikowany atrybut. Aparat synchronizacji upewnia się, że zduplikowane wartości nie są wstawiane do atrybutu docelowego. Z **MergeCaseInsensitive**, zduplikowane wartości z tylko różnicą w przypadku, gdy nie będą występować. Na przykład użytkownik nie powinien być widoczny zarówno "SMTP:bob@contoso.com"i"smtp:bob@contoso.com" atrybutu docelowego. **Scal** tylko przegląda dokładne wartości oraz wielu wartości w przypadku, gdy tylko różnią się w przypadku mogą być dostępne.

Opcja **Zastąp** jest taka sama jak **aktualizacji**, ale nie jest on używany.

### <a name="control-the-attribute-flow-process"></a>Formant procesu przepływu atrybutów
Po skonfigurowaniu przyczynić się do tego samego atrybutu metaverse wiele reguł synchronizacji ruchu przychodzącego, pierwszeństwo jest używany do określenia wygrał użytkownik. Reguła synchronizacji z najwyższy priorytet (najmniejsza wartość liczbowa) będzie przyczyniają się wartość. W tym celu dla reguł ruchu wychodzącego. Synchronizacja reguły o najwyższym priorytecie wins i współtworzenia wartość do połączonego katalogu.

W niektórych przypadkach zamiast współtworzenia wartość reguły synchronizacji należy określić zachowanie inne zasady. Istnieją pewne specjalne literałów używane dla tego przypadku.

Dla reguł synchronizacji ruchu przychodzącego, literał **NULL** można wskazać, że przepływ nie ma wartości do ich współtworzenia. Inna reguła o niższym priorytecie może przyczynić się wartość. Jeśli żadna reguła przyczyniły się wartość, atrybut metaverse jest usuwany. Wychodzące reguły Jeśli **NULL** jest końcowa wartość po przetworzeniu wszystkich reguł synchronizacji, a następnie wartość zostanie usunięta w połączonego katalogu.

Literał **AuthoritativeNull** jest podobny do **NULL** , ale z tą różnicą, że żadne reguły niższy priorytet może przyczynić się wartość.

Przepływ atrybutu można również użyć **IgnoreThisFlow**. Jest on podobny do wartości NULL w tym sensie, że wskazuje, że nie ma nic do ich współtworzenia. Różnica polega na tym, że nie usuwa już istniejącą wartość w elemencie docelowym. Jest tak przepływ atrybutów nigdy nie było dostępne.

Oto przykład:

W *mieszczą się w usłudze AD - hybrydowym programu Exchange użytkownika* można znaleźć następujący przepływ:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
To wyrażenie powinno zostać odczytany jako: Jeśli skrzynka pocztowa użytkownika znajduje się w usłudze Azure AD, następnie przepływu atrybutu z usługi Azure AD lub AD. Jeśli nie, nie przepływu niczego do usługi Active Directory. W takim przypadku będzie on zachować istniejącą wartość w usłudze AD.

### <a name="importedvalue"></a>ImportedValue
Funkcja ImportedValue różni się od innych funkcji, ponieważ nazwa atrybutu musi być ujęta w cudzysłów, a nie w nawiasy kwadratowe:  
`ImportedValue("proxyAddresses")`.

Zazwyczaj podczas synchronizacji wartość oczekiwana używa atrybutu, nawet jeśli nie zostaną wyeksportowane jeszcze lub błąd podczas eksportowania ("top wieża"). Synchronizacji ruchu przychodzącego przyjęto założenie, że atrybut, który nie jeszcze osiągnięto połączonego katalogu ostatecznie osiągnie on. W niektórych przypadkach należy zsynchronizować tylko wartość, która została potwierdzona przez połączonego katalogu (hologram i różnicowe zaimportować wieża").

Przykładem tej funkcji można znaleźć w reguły synchronizacji out-of-box *w z usługi Active Directory — typowe użytkownika z programu Exchange*. W programie Exchange hybrydowego wartości dodanej przez program Exchange online tylko mają być synchronizowane po potwierdzeniu, że wartość została pomyślnie wyeksportowano:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Pierwszeństwo
Gdy kilka reguł synchronizacji próbuje przyczyniają się taką samą wartość atrybutu do obiektu docelowego, wartość pierwszeństwa służy do określenia wygrał użytkownik. Reguła o najwyższym priorytecie, najmniejsza wartość liczbowa będzie współtworzenia atrybutu w konflikt.

![Scal typów](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/precedence1.png)  

Ta kolejność może służyć do definiowania dokładniejsze przepływów atrybutów dla małego podzbioru obiektów. Na przykład out z — okno — zasady upewnij się, że atrybutów z włączonym kontem (**AccountEnabled użytkownika**) mają priorytet z innych kont.

Można zdefiniować priorytet między łączników. Z danymi lepszą się wartości, która umożliwia łączników.

### <a name="multiple-objects-from-the-same-connector-space"></a>Wiele obiektów z tego samego obszaru łącznika
Priorytet musi być ustawiona, jeśli masz wiele obiektów w tej samej przestrzeni łącznika dołączony do tego samego obiektu metaverse. Jeśli niektóre obiekty znajdują się w zakresie tej samej reguły synchronizacji, aparat synchronizacji nie jest możliwe ustalenie, pierwszeństwo. Jest niejednoznaczny obiekt źródłowy, które powinny mieć udział wartość do środowiska metaverse. Ta konfiguracja jest raportowane jako niejednoznaczny, nawet jeśli atrybutów w źródle mają taką samą wartość.  
![Wiele obiektów dołączonych do tego samego obiektu mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple1.png)  

W tym scenariuszu należy zmienić zakres reguły synchronizacji, więc obiekty źródła synchronizacji różnych zasad w zakresie. Który można zdefiniować inny priorytet.  
![Wiele obiektów dołączonych do tego samego obiektu mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o języku wyrażenia w [opis deklaratywne inicjowania obsługi administracyjnej wyrażenia](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
* Zobacz, jak deklaratywne inicjowania obsługi administracyjnej jest używane out-of-box w [opis konfiguracji domyślnej](active-directory-aadconnectsync-understanding-default-configuration.md).
* W temacie jak zrobić praktyczne przy użyciu aprowizacją deklaratywną w [jak wprowadzić zmianę do domyślnej konfiguracji](active-directory-aadconnectsync-change-the-configuration.md).
* Nadal czytać, jak użytkownicy i kontakty współdziałać ze sobą [opis użytkowników i kontaktów](active-directory-aadconnectsync-understanding-users-and-contacts.md).

**Tematy poglądowe**

* [Synchronizacja programu Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](active-directory-aadconnectsync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)

**Tematy odwołań**

* [Synchronizacja programu Azure AD Connect: odwołanie do funkcji](active-directory-aadconnectsync-functions-reference.md)
