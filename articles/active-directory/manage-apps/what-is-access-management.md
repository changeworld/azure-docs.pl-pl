---
title: Zarządzanie dostępem do aplikacji przy użyciu usługi Azure AD | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak Azure Active Directory umożliwia organizacjom określić aplikacje, do których każdy użytkownik ma dostęp.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: barbkess
ms.openlocfilehash: 019bdecd315f6da91fef8e7ed5d088e328253ef6
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35304087"
---
# <a name="managing-access-to-apps"></a>Zarządzanie dostępem do aplikacji
Zarządzanie dostępem trwającą, użycia oceny i raportowania nadal żądanie po aplikacji jest zintegrowany system obsługi tożsamości organizacji. W wielu przypadkach administratorzy IT lub pomocą techniczną wymagać trwającą aktywną rolę w zarządzaniu dostępu do aplikacji. Czasami przypisania jest wykonywane przez zespół IT ogólne lub działów. Często decyzji przypisania ma na celu delegować do podejmującą biznesowych, wymaganie zatwierdzenia przed sprawia, że IT przypisania.  Inne organizacje inwestycji w Integracja z istniejącą automatycznych tożsamościami i dostępem system zarządzania, takich jak kontrola dostępu oparta na rolach (RBAC) lub kontroli dostępu na podstawie atrybutu (ABAC). Integracji i rozwoju reguły zwykle specjalistyczne i kosztowne. Monitorowania i raportowania w obu podejścia do zarządzania jest inwestycji oddzielne, kosztowne i skomplikowane.

## <a name="how-does-azure-active-directory-help"></a>Jak pomaga usługi Azure Active Directory?
 Usługi Azure AD obsługuje zarządzanie szeroką gamę dostępu skonfigurowanych aplikacji umożliwia organizacjom łatwe osiągnięcia zakresu z automatycznego na podstawie atrybutów przydziału (scenariusze ABAC lub RBAC) do delegowania i, w tym zasady odpowiednich uprawnień dostępu Zarządzanie administratorami. Z usługą Azure AD można łatwo uzyskać złożone zasady łączenie wielu modeli zarządzania dla jednej aplikacji i może nawet ponownie użyć zasad zarządzania dla aplikacji z tej samej grupy odbiorców.

* [Dodawanie nowych lub istniejących aplikacji](configure-single-sign-on-portal.md)

 Przypisanie aplikacji usługi Azure AD koncentruje się na dwóch trybów przypisania głównej:

* **Przypisanie poszczególnych** administratora z uprawnieniami administratora globalnego katalogu można wybrać poszczególnych kont użytkowników i przyznano im dostęp do aplikacji.
* **Przypisanie oparte na grupach (płatnej tylko usługi Azure AD)** administratora z uprawnieniami administratora globalnego katalogu można przypisać grupę aplikacji. Dostęp do określonych użytkowników zależy od tego, czy są oni członkami grupy w momencie próbują uzyskać dostęp do aplikacji. Innymi słowy administrator może efektywnie utworzyć regułę przypisania, podając "wszelkie bieżący element członkowski grupy przypisanej ma dostęp do aplikacji". Użycie tej opcji przypisania, Administratorzy mogą korzystać z tych opcji zarządzania grupami usługi Azure AD, w tym [opartych na atrybutach grup dynamicznych](../active-directory-groups-create-azure-portal.md), grup systemu zewnętrznego (na przykład w infrastrukturze lokalnej usługi Active Directory lub pracy), lub zarządzana przez administratora lub zarządzane eksploatacyjnych niezależne grup. Pojedynczej grupy mogą być łatwo przypisane do wielu aplikacji, zmniejsza się złożoność zarządzania ogólną zapewnienie, że aplikacje przypisania koligacji mogą współużytkować reguły przypisania. Należy pamiętać, że grup zagnieżdżonych członkostwa nie są obsługiwane dla grupy przypisywania do aplikacji w tej chwili.

Korzystanie z tych trybów dwóch przypisania, Administratorzy można osiągnąć wszystkie przypisania pożądane podejścia do zarządzania.

Z usługą Azure AD użycia i przydział raportowania jest w pełni zintegrowana, umożliwiające administratorom łatwe raport dotyczący stan przypisania, błędy przydziału i użycia nawet.

## <a name="complex-application-assignment-with-azure-ad"></a>Przypisanie złożonych aplikacji z usługą Azure AD
Należy wziąć pod uwagę aplikacji, takie jak Salesforce. W wielu organizacjach Salesforce jest używany głównie przez organizacje sprzedaży i marketingu. Często członkowie zespołu marketingu wysokiej mają uprzywilejowany dostęp do usług Salesforce, podczas gdy członkowie zespołu sprzedaży mają ograniczony dostęp do. W wielu przypadkach szerokie populacji pracowników przetwarzających informacje ograniczyć dostęp do aplikacji. Wyjątki od tych reguł skomplikować kwestii. Często jest prawa poszczególnych zespołów kierowniczej marketing lub sprzedaży, aby udzielić użytkownikowi dostępu lub zmienić ich ról niezależnie od zasady ogólne.

Z usługą Azure AD można wstępnie skonfigurowane dla rejestracji jednokrotnej (SSO) i automatyczne Inicjowanie obsługi aplikacji, takich jak Salesforce. Gdy aplikacja jest skonfigurowana, Administrator może przejąć Akcja jednorazowa, aby utworzyć i przypisać odpowiednie grupy. W tym przykładzie administrator może wykonać następujące przypisania:

* [Grupami dynamicznymi](../active-directory-groups-create-azure-portal.md) można zdefiniować automatycznie reprezentujący wszystkie elementy członkowskie zespołami sprzedaży i marketingu, za pomocą atrybutów, takich jak dział lub roli:
  
  * Wszyscy członkowie grupy marketing, może zostać przypisana do roli "marketing" w usłudze Salesforce
  * Wszyscy członkowie zespołu sprzedaży, które grupy może zostać przypisana do roli "sprzedaż" w usłudze Salesforce. Dalsze dopracowanie można użyć wielu grup, które reprezentują regionalnych zespoły przypisane do różnych ról usług Salesforce.
* Aby włączyć mechanizm wyjątków, można utworzyć grupy samoobsługi dla każdej roli. Na przykład grupę "Salesforce marketingu wyjątek" można utworzyć jako grupa samoobsługi. Grupy można przypisać do roli marketing Salesforce i marketingu kadry kierowniczej mogą być dokonywane właścicieli. Członkami marketing kadry kierowniczej można dodać lub usunąć użytkowników, ustawić zasady sprzężenia, lub nawet zatwierdzanie lub odrzucanie żądań poszczególnych użytkowników, aby dołączyć. Jest to obsługiwane przez środowisko odpowiednie procesu roboczego informacje, które nie wymagają specjalistyczne szkolenie właścicieli lub elementy członkowskie.

W takim przypadku wszystkich użytkowników przypisanych będzie automatycznie przygotowana do usług Salesforce, gdy są one dodawane do różnych grup, będzie można aktualizować ich przypisanie roli w usłudze Salesforce. Użytkownicy będą mogli na potrzeby odnajdywania i uzyskiwać dostęp do usług Salesforce, za pomocą panelu dostępu do aplikacji firmy Microsoft, klientom sieci web pakietu Office, lub nawet przechodząc do ich organizacyjnej strony logowania usługi Salesforce. Administratorzy będą mogli w łatwy sposób wyświetlania stanu użycia i przydział, za pomocą raportów usługi Azure AD.

Administratorzy mogą stosować [dostępu warunkowego dla usługi Azure AD](../active-directory-conditional-access-azure-portal.md) można ustawić zasady dostępu dla określonych ról. Te zasady mogą obejmować, czy dostęp jest dozwolony poza środowiska firmy i nawet uwierzytelnianie wieloskładnikowe wymagania lub urządzenia do uzyskania dostępu w przypadku różnych.

## <a name="next-steps"></a>Kolejne kroki

* [Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](../active-directory-apps-index.md)
* [Ochrona aplikacji przy użyciu dostępu warunkowego](../active-directory-conditional-access-azure-portal.md)
* [Zarządzanie grupami samoobsługi/SSAA](../active-directory-accessmanagement-self-service-group-management.md)
