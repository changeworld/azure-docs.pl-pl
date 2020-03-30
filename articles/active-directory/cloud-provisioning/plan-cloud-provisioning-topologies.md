---
title: Obsługa topologii i scenariuszy w chmurze usługi Azure AD Connect
description: W tym temacie opisano wymagania wstępne i wymagania sprzętowe obsługi administracyjnej w chmurze.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 386af46bbee623d37bc914d2ee9130c914c6c885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620873"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>Obsługa topologii i scenariuszy w chmurze usługi Azure AD Connect
W tym artykule opisano różne lokalne i usługi Azure Active Directory (Azure AD) topologie, które używają inicjowania obsługi administracyjnej w chmurze usługi Azure AD Connect. Ten artykuł zawiera tylko obsługiwane konfiguracje i scenariusze.

> [!IMPORTANT]
> Firma Microsoft nie obsługuje modyfikowania lub obsługi inicjowania obsługi administracyjnej w chmurze usługi Azure AD Connect poza konfiguracjami lub akcjami, które są formalnie udokumentowane. Każda z tych konfiguracji lub akcji może spowodować niespójne lub nieobsługiwanie stanu inicjowania obsługi administracyjnej w chmurze usługi Azure AD Connect. W związku z tym firma Microsoft nie może świadczyć pomocy technicznej w przypadku takich wdrożeń.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>O wszystkich scenariuszach i topologiach należy pamiętać
Poniżej znajduje się lista informacji, o którym należy pamiętać podczas wybierania rozwiązania.

- Użytkownicy i grupy muszą być jednoznacznie identyfikowane we wszystkich lasach
- Dopasowywanie między lasami nie występuje w przypadku inicjowania obsługi administracyjnej w chmurze
- Użytkownik lub grupa musi być reprezentowana tylko raz we wszystkich lasach
- Zakotwiczenie źródłowe dla obiektów jest wybierane automatycznie.  Używa ms-DS-ConsistencyGuid jeśli jest obecny, w przeciwnym razie objectguid jest używany.
- Nie można zmienić atrybutu, który jest używany do zakotwiczenia źródła.

## <a name="single-forest-single-azure-ad-tenant"></a>Pojedynczy las, pojedyncza dzierżawa usługi Azure AD
![Topologia dla pojedynczego lasu i jednego dzierżawcy](media/plan-cloud-provisioning-topologies/single-forest.png)

Najprostszą topologią jest pojedynczy las lokalny z jedną lub wieloma domenami i jedną dzierżawą usługi Azure AD.  Na przykład w tym scenariuszu zobacz [Samouczek: Pojedynczy las z pojedynczą dzierżawą usługi Azure AD](tutorial-single-forest.md)


## <a name="multi-forest-single-azure-ad-tenant"></a>Wielolesowa, pojedyncza dzierżawa usługi Azure AD
![Topologia dla wielolesowej i pojedynczej dzierżawcy](media/plan-cloud-provisioning-topologies/multi-forest.png)

Powszechna topologia to wiele lasów usługi AD z jedną lub wieloma domenami i jedną dzierżawą usługi Azure AD.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Istniejący las z usługą Azure AD Connect, nowy las z aprowewaniem w chmurze
![Topologia dla pojedynczego lasu i jednego dzierżawcy](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

Ten scenariusz jest topologia jest podobny do scenariusza wielu lasów, jednak ten obejmuje istniejące środowisko usługi Azure AD Connect, a następnie wprowadzenie nowego lasu przy użyciu obsługi administracyjnej chmury usługi Azure AD Connect.  Na przykład w tym scenariuszu zobacz [Samouczek: Istniejący las z pojedynczą dzierżawą usługi Azure AD](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>Pilotowanie aprowizacji w chmurze usługi Azure AD Connect w istniejącym hybrydowym lesie usługi AD
![Topologia dla pojedynczego lasu](media/plan-cloud-provisioning-topologies/migrate.png) i pojedynczej dzierżawy Scenariusz pilotażowy obejmuje istnienie zarówno usługi Azure AD Connect, jak i inicjowania obsługi administracyjnej chmury usługi Azure AD Connect w tym samym lesie i odpowiednio określania zakresu użytkowników i grup. UWAGA: Obiekt powinien znajdować się w zakresie tylko w jednym z narzędzi. 

Na przykład w tym scenariuszu zobacz [Samouczek: Pilotażowe udostępnianie obsługi administracyjnej chmury usługi Azure AD Connect w istniejącym zsynchronizowanym lesie usługi AD](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)

