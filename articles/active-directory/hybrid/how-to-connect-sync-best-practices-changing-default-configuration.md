---
title: 'Synchronizacja usługi Azure AD Connect: zmiana konfiguracji domyślnej | Dokumenty firmy Microsoft'
description: Zawiera najlepsze rozwiązania dotyczące zmiany domyślnej konfiguracji synchronizacji usługi Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 7638a031-1635-4942-94c3-fce8f09eed5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/29/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 940a35d89996b1eb9600fe4214863d2b5304750e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60242177"
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Synchronizacja usługi Azure AD Connect: najważniejsze wskazówki dotyczące zmiany konfiguracji domyślnej
Celem tego tematu jest opisanie obsługiwanych i nieobsługiconych zmian w synchronizacji usługi Azure AD Connect.

Konfiguracja utworzona przez usługę Azure AD Connect działa "tak jak jest" dla większości środowisk, które synchronizują lokalną usługę Active Directory z usługą Azure AD. Jednak w niektórych przypadkach konieczne jest zastosowanie pewnych zmian w konfiguracji, aby spełnić określone potrzeby lub wymagania.

## <a name="changes-to-the-service-account"></a>Zmiany na koncie usługi
Synchronizacja usługi Azure AD Connect jest uruchomiona w ramach konta usługi utworzonego przez kreatora instalacji. To konto usługi przechowuje klucze szyfrowania do bazy danych używanej przez synchronizację. Jest on tworzony z hasłem o długości 127 znaków, a hasło nie wygasa.

* Nie można **obsługiwać** zmiany lub zresetowania hasła konta usługi. W ten sposób niszczy klucze szyfrowania i usługa nie jest w stanie uzyskać dostępu do bazy danych i nie jest w stanie uruchomić.

## <a name="changes-to-the-scheduler"></a>Zmiany w harmonogramie
Począwszy od wersji z kompilacji 1.1 (luty 2016) można skonfigurować [harmonogram,](how-to-connect-sync-feature-scheduler.md) aby mieć inny cykl synchronizacji niż domyślne 30 minut.

## <a name="changes-to-synchronization-rules"></a>Zmiany w regułach synchronizacji
Kreator instalacji udostępnia konfigurację, która ma działać dla najbardziej typowych scenariuszy. W przypadku, gdy trzeba wprowadzić zmiany w konfiguracji, należy przestrzegać tych reguł, aby nadal mieć obsługiwaną konfigurację.

> [!WARNING]
> Jeśli wprowadzą zmiany do domyślnych reguł synchronizacji, zmiany te zostaną zastąpione przy następnej aktualizacji usługi Azure AD Connect, co spowoduje nieoczekiwane i prawdopodobnie niepożądane wyniki synchronizacji.

* [Przepływy atrybutów można zmienić,](how-to-connect-sync-change-the-configuration.md#other-common-attribute-flow-changes) jeśli domyślne przepływy atrybutów bezpośrednich nie są odpowiednie dla twojej organizacji.
* Jeśli nie chcesz [przepływać atrybutu](how-to-connect-sync-change-the-configuration.md#do-not-flow-an-attribute) i usunąć wszystkie istniejące wartości atrybutów w usłudze Azure AD, należy utworzyć regułę dla tego scenariusza.
* [Wyłącz niechcianą regułę synchronizacji](#disable-an-unwanted-sync-rule) zamiast jej usuwać. Usunięto regułę jest odtworzona podczas uaktualniania.
* Aby [zmienić regułę out-of-box,](#change-an-out-of-box-rule)należy wykonać kopię oryginalnej reguły i wyłączyć regułę po wyjęciu z pudełka. Edytor reguł synchronizacji monituje i pomaga.
* Wyeksportuj niestandardowe reguły synchronizacji za pomocą Edytora reguł synchronizacji. Edytor zapewnia skrypt programu PowerShell, którego można użyć do łatwego ponownego tworzenia ich w scenariuszu odzyskiwania po awarii.

> [!WARNING]
> Reguły synchronizacji out-of-box mają odcisk palca. Jeśli zmienisz te reguły, odcisk palca nie jest już pasując. Podczas próby zastosowania nowej wersji usługi Azure AD Connect mogą wystąpić problemy w przyszłości. Wprowadzaj zmiany tylko w sposób opisany w tym artykule.

### <a name="disable-an-unwanted-sync-rule"></a>Wyłączanie niechcianej reguły synchronizacji
Nie należy usuwać reguły synchronizacji po wyjęciu z pudełka. Jest on odtworzony podczas następnego uaktualnienia.

W niektórych przypadkach kreator instalacji stworzył konfigurację, która nie działa dla topologii. Na przykład jeśli masz topologię lasu zasobów kont, ale schemat w lesie kont został rozszerzony za pomocą schematu programu Exchange, reguły programu Exchange są tworzone dla lasu kont i lasu zasobów. W takim przypadku należy wyłączyć regułę synchronizacji dla programu Exchange.

![Wyłączona reguła synchronizacji](./media/how-to-connect-sync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Na powyższym rysunku kreator instalacji znalazł stary schemat programu Exchange 2003 w lesie kont. To rozszerzenie schematu zostało dodane przed wprowadzeniem lasu zasobów w środowisku firmy Fabrikam. Aby upewnić się, że żadne atrybuty ze starej implementacji programu Exchange nie są synchronizowane, reguła synchronizacji powinna być wyłączona, jak pokazano.

### <a name="change-an-out-of-box-rule"></a>Zmienianie reguły po wyjęciu z pudełka
Jedyną zmianą reguły out-of-box jest zmiana reguły sprzężenia. Jeśli chcesz zmienić przepływ atrybutów, należy utworzyć regułę synchronizacji o wyższym priorytecie niż reguły gotowe. Jedyną regułą, którą praktycznie musisz sklonować, jest reguła **In from AD - User Join**. Można zastąpić wszystkie inne reguły z wyższą regułą pierwszeństwa.

Jeśli musisz wprowadzić zmiany w regule out-of-box, należy wykonać kopię reguły out-of-box i wyłączyć oryginalną regułę. Następnie należy wprowadzić zmiany w sklonowanej regule. Edytor reguł synchronizacji pomaga w tych krokach. Po otwarciu reguły out-of-box, zostanie wyświetlone to okno dialogowe:  
![Reguła ostrzegania po wyjęciu z pola](./media/how-to-connect-sync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Wybierz **pozycję Tak,** aby utworzyć kopię reguły. Następnie otwierana jest reguła sklonowania.  
![Reguła sklonowana](./media/how-to-connect-sync-best-practices-changing-default-configuration/clonedrule.png)

W tej sklonowanej regule należy wprowadzić wszelkie niezbędne zmiany w zakresie, sprzężeniu i przekształceniach.

## <a name="next-steps"></a>Następne kroki
**Tematy omówienie**

* [Synchronizacja usługi Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
