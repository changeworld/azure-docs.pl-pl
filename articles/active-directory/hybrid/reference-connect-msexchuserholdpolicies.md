---
title: 'Usługa Azure AD Connect: msExchUserHoldPolicies i cloudMsExchUserHoldPolicies | Dokumenty firmy Microsoft'
description: W tym temacie opisano zachowanie atrybutów msExchUserHoldPolicies i cloudMsExchUserHoldPolicies atrybuty
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4c637a01825616334cda8faa594efd08f29de8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74213074"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Usługa Azure AD Connect — msExchUserHoldPolicies i cloudMsExchUserHoldPolicies
W poniższym dokumencie referencyjnym opisano te atrybuty używane przez program Exchange oraz prawidłowy sposób edytowania domyślnych reguł synchronizacji.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Co to są msExchUserHoldPolicies i cloudMsExchUserHoldPolicies?
Istnieją dwa typy [blokad](https://docs.microsoft.com/Exchange/policy-and-compliance/holds/holds?view=exchserver-2019) dostępne dla serwera Exchange Server: Wstrzymanie postępowania sądowego i Wstrzymanie w miejscu. Gdy wstrzymanie postępowania sądowego jest włączone, wszystkie skrzynki pocztowe wszystkie elementy są zawieszone.  Wstrzymanie w miejscu służy do zachowania tylko tych elementów, które spełniają kryteria kwerendy wyszukiwania zdefiniowane za pomocą narzędzia zbierania elektronicznych materiałów dowodowych w miejscu.

MsExchUserHoldPolcies i cloudMsExchUserHoldPolicies atrybuty umożliwiają lokalne ad i usługi Azure AD, aby określić, którzy użytkownicy są w wstrzymaniu w zależności od tego, czy są one przy użyciu lokalnego programu Exchange lub exchange on-line.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>msExchUserHoldPolitys przepływ synchronizacji
Domyślnie MsExchUserHoldPolcies jest synchronizowany przez usługę Azure AD Connect bezpośrednio z atrybutem msExchUserHoldPolicies w metaverse, a następnie do atrybutu msExchUserHoldPolices w usłudze Azure AD

W poniższych tabelach opisano przepływ:

Przychodzące z lokalnej usługi Active Directory:

|Atrybut usługi Active Directory|Nazwa atrybutu|Typ przepływu|Atrybut Metaverse|Reguła synchronizacji|
|-----|-----|-----|-----|-----|
|Lokalna usługa Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolices|In from AD — User Exchange|

Wychodzące usługi Azure AD:

|Atrybut Metaverse|Nazwa atrybutu|Typ przepływu|Atrybut usługi Azure AD|Reguła synchronizacji|
|-----|-----|-----|-----|-----|
|Usługa Azure Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolicies|Wyjście do AAD – UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>cloudMsExchUserHoldPolicies przepływ synchronizacji
Domyślnie cloudMsExchUserHoldPolicies jest synchronizowany przez usługę Azure AD Connect bezpośrednio do cloudMsExchUserHoldPolicies atrybut w metaverse. Następnie jeśli msExchUserHoldPolices nie jest null w metaverse, atrybut w wypłynął do usługi Active Directory.

W poniższych tabelach opisano przepływ:

Przychodzące z usługi Azure AD:

|Atrybut usługi Active Directory|Nazwa atrybutu|Typ przepływu|Atrybut Metaverse|Reguła synchronizacji|
|-----|-----|-----|-----|-----|
|Lokalna usługa Active Directory|cloudMsExchUserHoldPolicies|Direct|cloudMsExchUserHoldPolicies|In z usługi AAD — wymiana użytkownika|

Wychodzące do lokalnej usługi Active Directory:

|Atrybut Metaverse|Nazwa atrybutu|Typ przepływu|Atrybut usługi Azure AD|Reguła synchronizacji|
|-----|-----|-----|-----|-----|
|Usługa Azure Active Directory|cloudMsExchUserHoldPolicies|IF(NOT NULL)|msExchUserHoldPolicies|Wyjście do usługi AD — UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>Informacje o zachowaniu atrybutu
MsExchangeUserHoldPolicies jest atrybutem pojedynczego urzędu.  Atrybut pojedynczego urzędu można ustawić na obiekcie (w tym przypadku obiektu użytkownika) w katalogu lokalnym lub w katalogu w chmurze.  Reguły startu urzędu określają, że jeśli atrybut jest synchronizowany z lokalnego, usługa Azure AD nie będzie mogła zaktualizować tego atrybutu.

Aby umożliwić użytkownikom ustawienie zasad wstrzymania obiektu użytkownika w chmurze, używany jest atrybut cloudMSExchangeUserHoldPolicies. Ten atrybut jest używany, ponieważ usługa Azure AD nie można ustawić msExchangeUserHoldPolicies bezpośrednio na podstawie reguł wyjaśnionych powyżej.  Ten atrybut zostanie następnie zsynchronizowany z katalogiem lokalnym, jeśli msExchangeUserHoldPolicies nie ma wartości null i zastąpi bieżącą wartość msExchangeUserHoldPolicies.

W pewnych okolicznościach, na przykład jeśli oba zostały zmienione w środowisku lokalnym i na platformie Azure w tym samym czasie, może to spowodować pewne problemy.  

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
