---
title: Pisanie wyrażeń do mapowania atrybutów w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
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
ms.date: 07/31/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: e741e8d4d68c9862aaabffaccb86740a3e1e9b8a
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68694175"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Pisanie wyrażeń do mapowania atrybutów w usłudze Azure Active Directory
Podczas konfigurowania, inicjowania obsługi administracyjnej aplikacji SaaS, jest jeden z typów mapowania atrybutów, które można określić mapowanie wyrażenia. W tym przypadku trzeba napisać wyrażenia podobne do skryptu, która pozwala na przekształcanie danych użytkowników w formatach, które są bardziej akceptowalne dla aplikacji SaaS.

## <a name="syntax-overview"></a>Omówienie składni
Składnia wyrażeń do mapowania atrybutów jest przypominający języka Visual Basic for Applications (VBA) funkcje.

* Całe wyrażenie musi być zdefiniowany w zakresie funkcji, które składają się z nazwy argumentów w nawiasach: <br>
  *FunctionName (`<<argument 1>>`,`<<argument N>>`)*
* Może być zagnieżdżony funkcji w ramach siebie nawzajem. Na przykład: <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* Trzy różne rodzaje argumenty można przekazać do funkcji:
  
  1. Atrybuty, które muszą być ujęte w nawiasy kwadratowe. Na przykład: [attributeName]
  2. Stałe typu String, które muszą być ujęte w cudzysłów. Przykład: "Stany Zjednoczone"
  3. Inne funkcje. Na przykład: FunctionOne (`<<argument1>>`, FunctionTwo (`<<argument2>>`))
* Dla stałych ciągów Jeśli potrzebujesz kreski ułamkowej odwróconej (\) lub cudzysłowu (") w ciągu go należy użyć znaków ucieczki symbolem kreski ułamkowej odwróconej (\). Przykład: "Nazwa firmy: \\"Contoso\\" "

## <a name="list-of-functions"></a>Lista funkcji
[Dołącz](#append) [](#join) [FormatDateTime](#formatdatetime) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [](#mid) &nbsp; [Nie](#not) [](#normalizediacritics) NormalizeDiacritics Mid &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; Zastąp&nbsp; [SelectUniqueValue](#selectuniquevalue) [](#replace) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [ SingleAppRoleAssignment](#singleapproleassignment) &nbsp; SplitStripSpaces&nbsp; [](#stripspaces) [](#split)&nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp; &nbsp; &nbsp; PrzełączToLower&nbsp; [](#tolower) [](#switch) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [ToUpper](#toupper)

---
### <a name="append"></a>Append
**Funkcja:**<br> Append(source, suffix)

**Opis:**<br> Przyjmuje wartość ciągu źródła i dołącza sufiks na końcu.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego. |
| **suffix** |Wymagane |Ciąg |Ciąg, który chcesz dołączyć do końca wartość źródła. |

---
### <a name="formatdatetime"></a>FormatDateTime
**Funkcja:**<br> FormatDateTime (źródło, inputFormat outputFormat)

**Opis:**<br> Pobiera ciąg daty z jednego formatu i konwertuje je do innego formatu.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego. |
| **inputFormat** |Wymagane |Ciąg |Oczekiwany format wartość źródła. Aby uzyskać obsługiwanych formatów, zobacz [ https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx ](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Wymagane |Ciąg |Format wyjściowej daty. |

---
### <a name="join"></a>Join
**Funkcja:**<br> Dołącz do (separator, źródło1 źródło2...)

**Opis:**<br> JOIN() przypomina Append(), z tą różnicą, że można łączyć w wielu **źródła** ciąg wartości w pojedynczy ciąg, a każda wartość będzie rozdzielone **separator** ciągu.

Jeśli jedna z wartości źródłowych jest atrybutem wielowartościowym, każda wartość w tym atrybucie zostanie przyłączona wspólnie, oddzielona przez wartość separatora.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **Separator** |Wymagane |Ciąg |Ciąg używany do oddzielania wartości źródła, gdy są one połączone w jeden ciąg. Może być "" Jeśli separator nie jest wymagana. |
| **źródło1... źródłoN** |Wymagana zmienna — liczba razy |Ciąg |Ciąg wartości, które mają zostać połączone ze sobą. |

---
### <a name="mid"></a>MID
**Funkcja:**<br> MID (źródło, początek, długość)

**Opis:**<br> Zwraca podciąg wartość źródła. Podciąg jest ciąg zawierający tylko niektóre ze znaków z ciągu źródłowego.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Ciąg |Zazwyczaj nazwa atrybutu. |
| **start** |Wymagane |liczba całkowita |Indeks w **źródła** ciągu, gdzie ma się rozpocząć podciąg. Pierwszy znak w ciągu ma indeks 1, drugi znak ma indeksu 2 i tak dalej. |
| **Długość** |Wymagane |liczba całkowita |Długość podciągu. Jeśli długość kończy się poza **źródła** ciągu, funkcja zwraca podciąg z **start** indeksu do końca **źródła** ciągu. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funkcja:**<br> NormalizeDiacritics(source)

**Opis:**<br> Wymaga jednego argumentu ciągu. Zwraca ciąg, ale za pomocą wszystkie znaki diakrytyczne zastępowane znakami diakrytyczne równoważne. Zwykle używane do konwersji imiona i nazwiska, zawierające znaki diakrytyczne (znaki akcentu) do wartości prawne, które mogą być używane w różne identyfikatory użytkownika, takie jak nazwy głównej użytkownika, nazwy konta SAM i adresy e-mail.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |String | Zwykle jest to atrybut imię i nazwisko. |

---
### <a name="not"></a>nie
**Funkcja:**<br> Not(Source)

**Opis:**<br> Odwraca wartość logiczną **źródła**. Jeśli **źródła** wartość to "*True*", zwraca "*False*". W przeciwnym razie zwraca "*True*".

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Wartości logicznych |Oczekiwane wartości **źródłowe** to "true" lub "false". |

---
### <a name="replace"></a>Replace
**Funkcja:**<br> Zastąp (źródło oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, szablon)

**Opis:**<br>
Zamienia wartości ciągu. Działa inaczej w zależności od parametrów podanych:

* Gdy **oldValue** i **replacementValue** znajdują się:
  
  * Zamienia wszystkie wystąpienia wartości **OldValue** w **źródle** na **replacementValue**
* Gdy **oldValue** i **szablonu** znajdują się:
  
  * Zamienia wszystkie wystąpienia **oldValue** w **szablonu** z **źródła** wartość
* Jeśli **regexPattern** i **replacementValue** są podane:

  * Funkcja stosuje **regexPattern** do ciągu **źródłowego** i można użyć nazw grup wyrażeń regularnych do konstruowania ciągu dla **replacementValue**
* Gdy **regexPattern**, **regexGroupName**, **replacementValue** znajdują się:
  
  * Funkcja stosuje **regexPattern** do ciągu **źródłowego** i zastępuje wszystkie wartości pasujące do **regexGroupName** **replacementValue**
* Gdy **regexPattern**, **regexGroupName**, **replacementAttributeName** są podane:
  
  * Jeśli **źródła** nie ma wartości, **źródła** zwróceniem
  * Jeśli **Źródło** ma wartość, funkcja stosuje **regexPattern** do ciągu **źródłowego** i zastępuje wszystkie wartości pasujące do **regexGroupName** wartością skojarzoną z **replacementAttributeName**

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |String |Zwykle nazwa atrybutu w obiekcie **źródłowym** . |
| **oldValue** |Optional (Opcjonalność) |Ciąg |Wartość ma zostać zastąpione w **źródła** lub **szablonu**. |
| **regexPattern** |Optional (Opcjonalność) |Ciąg |Wzorzec wyrażenia regularnego dla wartości, które ma zostać zastąpione w **źródła**. Lub, gdy **replacementPropertyName** jest używany, wzorzec wyodrębniania wartości z **replacementPropertyName**. |
| **regexGroupName** |Optional (Opcjonalność) |Ciąg |Nazwa grupy wewnątrz **regexPattern**. Tylko wtedy, gdy **replacementPropertyName** jest używany, wyodrębnimy wartość tej grupy jako **replacementValue** z **replacementPropertyName**. |
| **replacementValue** |Optional (Opcjonalność) |Ciąg |Nowa wartość, aby zastąpić stary certyfikat za pomocą. |
| **replacementAttributeName** |Optional (Opcjonalność) |String |Nazwa atrybutu, który ma być używany na potrzeby wartości zamiennej |
| **Szablon** |Optional (Opcjonalność) |String |Gdy zostanie podana wartość **szablonu** , poszukamy wartości **OldValue** wewnątrz szablonu i Zastąp ją wartością **Source** . |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Funkcja:**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2 uniqueValueRule3...)

**Opis:**<br> Wymaga co najmniej dwa argumenty, które są definiowane przy użyciu wyrażeń zasad generowania unikatową wartość. Funkcja ocenia każdą regułę, a następnie sprawdza wartość generowane unikatowość w katalogu/aplikacji docelowej. Pierwszy unikatową wartość znalezione, zostanie zwrócony jeden. Jeśli wszystkie wartości już istnieje w docelowej, wpis będzie pobrać zdeponowane i przyczynę pobiera rejestrowane w dziennikach inspekcji. Nie ma żadnych górnej granicy liczby argumentów, które mogą być podane.

> [!NOTE]
>1. To jest funkcja najwyższego poziomu, nie mogą być zagnieżdżone.
>2. Ta funkcja jest przeznaczone tylko do użytku z Tworzenie wpisu. Podczas korzystania z atrybutem, ustaw **zastosować mapowanie** właściwości **tylko podczas tworzenia obiektu**.


**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **uniqueValueRule1... uniqueValueRuleN** |Co najmniej 2 są wymagane, nie górnej granicy |String | Lista unikatowych reguł generowania wartości do obliczenia. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funkcja:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Opis:**<br> Zwraca pojedynczy appRoleAssignment z listy wszystkich appRoleAssignments przypisanych do użytkownika dla danej aplikacji. Ta funkcja jest wymagana do przekonwertowania obiektu appRoleAssignments na ciąg o pojedynczej nazwie roli. Należy pamiętać, że najlepszym rozwiązaniem jest upewnienie się, że tylko jedna appRoleAssignment jest przypisana do jednego użytkownika w danym momencie i Jeśli przypiszesz wiele ról, zwracany ciąg roli może nie być przewidywalny. 

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Wymagane |Ciąg |**[appRoleAssignments]**  obiektu. |

---
### <a name="split"></a>Podziel
**Funkcja:**<br> Split (Źródło, ogranicznik)

**Opis:**<br> Dzieli ciąg na tablicę wartości Mulit przy użyciu określonego znaku ogranicznika.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Ciąg |**źródło** wartość do zaktualizowania. |
| **ogranicznik** |Wymagane |String |Określa znak, który będzie używany do dzielenia ciągu (przykład: ",") |

---
### <a name="stripspaces"></a>StripSpaces
**Funkcja:**<br> StripSpaces(source)

**Opis:**<br> Usuwa wszystkie spacje ("") znaków z ciągu źródłowego.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Ciąg |**źródło** wartość do zaktualizowania. |

---
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

---
### <a name="tolower"></a>ToLower
**Funkcja:**<br> ToLower (Źródło, kultura)

**Opis:**<br> Pobiera wartość ciągu *źródłowego* i konwertuje ją na małe litery przy użyciu określonych reguł kultury. Jeśli nie określono informacji o kulturze, będzie ona używać niezmiennej kultury.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego |
| **dziedzinie** |Optional |Ciąg |Format nazwy kultury opartej na dokumencie RFC 4646 to *languagecode2-Country/regioncode2*, gdzie *languagecode2* to kod języka dwuliterowego i *kraj/regioncode2* to kod podkultury dwuliterowej. Przykłady obejmują ja-JP dla języka japońskiego (Japonia) i EN-US dla języka angielskiego (Stany Zjednoczone). W przypadkach, gdy kod języka dwuliterowego nie jest dostępny, używany jest trzyliterowy kod pochodzący z normy ISO 639-2.|

---
### <a name="toupper"></a>ToUpper
**Funkcja:**<br> ToUpper (Źródło, kultura)

**Opis:**<br> Pobiera wartość ciągu *źródłowego* i konwertuje ją na wielkie litery przy użyciu określonych reguł kultury. Jeśli nie określono informacji o kulturze, będzie ona używać niezmiennej kultury.

**Parametry:**<br> 

| Name (Nazwa) | Wymagane / powtarzające się | Typ | Uwagi |
| --- | --- | --- | --- |
| **source** |Wymagane |Ciąg |Zazwyczaj nazwa atrybutu z obiektu źródłowego. |
| **dziedzinie** |Optional |Ciąg |Format nazwy kultury opartej na dokumencie RFC 4646 to *languagecode2-Country/regioncode2*, gdzie *languagecode2* to kod języka dwuliterowego i *kraj/regioncode2* to kod podkultury dwuliterowej. Przykłady obejmują ja-JP dla języka japońskiego (Japonia) i EN-US dla języka angielskiego (Stany Zjednoczone). W przypadkach, gdy kod języka dwuliterowego nie jest dostępny, używany jest trzyliterowy kod pochodzący z normy ISO 639-2.|

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

* **Dane wejściowe** (podanąname): Zawierają
* **Dane wejściowe** (nazwisko): Kowalski
* **DANE WYJŚCIOWE**:  "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Usuń znaki diakrytyczne z ciągu
Należy zastąpić znaki zawierające znaki akcentu równoważne znaki, które nie zawierają znaki akcentu.

**Wyrażenie:** <br>
NormalizeDiacritics([givenName])

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Dane wejściowe** (podanąname): "Zoë"
* **DANE WYJŚCIOWE**:  "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Dzielenie ciągu na tablicę wielowartościową
Należy wykonać rozdzielaną przecinkami listę ciągów i podzielić je na tablicę, która może być podłączona do atrybutu wielowartościowego, takiego jak PermissionSets. W tym przykładzie lista zestawów uprawnień została wypełniona w extensionAttribute5 w usłudze Azure AD.

**Wyrażenie:** <br>
Split ([extensionAttribute5], ",")

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Dane wejściowe** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **Output**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Dane wyjściowe daty w postaci ciągu w określonym formacie
Chcesz wysłać daty do aplikacji SaaS w określonym formacie. <br>
Na przykład chcesz formatować daty dla usługi ServiceNow.

**Wyrażenie:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Przykładowe dane wejściowe/wyjściowe:**

* **Dane wejściowe** (extensionAttribute1): "20150123105347.1Z"
* **DANE WYJŚCIOWE**:  "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Zastąp wartość, na podstawie zestawu wstępnie zdefiniowanych opcji

Musisz zdefiniować strefy czasowej użytkownika, na podstawie kodu stanu, przechowywane w usłudze Azure AD. <br>
Jeśli kod stanu nie odpowiada żadnemu z wstępnie zdefiniowanych opcji, należy użyć wartości domyślnej "Australia/Sydney".

**Wyrażenie:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Przykładowe dane wejściowe/wyjściowe:**

* **Dane wejściowe** (stan): "QLD"
* **DANE WYJŚCIOWE**: "Australia/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Zamienianie znaków przy użyciu wyrażenia regularnego
Musisz znaleźć znaki, które pasują do wartości wyrażenia regularnego, i usunąć je.

**Wyrażenie:** <br>

Replace ([mailNickname],, "[a-za-z_] *",, "",,)

**Przykładowe dane wejściowe/wyjściowe:**

* **Dane wejściowe** (mailNickname: "john_doe72"
* **DANE WYJŚCIOWE**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Konwertuj wygenerowaną wartość userPrincipalName (UPN) na małe litery
W poniższym przykładzie wartość UPN jest generowana przez połączenie pól źródłowych PreferredFirstName i PreferredLastName, a funkcja ToLower działa na wygenerowanym ciągu, aby przekonwertować wszystkie znaki na małe litery. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Przykładowe dane wejściowe/wyjściowe:**

* **Dane wejściowe** (PreferredFirstName): Zawierają
* **Dane wejściowe** (PreferredLastName): Nazwisk
* **WYNIK**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Generowanie unikatową wartość dla atrybutu userPrincipalName (UPN)
Oparte na użytkownika imię, drugie imię i nazwisko, należy do generowania wartości atrybutu nazwy UPN i sprawdzić jego unikatowości w katalogu docelowym AD przed przypisaniem wartości do atrybutu nazwy UPN.

**Wyrażenie:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com")
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Przykładowe dane wejściowe/wyjściowe:**

* **Dane wejściowe** (PreferredFirstName): Zawierają
* **Dane wejściowe** (PreferredLastName): Nazwisk
* **Dane wyjściowe**: "John.Smith@contoso.com" Jeśli wartość nazwy UPN John.Smith@contoso.com jeszcze nie istnieje w katalogu
* **Dane wyjściowe**: "J.Smith@contoso.com" Jeśli wartość nazwy UPN John.Smith@contoso.com już istnieje w katalogu
* **Dane wyjściowe**: "Jo.Smith@contoso.com" Jeśli powyższe dwie wartości nazwy UPN już istnieje w katalogu

## <a name="related-articles"></a>Powiązane artykuły
* [Automatyzowanie użytkownika aprowizacji/Deprovisioning do aplikacji SaaS](user-provisioning.md)
* [Dostosowywanie mapowań atrybutów dla aprowizacji użytkowników](customize-application-attributes.md)
* [Filtrów określania zakresu na potrzeby aprowizacji użytkownika](define-conditional-rules-for-provisioning-user-accounts.md)
* [Włączanie automatycznej aprowizacji użytkowników i grup z usługi Azure Active Directory do aplikacji przy użyciu SCIM](use-scim-to-provision-users-and-groups.md)
* [Powiadomienia z Aprowizacją kont](user-provisioning.md)
* [Lista samouczków dotyczących integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)
