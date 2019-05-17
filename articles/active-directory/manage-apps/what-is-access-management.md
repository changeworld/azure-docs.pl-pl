---
title: Zarządzanie dostępem do aplikacji przy użyciu usługi Azure AD | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak usługi Azure Active Directory umożliwia organizacjom określić aplikacje, do których każdy użytkownik ma dostęp.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 851b9ab3604e851bcaf4f22d031c51558b15e0e6
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826001"
---
# <a name="managing-access-to-apps"></a>Zarządzanie dostępem do aplikacji
Ciągły dostęp do zarządzania, użycie oceny i raportowania nadal żądanie po aplikacji jest zintegrowany system obsługi tożsamości organizacji. W wielu przypadkach administratorzy IT lub pomocą techniczną wymagać trwającą aktywną rolę w zarządzaniu dostępem do aplikacji. Czasami przydziału jest wykonywane przez zespół IT ogólne lub działów. Często decyzja przydziału jest przeznaczona do należy delegować domenę do osoba podejmująca decyzje biznesowe, wymaganie zatwierdzenia przed IT sprawia, że przypisanie.  Innym organizacjom inwestowanie w integracji z istniejących automatycznych tożsamościami i dostępem system zarządzania, takich jak kontrola dostępu oparta na rolach (RBAC) lub atrybutu-Based Access Control (ABAC). Integracji i programowania reguły zwykle wyspecjalizowanych i drogich. Monitorowania i raportowania w obu podejścia do zarządzania jest inwestycji oddzielne kosztownych i złożonych.

## <a name="how-does-azure-active-directory-help"></a>Jak pomaga usługi Azure Active Directory?
 Usługi Azure AD obsługuje zarządzanie doskonały dostęp skonfigurowanych aplikacji, umożliwiając organizacjom łatwo korzystać z zasad odpowiednich uprawnień dostępu — od przypisania automatycznego, oparte na atrybutach (scenariusze ABAC lub RBAC) za pośrednictwem delegowania oraz, takich jak Zarządzanie administratorami. Za pomocą usługi Azure AD można łatwo osiągnąć złożone zasady, łącząc wiele modeli zarządzania dla pojedynczej aplikacji, a nawet można ponownie użyć zasad zarządzania aplikacji przy użyciu tej samej grupy odbiorców.

* [Dodanie nowej lub istniejącej aplikacji](configure-single-sign-on-portal.md)

  Przypisania aplikacji usługi Azure AD koncentruje się na dwóch trybach podstawowy przypisania:

* **Przypisanie indywidualne** administratora z uprawnieniami administratora globalnego katalogu można wybrać poszczególnych kont użytkowników i przyznać im dostęp do aplikacji.
* **Przypisanie oparte na grupach, (płatność tylko usługi Azure AD)** administratora z uprawnieniami administratora globalnego katalogu można przypisać grupę do aplikacji. Dostęp do określonych użytkowników zależy od tego, czy są członkowie grupy w czasie użytkownik próbuje uzyskać dostęp do aplikacji. Innymi słowy administrator może skutecznie utworzyć reguły przypisania z informacją "bieżący członkom grupy przypisane ma dostęp do aplikacji". Przy użyciu tej opcji przypisania, Administratorzy mogą korzystać z dowolnej opcji zarządzania grupami usługi Azure AD, w tym [grupy dynamiczne oparte na atrybutach](../fundamentals/active-directory-groups-create-azure-portal.md), grup zewnętrznych systemu (na przykład w lokalnej usługi Active Directory lub pracy), lub grupy, zarządzane przez administratora lub zarządzanych eksploatacyjnych samoobsługowego. Pojedynczej grupy można łatwo przypisać do wielu aplikacji, upewniając się, aplikacje z użyciem koligacji przypisania udostępnić reguły przypisania, co zmniejsza ogólną złożoność zarządzania. Należy pamiętać, że członkostwo w grupach zagnieżdżonych nie są obsługiwane w przypadku oparte na grupach przypisywania do aplikacji, w tym momencie.

Za pomocą tych trybów dwóch przypisania, Administratorzy mogą osiągnąć wszystkie przypisania pożądane podejścia do zarządzania.

Za pomocą usługi Azure AD użycia i przypisanie raportowania jest w pełni zintegrowana, dzięki czemu nawet administratorzy łatwo sporządzić raport na temat stanu przypisania, błędy przypisania i nawet użycia.

## <a name="complex-application-assignment-with-azure-ad"></a>Przypisanie aplikacji złożonych z usługą Azure AD
Rozważmy aplikację, takich jak Salesforce. W wielu organizacjach Salesforce jest używany głównie przez zespoły sprzedaży i marketingu. Często członków zespołu ds. marketingu wysoce ma uprzywilejowany dostęp do usługi Salesforce, gdy członkowie zespołu sprzedaży mają ograniczony dostęp. W wielu przypadkach szerokiego populacji pracowników przetwarzających informacje ma ograniczony dostęp do aplikacji. Wyjątki od tych reguł skomplikować. Często jest prawa poszczególnych zespoły kierownicze marketingu i sprzedaży, aby udzielić użytkownikowi dostępu lub zmień ich ról, niezależnie od tych ogólnych reguł.

Z usługą Azure AD aplikacji, takich jak Salesforce, można wstępnie skonfigurowane dla logowania jednokrotnego (SSO) i automatyczną aprowizację. Gdy aplikacja jest skonfigurowana, Administrator może przejąć Akcja jednorazowa, aby utworzyć i przypisać odpowiednie grupy. W tym przykładzie administrator można wykonać następujące przypisania:

* [Grupy dynamiczne](../fundamentals/active-directory-groups-create-azure-portal.md) można zdefiniować, aby automatycznie reprezentują wszyscy członkowie zespołów sprzedaży i marketingu, przy użyciu atrybutów, takich jak dział lub ról:
  
  * Wszyscy członkowie grupy marketing, może zostać przypisana do roli "marketing" w usłudze Salesforce
  * Wszyscy członkowie zespołu sprzedaży, które grupy zostanie przypisana do roli "terminy sprzedaż" w usłudze Salesforce. Dalsze dopracowanie można użyć wielu grup, które reprezentują regionalnych zespołów sprzedaży, przypisane do różnych ról usługi Salesforce.
* Aby włączyć mechanizm wyjątków, można utworzyć grupy samoobsługi dla każdej roli. Na przykład "Salesforce marketingowych wyjątek" grupy tworzyć jako grupa samoobsługi. Grupy można przypisać do marketingu rola usługi Salesforce i zespołu ds. marketingu kierownictwo może się właściciela. Członkowie zespołu ds. marketingu kierownictwo może dodać lub usunąć użytkowników, ustawić zasady dołączania lub nawet zatwierdzenia lub odmowy żądania dołączenia do poszczególnych użytkowników. Ten mechanizm jest obsługiwany za pośrednictwem informacji procesu roboczego odpowiednie środowisko, które wymagają specjalistycznego szkolenia dla właścicieli i członków.

W tym przypadku wszystkich przypisanych użytkowników będzie automatycznie aprowizowane do usługi Salesforce, gdy są one dodawane do różnych grup, ich przypisanie roli może zostać zaktualizowane w usłudze Salesforce. Użytkownicy będą mogli odnaleźć i dostęp do usługi Salesforce, za pomocą panelu dostępu do aplikacji firmy Microsoft, klientów sieci web pakietu Office, lub nawet przechodząc do ich organizacji strony logowania usługi Salesforce. Administratorzy będzie można łatwo wyświetlić stan użycia i przydziałów za pomocą raportowania usługi Azure AD.

Administratorzy mogą stosować [dostępu warunkowego usługi Azure AD](../active-directory-conditional-access-azure-portal.md) można ustawić zasady dostępu dla określonych ról. Zasady te mogą obejmować, czy dostęp jest dozwolony w środowisku firmowym i nawet Multi-Factor Authentication urządzenie wymagania lub uzyskanie dostępu w różnych przypadkach poza.

## <a name="next-steps"></a>Kolejne kroki
* [Ochrona aplikacji przy użyciu dostępu warunkowego](../active-directory-conditional-access-azure-portal.md)
* [Zarządzanie grupami samoobsługi/SSAA](../users-groups-roles/groups-self-service-management.md)
