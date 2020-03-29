---
title: 'Synchronizacja usługi Azure AD Connect: włączanie kosza usługi AD | Dokumenty firmy Microsoft'
description: W tym temacie zaleca się korzystanie z funkcji Kosza usługi AD w usłudze Azure AD Connect.
services: active-directory
keywords: Kosz usługi AD, przypadkowe usunięcie, kotwica źródłowa
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/17/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fe7d3ea7d4f6d648438efc1a484d5909ade2f23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60382899"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Synchronizacja usługi Azure AD Connect: włączanie kosza usługi AD
Zaleca się włączenie funkcji Kosza usługi AD dla lokalnych usług Active Directories, które są synchronizowane z usługą Azure AD. 

Jeśli przypadkowo usunięto lokalny obiekt użytkownika usługi AD i przywrócić go przy użyciu tej funkcji, usługa Azure AD przywróci odpowiedni obiekt użytkownika usługi Azure AD.  Aby uzyskać informacje na temat funkcji Kosz usługi AD, zobacz artykuł [Omówienie scenariusza przywracania usuniętych obiektów usługi Active Directory](https://technet.microsoft.com/library/dd379542.aspx).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Korzyści z włączenia kosza AD
Ta funkcja pomaga w przywracaniu obiektów użytkowników usługi Azure AD, wykonując następujące czynności:

* Jeśli przypadkowo usuniesz lokalny obiekt użytkownika usługi AD, odpowiedni obiekt użytkownika usługi Azure AD zostanie usunięty w następnym cyklu synchronizacji. Domyślnie usługa Azure AD przechowuje usunięty obiekt użytkownika usługi Azure AD w stanie nietrwale usuniętym przez 30 dni.

* Jeśli masz włączoną funkcję lokalnego Kosza usługi AD, można przywrócić usunięty lokalny obiekt użytkownika usługi AD bez zmiany jego wartości zakotwiczenia źródła. Gdy odzyskany lokalny obiekt użytkownika usługi AD jest synchronizowany z usługą Azure AD, usługa Azure AD przywróci odpowiedni obiekt użytkownika usługi Azure AD usuniętych programem. Aby uzyskać informacje na temat atrybutu Kotwica źródła, zobacz artykuł [Azure AD Connect: Pojęcia dotyczące projektowania](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor).

* Jeśli nie masz włączonej funkcji lokalnego Kosza usługi AD, może być konieczne utworzenie obiektu użytkownika usługi AD w celu zastąpienia usuniętego obiektu. Jeśli usługa synchronizacji usługi Azure AD Connect jest skonfigurowana do używania atrybutu usługi AD generowanego przez system (takiego jak ObjectGuid) dla atrybutu Zakotwiczenie źródła, nowo utworzony obiekt użytkownika usługi AD nie będzie miał tej samej wartości zakotwiczenia źródła, co usunięty obiekt użytkownika usługi AD. Gdy nowo utworzony obiekt użytkownika usługi AD jest synchronizowany z usługą Azure AD, usługa Azure AD tworzy nowy obiekt użytkownika usługi Azure AD zamiast przywracania obiektu użytkownika usługi Azure AD usuniętego nietrwale.

> [!NOTE]
> Domyślnie usługa Azure AD przechowuje usunięte obiekty użytkowników usługi Azure AD w stanie nietrwałym usuniętym przez 30 dni, zanim zostaną trwale usunięte. Jednak administratorzy mogą przyspieszyć usuwanie takich obiektów. Po trwałym usunięciu obiektów nie można ich już odzyskać, nawet jeśli lokalna funkcja Kosza usługi AD jest włączona.

## <a name="next-steps"></a>Następne kroki
**Tematy omówienie**

* [Synchronizacja usługi Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)

* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
