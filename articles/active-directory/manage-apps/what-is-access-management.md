---
title: Zarządzanie dostępem do aplikacji przy użyciu usługi Azure AD | Microsoft Docs
description: Opisuje, w jaki sposób Azure Active Directory umożliwia organizacjom Określanie aplikacji, do których każdy użytkownik ma dostęp.
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
ms.openlocfilehash: 99c7947b6469f64f2ea05b2290305710db1ee796
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477108"
---
# <a name="managing-access-to-apps"></a>Zarządzanie dostępem do aplikacji
Ciągłe zarządzanie dostępem, Ocena użycia i raportowanie nadal są wyzwaniem, gdy aplikacja jest zintegrowana z systemem tożsamości w organizacji. W wielu przypadkach Administratorzy IT lub pomoc techniczna muszą zająć się trwającą aktywną rolą zarządzania dostępem do aplikacji. Czasami przypisanie jest wykonywane przez zespół IT ogólnego lub rozdziałowego. Często decyzja o przypisaniu jest przeznaczona do delegowania do producenta decyzji biznesowej, wymagając do zatwierdzenia przed przypisaniem.  Inne organizacje zainwestowali w integrację z istniejącym automatycznym systemem zarządzania tożsamościami i dostępem, takimi jak Access Control oparte na rolach (RBAC) lub Access Control opartych na atrybutach (ABAC). Rozwój integracji i reguł jest wyspecjalizowany i kosztowny. Monitorowanie lub raportowanie w ramach podejścia do zarządzania to własne oddzielne, kosztowne i złożone inwestycje.

## <a name="how-does-azure-active-directory-help"></a>Jak Azure Active Directory pomoc?
 Usługa Azure AD obsługuje szerokie zarządzanie dostępem do skonfigurowanych aplikacji, co pozwala organizacjom łatwo osiągnąć odpowiednie zasady dostępu w zakresie od automatycznego przypisania opartego na atrybutach (ABAC lub RBAC scenariusze) za pośrednictwem delegowania, a także Zarządzanie administratorami. Za pomocą usługi Azure AD można łatwo uzyskać złożone zasady, łącząc wiele modeli zarządzania dla jednej aplikacji, a nawet ponownie używać reguł zarządzania w aplikacjach z tymi samymi odbiorcami.

* [Dodawanie nowych lub istniejących aplikacji](add-gallery-app.md)

  Przypisanie aplikacji usługi Azure AD koncentruje się na dwóch podstawowych trybach przypisania:

* **Przypisanie indywidualne** Administrator IT z uprawnieniami administratora globalnego katalogu może wybrać poszczególne konta użytkowników i udzielić im dostępu do aplikacji.
* **Przypisanie oparte na grupach (tylko płatna usługa Azure AD)** Administrator IT z uprawnieniami administratora globalnego katalogu może przypisać grupę do aplikacji. Dostęp określonego użytkownika jest określany na podstawie tego, czy są członkami grupy w momencie próby uzyskania dostępu do aplikacji. Innymi słowy, administrator może efektywnie utworzyć regułę przypisania informującą o tym, że wszyscy bieżący członek przypisanej grupy ma dostęp do aplikacji. Za pomocą tej opcji przypisania Administratorzy mogą korzystać z dowolnej opcji zarządzania grupami usługi Azure AD, w tym [grup dynamicznych opartych na atrybutach](../fundamentals/active-directory-groups-create-azure-portal.md), zewnętrznych grup systemu (na przykład lokalnych Active Directory lub Workday) lub Grupy zarządzane przez administratora lub samoobsługowe. Pojedyncza grupa może być łatwo przypisana do wielu aplikacji, co zapewnia, że aplikacje z koligacją przypisania mogą udostępniać reguły przypisywania, co zmniejsza ogólną złożoność zarządzania. Należy zauważyć, że członkostwa w grupach zagnieżdżonych nie są obsługiwane w przypadku przypisywania do aplikacji w tej chwili.

Korzystając z tych dwóch trybów przypisywania, Administratorzy mogą uzyskać wszelkie pożądane podejście do zarządzania przydziałami.

W przypadku korzystania z usługi Azure AD raportowanie użycia i przypisywania jest w pełni zintegrowane, co umożliwia administratorom łatwe raportowanie stanu przypisania, błędów przypisań i nawet użycia.

## <a name="complex-application-assignment-with-azure-ad"></a>Złożone przypisanie aplikacji za pomocą usługi Azure AD
Weź pod uwagę aplikację, taką jak Salesforce. W wielu organizacjach usługi Salesforce są używane głównie przez zespoły marketingowe i sprzedaży. Często członkowie zespołu ds. marketingu mają wysoce uprzywilejowany dostęp do usługi Salesforce, natomiast członkowie zespołu ds. sprzedaży mają ograniczony dostęp. W wielu przypadkach szeroka populacja pracowników przetwarzających informacje ma ograniczony dostęp do aplikacji. Wyjątki dotyczące tych reguł komplikują się. Często jest to przywilej zespołu lidera marketingu lub sprzedaży, aby udzielić użytkownikowi dostępu lub zmienić jego role niezależnie od tych reguł ogólnych.

W usłudze Azure AD aplikacje, takie jak Salesforce, można wstępnie skonfigurować pod kątem logowania jednokrotnego (SSO) i automatycznej aprowizacji. Po skonfigurowaniu aplikacji administrator może wykonać akcję jednorazową w celu utworzenia i przypisania odpowiednich grup. W tym przykładzie administrator może wykonać następujące przypisania:

* [Grupy dynamiczne](../fundamentals/active-directory-groups-create-azure-portal.md) można zdefiniować do automatycznego reprezentowania wszystkich członków zespołów marketingowych i sprzedaży przy użyciu atrybutów, takich jak dział lub rola:
  
  * Wszyscy członkowie grup marketingowych zostaną przypisani do roli "Marketing" w usłudze Salesforce
  * Wszyscy członkowie grup zespołu sprzedaży zostaną przypisani do roli "sprzedaż" w usłudze Salesforce. Dodatkowe udoskonalenie mogą wykorzystywać wiele grup reprezentujących zespoły sprzedaży regionalnej przypisane do różnych ról usługi Salesforce.
* Aby włączyć mechanizm wyjątku, można utworzyć grupę samoobsługową dla każdej roli. Na przykład grupa "wyjątek marketingowy Marketing" może zostać utworzona jako Grupa samoobsługi. Grupa może być przypisana do roli marketingu usługi Salesforce, a zespół liderów marketingu może być właścicielem. Członkowie zespołu liderów marketingu mogą dodawać lub usuwać użytkowników, ustawiać zasady dołączania, a nawet zatwierdzać lub odrzucać żądania poszczególnych użytkowników do przyłączenia. Ten mechanizm jest obsługiwany przez pracownika przetwarzający informacje, który nie wymaga specjalistycznego szkolenia dla właścicieli lub członków.

W takim przypadku wszystkim przypisanym użytkownikom zostanie automatycznie przydzielona do usługi Salesforce, ponieważ zostaną one dodane do różnych grup, ich przypisanie roli zostanie zaktualizowane w usłudze Salesforce. Użytkownicy będą mogli odnajdywać usługi Salesforce i uzyskiwać do nich dostęp za pomocą panelu dostępu do aplikacji firmy Microsoft, klientów sieci Web pakietu Office, a nawet przez przejście na stronę logowania do swojej organizacji. Administratorzy mogą łatwo wyświetlać stan użytkowania i przypisywania za pomocą funkcji raportowania usługi Azure AD.

Administratorzy mogą [korzystać z dostępu warunkowego usługi Azure AD](../active-directory-conditional-access-azure-portal.md) w celu ustawiania zasad dostępu dla konkretnych ról. Te zasady mogą obejmować, czy dostęp jest dozwolony poza środowiskiem firmy, a nawet uwierzytelnianie wieloskładnikowe lub wymagania dotyczące urządzeń w celu uzyskania dostępu w różnych przypadkach.

## <a name="next-steps"></a>Następne kroki
* [Ochrona aplikacji przy użyciu dostępu warunkowego](../active-directory-conditional-access-azure-portal.md)
* [Samoobsługowe zarządzanie grupami/SSAA](../users-groups-roles/groups-self-service-management.md)
