---
title: 'Program Azure AD Connect: Opis Aprowizacja Deklaratywna | Dokumentacja firmy Microsoft'
description: W tym artykule wyjaśniono, deklaratywny model konfiguracji inicjowania obsługi administracyjnej w programie Azure AD Connect.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60246334"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Synchronizacja programu Azure AD Connect: Omówienie aprowizacji deklaratywnej
W tym temacie opisano model konfiguracji w programie Azure AD Connect. Model jest wywoływana, Aprowizacja Deklaratywna i umożliwia zapewnienie zmiany z łatwością konfiguracji. Wiele rzeczy, które opisano w tym temacie są zaawansowane i nie jest wymagana dla większości scenariuszy.

## <a name="overview"></a>Omówienie
Aprowizacja deklaratywna są przetwarzane obiekty z katalogu źródłowego połączonych i określa, jak obiektów i atrybuty powinny zostać przekształcone ze źródła do obiektu docelowego. Obiekt są przetwarzane w potoku synchronizacji i potoku jest taka sama dla reguły ruchu przychodzącego i wychodzącego. Reguła ruchu przychodzącego obejmuje adresy od obszaru łącznika do środowiska metaverse, a następnie do obszaru łącznika regułę dla ruchu wychodzącego jest metaverse.

![Potok synchronizacji](./media/concept-azure-ad-connect-sync-declarative-provisioning/sync1.png)  

Ten potok ma kilka różnych modułów. Każdy z nich jest odpowiedzialny za jedną pojęciem synchronizacji obiektu.

![Potok synchronizacji](./media/concept-azure-ad-connect-sync-declarative-provisioning/pipeline.png)  

* Źródło i obiekt źródłowy
* [Zakres](#scope), znajdzie wszystkie reguły synchronizacji, które znajdują się w zakresie
* [Dołącz do](#join), określa relację między obszar łączników i metaverse
* Przekształcanie powoduje obliczenie jak atrybuty powinny zostać przekształcone i flow
* [Pierwszeństwo](#precedence), rozwiązuje powodujące konflikt wkładów atrybutu
* Cel, obiekt docelowy

## <a name="scope"></a>Zakres
Moduł zakresie ocenia, obiekt i określa reguły, które znajdują się w zakresie i powinny zostać uwzględnione w czasie przetwarzania. W zależności od wartości atrybutów obiektu reguły synchronizacji różnych są oceniane się w zakresie. Na przykład wyłączony użytkownik z nie skrzynki pocztowej programu Exchange mają różne reguły niż włączonego użytkownika ze skrzynki pocztowej.  
![Zakres](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope1.png)  

Zakres jest zdefiniowany jako grupy oraz klauzul. Klauzule znajdują się wewnątrz grupy. Operator logiczny oraz jest używane między wszystkie klauzule w grupie. Na przykład (dział IT i kraj = = Dania). Operator logiczny lub jest używany między grupami.

![Zakres](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope2.png)  
Zakres na tej ilustracji powinny być odczytywane jako (Dział = dział IT i kraju = Dania) lub (kraj = Szwecja). Jeśli grupa 1 lub 2 grupy nie zostało ocenione na true, reguła znajduje się w zakresie.

Moduł zakresu obsługuje następujące operacje.

| Operacja | Opis |
| --- | --- |
| EQUAL, NOTEQUAL |Porównaj ciąg, który ocenia, czy wartość jest równa wartości atrybutu. W przypadku atrybutów wielowartościowych Zobacz ISIN i ISNOTIN. |
| MNIEJSZE, LESSTHAN_OR_EQUAL |Porównaj ciąg, który ocenia, czy wartość jest poniżej wartości w atrybucie. |
| ZAWIERA, NOTCONTAINS |Porównaj ciąg, który ocenia, czy wartość znajduje się gdzieś w wartości w atrybucie. |
| STARTSWITH, NOTSTARTSWITH |Porównaj ciąg, który ocenia, czy wartość jest na początku wartości w atrybucie. |
| ENDSWITH, NOTENDSWITH |Porównaj ciąg, który ocenia, czy wartość jest na końcu wartości w atrybucie. |
| WIĘKSZE, GREATERTHAN_OR_EQUAL |Porównaj ciąg, który ocenia, czy wartość jest większa niż wartość atrybutu. |
| ISNULL, ISNOTNULL |Ocenia, czy ten atrybut jest nieobecne z obiektu. Jeśli ten atrybut nie jest obecnie i w związku z tym wartość null, reguła jest w zakresie. |
| ISIN, ISNOTIN |Ocenia, jeśli wartość znajduje się w zdefiniowanych atrybutów. Ta operacja jest odmianą wielowartościowe równe i NOTEQUAL. Ten atrybut powinien być atrybutu wielowartościowego, a jeśli wartość można znaleźć w dowolnej wartości atrybutów, następnie reguła znajduje się w zakresie. |
| ISBITSET, ISNOTBITSET |Ocenia, jeśli ustawiono bit określonej. Na przykład może służyć do oceny, bity w kontroli konta użytkownika, aby zobaczyć, czy użytkownik jest włączone. |
| ISMEMBEROF, ISNOTMEMBEROF |Wartość może zawierać nazwy domeny do grupy w przestrzeni łącznika. Jeśli obiekt jest członkiem grupy określonej, reguła jest w zakresie. |

## <a name="join"></a>Join
Moduł sprzężenia w potoku synchronizacji jest odpowiedzialny za znalezienie relację między obiektem w źródle i obiekt w elemencie docelowym. W przychodzącej regule ta relacja będzie obiektu w przestrzeni łącznika, znajdowanie relacji z obiektem w obiekcie metaverse.  
![Dołącz do między cs i mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/join1.png)  
Celem jest, aby zobaczyć, jeśli istnieje obiekt już w magazynie metaverse utworzonych przez inny łącznik, powinna być skojarzona z. Na przykład w lesie zasobów dla konta użytkownika z lasu kont powinien być połączony z użytkownikiem w lesie zasobów.

Sprzężeń są używane przede wszystkim na reguły dla ruchu przychodzącego do dołączenia do obiektów przestrzeni łącznika ze sobą do tego samego obiektu metaverse.

Sprzężeń są definiowane jako co najmniej jedną grupę. Wewnątrz grupy możesz mieć klauzul. Operator logiczny oraz jest używane między wszystkie klauzule w grupie. Operator logiczny lub jest używany między grupami. Grupy są przetwarzane w kolejności od góry do dołu. Czy po jednej grupie ma dokładnie jednego dopasowania z obiektem w elemencie docelowym żadnych reguł dołączania są oceniane. Jeśli zero lub więcej niż jeden obiekt zostanie znaleziony, przetwarzanie w dalszym ciągu następną grupę reguł. Z tego powodu reguł powinien być utworzony zgodnie z kolejnością najbardziej jawne pierwszy i bardziej rozmyte na końcu.  
![Dołącz do definicji](./media/concept-azure-ad-connect-sync-declarative-provisioning/join2.png)  
Sprzężenia na tej ilustracji są przetwarzane od góry do dołu. Pierwszy potok synchronizacji widzi, jeśli istnieje dopasowanie w employeeID. W przeciwnym razie drugiej reguły, widzi, jeśli nazwa konta może służyć do łączenie obiektów. Jeśli nie jest zgodny albo, reguła trzeciej i ostatniej jest dopasowania rozmyte więcej przy użyciu nazwy użytkownika.

Wszystkie reguły dołączania zostały ocenione, jeśli ma dokładnie jedno dopasowanie **typu łącza** na **opis** strona jest używana. Jeśli ta opcja jest ustawiona na **Aprowizowanie**, a następnie tworzony jest nowy obiekt w elemencie docelowym.  
![Aprowizacja lub łączony albo oczekuje](./media/concept-azure-ad-connect-sync-declarative-provisioning/join3.png)  

Obiekt powinien mieć tylko jedną regułę synchronizacji pojedynczego przy użyciu reguł dołączania w zakresie. W przypadku wielu reguł synchronizacji, jest definiowana sprzężenia, wystąpi błąd. Priorytet nie jest używana do rozwiązywania konfliktów sprzężenia. Obiekt musi mieć regułę dołączania w zakresie dla atrybutów, które będą przepływać z tym samym kierunku ruchu przychodzącego/wychodzącego. Jeśli potrzebujesz przepływ atrybutów ruchu przychodzącego i wychodzącego do tego samego obiektu, konieczne jest posiadanie zarówno w przypadku ruchu przychodzącego, jak i regułę synchronizacji ruchu wychodzącego z sprzężenia.

Wychodzące sprzężenia ma specjalne zachowanie podczas próby aprowizacji obiektu obszaru łącznika docelowego. Atrybut nazwy domeny jest używany wypróbowanie sprzężenia zwrotnego. Jeśli istnieje już obiekt w obszarze łącznika docelowego o tej samej nazwy domeny, są łączone obiekty.

Moduł sprzężenia jest oceniane tylko po gdy nowa reguła synchronizacji jest dostarczany do zakresu. Jeśli została przyłączona do obiektu, nie jest odłączania, nawet jeśli nie jest już spełnieniu kryteriów dołączania. Jeśli chcesz odłączyć obiektu reguły synchronizacji, w której dołączony obiekty muszą wykraczają poza zakres.

### <a name="metaverse-delete"></a>Usuwanie obiektu Metaverse
Obiektu metaverse pozostaje tak długo, jak istnieje jedna reguła synchronizacji w zakresie z **typu łącza** równa **Aprowizowanie** lub **StickyJoin**. StickyJoin jest używany, gdy łącznik nie może aprowizować nowy obiekt do środowiska metaverse, ale została przyłączona do, musi ona zostać usunięta w źródle przed usunięciem obiektu metaverse.

Po usunięciu obiektu metaverse wszystkie obiekty skojarzone z regułą synchronizacji ruchu wychodzącego jest oznaczony do **aprowizowanie** są oznaczone do usunięcia.

## <a name="transformations"></a>Przekształcenia
Przekształcenia są używane do definiowania, przepływ atrybutów ze źródła do docelowego. Przepływy mogą mieć jedną z następujących **przepływu typy**: Bezpośrednie, stałej lub wyrażenia. Bezpośrednie przepływu, przepływy wartość atrybutu jako — jest nie dodatkowych przekształceń. Stała wartość Ustawia określoną wartość. Wyrażenie używa deklaratywnych języka wyrażeń inicjowania obsługi administracyjnej programu express, jak powinny być transformacji. Szczegółowe informacje dotyczące języka wyrażeń można znaleźć w [zrozumienie deklaratywne inicjowania obsługi administracyjnej język wyrażeń](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) tematu.

![Aprowizacja lub łączony albo oczekuje](./media/concept-azure-ad-connect-sync-declarative-provisioning/transformations1.png)  

**Zastosuj raz** pole wyboru określa, czy atrybut może zostać ustawiona tylko podczas tworzenia obiektu. Na przykład tej konfiguracji można ustawić początkowe hasło dla nowego obiektu użytkownika.

### <a name="merging-attribute-values"></a>Scalanie wartości atrybutów
W ramach przepływów atrybutów ma ustawienie określające, jeśli atrybuty wielowartościowe powinny zostać scalone z kilku różnych łączników. Wartość domyślna to **aktualizacji**, co oznacza, że reguły synchronizacji o najwyższym priorytecie powinno wygrać.

![Scal typów](./media/concept-azure-ad-connect-sync-declarative-provisioning/mergetype.png)  

Istnieje również **scalania** i **MergeCaseInsensitive**. Te opcje umożliwiają scalić wartości z różnych źródeł. Na przykład może służyć do scalenia atrybut elementu członkowskiego lub proxyAddresses z kilku różnych lasach. Tej opcji wszystkie reguły synchronizacji w zakresie dla obiektu, należy użyć tego samego typu scalania. Nie można zdefiniować **aktualizacji** z jeden łącznik i **scalania** z innego. Jeśli spróbujesz, otrzymasz komunikat o błędzie.

Różnica między **scalania** i **MergeCaseInsensitive** przedstawiono sposób przetwarzania zduplikowanymi wartościami atrybutów. Aparat synchronizacji zapewnia, że powtarzające się wartości nie są wstawiane do atrybutu docelowego. Za pomocą **MergeCaseInsensitive**, zduplikowane wartości za pomocą tylko różnica w przypadku, gdy nie mają być obecne. Na przykład, nie powinny występować zarówno "SMTP:bob@contoso.com"i"smtp:bob@contoso.com" w atrybucie docelowym. **Scal** tylko zagląda dokładne wartości oraz wielu wartości w przypadku, gdy tylko różnią się w przypadku może być obecny.

Opcję **Zastąp** jest taka sama jak **aktualizacji**, ale nie są używane.

### <a name="control-the-attribute-flow-process"></a>Kontroluj proces przepływ atrybutu
Kiedy wiele reguł synchronizacji ruchu przychodzącego są skonfigurowane do przyczyniają się do tego samego atrybutu magazynu metaverse, a następnie pierwszeństwo służy do określania zwycięzca. Reguły synchronizacji o najwyższym priorytecie (najmniejsza wartość liczbową) będzie przyczyniają się wartość. W tym celu dla reguł ruchu wychodzącego. Synchronizacja reguł najwyższy priorytet WINS i Współtworzenie wartości do połączonego katalogu.

W niektórych przypadkach, a nie przyczyniają się wartość reguły synchronizacji należy określić, jak inne zasady powinny zachowywać się. Istnieją pewne specjalne literałów używane dla tego przypadku.

Dla reguły synchronizacji ruchu przychodzącego, literał **NULL** może służyć do wskazania, że przepływ nie ma żadnej wartości, aby współtworzyć. Inną regułę o niższym priorytecie może współtworzyć zawartość wartości. Jeśli żadna reguła współtworzonych przez wartość, atrybut metaverse zostaną usunięte. Reguły ruchu wychodzącego Jeśli **NULL** jest końcowa wartość po przetworzeniu wszystkie reguły synchronizacji, a następnie wartość zostanie usunięta w połączonym katalogu.

Literał **AuthoritativeNull** przypomina **NULL** , ale z tą różnicą, nie niższe reguły pierwszeństwa współtworzyli zawartość wartości.

Przepływ atrybutu można również użyć **IgnoreThisFlow**. Jest on podobny do wartości NULL, w tym sensie, że wskazuje, że nie ma nic do współtworzenia. Różnica polega na tym, że nie usuwa już istniejącą wartość w elemencie docelowym. Jest jak przepływ atrybutów nigdy nie było dostępne.

Oto przykład:

W *poza usługą AD — wdrożenie hybrydowe programu Exchange użytkownika* znajduje się następujący przepływ:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
To wyrażenie powinny być odczytywane jako: Jeśli skrzynka pocztowa użytkownika znajduje się w usłudze Azure AD, przepływ atrybutów z usługi Azure AD do usługi AD. W przeciwnym razie nie przepływać do niczego do usługi Active Directory. W tym przypadku będzie ją zachować istniejącą wartość w AD.

### <a name="importedvalue"></a>ImportedValue
Funkcja ImportedValue różni się od innych funkcji, ponieważ nazwa atrybutu muszą być ujęte w cudzysłowy, a nie kwadratowe:  
`ImportedValue("proxyAddresses")`.

Zazwyczaj podczas synchronizacji atrybut używa wartość oczekiwana, nawet jeśli go nie zostały jeszcze wyeksportowane lub otrzymano komunikat o błędzie podczas eksportowania ("top tower"). Synchronizacji ruchu przychodzącego przyjęto założenie, że atrybut, który nie zostało jeszcze osiągnięcia połączonego katalogu ostatecznie osiągnie on. W niektórych przypadkach jest ważne, aby synchronizować tylko wartość, która zostało potwierdzone przez połączonego katalogu ("hologram i różnicowych, zaimportuj tower").

Przykładem tej funkcji można znaleźć w reguły synchronizacji out-of-box *w z usługi AD — typowe użytkownika z programu Exchange*. W programie Exchange hybrydowych wartości dodanej przez program Exchange online tylko mają być synchronizowane po potwierdzeniu, że wartość została pomyślnie wyeksportowana:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Pierwszeństwo
Podczas próby kilka reguł synchronizacji do współtworzenia taką samą wartość atrybutu do elementu docelowego, wartość pierwszeństwa służy do określenia zwycięzca. Reguły z najwyższy priorytet i najniższe wartości liczbowej, będzie współtworzyć ten atrybut w konflikcie.

![Scal typów](./media/concept-azure-ad-connect-sync-declarative-provisioning/precedence1.png)  

Ta kolejność może służyć do definiowania bardziej precyzyjne przepływy atrybutów dla małego podzbioru obiektów. Na przykład, out z okno — zasady upewnij się, który atrybuty z włączone konto (**AccountEnabled użytkownika**) mają wyższy priorytet od innych kont.

Pierwszeństwo można zdefiniować między łączników. Łączniki, która umożliwia lepsze danymi się wartości.

### <a name="multiple-objects-from-the-same-connector-space"></a>Wiele obiektów, z tą samą przestrzenią łącznika
Jeśli masz kilka obiektów w tej samej przestrzeni łącznika, dołączony do tego samego obiektu metaverse pierwszeństwo musi zostać dostosowana. Jeśli niektóre obiekty znajdują się w zakresie tę samą regułę synchronizacji, aparat synchronizacji nie jest możliwe ustalenie, pierwszeństwo. Jest niejednoznaczny obiektu źródłowego, które powinny mieć udział wartość do obiektu metaverse. Ta konfiguracja jest raportowana jako niejednoznaczny, nawet jeśli atrybuty w źródle mają taką samą wartość.  
![Wiele obiektów dołączonych do tego samego obiektu mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple1.png)  

W tym scenariuszu należy zmienić zakres reguły synchronizacji, więc obiekty źródła synchronizacji różnych zasad w zakresie. Pozwala zdefiniować różne pierwszeństwo.  
![Wiele obiektów dołączonych do tego samego obiektu mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o języku wyrażeń w [interpretacji wyrażenia inicjowania obsługi administracyjnej deklaratywne](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Zobacz, jak deklaratywne Inicjowanie obsługi administracyjnej jest używane out-of-box w [opis konfiguracji domyślnej](concept-azure-ad-connect-sync-default-configuration.md).
* Zobacz, jak zrobić praktyczne przy użyciu aprowizacja deklaratywna w [sposób wprowadzania zmian w domyślnej konfiguracji](how-to-connect-sync-change-the-configuration.md).
* Przeczytaj, jak użytkownicy i kontakty współpracują ze sobą w dalszym ciągu [opis użytkowników i kontaktów](concept-azure-ad-connect-sync-user-and-contacts.md).

**Tematy poglądowe**

* [Synchronizacja w programie Azure AD Connect: Zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)

**Tematy odwołań**

* [Synchronizacja w programie Azure AD Connect: Informacje ogólne o funkcjach](reference-connect-sync-functions-reference.md)
