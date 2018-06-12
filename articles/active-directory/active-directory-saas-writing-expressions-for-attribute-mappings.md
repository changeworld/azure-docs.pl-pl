---
title: Tworzenie wyrażeń na potrzeby mapowań atrybutów w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać wyrażenia mapowania do przekształcania dozwolonego formatu wartości atrybutów podczas automatycznego inicjowania obsługi obiektów aplikacji SaaS w usłudze Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: b13c51cd-1bea-4e5e-9791-5d951a518943
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.openlocfilehash: 06fd2f3ef4a17c5626afc95ed8ae5999778ebda6
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293164"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Tworzenie wyrażeń na potrzeby mapowań atrybutów w usłudze Azure Active Directory
Po skonfigurowaniu udostępniania do aplikacji SaaS, jest jeden z typów mapowań atrybutów, które można określić mapowanie wyrażenia. W tym przypadku należy napisać wyrażenie przypominającej skryptu, które pozwala na przekształcanie danych użytkowników do formatów, które są bardziej dozwolone dla aplikacji SaaS.

## <a name="syntax-overview"></a>Omówienie składni
Składnia wyrażeń dla mapowań atrybutów jest przypominający Visual Basic dla funkcji aplikacji (VBA).

* Całe wyrażenie musi być zdefiniowana w zakresie funkcji, które składają się z nazwy, a następnie argumenty w nawiasach: <br>
  *FunctionName (<< argumentu 1 >> <<argument N>>)*
* Może być zagnieżdżony funkcje w sobie. Na przykład: <br> *FunctionOne(FunctionTwo(<<argument1>>))*
* Trzy różne typy argumentów można przekazać do funkcji:
  
  1. Atrybuty, które muszą być ujęte w nawiasy kwadratowe kwadratowych. Na przykład: [attributeName]
  2. Stałe typu String, które muszą być ujęte w cudzysłów. Na przykład: "Stanów Zjednoczonych"
  3. Inne funkcje. Na przykład: FunctionOne (<<argument1>>, FunctionTwo (<<argument2>>))
* Dla stałe typu string Jeśli potrzebujesz ukośnik odwrotny (\) lub cudzysłowu (") w ciągu go należy użyć znaków ucieczki ze znakiem ukośnika odwrotnego (\\). Na przykład: "Nazwa firmy: \"Contoso\""

## <a name="list-of-functions"></a>Lista funkcji
[Dołącz](#append) &nbsp; &nbsp; &nbsp; &nbsp; [FormatDateTime](#formatdatetime) &nbsp; &nbsp; &nbsp; &nbsp; [Join](#join) &nbsp; &nbsp; &nbsp; &nbsp; [Mid](#mid) &nbsp; &nbsp; &nbsp; &nbsp; [nie](#not) &nbsp; &nbsp; &nbsp; &nbsp; [Zastąp](#replace) &nbsp; &nbsp; &nbsp; &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp; &nbsp; [StripSpaces](#stripspaces) &nbsp; &nbsp; &nbsp; &nbsp; [przełącznika](#switch)

- - -
### <a name="append"></a>Append
**Funkcja:**<br> Append(source, suffix)

**Opis:**<br> Przyjmuje wartość ciągu źródła i dołącza sufiks na końcu.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzanej | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego |
| **suffix** |Wymagane |Ciąg |Ciąg, który ma być dołączany na końcu wartość źródła. |

- - -
### <a name="formatdatetime"></a>FormatDateTime
**Funkcja:**<br> FormatDateTime (źródło, inputFormat outputFormat)

**Opis:**<br> Pobiera ciąg daty z jednego formatu i konwertuje ją na innym formacie.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzanej | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Ciąg |Zazwyczaj nazwa atrybutu z obiektem źródłowym. |
| **inputFormat** |Wymagane |Ciąg |Oczekiwany format wartości źródłowej. Dla obsługiwanych formatów, zobacz [ http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx ](http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Wymagane |Ciąg |Format wyjściowej daty. |

- - -
### <a name="join"></a>Join
**Funkcja:**<br> Dołącz (separatora, źródło1, źródło2,...)

**Opis:**<br> JOIN() przypomina Append(), z wyjątkiem tego, czy można połączyć wiele **źródła** wartości ciągu w jeden ciąg i każdej wartości będą oddzielone **separatora** ciągu.

Jeśli jedna z wartości źródła jest atrybutu wielowartościowego, każda wartość w ten atrybut zostanie połączone razem, oddzielonych wartość separatora.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzanej | Typ | Uwagi |
| --- | --- | --- | --- |
| **Separator** |Wymagane |Ciąg |Ciąg używany do rozdzielania wartości źródła, gdy są one połączone w jeden ciąg. Może być "" Jeśli separator nie jest wymagane. |
| ** źródło1... źródłoN ** |Wymagana zmienna — liczba |Ciąg |Ciąg wartości, które mają zostać połączone ze sobą. |

- - -
### <a name="mid"></a>MID
**Funkcja:**<br> Środek (źródło, początek, długość)

**Opis:**<br> Zwraca podciąg wartość źródła. Ciąg jest ciąg znaków zawierający tylko niektórych znaków z ciągu źródła.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzanej | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Ciąg |Zazwyczaj nazwa atrybutu. |
| **start** |Wymagane |liczba całkowita |Indeks w **źródła** ciąg, gdzie powinna zaczynać się podciąg. Pierwszy znak w ciągu ma indeks równy 1, drugi ma indeks 2 i tak dalej. |
| **długość** |Wymagane |liczba całkowita |Długość podciąg. Jeśli długość kończy się poza **źródła** ciągu, funkcja zwraca podciąg z **start** indeksu do końca **źródła** ciągu. |

- - -
### <a name="not"></a>nie
**Funkcja:**<br> Not(Source)

**Opis:**<br> Odwraca wartość logiczną **źródła**. Jeśli **źródła** wartość to "*True*", zwraca "*False*". W przeciwnym razie zwraca wartość "*True*".

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzanej | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Wartości logicznych |Oczekiwano **źródła** wartości to "True" lub "False". |

- - -
### <a name="replace"></a>Replace
**Funkcja:**<br> Zastąp (źródła, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, szablon)

**Opis:**<br>
Zamienia wartości ciągu. Działa inaczej w zależności od parametry podane:

* Gdy **oldValue** i **replacementValue** są udostępniane:
  
  * Zamienia wszystkie wystąpienia oldValue w źródle replacementValue
* Gdy **oldValue** i **szablonu** są udostępniane:
  
  * Zamienia wszystkie wystąpienia **oldValue** w **szablonu** z **źródła** wartość
* Gdy **regexPattern**, **regexGroupName**, **replacementValue** są udostępniane:
  
  * Zamienia wszystkie wartości dopasowania oldValueRegexPattern w ciągu z replacementValue ze źródłem
* Gdy **regexPattern**, **regexGroupName**, **replacementPropertyName** są udostępniane:
  
  * Jeśli **źródła** nie ma wartości, **źródła** jest zwracana
  * Jeśli **źródła** ma wartość, używa **regexPattern** i **regexGroupName** można wyodrębnić wartość zastępcza z właściwości z **replacementPropertyName** . Zastąpienie wartości jest zwracana w wyniku

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzanej | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Ciąg |Zazwyczaj nazwa atrybutu z obiektem źródłowym. |
| **oldValue** |Optional (Opcjonalność) |Ciąg |Wartość, które mają zostać zastąpione w **źródła** lub **szablonu**. |
| **regexPattern** |Optional (Opcjonalność) |Ciąg |Wzorzec wyrażenia regularnego dla wartości, które mają zostać zastąpione w **źródła**. Lub, w przypadku replacementPropertyName wzorzec do wyodrębniania wartości z właściwości zastąpienia. |
| **regexGroupName** |Optional (Opcjonalność) |Ciąg |Nazwa grupy wewnątrz **regexPattern**. Tylko wtedy, gdy jest używana replacementPropertyName, firma Microsoft będzie Wyodrębnij wartości tej grupy jako replacementValue z właściwości zastąpienia. |
| **replacementValue** |Optional (Opcjonalność) |Ciąg |Nowa wartość Aby zastąpić stary z. |
| **replacementAttributeName** |Optional (Opcjonalność) |Ciąg |Nazwa atrybutu do użycia podczas wartość zastępcza źródło nie ma wartości. |
| **Szablon** |Optional (Opcjonalność) |Ciąg |Gdy **szablonu** wartość zostanie podana, firma Microsoft będzie szukać **oldValue** wewnątrz szablonu i zamień ją na wartość źródła. |

- - -
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funkcja:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Opis:**<br> Zwraca pojedynczy appRoleAssignment z listy wszystkich appRoleAssignments przypisana do użytkownika dla danej aplikacji. Ta funkcja jest wymagana do konwertowania appRoleAssignments obiektu na ciąg nazwy jedną rolę. Należy pamiętać, że najlepszym rozwiązaniem jest zapewnienie appRoleAssignment tylko jeden jest przypisany do jednego użytkownika w czasie, a jeśli wiele ról są przypisane zwrócony ciąg roli może nie być przewidywalne.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzanej | Typ | Uwagi |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Wymagane |Ciąg |**[appRoleAssignments]**  obiektu. |

- - -
### <a name="stripspaces"></a>StripSpaces
**Funkcja:**<br> StripSpaces(source)

**Opis:**<br> Usuwa wszystkie spacje ("") znaków z ciągu źródła.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzanej | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Ciąg |**Źródło** wartość do aktualizacji. |

- - -
### <a name="switch"></a>Przełącznik
**Funkcja:**<br> Przełącznik (źródła, defaultValue, key1, wartość1, key2, wartość2,...)

**Opis:**<br> Gdy **źródła** wartość dopasowań **klucza**, zwraca **wartość** tego **klucza**. Jeśli **źródła** wartość nie jest zgodna klucze zwraca **defaultValue**.  **Klucz** i **wartość** parametrów musi zawsze występować w parach. Funkcja oczekuje zawsze parzystą liczbą parametrów.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzanej | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Ciąg |**Źródło** wartość do aktualizacji. |
| **Wartość domyślna** |Optional (Opcjonalność) |Ciąg |Wartość domyślna ma być używany podczas źródłowym nie odpowiada żadnych kluczy. Może być pustym ciągiem (""). |
| **Klucz** |Wymagane |Ciąg |**Klucz** do porównania **źródła** wartości z. |
| **value** |Wymagane |Ciąg |Wartość zastąpienia **źródła** pasujących do klucza. |

## <a name="examples"></a>Przykłady
### <a name="strip-known-domain-name"></a>Nazwa domeny znane taśmy
Należy usunąć nazwy domeny znane z poczty e-mail użytkownika można uzyskać nazwy użytkownika. <br>
Na przykład jeśli domena "contoso.com", następnie można użyć następującego wyrażenia:

**Wyrażenie:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Przykładowe dane wejściowe / wyjściowe:** <br>

* **Dane wejściowe** (poczta): "john.doe@contoso.com"
* **Dane wyjściowe**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>Dołącz sufiks stałej nazwie użytkownika
Jeśli używasz piaskownicy Salesforce, może być konieczne Dołącz dodatkowe sufiks nazwy użytkownika przed synchronizacją je.

**Wyrażenie:** <br>
`Append([userPrincipalName], ".test"))`

**Próba wejścia/wyjścia:** <br>

* **Dane wejściowe**: (userPrincipalName): "John.Doe@contoso.com"
* **DANE WYJŚCIOWE**: "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generowanie alias użytkownika przez łączenie części imię i nazwisko
Należy wygenerować użytkownika alias przez pierwsze 3 litery imię użytkownika i 5 pierwszych liter nazwiska użytkownika.

**Wyrażenie:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Próba wejścia/wyjścia:** <br>

* **Dane wejściowe** (imię): "Jan"
* **Dane wejściowe** (nazwisko): "Nowak"
* **Dane wyjściowe**: "JohDoe"

### <a name="remove-diacritics-from-a-string-and-convert-to-lowercase"></a>Usuń z ciągu znaków diakrytycznych i przekonwertować na małe litery.
Należy usunąć znaki specjalne z ciągu i przekonwertować wielkich liter na małe litery.

**Wyrażenie:** <br>
`Replace(Replace(Replace(Replace(Replace(Replace(Replace( Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace( Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace([givenName], , "([Øø])", , "oe", , ), , "[Ææ]", , "ae", , ), , "([äãàâãåáąÄÃÀÂÃÅÁĄA])", , "a", , ), , "([B])", , "b", , ), , "([CçčćÇČĆ])", , "c", , ), , "([ďĎD])", , "d", , ), , "([ëèéêęěËÈÉÊĘĚE])", , "e", , ), , "([F])", , "f", , ), , "([G])", , "g", , ), , "([H])", , "h", , ), , "([ïîìíÏÎÌÍI])", , "i", , ), , "([J])", , "j", , ), , "([K])", , "k", , ), , "([ľłŁĽL])", , "l", , ), , "([M])", , "m", , ), , "([ñńňÑŃŇN])", , "n", , ), , "([öòőõôóÖÒŐÕÔÓO])", , "o", , ), , "([P])", , "p", , ), , "([Q])", , "q", , ), , "([řŘR])", , "r", , ), , "([ßšśŠŚS])", , "s", , ), , "([TŤť])", , "t", , ), , "([üùûúůűÜÙÛÚŮŰU])", , "u", , ), , "([V])", , "v", , ), , "([W])", , "w", , ), , "([ýÿýŸÝY])", , "y", , ), , "([źžżŹŽŻZ])", , "z", , ), " ", , , "", , )`

**Próba wejścia/wyjścia:** <br>

* **Dane wejściowe** (imię): "Zoë"
* **Dane wyjściowe**: "zoe"

### <a name="output-date-as-a-string-in-a-certain-format"></a>Dane wyjściowe daty w postaci ciągu w określonym formacie
Chcesz wysłać daty do aplikacji SaaS w określonym formacie. <br>
Na przykład które chcesz sformatować dat dla usługi ServiceNow.

**Wyrażenie:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Próba wejścia/wyjścia:**

* **Dane wejściowe** (extensionAttribute1): "20150123105347.1Z"
* **DANE WYJŚCIOWE**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Zastąp wartość na podstawie zestawu wstępnie zdefiniowanych opcji
Należy określić strefę czasową użytkownika na podstawie kodu stanu przechowywane w usłudze Azure AD. <br>
Jeśli kod stanu nie odpowiada żadnemu z wstępnie zdefiniowanych opcji, należy użyć wartości domyślnej "Australii/Sydney".

**Wyrażenie:** <br>

`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Próba wejścia/wyjścia:**

* **Dane wejściowe** (stan): "QLD"
* **Dane wyjściowe**: "Australii/Brisbane"

## <a name="related-articles"></a>Pokrewne artykuły
* [Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](active-directory-apps-index.md)
* [Automatyzowanie użytkownika udostępniania/anulowania obsługi do aplikacji SaaS](active-directory-saas-app-provisioning.md)
* [Dostosowywanie mapowań atrybutów do inicjowania obsługi użytkowników](active-directory-saas-customizing-attribute-mappings.md)
* [Filtry zakresu dla Inicjowanie obsługi użytkowników](active-directory-saas-scoping-filters.md)
* [Włączanie automatycznej aprowizacji użytkowników i grup z usługi Azure Active Directory do aplikacji przy użyciu SCIM](manage-apps/use-scim-to-provision-users-and-groups.md)
* [Powiadomienia aprowizacji kont](active-directory-saas-account-provisioning-notifications.md)
* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS](active-directory-saas-tutorial-list.md)

