---
title: 'Synchronizacja programu Azure AD Connect: Włączanie Kosza usługi AD | Dokumentacja firmy Microsoft'
description: W tym temacie zaleca używanie funkcji Kosz usługi AD za pomocą usługi Azure AD Connect.
services: active-directory
keywords: Kosz usługi AD, przypadkowemu usunięciu, zakotwiczenie źródła
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
ms.date: 12/17/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: d1a09c077adbc3c9176740bce69bf051a912bc22
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555578"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Synchronizacja programu Azure AD Connect: Włączanie kosza usługi AD
Zalecane jest, Włącz funkcję Kosz usługi AD dla katalogów Active w środowisku lokalnym, które są synchronizowane z usługą Azure AD. 

Jeśli przypadkowo usunięty lokalnego obiektu użytkownika AD i przywracanie jej przy użyciu funkcji usługi Azure AD przywraca odpowiedni obiekt użytkownika usługi Azure AD.  Informacje o funkcji Kosz usługi AD, można znaleźć w artykule [omówienie scenariusza dla przywracania usunięte obiekty usługi Active Directory](https://technet.microsoft.com/library/dd379542.aspx).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Zalety włączenie Kosza usługi AD
Ta funkcja pomaga z przywracaniem obiekty użytkownika usługi Azure AD, wykonując następujące czynności:

* Jeśli przypadkowo usunięty lokalnego obiektu użytkownika AD odpowiedni obiekt użytkownika usługi Azure AD zostaną usunięte podczas następnego cyklu synchronizacji. Domyślnie usługa Azure AD przechowuje usuniętego obiektu użytkownika w usłudze Azure AD w stanie usunięty nietrwale przez 30 dni.

* W przypadku lokalnej usługi AD Recycle Bin funkcja jest włączona, można przywrócić usunięte lokalnego obiektu użytkownika AD bez konieczności zmieniania jego wartości zakotwiczenie źródła. Gdy odzyskane lokalnej obiektu użytkownika AD, jest synchronizowany z usługą Azure AD, usługa Azure AD będą obiektu użytkownika przywracania odpowiedniej wszystkie usunięte nietrwale usługi Azure AD. Informacje o atrybut zakotwiczenia źródła, można znaleźć w artykule [program Azure AD Connect: Pojęcia dotyczące projektowania](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor).

* Jeśli nie masz lokalnego włączoną funkcję Kosz usługi AD, może być wymagane do tworzenia obiektu użytkownika AD, aby zastąpić usuniętego obiektu. Jeśli usługa Azure AD Connect synchronizacji jest skonfigurowany do używania generowanych przez system atrybutu usługi AD (na przykład ObjectGuid) dla atrybutu zakotwiczenia źródła, nowo utworzonego obiektu użytkownika AD nie będzie taka sama wartość zakotwiczenia źródła, jak usuniętego obiektu użytkownika AD. Nowo utworzony obiekt użytkownika AD są synchronizowane z usługą Azure AD, usługa Azure AD tworzy nowy obiekt użytkownika usługi Azure AD zamiast usunięty nietrwale obiektu użytkownika w usłudze Azure AD.

> [!NOTE]
> Domyślnie usługa Azure AD przechowuje usunięte obiekty użytkownika usługi Azure AD w stanie usunięty nietrwale przez 30 dni, zanim zostaną one trwale usunięte. Jednak administratorzy mogą przyspieszać usuwania tych obiektów. Gdy obiekty są trwale usuwane, nie będzie można odzyskać, nawet wtedy, gdy lokalne jest włączona funkcja Kosza usługi AD.

>[!NOTE]
>Poprzednio po użytkownik został usunięty z zakresu synchronizacji w środowisku lokalnym i usunięte w chmurze, stan DirSyncEnabled konta błędnie została ustawiona na "False". Jeśli później, ten użytkownik został przywrócony ręcznie z Kosza usługi Azure AD, a potem nieprawidłowym stanie "chmura tylko do" konta. Teraz zostało to poprawione i wartość stanu DirSyncEnabled jest zawsze mieć "wartość True" po użytkownik zostanie usunięty z zakresu synchronizacji, następnie wszystkie usunięte nietrwale i ręcznie odtworzonych z Kosza usługi Azure AD.

## <a name="next-steps"></a>Kolejne kroki
**Tematy poglądowe**

* [Synchronizacja programu Azure AD Connect: Zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)

* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
