---
title: Pisanie wyrażeń do mapowania atrybutów w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać mapowań wyrażenia do przekształcania wartości atrybutów akceptowalny format podczas automatycznego inicjowania obsługi obiektów aplikacji SaaS w usłudze Azure Active Directory.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: barbkess
ms.openlocfilehash: cf0e94b859b49eddb8e2471d5319b3cc4a2c17ba
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44720297"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Pisanie wyrażeń do mapowania atrybutów w usłudze Azure Active Directory
Podczas konfigurowania, inicjowania obsługi administracyjnej aplikacji SaaS, jest jeden z typów mapowania atrybutów, które można określić mapowanie wyrażenia. W tym przypadku trzeba napisać wyrażenia podobne do skryptu, która pozwala na przekształcanie danych użytkowników w formatach, które są bardziej akceptowalne dla aplikacji SaaS.

## <a name="syntax-overview"></a>Omówienie składni
Składnia wyrażeń do mapowania atrybutów jest przypominający języka Visual Basic for Applications (VBA) funkcje.

* Całe wyrażenie musi być zdefiniowany w zakresie funkcji, które składają się z nazwy argumentów w nawiasach: <br>
  *FunctionName (<< argumentu 1 >> <<argument N>>)*
* Może być zagnieżdżony funkcji w ramach siebie nawzajem. Na przykład: <br> *FunctionOne(FunctionTwo(<<argument1>>))*
* Trzy różne rodzaje argumenty można przekazać do funkcji:
  
  1. Atrybuty, które muszą być ujęte w nawiasy kwadratowe. Na przykład: [attributeName]
  2. Stałe typu String, które muszą być ujęte w cudzysłów. Na przykład: "United States"
  3. Inne funkcje. Na przykład: FunctionOne (<<argument1>>, FunctionTwo (<<argument2>>))
* Dla stałych ciągów Jeśli potrzebujesz kreski ułamkowej odwróconej (\) lub cudzysłowu (") w ciągu go należy użyć znaków ucieczki symbolem kreski ułamkowej odwróconej (\). Na przykład: "Nazwa firmy: \"Contoso\""

## <a name="list-of-functions"></a>Lista funkcji
[Dołącz](#append) &nbsp; &nbsp; &nbsp; &nbsp; [FormatDateTime](#formatdatetime) &nbsp; &nbsp; &nbsp; &nbsp; [Dołącz](#join) &nbsp; &nbsp; &nbsp; &nbsp; [Mid](#mid) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [NormalizeDiacritics](#normalizediacritics) [nie](#not) &nbsp; &nbsp; &nbsp; &nbsp; [Zastąp](#replace) &nbsp; &nbsp; &nbsp; &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp; &nbsp; [StripSpaces](#stripspaces) &nbsp; &nbsp; &nbsp; &nbsp; [Przełącznika](#switch)

- - -
### <a name="append"></a>Append
**Funkcja:**<br> Append(source, suffix)

**Opis:**<br> Przyjmuje wartość ciągu źródła i dołącza sufiks na końcu.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego |
| **suffix** |Wymagane |Ciąg |Ciąg, który chcesz dołączyć do końca wartość źródła. |

- - -
### <a name="formatdatetime"></a>FormatDateTime
**Funkcja:**<br> FormatDateTime (źródło, inputFormat outputFormat)

**Opis:**<br> Pobiera ciąg daty z jednego formatu i konwertuje je do innego formatu.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego. |
| **inputFormat** |Wymagane |Ciąg |Oczekiwany format wartość źródła. Aby uzyskać obsługiwanych formatów, zobacz [ http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx ](http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Wymagane |Ciąg |Format wyjściowej daty. |

- - -
### <a name="join"></a>Join
**Funkcja:**<br> Dołącz do (separator, źródło1 źródło2...)

**Opis:**<br> JOIN() przypomina Append(), z tą różnicą, że można łączyć w wielu **źródła** ciąg wartości w pojedynczy ciąg, a każda wartość będzie rozdzielone **separator** ciągu.

Jeśli jedna z wartości źródła jest atrybutu wielowartościowego, każda wartość w tego atrybutu zostaną połączone razem, oddzielonych wartości separatora.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **Separator** |Wymagane |Ciąg |Ciąg używany do oddzielania wartości źródła, gdy są one połączone w jeden ciąg. Może być "" Jeśli separator nie jest wymagana. |
| ** źródło1... źródłoN ** |Wymagana zmienna — liczba razy |Ciąg |Ciąg wartości, które mają zostać połączone ze sobą. |

- - -
### <a name="mid"></a>MID
**Funkcja:**<br> MID (źródło, początek, długość)

**Opis:**<br> Zwraca podciąg wartość źródła. Podciąg jest ciąg zawierający tylko niektóre ze znaków z ciągu źródłowego.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Ciąg |Zazwyczaj nazwa atrybutu. |
| **start** |Wymagane |liczba całkowita |Indeks w **źródła** ciągu, gdzie ma się rozpocząć podciąg. Pierwszy znak w ciągu ma indeks 1, drugi znak ma indeksu 2 i tak dalej. |
| **Długość** |Wymagane |liczba całkowita |Długość podciągu. Jeśli długość kończy się poza **źródła** ciągu, funkcja zwraca podciąg z **start** indeksu do końca **źródła** ciągu. |

- - -
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funkcja:**<br> NormalizeDiacritics(source)

**Opis:**<br> Wymaga jednego argumentu ciągu. Zwraca ciąg, ale za pomocą wszystkie znaki diakrytyczne zastępowane znakami diakrytyczne równoważne. Zwykle używane do konwersji imiona i nazwiska, zawierające znaki diakrytyczne (znaki akcentu) do wartości prawne, które mogą być używane w różne identyfikatory użytkownika, takie jak nazwy głównej użytkownika, nazwy konta SAM i adresy e-mail.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Ciąg | Zazwyczaj nazwę pierwszego lub ostatniego atrybutu nazwy |

- - -
### <a name="not"></a>nie
**Funkcja:**<br> Not(Source)

**Opis:**<br> Odwraca wartość logiczną **źródła**. Jeśli **źródła** wartość to "*True*", zwraca "*False*". W przeciwnym razie zwraca "*True*".

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Wartości logicznych |Oczekiwano **źródła** wartości to "True" lub "False"... |

- - -
### <a name="replace"></a>Replace
**Funkcja:**<br> Zastąp (źródło oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, szablon)

**Opis:**<br>
Zamienia wartości ciągu. Działa inaczej w zależności od parametrów podanych:

* Gdy **oldValue** i **replacementValue** znajdują się:
  
  * Zamienia wszystkie wystąpienia oldValue w źródle replacementValue
* Gdy **oldValue** i **szablonu** znajdują się:
  
  * Zamienia wszystkie wystąpienia **oldValue** w **szablonu** z **źródła** wartość
* Gdy **regexPattern**, **regexGroupName**, **replacementValue** znajdują się:
  
  * Zamienia wartości wszystkie dopasowania oldValueRegexPattern w ciągu źródłowym z replacementValue
* Gdy **regexPattern**, **regexGroupName**, **replacementPropertyName** znajdują się:
  
  * Jeśli **źródła** nie ma wartości, **źródła** zwróceniem
  * Jeśli **źródła** ma wartość, używa **regexPattern** i **regexGroupName** można wyodrębnić wartość zastąpienia z właściwości o **replacementPropertyName** . Wartość zastąpienia jest zwracana w wyniku

**Parametry:**<br> 
| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego. |
| **oldValue** |Optional (Opcjonalność) |Ciąg |Wartość ma zostać zastąpione w **źródła** lub **szablonu**. |
| **regexPattern** |Optional (Opcjonalność) |Ciąg |Wzorzec wyrażenia regularnego dla wartości, które ma zostać zastąpione w **źródła**. Lub, w przypadku replacementPropertyName wzorzec do wyodrębnienia wartości z właściwości zastąpienia. |
| **regexGroupName** |Optional (Opcjonalność) |Ciąg |Nazwa grupy wewnątrz **regexPattern**. Tylko wtedy, gdy jest używana replacementPropertyName, wyodrębnimy wartość tej grupy jako replacementValue z właściwości zastąpienia. |
| **replacementValue** |Optional (Opcjonalność) |Ciąg |Nowa wartość, aby zastąpić stary certyfikat za pomocą. |
| **replacementAttributeName** |Optional (Opcjonalność) |Ciąg |Nazwa atrybutu używanego do wartość zastąpienia, gdy źródło nie ma wartości. |
| **Szablon** |Optional (Opcjonalność) |Ciąg |Gdy **szablonu** wartość zostanie podana, firma Microsoft będzie szukał **oldValue** wewnątrz szablonu i zastąp go wartością źródłową. |

- - -
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funkcja:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Opis:**<br> Wymaga jednego argumentu ciągu. Zwraca ciąg, ale z dowolnego repalced znaków diakrytycznych znakami diakrytyczne równoważne.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Wymagane |Ciąg |**[appRoleAssignments]**  obiektu. |

- - -
### <a name="stripspaces"></a>StripSpaces
**Funkcja:**<br> StripSpaces(source)

**Opis:**<br> Usuwa wszystkie spacje ("") znaków z ciągu źródłowego.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Ciąg |**źródło** wartość do zaktualizowania. |

- - -
### <a name="switch"></a>Przełącznik
**Funkcja:**<br> Przełącznik (źródło, defaultValue, klucz1, wartość1, klucz2, wartość2,...)

**Opis:**<br> Gdy **źródła** wartość dopasowania **klucz**, zwraca **wartość** tego **klucz**. Jeśli **źródła** wartość nie jest zgodna żadnych kluczy zwraca **defaultValue**.  **Klucz** i **wartość** parametrów musi zawsze występować w parach. Funkcja zawsze oczekuje parzystą liczbą parametrów.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Ciąg |**źródło** wartość do zaktualizowania. |
| **defaultValue** |Optional (Opcjonalność) |Ciąg |Wartość domyślna ma być używany, gdy źródło nie jest zgodna żadnych kluczy. Może być pustym ciągiem (""). |
| **Klucz** |Wymagane |Ciąg |**Klucz** do porównania **źródła** wartością. |
| **value** |Wymagane |Ciąg |Wartość zastąpienia dla **źródła** pasujący do klucza. |

## <a name="examples"></a>Przykłady
### <a name="strip-known-domain-name"></a>Nazwa domeny znanych paska
Musisz usunąć nazwę domeny znane z wiadomości e-mail użytkownika, aby uzyskać nazwę użytkownika. <br>
Na przykład jeśli domena "contoso.com", następnie można użyć następującego wyrażenia:

**Wyrażenie:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Przykładowe dane wejściowe / wyjściowe:** <br>

* **Dane wejściowe** (poczta): "john.doe@contoso.com"
* **Dane wyjściowe**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>Dołącz stałej sufiks do nazw użytkowników
Jeśli używasz piaskownicy usługi Salesforce, może być konieczne dołączyć dodatkowe sufiks do nazw użytkowników przed ich zsynchronizowaniem.

**Wyrażenie:** <br>
`Append([userPrincipalName], ".test")`

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Dane wejściowe**: (userPrincipalName): "John.Doe@contoso.com"
* **DANE WYJŚCIOWE**: "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generowanie alias użytkownika przez złączenie części imię i nazwisko
Należy wygenerować użytkownika aliasu, wykonując pierwsze 3 litery imienia użytkownika i 5 pierwszych liter nazwisko użytkownika.

**Wyrażenie:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Dane wejściowe** (givenName): "John"
* **Dane wejściowe** (nazwisko): "Kowalski"
* **Dane wyjściowe**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Usuń znaki diakrytyczne z ciągu
Należy zastąpić znaki zawierające znaki akcentu równoważne znaki, które nie zawierają znaki akcentu.

**Wyrażenie:** <br>
NormalizeDiacritics([givenName])

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Dane wejściowe** (givenName): "Zoë"
* **Dane wyjściowe**: "Zoe"

### <a name="output-date-as-a-string-in-a-certain-format"></a>Dane wyjściowe daty w postaci ciągu w określonym formacie
Chcesz wysłać daty do aplikacji SaaS w określonym formacie. <br>
Na przykład chcesz formatować daty dla usługi ServiceNow.

**Wyrażenie:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Przykładowe dane wejściowe/wyjściowe:**

* **Dane wejściowe** (extensionAttribute1): "20150123105347.1Z"
* **DANE WYJŚCIOWE**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Zastąp wartość, na podstawie zestawu wstępnie zdefiniowanych opcji
Musisz zdefiniować strefy czasowej użytkownika, na podstawie kodu stanu, przechowywane w usłudze Azure AD. <br>
Jeśli kod stanu nie odpowiada żadnemu z wstępnie zdefiniowanych opcji, należy użyć wartości domyślnej "Australia/Sydney".

**Wyrażenie:** <br>

`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Przykładowe dane wejściowe/wyjściowe:**

* **Dane wejściowe** (stan): "QLD"
* **Dane wyjściowe**: "Australia/Brisbane"

## <a name="related-articles"></a>Powiązane artykuły
* [Automatyzowanie użytkownika aprowizacji/Deprovisioning do aplikacji SaaS](user-provisioning.md)
* [Dostosowywanie mapowań atrybutów dla aprowizacji użytkowników](customize-application-attributes.md)
* [Filtrów określania zakresu na potrzeby aprowizacji użytkownika](define-conditional-rules-for-provisioning-user-accounts.md)
* [Włączanie automatycznej aprowizacji użytkowników i grup z usługi Azure Active Directory do aplikacji przy użyciu SCIM](use-scim-to-provision-users-and-groups.md)
* [Powiadomienia z Aprowizacją kont](user-provisioning.md)
* [Lista samouczków dotyczących integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)

