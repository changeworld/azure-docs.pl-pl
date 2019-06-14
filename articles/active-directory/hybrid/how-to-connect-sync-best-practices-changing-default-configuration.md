---
title: 'Synchronizacja programu Azure AD Connect: Zmienianie konfiguracji domyślnej | Dokumentacja firmy Microsoft'
description: Przedstawia najlepsze rozwiązania dotyczące zmieniania konfiguracji domyślnej, program Azure AD Connect Sync.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60242177"
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Synchronizacja programu Azure AD Connect: Najlepsze rozwiązania dotyczące zmieniania konfiguracji domyślnej
Ten temat ma na celu opisania obsługiwanych i nieobsługiwanych zmian do synchronizacji Azure AD Connect.

Konfiguracja utworzone przez program Azure AD Connect będzie działać "" dla większości środowisk, które synchronizują w lokalnej usłudze Active Directory z usługą Azure AD. Jednak w niektórych przypadkach należy zastosować niektóre zmiany konfiguracji do zaspokojenia potrzeb określonego lub wymagań.

## <a name="changes-to-the-service-account"></a>Zmiany na koncie usługi
Synchronizacja programu Azure AD Connect jest uruchomiony w ramach konta usługi, tworzone przez Kreatora instalacji. To konto usługi przechowuje klucze szyfrowania do bazy danych używane przez synchronizacji. Jest tworzony z 127 znaków długie hasło, a hasło jest ustawiona na nie wygaśnie.

* Jest **nieobsługiwany** zmienić lub zresetować hasło do konta usługi. Sposób niszczy klucze szyfrowania i nie jest w stanie dostępu do bazy danych i nie jest możliwe jej uruchomienie.

## <a name="changes-to-the-scheduler"></a>Zmiany do harmonogramu
Począwszy od wersji z kompilacji 1.1 (luty 2016) można skonfigurować [harmonogramu](how-to-connect-sync-feature-scheduler.md) mieć cykl synchronizacji inną niż domyślna, 30 minut.

## <a name="changes-to-synchronization-rules"></a>Zmiany reguł synchronizacji
Kreator instalacji umożliwia konfigurację, która powinna działać w przypadku najbardziej typowych scenariuszy. W przypadku, gdy trzeba wprowadzić zmiany w konfiguracji, należy wykonać te zasady, aby nadal ma obsługiwanej konfiguracji.

> [!WARNING]
> W przypadku wprowadzenia zmian do domyślnych reguł synchronizacji następnie zmiany te zostaną zastąpione przy kolejnym uruchomieniu program Azure AD Connect jest aktualizowany, wynikiem wyniki nieoczekiwany i prawdopodobnie niechciane synchronizacji.

* Możesz [zmienić przepływy atrybutów](how-to-connect-sync-change-the-configuration.md#other-common-attribute-flow-changes) Jeśli przepływy atrybutów bezpośrednie domyślne nie są odpowiednie dla Twojej organizacji.
* Jeśli chcesz [nie przepływu atrybutu](how-to-connect-sync-change-the-configuration.md#do-not-flow-an-attribute) i Usuń wszystkie istniejące atrybutu wartości w usłudze Azure AD, a następnie należy utworzyć regułę dla tego scenariusza.
* [Wyłącz regułę synchronizacji niechciane](#disable-an-unwanted-sync-rule) zamiast usuwania go. Usunięto regułę są odtwarzane podczas uaktualniania.
* Aby [zmienić regułę out-of-box](#change-an-out-of-box-rule), należy utworzyć kopię oryginalnej reguły i wyłączyć zasadę out-of-box. Edytor reguł synchronizacji monitu i pomoże Ci.
* Eksportowanie reguł niestandardowych za pomocą narzędzia Synchronization Rules Editor. Edytor umożliwia za pomocą skryptu programu PowerShell, w którym można łatwo je ponownie utworzyć w scenariuszu odzyskiwania po awarii.

> [!WARNING]
> Reguły synchronizacji out-of-box mają odcisku palca. Jeśli wprowadzisz zmiany w tych reguł odcisk palca nie jest zgodny. Mogą wystąpić problemy w przyszłości, gdy użytkownik próbuje zastosować nowej wersji programu Azure AD Connect. Tylko zmiany sposobu, w których jest on opisany w tym artykule.

### <a name="disable-an-unwanted-sync-rule"></a>Wyłącz niechciane reguły synchronizacji
Nie usuwaj regułę synchronizacji out-of-box. Jego są odtwarzane podczas uaktualniania dalej.

W niektórych przypadkach Kreator instalacji tworzył konfigurację, która nie działa dla topologii. Na przykład jeśli masz topologią lasu zasobów dla konta, ale mają rozszerzenia schematu w lesie konta przy użyciu schematu programu Exchange, reguł dla programu Exchange są tworzone dla lasu konta i lasu zasobów. W takim przypadku należy wyłączyć zasadę synchronizacji dla programu Exchange.

![Reguły synchronizacji wyłączone](./media/how-to-connect-sync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Na powyższym rysunku Kreator instalacji wykryła stare schematu programu Exchange 2003 w lesie konta. To rozszerzenie schematu został dodany przed wprowadzeniem lasu zasobów w środowisku firmy Fabrikam. Aby upewnić się, że żadne atrybuty od starego wdrożenia programu Exchange są synchronizowane, reguły synchronizacji powinna być wyłączona, jak pokazano.

### <a name="change-an-out-of-box-rule"></a>Zmień reguły out-of-box
Jedyną sytuacją, należy zmienić regułę out-of-box jest, gdy zajdzie potrzeba zmiany reguł dołączania. Jeśli musisz zmienić przepływ atrybutu, należy utworzyć regułę synchronizacji o wyższym priorytecie niż reguły out-of-box. Tylko reguły, musisz sklonować praktycznie jest reguła **w z usługi AD — użytkownik przyłączyć**. Można zastąpić wszystkie reguły z regułę o wyższym priorytecie.

Jeśli musisz wprowadzić zmiany w regule out-of-box należy utworzyć kopię reguły out-of-box i Wyłącz istniejącą regułę. Następnie wprowadź zmiany do sklonowanego reguły. Edytor reguł synchronizacji korzystającą z tych kroków. Po otwarciu regułę out-of-box, są prezentowane za pomocą tego okna dialogowego:  
![Ostrzeżenie poza reguły pola](./media/how-to-connect-sync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Wybierz **tak** do utworzenia kopii reguły. Następnie zostanie otwarty sklonowany reguły.  
![Sklonowany reguły](./media/how-to-connect-sync-best-practices-changing-default-configuration/clonedrule.png)

W tej regule sklonowany wprowadź niezbędne zmiany w zakresie sprzężenia i przekształcenia.

## <a name="next-steps"></a>Kolejne kroki
**Tematy poglądowe**

* [Synchronizacja w programie Azure AD Connect: Zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
