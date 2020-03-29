---
title: 'Usługa Azure AD Connect: wyrażenia inicjowania obsługi administracyjnej deklaratywne | Dokumenty firmy Microsoft'
description: W tym artykule wyjaśniono wyrażenia inicjujące inicjowanie obsługi administracyjnej.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: e3ea53c8-3801-4acf-a297-0fb9bb1bf11d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdc7c9dba49bf37db1f039d43b0450c65884c74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60245501"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Synchronizacja usługi Azure AD Connect: opis wyrażeń deklaratywnego inicjowania obsługi administracyjnej
Synchronizacja usługi Azure AD Connect opiera się na deklaratywnym inicjowaniu obsługi administracyjnej wprowadzonym po raz pierwszy w programie Forefront Identity Manager 2010. Umożliwia zaimplementowanie logiki biznesowej integracji tożsamości bez konieczności pisania skompilowanego kodu.

Istotną częścią deklaratywnej inicjowania obsługi administracyjnej jest język wyrażenia używany w przepływach atrybutów. Używany język jest podzbiorem programu Microsoft® Visual Basic® for Applications (VBA). Ten język jest używany w pakiecie Microsoft Office, a użytkownicy z doświadczeniem w języku VBScript również go rozpoznają. Deklaratywny język wyrażenia inicjowania obsługi administracyjnej jest tylko przy użyciu funkcji i nie jest językiem strukturalnym. Nie ma żadnych metod ani instrukcji. Zamiast tego funkcje są zagnieżdżone do wyrażania przepływu programu.

Aby uzyskać więcej informacji, zobacz [Witamy w języku visual basic for applications— informacje dotyczące języka pakietu Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Atrybuty są silnie wpisane. Funkcja akceptuje tylko atrybuty poprawnego typu. Jest również rozróżniana wielkość liter. Zarówno nazwy funkcji, jak i nazwy atrybutów muszą mieć prawidłową wielkość liter lub zostanie zgłoszony błąd.

## <a name="language-definitions-and-identifiers"></a>Definicje języków i identyfikatory
* Funkcje mają nazwę, po której następują argumenty w nawiasach: FunctionName(argument 1, argument N).
* Atrybuty są identyfikowane za pomocą nawiasów kwadratowych: [attributeName]
* Parametry są identyfikowane przez znaki procentowe: %ParameterName%
* Stałe ciągów są otoczone cudzysłowami: Na przykład "Contoso" (Uwaga: należy użyć cudzysłowów prostych "", a nie cudzysłowów inteligentnych "")
* Wartości liczbowe są wyrażone bez cudzysłowów i oczekuje się, że będą dziesiętne. Wartości szesnastkowe są poprzedzone &H. Na przykład 98052, &HFF
* Wartości logiczne są wyrażane za pomocą stałych: True, False.
* Wbudowane stałe i literały są wyrażane tylko z ich nazwą: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Funkcje
Deklaratywny inicjowania obsługi administracyjnej używa wielu funkcji, aby włączyć możliwość przekształcania wartości atrybutów. Te funkcje mogą być zagnieżdżone, więc wynik z jednej funkcji jest przekazywany do innej funkcji.

`Function1(Function2(Function3()))`

Pełną listę funkcji można znaleźć w [odwołaniu do funkcji](reference-connect-sync-functions-reference.md).

### <a name="parameters"></a>Parametry
Parametr jest definiowany przez łącznik lub przez administratora korzystającego z programu PowerShell. Parametry zwykle zawierają wartości, które różnią się od systemu do systemu, na przykład nazwa domeny, w których znajduje się użytkownik. Parametry te mogą być używane w przepływach atrybutów.

Łącznik usługi Active Directory dostarczył następujące parametry reguł synchronizacji przychodzącej:

| Nazwa parametru | Komentarz |
| --- | --- |
| Domena.Netbios |Format Netbios domeny aktualnie importowanej, na przykład FABRIKAMSALES |
| Nazwa domeny.FQDN |Format FQDN aktualnie importowanej domeny, na przykład sales.fabrikam.com |
| Domena.LDAP |Format LDAP aktualnie importowanej domeny, na przykład DC=sales,DC=fabrikam,DC=com |
| Forest.Netbios (Forest.Netbios) |Format Netbios aktualnie importowanej nazwy lasu, na przykład FABRIKAMCORP |
| Forest.FQDN |Format nazwy FQDN aktualnie importowanej nazwy lasu, na przykład fabrikam.com |
| Forest.LDAP |Format LDAP aktualnie importowanej nazwy lasu, na przykład DC=fabrikam,DC=com |

System zawiera następujący parametr, który jest używany do uzyskania identyfikatora łącznika aktualnie uruchomionego:  
`Connector.ID`

Oto przykład, który wypełnia domenę atrybutów metaverse nazwą netbios domeny, w której znajduje się użytkownik:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operatory
Można użyć następujących operatorów:

* **Porównanie**: <, <=, <>, =, >, >=
* **Matematyka**: +, \*-, , -
* **Ciąg**: & (konkapacji)
* **Logiczne**: && (i), || (lub)
* **Kolejność oceny**: ( )

Operatory są oceniane od lewej do prawej i mają ten sam priorytet oceny. Oznacza to, \* że (mnożnik) nie jest oceniany przed - (odejmowanie). 2\*(5+3) to nie to\*samo co 2 5+3. Nawiasy ( ) są używane do zmiany kolejności oceny, gdy kolejność oceny od lewej do prawej nie jest odpowiednia.

## <a name="multi-valued-attributes"></a>Atrybuty wielowartościowe
Funkcje mogą działać zarówno na atrybuty o pojedynczej i wielu wartościach. W przypadku atrybutów wielowartościowych funkcja działa nad każdą wartością i stosuje tę samą funkcję do każdej wartości.

Przykład:  
`Trim([proxyAddresses])`Wykonaj przycinanie każdej wartości w proxyAddress atrybutu.  
`Word([proxyAddresses],1,"@") & "@contoso.com"`Dla każdej wartości @-signz domeną @contoso.comzastąp domenę .  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])`Poszukaj adresu SIP i usuń go z wartości.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o modelu konfiguracji w [opisie aprowizacji deklaratywnej](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Zobacz, jak deklaratywny inicjowania obsługi administracyjnej jest używany po wyjęciu z pudełka [w opisie konfiguracji domyślnej](concept-azure-ad-connect-sync-default-configuration.md).
* Zobacz, jak wprowadzić praktyczną zmianę za pomocą deklaratywnych inicjowania obsługi administracyjnej w [jak wprowadzić zmiany w konfiguracji domyślnej](how-to-connect-sync-change-the-configuration.md).

**Tematy omówienie**

* [Synchronizacja usługi Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)

**Tematy informacyjne**

* [Synchronizacja usługi Azure AD Connect: odwołanie do funkcji](reference-connect-sync-functions-reference.md)

