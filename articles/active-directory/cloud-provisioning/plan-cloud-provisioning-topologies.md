---
title: Azure AD Connect obsługiwane topologie i scenariusze aprowizacji w chmurze
description: Ten temat zawiera opis wymagań wstępnych i wymagania sprzętowe dotyczące aprowizacji w chmurze.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 142974423816b07d754a5425017aedc3195e2f4e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793999"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>Azure AD Connect obsługiwane topologie i scenariusze aprowizacji w chmurze
W tym artykule opisano różne topologie lokalne i Azure Active Directory (Azure AD) korzystające Azure AD Connect aprowizacji w chmurze. Ten artykuł zawiera tylko obsługiwane konfiguracje i scenariusze.

> [!IMPORTANT]
> Firma Microsoft nie obsługuje modyfikacji ani współpracy Azure AD Connect aprowizacji w chmurze poza konfiguracją lub akcjami, które są formalnie udokumentowane. Każda z tych konfiguracji lub akcji może spowodować niespójny lub nieobsługiwany stan Azure AD Connect aprowizacji w chmurze. W związku z tym firma Microsoft nie może świadczyć pomocy technicznej w przypadku takich wdrożeń.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Zapamiętaj o wszystkich scenariuszach i topologii
Poniżej znajduje się lista informacji, które należy wziąć pod uwagę podczas wybierania rozwiązania.

- Użytkownicy i grupy muszą być jednoznacznie identyfikowane we wszystkich lasach
- Nie występuje dopasowanie między lasami a obsługą chmury
- Użytkownik lub grupa musi być reprezentowana tylko raz we wszystkich lasach
- Zakotwiczenie źródła dla obiektów jest wybierane automatycznie.  Używa on usługi MS-DS-ConsistencyGuid, jeśli jest obecny, w przeciwnym razie jest używany element ObjectGUID.
- Nie można zmienić atrybutu, który jest używany dla kotwicy źródłowej.



## <a name="multi-forest-single-azure-ad-tenant"></a>Wiele lasów, pojedyncza dzierżawa usługi Azure AD
![Topologia dla wielu lasów i pojedynczej dzierżawy](media/plan-cloud-provisioning-topologies/multi-forest.png)

Najbardziej typową topologią jest wiele lasów usługi AD, z co najmniej jedną domeną i jedną dzierżawą usługi Azure AD.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Istniejący Las z Azure AD Connectm, nowym lasem z obsługą chmury
![Topologia jednego lasu i pojedynczej dzierżawy](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

Ten scenariusz jest podobny do scenariusza obejmującego wiele lasów, jednak ten element obejmuje istniejące środowisko Azure AD Connect, a następnie umieszcza je w nowym lesie przy użyciu Azure AD Connect aprowizacji w chmurze.  Aby zapoznać się z przykładem tego scenariusza, zobacz [Samouczek: istniejący Las z pojedynczą dzierżawą usługi Azure AD](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>Pilotażowe Azure AD Connect aprowizacji w chmurze w istniejącym hybrydowym lesie usługi AD
Topologia ![jednego lasu i pojedynczej dzierżawy](media/plan-cloud-provisioning-topologies/migrate.png) scenariusz pilotażowy obejmuje istnienie zarówno Azure AD Connect, jak i Azure AD Connect aprowizacji w chmurze w tym samym lesie i odpowiednio określające zakres użytkowników i grup. Uwaga: obiekt powinien znajdować się w zakresie tylko w jednym z narzędzi. 

Aby zapoznać się z przykładem tego scenariusza, zobacz [Samouczek: pilotaż Azure AD Connect aprowizacji w chmurze w istniejącym synchronizowanym lesie usługi AD](tutorial-pilot-aadc-aadccp.md)

## <a name="single-forest-single-azure-ad-tenant"></a>Pojedynczy las, pojedyncza dzierżawa usługi Azure AD
![Topologia jednego lasu i pojedynczej dzierżawy](media/plan-cloud-provisioning-topologies/single-forest.png)

Najprostsza topologia to pojedynczy las lokalny, z co najmniej jedną domeną i jedną dzierżawą usługi Azure AD.  Przykład tego scenariusza można znaleźć w [samouczku: pojedynczy las z jedną dzierżawą usługi Azure AD](tutorial-single-forest.md)

## <a name="next-steps"></a>Następne kroki 

- [Co to jest inicjowanie obsługi?](what-is-provisioning.md)
- [Co to jest Azure AD Connect aprowizacji w chmurze?](what-is-cloud-provisioning.md)

