---
title: 'Synchronizacja programu Azure AD Connect: Włączanie Kosza AD | Dokumentacja firmy Microsoft'
description: W tym temacie zalecane jest stosowanie funkcji Kosz usługi AD w programie Azure AD Connect.
services: active-directory
keywords: Kosz usługi AD, przypadkowego usunięcia, zakotwiczenie źródła
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: f80d16c36714a971e3878ad7dedffaac55103d92
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593078"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Synchronizacja programu Azure AD Connect: Włączanie Kosza usługi AD
Zalecane jest, Włącz funkcję Kosz usługi AD do katalogów Active lokalnych, które są synchronizowane z usługą Azure AD. 

Jeśli przypadkowo usunięto lokalnego obiektu użytkownika usługi Active Directory i przywracania go za pomocą funkcji Azure AD przywraca odpowiedni obiekt użytkownika usługi Azure AD.  Informacje o funkcji Kosz usługi AD, można znaleźć w artykule [omówienie scenariusza dla Przywracanie usunięte obiekty usługi Active Directory](https://technet.microsoft.com/library/dd379542.aspx).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Zalety włączenie Kosza usługi AD
Funkcja ta ułatwia przywracanie obiekty użytkownika usługi Azure AD, wykonując następujące czynności:

* Jeśli przypadkowo usunięto lokalnego obiektu użytkownika AD, odpowiedni obiekt użytkownika usługi Azure AD zostaną usunięte podczas następnego cyklu synchronizacji. Domyślnie usługi Azure AD śledzi usunięte obiektu użytkownika usługi Azure AD w stanie usunięte nietrwale przez 30 dni.

* Jeśli masz lokalnej AD Odtwórz włączona funkcja Bin, możesz przywrócić usunięte lokalnie obiektu użytkownika usługi Active Directory bez modyfikowania jej wartość zakotwiczenie źródła. Gdy odzyskane lokalną obiektu użytkownika usługi Active Directory jest synchronizowane z usługą Azure AD, Azure AD będą obiektu użytkownika przywracania odpowiedniej wszystkie usunięte nietrwale usługi Azure AD. Informacje o atrybut zakotwiczenia źródła, można znaleźć w artykule [Azure AD Connect: zagadnienia dotyczące projektowania](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor).

* Jeśli nie masz lokalnego włączona funkcja Kosz usługi AD, może być wymagane, aby utworzyć obiekt użytkownika AD, aby zastąpić usuniętego obiektu. Jeśli usługi Azure AD Connect usługi synchronizacji jest skonfigurowany do używania wygenerowany przez system atrybut AD (na przykład ObjectGuid) dla atrybutu zakotwiczenia źródła, nowo utworzony obiekt użytkownika AD nie ma taką samą wartość zakotwiczenie źródła jako usuniętego obiektu użytkownika AD. Nowo utworzony obiekt użytkownika AD jest synchronizowane z usługą Azure AD, Azure AD tworzy nowy obiekt użytkownika usługi Azure AD zamiast Przywracanie usunięcie nietrwałe obiektu użytkownika w usłudze Azure AD.

> [!NOTE]
> Domyślnie usługi Azure AD przechowuje usunięte obiekty użytkownika usługi Azure AD w stanie usunięte nietrwale przez 30 dni, zanim zostaną trwale usunięte. Jednak Administratorzy może przyspieszyć usuwanie tych obiektów. Po obiekty zostaną trwale usunięte, nie będzie można odzyskać, nawet jeśli lokalne się, że włączono funkcję Kosz usługi AD.



## <a name="next-steps"></a>Kolejne kroki
**Tematy poglądowe**

* [Synchronizacja programu Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](active-directory-aadconnectsync-whatis.md)

* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)
