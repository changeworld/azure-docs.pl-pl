---
title: 'Azure AD Connect: Deklaratywne inicjowania obsługi administracyjnej wyrażeń | Dokumentacja firmy Microsoft'
description: W tym artykule wyjaśniono deklaratywne wyrażenia inicjowania obsługi administracyjnej.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: e3ea53c8-3801-4acf-a297-0fb9bb1bf11d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 12d7e997d82c0ac15d1c81b92491e0a16f4c214e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593680"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Synchronizacja programu Azure AD Connect: opis deklaratywne wyrażenia inicjowania obsługi administracyjnej
Synchronizacja programu Azure AD Connect tworzy na aprowizacją deklaratywną po raz pierwszy wprowadzone w programie Forefront Identity Manager 2010. Umożliwia wdrożenie logiki biznesowej tożsamości pełnej integracji bez konieczności pisania kodu skompilowanego.

Integralną część aprowizacją deklaratywną jest język wyrażenia w przepływów atrybutów. Język używany jest podzbiorem Visual Basic for Applications (VBA) Microsoft®. Ten język jest używany w programie Microsoft Office, a użytkownicy mający doświadczenie VBScript również rozpozna go. Wyrażenie języka deklaratywne inicjowania obsługi tylko przy użyciu funkcji i nie jest języka structured. Nie ma żadnych metod ani instrukcji. Funkcje zamiast tego są zagnieżdżone przepływem programu express.

Aby uzyskać więcej informacji, zobacz [Visual Basic dla odwołanie językowe aplikacje pakietu Office 2013 — Zapraszamy](https://msdn.microsoft.com/library/gg264383.aspx).

Atrybuty są silnie typizowane. Funkcja akceptuje tylko atrybuty poprawnego typu. Jest również wielkość liter. Zarówno funkcja nazw i atrybut musi mieć odpowiednie wielkości liter lub zostanie zgłoszony błąd.

## <a name="language-definitions-and-identifiers"></a>Język definicji i identyfikatory
* Funkcje mają nazwy, a następnie argumenty w nawiasach: FunctionName (argument 1, argument N).
* Atrybuty są identyfikowane za pomocą nawiasy kwadratowe: [attributeName]
* Parametry są identyfikowane za pomocą procentu: % ParameterName %
* Stałe typu String są ujęte w cudzysłowy: na przykład "Contoso" (Uwaga: należy użyć cudzysłowów prostych "" oraz nie cudzysłów "")
* Wartości numeryczne wyrażone bez cudzysłowów i powinien być dziesiętną. Wartości szesnastkowe są poprzedzane prefiksem & h Na przykład 98052 & HFF
* Wartości logiczna są wyrażane za pomocą stałych: True i False.
* Literały i wbudowane stałe są wyrażane tylko ich nazwą: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Funkcje
Aprowizacja deklaratywna używa wielu funkcji, aby umożliwić możliwości przekształcania wartości atrybutu. Funkcje te mogą być zagnieżdżane tak wyników z jednej funkcji jest przekazywana do innej funkcji.

`Function1(Function2(Function3()))`

Pełną listę funkcji można znaleźć w [funkcji odwołanie](active-directory-aadconnectsync-functions-reference.md).

### <a name="parameters"></a>Parametry
Parametr jest zdefiniowany przez łącznik lub przez administratora przy użyciu programu PowerShell. Parametry zwykle zawierają wartości, które różnią się od systemu, na przykład nazwa domeny użytkownika znajdują się w temacie. Te parametry mogą być używane w przepływów atrybutów.

Łącznik usługi Active Directory przewidzianych reguł synchronizacji ruchu przychodzącego następujące parametry:

| Nazwa parametru | Komentarz |
| --- | --- |
| Domain.Netbios |Format NetBIOS domeny obecnie importowany, na przykład FABRIKAMSALES |
| Domain.FQDN |Format nazwy FQDN domeny obecnie importowany, na przykład sales.fabrikam.com |
| Domain.LDAP |Format LDAP domeny obecnie importowany, na przykład kontroler domeny sprzedaż, DC = = firmy fabrikam, DC = com |
| Forest.Netbios |NetBIOS format nazwy lasu, w obecnie importowany, na przykład FABRIKAMCORP |
| Forest.FQDN |Format nazwy FQDN Nazwa lasu, w obecnie importowany, na przykład fabrikam.com |
| Forest.LDAP |LDAP format nazwy lasu obecnie importowany, na przykład DC = Firma fabrikam, DC = com |

System zawiera następujący parametr jest używany do pobierania identyfikator łącznika, w obecnie uruchomiona:  
`Connector.ID`

Oto przykład wypełniająca domeny atrybut metaverse z nazwą netbios domeny, w której znajduje się użytkownik:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operatory
Można użyć następujących operatorów:

* **Porównanie**: <, < =, <>, =, >, > =
* **Matematyce**: +, -, \*, -
* **Ciąg**: & (konkatenacji)
* **Logiczne**: & & (a), || (lub)
* **Kolejność obliczania**:)

Operatory są wykonywane od lewej do prawej i mają ten sam priorytet oceny. Oznacza to \* (mnożnik) nie jest obliczane przed - (odejmowanie). 2\*(5 + 3) nie jest taka sama jak 2\*5 + 3. Nawiasy () są używane do zmianie kolejności oceny od lewej do prawej oceny zlecenia nie jest odpowiedni.

## <a name="multi-valued-attributes"></a>Atrybuty wielowartościowe
Funkcje mogą działać na atrybutach zarówno jedno- i wielowartościowych. W przypadku atrybutów wielowartościowych funkcja działa w każdej wartości i stosuje taką samą funkcję do każdej wartości.

Na przykład:  
`Trim([proxyAddresses])` Czy przycinanie każdej wartości w atrybucie proxyAddress.  
`Word([proxyAddresses],1,"@") & "@contoso.com"` Dla każdej wartości z @-sign, Zastąp domeny za pomocą @contoso.com.  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` Znajdź adres SIP i usunąć go z wartości.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o model konfiguracji w [Aprowizacją deklaratywną opis](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Zobacz, jak deklaratywne inicjowania obsługi administracyjnej jest używane out-of-box w [opis konfiguracji domyślnej](active-directory-aadconnectsync-understanding-default-configuration.md).
* W temacie jak zrobić praktyczne przy użyciu aprowizacją deklaratywną w [jak wprowadzić zmianę do domyślnej konfiguracji](active-directory-aadconnectsync-change-the-configuration.md).

**Tematy poglądowe**

* [Synchronizacja programu Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](active-directory-aadconnectsync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)

**Tematy odwołań**

* [Synchronizacja programu Azure AD Connect: odwołanie do funkcji](active-directory-aadconnectsync-functions-reference.md)

