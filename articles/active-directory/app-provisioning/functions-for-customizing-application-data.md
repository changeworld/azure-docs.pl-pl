---
title: Pisanie wyrażeń dla mapowań atrybutów w usłudze Azure AD
description: Dowiedz się, jak używać mapowań wyrażenia do przekształcania wartości atrybutów akceptowalny format podczas automatycznego inicjowania obsługi obiektów aplikacji SaaS w usłudze Azure Active Directory.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: a01f7f48dd93983edf4be4b797f62afede273c66
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066670"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Pisanie wyrażeń na potrzeby mapowania atrybutów w usłudze Azure Active Directory
Podczas konfigurowania, inicjowania obsługi administracyjnej aplikacji SaaS, jest jeden z typów mapowania atrybutów, które można określić mapowanie wyrażenia. W tym przypadku trzeba napisać wyrażenia podobne do skryptu, która pozwala na przekształcanie danych użytkowników w formatach, które są bardziej akceptowalne dla aplikacji SaaS.

## <a name="syntax-overview"></a>Omówienie składni
Składnia wyrażeń do mapowania atrybutów jest przypominający języka Visual Basic for Applications (VBA) funkcje.

* Całe wyrażenie musi być zdefiniowany w zakresie funkcji, które składają się z nazwy argumentów w nawiasach: <br>
  *FunctionName (`<<argument 1>>`,`<<argument N>>`)*
* Może być zagnieżdżony funkcji w ramach siebie nawzajem. Na przykład: <br> *FunctionOne (FunctionTwo (`<<argument1>>`))*
* Trzy różne rodzaje argumenty można przekazać do funkcji:
  
  1. Atrybuty, które muszą być ujęte w nawiasy kwadratowe. Na przykład: [attributeName]
  2. Stałe typu String, które muszą być ujęte w cudzysłów. Na przykład: "United States"
  3. Inne funkcje. Na przykład: FunctionOne (`<<argument1>>`, FunctionTwo (`<<argument2>>`))
* Dla stałych ciągów Jeśli potrzebujesz kreski ułamkowej odwróconej (\) lub cudzysłowu (") w ciągu go należy użyć znaków ucieczki symbolem kreski ułamkowej odwróconej (\). Na przykład: "Nazwa firmy: \\" contoso\\""

## <a name="list-of-functions"></a>Lista funkcji
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [Coalesce](#coalesce) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;[FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Guid](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [NumFromDate](#numfromdate) &nbsp;&nbsp;&nbsp;&nbsp;[RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)&nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

---
### <a name="append"></a>Append
**Funkcyjn**<br> Append(source, suffix)

**Zharmonizowan**<br> Przyjmuje wartość ciągu źródła i dołącza sufiks na końcu.

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagany |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego. |
| **przedrostk** |Wymagany |Ciąg |Ciąg, który chcesz dołączyć do końca wartość źródła. |

---
### <a name="bitand"></a>BitAnd
**Funkcyjn**<br> BitAnd (wartość1, wartość2)

**Zharmonizowan**<br> Ta funkcja konwertuje oba parametry na reprezentację binarną i ustawia bit na:

0 — Jeśli jedna lub obie odpowiadające bity w wartość1 i wartość2 są równe 0                                                  
1 — Jeśli obie odpowiadające bity są 1.                                    

Innymi słowy zwraca 0 we wszystkich przypadkach, z wyjątkiem sytuacji, gdy odpowiadające im bity obu parametrów są 1.

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **sekwencj** |Wymagany |numerowan |Wartość liczbowa, która powinna być AND'ed z wartość2|
| **wartość2** |Wymagany |numerowan |Wartość liczbowa, która powinna być AND'ed z wartość1|

**Przykład:**<br>
BitAnd (& HF, & HF7)                                                                                
11110111 i 00000111 = 00000111, tak aby BitAnd zwracała 7, wartość binarną 00000111

---
### <a name="cbool"></a>CBool
**Funkcyjn**<br> CBool (wyrażenie)

**Zharmonizowan**<br> CBool zwraca wartość logiczną opartą na obliczonym wyrażeniu. Jeśli wyrażenie zwróci wartość różną od zera, Funkcja CBool zwraca wartość true, w przeciwnym razie zwraca wartość false..

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **wyrażenia** |Wymagany | wyrażenia | Dowolne prawidłowe wyrażenie |

**Przykład:**<br>
CBool ([Attribute1] = [attribute2])                                                                    
Zwraca wartość true, jeśli oba atrybuty mają tę samą wartość.

---
### <a name="coalesce"></a>łączonych
**Funkcyjn**<br> Połączenie (source1, SOURCE2,..., DefaultValue)

**Zharmonizowan**<br> Zwraca pierwszą wartość źródłową, która nie jest RÓWNa NULL. Jeśli wszystkie argumenty mają wartość NULL, a właściwość DefaultValue jest obecna, zostanie zwrócona wartość DefaultValue. Jeśli wszystkie argumenty mają wartość NULL i właściwość DefaultValue nie istnieje, funkcja łączenia zwraca wartość NULL.

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **source1 ... sourceN** | Wymagany | Ciąg |Wymagana, zmienna liczba razy. Zazwyczaj nazwa atrybutu z obiektu źródłowego. |
| **defaultValue** | Optional (Opcjonalność) | Ciąg | Wartość domyślna, która ma być używana, jeśli wszystkie wartości źródłowe mają wartość NULL. Może być pustym ciągiem ("").

---
### <a name="converttobase64"></a>ConvertToBase64
**Funkcyjn**<br> ConvertToBase64 (Źródło)

**Zharmonizowan**<br> Funkcja ConvertToBase64 konwertuje ciąg na ciąg Unicode Base64.

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagany |Ciąg |Ciąg do przekonwertowania na podstawowy 64|

**Przykład:**<br>
ConvertToBase64 ("Witaj świecie!")                                                                                                        
Zwraca wartość "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Funkcyjn**<br> ConvertToUTF8Hex (Źródło)

**Zharmonizowan**<br> Funkcja ConvertToUTF8Hex konwertuje ciąg na zakodowaną wartość szesnastkową UTF8.

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagany |Ciąg |Ciąg, który ma zostać przekonwertowany na kodowanie szesnastkowe|

**Przykład:**<br>
ConvertToUTF8Hex ("Witaj świecie!")                                                                                                         
Zwraca 48656C6C6F20776F726C6421

---
### <a name="count"></a>Licznik
**Funkcyjn**<br> Count (atrybut)

**Zharmonizowan**<br> Funkcja count zwraca liczbę elementów w atrybucie wielowartościowym

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **przypisane** |Wymagany |— atrybut |Wielowartościowy atrybut, który będzie miał zliczane elementy|

---
### <a name="cstr"></a>CStr
**Funkcyjn**<br> CStr (wartość)

**Zharmonizowan**<br> Funkcja CStr konwertuje wartość na typ danych ciągu.

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **value** |Wymagany | liczbowe, odwołanie lub wartość logiczna | Może być wartością liczbową, atrybutem odwołania lub wartością logiczną. |

**Przykład:**<br>
CStr ([DN])                                                            
Zwraca wartość "CN = Jan, DC = contoso, DC = com"

---
### <a name="datefromnum"></a>DateFromNum
**Funkcyjn**<br> DateFromNum (wartość)

**Zharmonizowan**<br> Funkcja DateFromNum konwertuje wartość w formacie daty usługi AD na typ DateTime.

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **value** |Wymagany | Date | Data usługi AD do przekonwertowania na typ DateTime |

**Przykład:**<br>
DateFromNum([lastLogonTimestamp])                                                                                                   
DateFromNum(129699324000000000)                                                            
Zwraca datę i godzinę reprezentującą 2012-01-01 23:00:00

---
### <a name="formatdatetime"></a>FormatDateTime
**Funkcyjn**<br> FormatDateTime (źródło, inputFormat outputFormat)

**Zharmonizowan**<br> Pobiera ciąg daty z jednego formatu i konwertuje je do innego formatu.

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagany |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego. |
| **inputFormat** |Wymagany |Ciąg |Oczekiwany format wartość źródła. Obsługiwane formaty można znaleźć w temacie [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Wymagany |Ciąg |Format wyjściowej daty. |

---
### <a name="guid"></a>Guid
**Funkcyjn**<br> Identyfikator GUID ()

**Zharmonizowan**<br> Identyfikator GUID funkcji generuje nowy losowy identyfikator GUID

---
### <a name="instr"></a>InStr
**Funkcyjn**<br> InStr (wartość1, wartość2, początek, comparetype)

**Zharmonizowan**<br> Funkcja InStr Znajdowanie pierwszego wystąpienia podciągu w ciągu

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **sekwencj** |Wymagany |Ciąg |Ciąg do przeszukania |
| **wartość2** |Wymagany |Ciąg |Ciąg, który ma zostać znaleziony |
| **start** |Optional (Opcjonalność) |Liczba całkowita |Pozycja początkowa do znalezienia podciągu|
| **comparetype** |Optional (Opcjonalność) |Wyliczenie |Może być vbTextCompare lub vbBinaryCompare |

**Przykład:**<br>
InStr ("Quick brązowy Fox", "Quick")                                                                             
Evalues do 5

InStr ("powtórzone", "e", 3, vbBinaryCompare)                                                                                  
Szacuje się w 7

---
### <a name="isnull"></a>IsNull
**Funkcyjn**<br> IsNull (wyrażenie)

**Zharmonizowan**<br> Jeśli wyrażenie daje w wyniku wartość null, Funkcja IsNull zwraca wartość true. W przypadku atrybutu wartość null jest wyrażana przez nieobecność atrybutu.

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **wyrażenia** |Wymagany |wyrażenia |Wyrażenie, które ma zostać obliczone |

**Przykład:**<br>
IsNull ([displayName])                                                                                                
Zwraca wartość true, jeśli atrybut nie jest obecny

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Funkcyjn**<br> IsNullOrEmpty (wyrażenie)

**Zharmonizowan**<br> Jeśli wyrażenie ma wartość null lub jest pustym ciągiem, funkcja IsNullOrEmpty zwraca wartość true. W przypadku atrybutu wynikiem tego jest wartość true, jeśli atrybut jest nieobecny lub jest obecny, ale jest ciągiem pustym.
Odwrotność tej funkcji ma nazwę isobecne.

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **wyrażenia** |Wymagany |wyrażenia |Wyrażenie, które ma zostać obliczone |

**Przykład:**<br>
IsNullOrEmpty ([displayName])                                               
Zwraca wartość true, jeśli atrybut nie jest obecny lub jest ciągiem pustym

---
### <a name="ispresent"></a>Isobecne
**Funkcyjn**<br> Isobecny (wyrażenie)

**Zharmonizowan**<br> Jeśli wyrażenie daje w wyniku ciąg, który nie ma wartości null i nie jest pusty, funkcja isprezent zwraca wartość true. Odwrotność tej funkcji ma nazwę IsNullOrEmpty.

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **wyrażenia** |Wymagany |wyrażenia |Wyrażenie, które ma zostać obliczone |

**Przykład:**<br>
Przełącznik (isobecny ([directmanager]), [directmanager], isobecny ([skiplevelManager]), [skiplevelManager], isobecny ([dyrektor]), [dyrektor])

---
### <a name="isstring"></a>IsString
**Funkcyjn**<br> IsString (wyrażenie)

**Zharmonizowan**<br> Jeśli wyrażenie może być szacowane do typu ciągu, funkcja IsString daje w wyniku wartość true.

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **wyrażenia** |Wymagany |wyrażenia |Wyrażenie, które ma zostać obliczone |

---
### <a name="item"></a>Element
**Funkcyjn**<br> Element (atrybut, indeks)

**Zharmonizowan**<br> Funkcja Item zwraca jeden element z wielowartościowego ciągu/atrybutu.

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **przypisane** |Wymagany |Atrybut |Wielowartościowy atrybut do przeszukania |
| **indeks** |Wymagany |Liczba całkowita | Indeksuj do elementu w ciągu wielowartościowym|

**Przykład:**<br>
Element ([proxyAddresses], 1)

---
### <a name="join"></a>Join
**Funkcyjn**<br> Dołącz do (separator, źródło1 źródło2...)

**Zharmonizowan**<br> Join () jest podobny do dołączania (), z tą różnicą, że może połączyć wiele wartości ciągu **źródłowego** w jeden ciąg, a każda wartość zostanie oddzielona przez ciąg **separatora** .

Jeśli jedna z wartości źródłowych jest atrybutem wielowartościowym, każda wartość w tym atrybucie zostanie przyłączona wspólnie, oddzielona przez wartość separatora.

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **rozdzielając** |Wymagany |Ciąg |Ciąg używany do oddzielania wartości źródła, gdy są one połączone w jeden ciąg. Może być "" Jeśli separator nie jest wymagana. |
| **source1 ... sourceN** |Wymagana zmienna — liczba razy |Ciąg |Ciąg wartości, które mają zostać połączone ze sobą. |

---
### <a name="left"></a>Lewym
**Funkcyjn**<br> Left (ciąg, NumChars)

**Zharmonizowan**<br> Funkcja Left Zwraca określoną liczbę znaków z lewej strony ciągu. Jeśli numChars = 0, zwraca pusty ciąg.
Jeśli numChars < 0, zwracany ciąg wejściowy.
Jeśli ciąg ma wartość null, zwracany jest pusty ciąg.
Jeśli ciąg zawiera mniej znaków niż liczba określona w numChars, zwracany jest ciąg identyczny jak ciąg (czyli zawierający wszystkie znaki w parametrze 1).

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **Ciąg** |Wymagany |Atrybut | Ciąg, z którego mają zostać zwrócone znaki |
| **NumChars** |Wymagany |Liczba całkowita | Liczba określająca liczbę znaków do zwrócenia od początku (po lewej) ciągu|

**Przykład:**<br>
Left ("Jan Nowak", 3)                                                            
Zwraca wartość "Joh"

---
### <a name="mid"></a>MID
**Funkcyjn**<br> MID (źródło, początek, długość)

**Zharmonizowan**<br> Zwraca podciąg wartość źródła. Podciąg jest ciąg zawierający tylko niektóre ze znaków z ciągu źródłowego.

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagany |Ciąg |Zazwyczaj nazwa atrybutu. |
| **start** |Wymagany |liczba całkowita |Indeks w ciągu **źródłowym** , w którym powinien zostać uruchomiony podciąg. Pierwszy znak w ciągu ma indeks 1, drugi znak ma indeksu 2 i tak dalej. |
| **Długość** |Wymagany |liczba całkowita |Długość podciągu. Jeśli długość kończy się poza ciągiem **źródłowym** , funkcja zwróci podciąg z **początkowego** indeksu do końca ciągu **źródłowego** . |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funkcyjn**<br> NormalizeDiacritics(source)

**Zharmonizowan**<br> Wymaga jednego argumentu ciągu. Zwraca ciąg, ale za pomocą wszystkie znaki diakrytyczne zastępowane znakami diakrytyczne równoważne. Zwykle używane do konwersji imiona i nazwiska, zawierające znaki diakrytyczne (znaki akcentu) do wartości prawne, które mogą być używane w różne identyfikatory użytkownika, takie jak nazwy głównej użytkownika, nazwy konta SAM i adresy e-mail.

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagany |Ciąg | Zwykle jest to atrybut imię i nazwisko. |

---
### <a name="not"></a>Not
**Funkcyjn**<br> Not(Source)

**Zharmonizowan**<br> Odwraca wartość logiczną **źródła**. Jeśli wartością **źródłową** jest "*true*", zwraca wartość "*false*". W przeciwnym razie zwraca wartość "*true*".

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagany |Wartości logicznych |Oczekiwane wartości **źródłowe** to "true" lub "false". |

---
### <a name="numfromdate"></a>NumFromDate
**Funkcyjn**<br> NumFromDate (wartość)

**Zharmonizowan**<br> Funkcja NumFromDate konwertuje wartość DateTime na format Active Directory, który jest wymagany do ustawiania atrybutów, takich jak [accountExpires](https://docs.microsoft.com/windows/win32/adschema/a-accountexpires). Ta funkcja umożliwia konwertowanie wartości DateTime otrzymywanych z aplikacji w chmurze, takich jak Workday i SuccessFactors, na ich równoważną reprezentację usługi AD. 

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **value** |Wymagany | Ciąg | Ciąg daty i godziny w obsługiwanym formacie. Obsługiwane formaty można znaleźć w temacie https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx. |

**Przykład:**<br>
* Przykład produktu Workday <br>
  Przy założeniu, że chcesz zmapować atrybut *ContractEndDate* z produktu Workday, który znajduje się w formacie *2020-12-31-08:00* do *accountExpires* w usłudze AD, Oto jak można użyć tej funkcji i zmienić przesunięcie strefy czasowej na zgodność z ustawieniami regionalnymi. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* Przykład SuccessFactors <br>
  Przy założeniu, że chcesz zmapować atrybut *EndDate* z SuccessFactors, który jest w formacie *M/d/rrrr hh: mm: SS TT* to *accountExpires* w usłudze AD, Oto jak można użyć tej funkcji i zmienić przesunięcie strefy czasowej zgodnie z ustawieniami regionalnymi.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates —
**Funkcyjn**<br> RemoveDuplicates — (atrybut)

**Zharmonizowan**<br> Funkcja RemoveDuplicates — przyjmuje ciąg o wartości wielowartościowej i upewnij się, że każda wartość jest unikatowa.

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **przypisane** |Wymagany |Wielowartościowy atrybut |Atrybut wielowartościowy, który zostanie usunięty duplikaty|

**Przykład:**<br>
RemoveDuplicates — ([proxyAddresses])                                                                                                       
Zwraca oczyszczony atrybut proxyAddress, w którym usunięto wszystkie zduplikowane wartości

---
### <a name="replace"></a>Replace
**Funkcyjn**<br> Zastąp (źródło oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, szablon)

**Zharmonizowan**<br>
Zamienia wartości ciągu. Działa inaczej w zależności od parametrów podanych:

* Gdy są podane **OldValue** i **replacementValue** :
  
  * Zamienia wszystkie wystąpienia wartości **OldValue** w **źródle** na **replacementValue**
* Gdy jest udostępniana **OldValue** i **szablon** :
  
  * Zamienia wszystkie wystąpienia wartości **OldValue** w **szablonie** na wartość **Source**
* Jeśli **regexPattern** i **replacementValue** są podane:

  * Funkcja stosuje **regexPattern** do ciągu **źródłowego** i można użyć nazw grup wyrażeń regularnych do konstruowania ciągu dla **replacementValue**
* Gdy **regexPattern**, **regexGroupName**, **replacementValue** są podane:
  
  * Funkcja stosuje **regexPattern** do ciągu **źródłowego** i zastępuje wszystkie wartości pasujące do **regexGroupName** **replacementValue**
* Gdy **regexPattern**, **regexGroupName**, **replacementAttributeName** są podane:
  
  * Jeśli **Źródło** nie ma wartości, zwracane jest **Źródło**
  * Jeśli **Źródło** ma wartość, funkcja stosuje **regexPattern** do ciągu **źródłowego** i zastępuje wszystkie wartości pasujące do **regexGroupName** wartością skojarzoną z **replacementAttributeName**

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagany |Ciąg |Zwykle nazwa atrybutu w obiekcie **źródłowym** . |
| **oldValue** |Optional (Opcjonalność) |Ciąg |Wartość, która ma zostać zastąpiona w **źródle** lub **szablonie**. |
| **regexPattern** |Optional (Opcjonalność) |Ciąg |Wzorzec wyrażenia regularnego dla wartości, która ma zostać zastąpiona w **źródle**. Lub, gdy **replacementPropertyName** jest używany, wzorzec wyodrębniania wartości z **replacementPropertyName**. |
| **regexGroupName** |Optional (Opcjonalność) |Ciąg |Nazwa grupy w **regexPattern**. Tylko wtedy, gdy **replacementPropertyName** jest używany, wyodrębnimy wartość tej grupy jako **replacementValue** z **replacementPropertyName**. |
| **replacementValue** |Optional (Opcjonalność) |Ciąg |Nowa wartość, aby zastąpić stary certyfikat za pomocą. |
| **replacementAttributeName** |Optional (Opcjonalność) |Ciąg |Nazwa atrybutu, który ma być używany na potrzeby wartości zamiennej |
| **formularza** |Optional (Opcjonalność) |Ciąg |Gdy zostanie podana wartość **szablonu** , poszukamy wartości **OldValue** wewnątrz szablonu i Zastąp ją wartością **Source** . |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Funkcyjn**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2 uniqueValueRule3...)

**Zharmonizowan**<br> Wymaga co najmniej dwa argumenty, które są definiowane przy użyciu wyrażeń zasad generowania unikatową wartość. Funkcja ocenia każdą regułę, a następnie sprawdza wartość generowane unikatowość w katalogu/aplikacji docelowej. Pierwszy unikatową wartość znalezione, zostanie zwrócony jeden. Jeśli wszystkie wartości już istnieje w docelowej, wpis będzie pobrać zdeponowane i przyczynę pobiera rejestrowane w dziennikach inspekcji. Nie ma żadnych górnej granicy liczby argumentów, które mogą być podane.

> [!NOTE]
> - To jest funkcja najwyższego poziomu, nie mogą być zagnieżdżone.
> - Nie można zastosować tej funkcji do atrybutów, które mają pasujące pierwszeństwo.  
> - Ta funkcja jest przeznaczone tylko do użytku z Tworzenie wpisu. Gdy jest używany z atrybutem, ustaw właściwość **Zastosuj mapowanie** na **tylko podczas tworzenia obiektu**.
> - Ta funkcja jest obecnie obsługiwana tylko w przypadku "Workday, Active Directory aprowizacji użytkowników". Nie można jej używać z innymi aplikacjami aprowizacji. 


**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **uniqueValueRule1 ... uniqueValueRuleN** |Co najmniej 2 są wymagane, nie górnej granicy |Ciąg | Lista unikatowych reguł generowania wartości do obliczenia. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funkcyjn**<br> SingleAppRoleAssignment([appRoleAssignments])

**Zharmonizowan**<br> Zwraca pojedynczy appRoleAssignment z listy wszystkich appRoleAssignments przypisanych do użytkownika dla danej aplikacji. Ta funkcja jest wymagana do przekonwertowania obiektu appRoleAssignments na ciąg o pojedynczej nazwie roli. Należy pamiętać, że najlepszym rozwiązaniem jest upewnienie się, że tylko jedna appRoleAssignment jest przypisana do jednego użytkownika w danym momencie i Jeśli przypiszesz wiele ról, zwracany ciąg roli może nie być przewidywalny. 

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **AppRoleAssignments** |Wymagany |Ciąg |**[appRoleAssignments]** obiekt. |

---
### <a name="split"></a>Podział
**Funkcyjn**<br> Split (Źródło, ogranicznik)

**Zharmonizowan**<br> Dzieli ciąg na tablicę wielowartościową przy użyciu określonego znaku ogranicznika.

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagany |Ciąg |wartość **źródłowa** do zaktualizowania. |
| **ogranicznik** |Wymagany |Ciąg |Określa znak, który będzie używany do dzielenia ciągu (przykład: ",") |

---
### <a name="stripspaces"></a>StripSpaces
**Funkcyjn**<br> StripSpaces(source)

**Zharmonizowan**<br> Usuwa wszystkie spacje ("") znaków z ciągu źródłowego.

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagany |Ciąg |wartość **źródłowa** do zaktualizowania. |

---
### <a name="switch"></a>Przełącznik
**Funkcyjn**<br> Przełącznik (źródło, defaultValue, klucz1, wartość1, klucz2, wartość2,...)

**Zharmonizowan**<br> Gdy wartość **źródłowa** jest zgodna z **kluczem**, zwraca **wartość** dla tego **klucza**. Jeśli wartość **źródłowa** nie jest zgodna z żadnymi kluczami, zwraca wartość **DefaultValue**.  Parametry **klucza** i **wartości** muszą zawsze znajdować się w parach. Funkcja zawsze oczekuje parzystą liczbą parametrów. Funkcja nie powinna być używana dla atrybutów referencyjnych, takich jak Menedżer. 

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagany |Ciąg |Wartość **źródłowa** do zaktualizowania. |
| **defaultValue** |Optional (Opcjonalność) |Ciąg |Wartość domyślna ma być używany, gdy źródło nie jest zgodna żadnych kluczy. Może być pustym ciągiem (""). |
| **Klucz** |Wymagany |Ciąg |**Klucz** do porównywania wartości **źródłowej** z. |
| **value** |Wymagany |Ciąg |Wartość zastępcza dla **źródła** pasującego do klucza. |

---
### <a name="tolower"></a>toLower
**Funkcyjn**<br> ToLower (Źródło, kultura)

**Zharmonizowan**<br> Pobiera wartość ciągu *źródłowego* i konwertuje ją na małe litery przy użyciu określonych reguł kultury. Jeśli nie określono informacji o *kulturze* , będzie ona używać niezmiennej kultury.

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagany |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego |
| **dziedzinie** |Optional (Opcjonalność) |Ciąg |Format nazwy kultury opartej na dokumencie RFC 4646 to *languagecode2-Country/regioncode2*, gdzie *languagecode2* to kod języka dwuliterowego i *kraj/regioncode2* to kod podkultury dwuliterowej. Przykłady obejmują ja-JP dla języka japońskiego (Japonia) i EN-US dla języka angielskiego (Stany Zjednoczone). W przypadkach, gdy kod języka dwuliterowego nie jest dostępny, używany jest trzyliterowy kod pochodzący z normy ISO 639-2.|

---
### <a name="toupper"></a>ToUpper
**Funkcyjn**<br> ToUpper (Źródło, kultura)

**Zharmonizowan**<br> Pobiera wartość ciągu *źródłowego* i konwertuje ją na wielkie litery przy użyciu określonych reguł kultury. Jeśli nie określono informacji o *kulturze* , będzie ona używać niezmiennej kultury.

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **zewnętrz** |Wymagany |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego. |
| **dziedzinie** |Optional (Opcjonalność) |Ciąg |Format nazwy kultury opartej na dokumencie RFC 4646 to *languagecode2-Country/regioncode2*, gdzie *languagecode2* to kod języka dwuliterowego i *kraj/regioncode2* to kod podkultury dwuliterowej. Przykłady obejmują ja-JP dla języka japońskiego (Japonia) i EN-US dla języka angielskiego (Stany Zjednoczone). W przypadkach, gdy kod języka dwuliterowego nie jest dostępny, używany jest trzyliterowy kod pochodzący z normy ISO 639-2.|

---
### <a name="word"></a>Word
**Funkcyjn**<br> Słowo (String, WordNumber, ograniczniki)

**Zharmonizowan**<br> Funkcja słowa zwraca słowo zawarte w ciągu, w oparciu o parametry opisujące ograniczniki do użycia i numer wyrazu do zwrócenia. Każdy ciąg znaków w ciągu rozdzielony przez jeden ze znaków w ogranicznikach jest identyfikowany jako wyrazy:

Jeśli liczba < 1, zwraca pusty ciąg.
Jeśli ciąg ma wartość null, zwraca pusty ciąg.
Jeśli ciąg zawiera mniej niż liczbowe słowa lub ciąg nie zawiera słów identyfikowanych przez ograniczniki, zwracany jest pusty ciąg.

**Wejściowe**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **Ciąg** |Wymagany |Wielowartościowy atrybut |Ciąg, z którego ma zostać zwrócony wyraz.|
| **WordNumber** |Wymagany | Liczba całkowita | Numer identyfikacyjny, który ma zwracać numer wyrazu|
| **Ograniczniki** |Wymagany |Ciąg| Ciąg reprezentujący ograniczniki, które powinny być używane do identyfikowania wyrazów|

**Przykład:**<br>
Word ("Quick Brown Fox", 3, "")                                                                                       
Zwraca "brązowy"

Słowo ("this, String! & wiele separatorów", 3, ",! & #")                                                                       
Zwraca "ma"

---

## <a name="examples"></a>Przykłady
### <a name="strip-known-domain-name"></a>Nazwa domeny znanych paska
Musisz usunąć nazwę domeny znane z wiadomości e-mail użytkownika, aby uzyskać nazwę użytkownika. <br>
Na przykład jeśli domena "contoso.com", następnie można użyć następującego wyrażenia:

**Wyrażenia** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Dane wejściowe** (poczta): "john.doe@contoso.com"
* **Wynik**: "Jan. Nowak"

### <a name="append-constant-suffix-to-user-name"></a>Dołącz stałej sufiks do nazw użytkowników
Jeśli używasz piaskownicy usługi Salesforce, może być konieczne dołączyć dodatkowe sufiks do nazw użytkowników przed ich zsynchronizowaniem.

**Wyrażenia** <br>
`Append([userPrincipalName], ".test")`

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Wejście**: (userPrincipalName): "John.Doe@contoso.com"
* **Dane wyjściowe**: "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generowanie alias użytkownika przez złączenie części imię i nazwisko
Należy wygenerować użytkownika aliasu, wykonując pierwsze 3 litery imienia użytkownika i 5 pierwszych liter nazwisko użytkownika.

**Wyrażenia** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Dane wejściowe** (imię): "Jan"
* **Dane wejściowe** (nazwisko): "Nowak"
* **Dane wyjściowe**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Usuń znaki diakrytyczne z ciągu
Należy zastąpić znaki zawierające znaki akcentu równoważne znaki, które nie zawierają znaki akcentu.

**Wyrażenia** <br>
NormalizeDiacritics([givenName])

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Dane wejściowe** (o podanej): "Zoë"
* **Dane wyjściowe**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Dzielenie ciągu na tablicę wielowartościową
Należy wykonać rozdzielaną przecinkami listę ciągów i podzielić je na tablicę, która może być podłączona do atrybutu wielowartościowego, takiego jak PermissionSets. W tym przykładzie lista zestawów uprawnień została wypełniona w extensionAttribute5 w usłudze Azure AD.

**Wyrażenia** <br>
Split ([extensionAttribute5], ",")

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Input** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **Output**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Dane wyjściowe daty w postaci ciągu w określonym formacie
Chcesz wysłać daty do aplikacji SaaS w określonym formacie. <br>
Na przykład chcesz formatować daty dla usługi ServiceNow.

**Wyrażenia** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Przykładowe dane wejściowe/wyjściowe:**

* **Wejście** (extensionAttribute1): "20150123105347.1 z"
* **Dane wyjściowe**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Zastąp wartość, na podstawie zestawu wstępnie zdefiniowanych opcji

Musisz zdefiniować strefy czasowej użytkownika, na podstawie kodu stanu, przechowywane w usłudze Azure AD. <br>
Jeśli kod stanu nie odpowiada żadnemu z wstępnie zdefiniowanych opcji, należy użyć wartości domyślnej "Australia/Sydney".

**Wyrażenia** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Przykładowe dane wejściowe/wyjściowe:**

* **Wejście** (stan): "Qld"
* **Dane wyjściowe**: "Australia/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Zamienianie znaków przy użyciu wyrażenia regularnego
Musisz znaleźć znaki, które pasują do wartości wyrażenia regularnego, i usunąć je.

**Wyrażenia** <br>

Replace ([mailNickname],, "[a-za-Z_] *",, "",,)

**Przykładowe dane wejściowe/wyjściowe:**

* **Dane wejściowe** (mailNickname: "john_doe72"
* **Dane wyjściowe**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Konwertuj wygenerowaną wartość userPrincipalName (UPN) na małe litery
W poniższym przykładzie wartość UPN jest generowana przez połączenie pól źródłowych PreferredFirstName i PreferredLastName, a funkcja ToLower działa na wygenerowanym ciągu, aby przekonwertować wszystkie znaki na małe litery. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Przykładowe dane wejściowe/wyjściowe:**

* **Wejście** (PreferredFirstName): "Jan"
* **Wejście** (PreferredLastName): "Smith"
* **Dane wyjściowe**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Generowanie unikatową wartość dla atrybutu userPrincipalName (UPN)
Oparte na użytkownika imię, drugie imię i nazwisko, należy do generowania wartości atrybutu nazwy UPN i sprawdzić jego unikatowości w katalogu docelowym AD przed przypisaniem wartości do atrybutu nazwy UPN.

**Wyrażenia** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Przykładowe dane wejściowe/wyjściowe:**

* **Wejście** (PreferredFirstName): "Jan"
* **Wejście** (PreferredLastName): "Smith"
* **Wynik**: "John.Smith@contoso.com", jeśli wartość UPN John.Smith@contoso.com nie istnieje już w katalogu
* **Wynik**: "J.Smith@contoso.com", jeśli wartość UPN John.Smith@contoso.com już istnieje w katalogu
* **Wynik**: "Jo.Smith@contoso.com", jeśli powyższe dwie wartości nazwy UPN znajdują się już w katalogu

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>Wartość poczty w przepływie, jeśli nie ma wartości NULL, w przeciwnym wypadku Flow
Jeśli ten atrybut jest obecny, należy go przepływać. Jeśli tak nie jest, chcesz zamiast tego przepływać wartość userPrincipalName.

**Wyrażenia** <br>
`Coalesce([mail],[userPrincipalName])`

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Dane wejściowe** (poczta): null
* **Dane wejściowe** (userPrincipalName): "John.Doe@contoso.com"
* **Dane wyjściowe**: "John.Doe@contoso.com"

## <a name="related-articles"></a>Powiązane artykuły
* [Automatyzacja aprowizacji użytkowników/anulowania obsługi administracyjnej w aplikacjach SaaS](../app-provisioning/user-provisioning.md)
* [Dostosowywanie mapowań atrybutów na potrzeby aprowizacji użytkowników](../app-provisioning/customize-application-attributes.md)
* [Filtry zakresu dla aprowizacji użytkowników](define-conditional-rules-for-provisioning-user-accounts.md)
* [Włączanie automatycznej aprowizacji użytkowników i grup z usługi Azure Active Directory do aplikacji przy użyciu SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Powiadomienia o aprowizacji konta](../app-provisioning/user-provisioning.md)
* [Lista samouczków dotyczących integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)
