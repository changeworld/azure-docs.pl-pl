---
title: 'Program Azure AD Connect: Deklaratywne, inicjowanie obsługi administracyjnej wyrażeń | Dokumentacja firmy Microsoft'
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
origin.date: 07/18/2017
ms.date: 11/08/2018
ms.component: hybrid
ms.author: v-junlch
ms.openlocfilehash: cdc7c9dba49bf37db1f039d43b0450c65884c74b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60245501"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Synchronizacja programu Azure AD Connect: Opis wyrażeń związanych z aprowizacją deklaratywną
Synchronizacja programu Azure AD Connect opiera się na aprowizacja deklaratywna po raz pierwszy wprowadzone w programie Forefront Identity Manager 2010. Dzięki temu można zaimplementować logikę biznesową pełną tożsamość integracji bez konieczności pisania kodu skompilowanego.

Integralną część aprowizacja deklaratywna to język wyrażeń w przepływach atrybutu. Język używany jest podzbiorem programu Microsoft® Visual Basic® Applications (VBA). Ten język jest używany w programie Microsoft Office, a użytkownicy mający doświadczenie VBScript również rozpozna go. Deklaratywne język wyrażeń aprowizacji wyłącznie przy użyciu funkcji i nie jest język structured. Brak metody i instrukcji. Funkcje zamiast tego są zagnieżdżone w celu przepływu programu express.

Aby uzyskać więcej informacji, zobacz [Witamy w języku Visual Basic for Skorowidz języka aplikacji pakietu Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Atrybuty są silnie typizowane. Funkcja akceptuje tylko atrybuty poprawnego typu. Jest również wielkość liter. Zarówno nazwy funkcji, jak i nazwy atrybutu jest posiadanie odpowiedniej wielkości lub zostanie zgłoszony błąd.

## <a name="language-definitions-and-identifiers"></a>Język definicji i identyfikatory
- Funkcje mają nazwę, oraz argumenty w nawiasach: FunctionName (argumentu 1, argument N).
- Atrybuty są identyfikowane przez nawiasy kwadratowe: [attributeName]
- Parametry są identyfikowane za pomocą procentu: ParameterName %
- Stałe typu String są ujęte w cudzysłowy: Na przykład "Contoso" (Uwaga: należy użyć cudzysłowów prostych "" i nie inteligentne cudzysłowy "")
- Wartości numeryczne są wyrażone bez znaków cudzysłowu i powinny być dziesiętną. Wartości szesnastkowe mają prefiks & H. Na przykład 98052 & HFF
- Wartości logiczne są wyrażone za pomocą stałych: True, False.
- Stałe wbudowanych i wszystkie literały są wyrażone za pomocą tylko nazwy: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Funkcje
Aprowizacja deklaratywna używa wiele funkcji, aby umożliwić możliwość przekształcania wartości atrybutów. Te funkcje mogą być zagnieżdżane, wynikiem funkcji jest przekazywana do innej funkcji.

`Function1(Function2(Function3()))`

Pełną listę funkcji można znaleźć w [funkcji odwołanie](reference-connect-sync-functions-reference.md).

### <a name="parameters"></a>Parametry
Parametr jest zdefiniowany przez łącznik lub przez administratora przy użyciu programu PowerShell. Parametry zwykle zawierają wartości, które różnią się od systemu, na przykład nazwa domeny użytkownika znajduje się w. Te parametry mogą być używane w ramach przepływów atrybutów.

Łącznik usługi Active Directory dostępne następujące parametry dla reguły synchronizacji ruchu przychodzącego:

| Nazwa parametru | Komentarz |
| --- | --- |
| Domain.Netbios |Format NetBIOS domeny obecnie importowany, na przykład FABRIKAMSALES |
| Domain.FQDN |Format nazwy FQDN domeny, obecnie importowany, na przykład sales.fabrikam.com |
| Domain.LDAP |Format LDAP obecnie importowany, na przykład kontrolera domeny = sprzedaż, DC = fabrikam, DC = com |
| Forest.Netbios |Format nazwy lasu, obecnie importowany, na przykład FABRIKAMCORP NetBIOS |
| Forest.FQDN |Format nazwy FQDN lasu nazwę aktualnie importowany, na przykład fabrikam.com |
| Forest.LDAP |LDAP format nazwy lasu, obecnie importowany, na przykład DC = fabrikam, DC = com |

System zawiera następujący parametr, który jest używany, aby uzyskać identyfikator łącznika, aktualnie uruchomione:  
`Connector.ID`

Oto przykład, który wypełnia domena atrybut metaverse z nazwą netbios domeny, w którym znajduje się użytkownik:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operatory
Można używać następujących operatorów:

- **Porównanie**: <, < = <>, =, >, > =
- **Matematyce**: +, -, \*, -
- **Ciąg**: & (konkatenacji)
- **Logiczne**: & & (i), || (lub)
- **Kolejność obliczania**:)

Operatory są obliczane od lewej do prawej i mają ten sam priorytet oceny. Oznacza to, że \* (mnożnik) nie jest oceniany przed - (odejmowanie). 2\*(5 + 3) nie jest taka sama jak 2\*5 + 3. (Nawiasów) są używane do Zmień kolejność obliczania po lewej strony, aby kolejność obliczania prawo nie jest właściwe.

## <a name="multi-valued-attributes"></a>Atrybuty wielowartościowe
Funkcje mogą działać na zarówno jedno- i wielowartościowych atrybutach. W przypadku atrybutów wielowartościowych funkcja działa przez każda wartość i stosuje taką samą funkcję do każdej wartości.

Na przykład:  
`Trim([proxyAddresses])` Czy Trim każdej wartości w atrybucie proxyAddress.  
`Word([proxyAddresses],1,"@") & "@contoso.com"` Dla każdej wartości z @-sign, Zastąp domeny za pomocą @contoso.com.  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` Wyszukaj adres SIP i usunąć go z wartości.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o modelu konfiguracji w [Aprowizacja Deklaratywna opis](concept-azure-ad-connect-sync-declarative-provisioning.md).
- Zobacz, jak deklaratywne Inicjowanie obsługi administracyjnej jest używane out-of-box w [opis konfiguracji domyślnej](concept-azure-ad-connect-sync-default-configuration.md).
- Zobacz, jak zrobić praktyczne przy użyciu aprowizacja deklaratywna w [sposób wprowadzania zmian w domyślnej konfiguracji](how-to-connect-sync-change-the-configuration.md).

**Tematy poglądowe**

- [Synchronizacja w programie Azure AD Connect: Zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
- [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)

**Tematy odwołań**

- [Synchronizacja w programie Azure AD Connect: Informacje ogólne o funkcjach](reference-connect-sync-functions-reference.md)


