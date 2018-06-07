---
title: 'Synchronizacja programu Azure AD Connect: Zmiana domyślnej konfiguracji | Dokumentacja firmy Microsoft'
description: Zawiera najlepsze rozwiązania do zmiany domyślnej konfiguracji synchronizacji usługi Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 7638a031-1635-4942-94c3-fce8f09eed5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2c2fc3bcba4b685fba36683f89c0b6ad877dbb1d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595142"
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Synchronizacja programu Azure AD Connect: najlepsze rozwiązania dotyczące zmieniania konfiguracji domyślnej
Celem tego tematu jest opisano obsługiwane i nieobsługiwane zmiany do synchronizacji Azure AD Connect.

Utworzono przy użyciu usługi Azure AD Connect konfigurację działa "jest" dla większości środowisk, które synchronizują lokalnymi usługi Active Directory z usługą Azure AD. Jednak w niektórych przypadkach należy zastosować pewnych zmian do konfiguracji przez potrzebne lub wymagań.

## <a name="changes-to-the-service-account"></a>Zmiany konta usługi
Synchronizacja programu Azure AD Connect jest uruchomiony na koncie usługi tworzone przez Kreatora instalacji. To konto usługi przechowuje klucze szyfrowania do bazy danych używane przez synchronizacji. Jest tworzony z 127 znaków długie hasło, a hasło nie wygasa.

* Jest **nieobsługiwany** zmienić lub zresetować hasło do konta usługi. W ten sposób niszczy kluczy szyfrowania i nie będzie mógł uzyskać dostępu do bazy danych i nie jest możliwe jej uruchomienie.

## <a name="changes-to-the-scheduler"></a>Zmiany do harmonogramu
Począwszy od wersji z kompilacji 1.1 (luty 2016) można skonfigurować [harmonogramu](active-directory-aadconnectsync-feature-scheduler.md) mają cykl synchronizacji inną niż domyślna 30 minut.

## <a name="changes-to-synchronization-rules"></a>Zmiany reguły synchronizacji
Kreator instalacji umożliwia konfigurację, w której ma działać w przypadku najbardziej typowych scenariuszy. W przypadku, gdy trzeba wprowadzić zmiany w konfiguracji, należy wykonać te reguły, aby nadal ma obsługiwanej konfiguracji.

* Możesz [zmienić przepływów atrybutów](active-directory-aadconnectsync-change-the-configuration.md#other-common-attribute-flow-changes) Jeśli przepływy atrybutów bezpośredniego domyślne nie są odpowiednie dla Twojej organizacji.
* Jeśli chcesz [nie przepływu atrybutu](active-directory-aadconnectsync-change-the-configuration.md#do-not-flow-an-attribute) i usuń wszelkie istniejący atrybut wartości w usłudze Azure AD, a następnie należy utworzyć regułę dla tego scenariusza.
* [Wyłącz niechciane regułę synchronizacji](#disable-an-unwanted-sync-rule) zamiast usuwania. Usunięto regułę zostaje odtworzone podczas uaktualniania.
* Aby [zmienić regułę out-of-box](#change-an-out-of-box-rule), należy utworzyć kopię oryginalnej reguły i wyłączyć regułę out-of-box. Edytor reguł synchronizacji monity i pomaga.
* Eksportowanie reguł niestandardowych za pomocą edytora reguły synchronizacji. Edytor dostarcza skrypt programu PowerShell, w którym można łatwo utworzyć je ponownie w przypadku odzyskiwania po awarii.

> [!WARNING]
> Reguły synchronizacji out-of-box mają odcisk palca. Jeśli wprowadzisz zmiany tych reguł odcisk palca nie jest zgodne. Mogą wystąpić problemy w przyszłości podczas próby zastosowania nową wersję programu Azure AD Connect. Tylko zmiany sposobu opisano w tym artykule.

### <a name="disable-an-unwanted-sync-rule"></a>Wyłącz niechciane reguły synchronizacji
Nie należy usuwać regułę synchronizacji out-of-box. Zostanie utworzona ponownie podczas uaktualniania dalej.

W niektórych przypadkach konfiguracji, który nie działa dla topologii ma wygenerowane przez Kreatora instalacji. Na przykład jeśli masz topologią lasu zasobów dla konta, ale rozszerzył schematu w lesie konta ze schematem programu Exchange, reguł dla programu Exchange są tworzone dla lasu kont i lasu zasobów. W takim przypadku należy wyłączyć zasadę synchronizacji dla programu Exchange.

![Reguła synchronizacji wyłączone](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Na powyższym rysunku Kreator instalacji znalazł starego schematu Exchange 2003 w lesie konta. To rozszerzenie schematu został dodany przed wprowadzeniem w firmie Fabrikam środowisku lasu zasobów. Aby zapewnić, że żadne atrybuty od starego wdrożenia programu Exchange są synchronizowane, reguła synchronizacji powinno zostać wyłączone, jak pokazano.

### <a name="change-an-out-of-box-rule"></a>Zmień reguły out-of-box
Tylko wtedy, należy zmienić regułę out-of-box jest, gdy trzeba będzie zmienić reguły sprzężenia. Jeśli musisz zmienić przepływu atrybutów, należy utworzyć regułę synchronizacji z wyższy priorytet niż zasady out-of-box. Tylko reguły musisz praktycznie klonowania jest reguła **w z usługi Active Directory — użytkownik przyłączyć**. Można zastąpić wszystkie reguły z regułą wyższy priorytet.

Jeśli chcesz wprowadzić zmiany w regule out-of-box należy utworzyć kopię reguły out-of-box i wyłączyć oryginalnej reguły. Następnie wprowadź zmiany do sklonowany reguły. Edytor reguł synchronizacji korzystającą z tych kroków. Po otwarciu regułę out-of-box prezentowany to okno dialogowe:  
![Ostrzeżenie poza reguły pola](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Wybierz **tak** można utworzyć kopii reguły. Reguła sklonowany następnie jest otwarty.  
![Sklonowany reguły](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

W tej regule sklonowany wprowadź niezbędne zmiany w zakresie sprzężenia i przekształcenia.

## <a name="next-steps"></a>Kolejne kroki
**Tematy poglądowe**

* [Synchronizacja programu Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](active-directory-aadconnectsync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)
