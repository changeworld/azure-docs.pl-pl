---
title: Środowisko pulpitu wirtualnego systemu Windows — platforma Azure
description: Podstawowe elementy środowiska pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 33d058f028b7032f296ffcf82f0e5fe2c993e6fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127916"
---
# <a name="windows-virtual-desktop-environment"></a>Środowisko usługi Windows Virtual Desktop

Windows Virtual Desktop to usługa, która zapewnia użytkownikom łatwy i bezpieczny dostęp do ich zwirtualizowanych pulpitów i remoteapps. W tym temacie dowiesz się nieco więcej o ogólnej strukturze środowiska pulpitu wirtualnego systemu Windows.

## <a name="tenants"></a>Dzierżawy

Dzierżawa pulpitu wirtualnego systemu Windows jest podstawowym interfejsem do zarządzania środowiskiem pulpitu wirtualnego systemu Windows. Każda dzierżawa pulpitu wirtualnego systemu Windows musi być skojarzona z usługą Azure Active Directory zawierającą użytkowników, którzy zalogują się do środowiska. W dzierżawie pulpitu wirtualnego systemu Windows można rozpocząć tworzenie pul hostów w celu uruchamiania obciążeń użytkowników.

## <a name="host-pools"></a>Hosty

Pula hostów to zbiór maszyn wirtualnych platformy Azure, które rejestrują się na pulpicie wirtualnym systemu Windows jako hosty sesji po uruchomieniu agenta pulpitu wirtualnego systemu Windows. Wszystkie maszyny wirtualne hosta sesji w puli hosta powinny pochodzić z tego samego obrazu dla spójnego środowiska użytkownika.

Pula hostów może być jednym z dwóch typów:

- Osobiste, gdzie każdy host sesji jest przypisany do poszczególnych użytkowników.
- Połączone, gdzie hosty sesji mogą akceptować połączenia od dowolnego użytkownika autoryzowanego do grupy aplikacji w puli hostów.

Można ustawić dodatkowe właściwości w puli hostów, aby zmienić jego zachowanie równoważenia obciążenia, ile sesji może wykonać każdy host sesji i co użytkownik może zrobić z hostami sesji w puli hostów po zalogowaniu się do sesji pulpitu wirtualnego systemu Windows. Możesz kontrolować zasoby publikowane użytkownikom za pośrednictwem grup aplikacji.

## <a name="app-groups"></a>Grupy aplikacji

Grupa aplikacji to logiczne grupowanie aplikacji zainstalowanych na hostach sesji w puli hostów. Grupa aplikacji może być jednym z dwóch typów:

- RemoteApp, w którym użytkownicy uzyskują dostęp do aplikacji RemoteApps, które indywidualnie wybierasz i publikujesz w grupie aplikacji
- Pulpit, na którym użytkownicy uzyskują dostęp do pełnego pulpitu

Domyślnie grupa aplikacji klasycznych (o nazwie "Desktop Application Group") jest tworzona automatycznie przy każdym utworzeniu puli hostów. Możesz usunąć tę grupę aplikacji w dowolnym momencie. Nie można jednak utworzyć innej grupy aplikacji klasycznych w puli hostów, gdy istnieje grupa aplikacji klasycznych. Aby opublikować aplikacje RemoteApps, należy utworzyć grupę aplikacji RemoteApp. Można utworzyć wiele grup aplikacji usługi RemoteApp, aby dostosować się do różnych scenariuszy procesu roboczego. Różne grupy aplikacji RemoteApp mogą również zawierać nakładające się aplikacje RemoteApp.

Aby opublikować zasoby dla użytkowników, należy przypisać je do grup aplikacji. Podczas przypisywania użytkowników do grup aplikacji należy wziąć pod uwagę następujące kwestie:

- Nie można przypisać użytkownika zarówno do grupy aplikacji klasycznych, jak i do grupy aplikacji RemoteApp w tej samej puli hostów.
- Użytkownik może być przypisany do wielu grup aplikacji w tej samej puli hostów, a ich kanał będzie kumulacją obu grup aplikacji.

## <a name="tenant-groups"></a>Grupy dzierżawców

W programie Windows Virtual Desktop dzierżawa pulpitu wirtualnego systemu Windows jest miejscem, w którym odbywa się większość konfiguracji i konfiguracji. Dzierżawa pulpitu wirtualnego systemu Windows zawiera pule hostów, grupy aplikacji i przypisania użytkowników grupy aplikacji. Jednak mogą wystąpić pewne sytuacje, w których musisz zarządzać wieloma dzierżawami pulpitu wirtualnego systemu Windows jednocześnie, szczególnie jeśli jesteś dostawcą usług w chmurze (CSP) lub partnerem hostingowym. W takich sytuacjach można użyć niestandardowej grupy dzierżawy pulpitu wirtualnego systemu Windows, aby umieścić każdego z dzierżaw pulpitu wirtualnego systemu Windows klientów i centralnie zarządzać dostępem. Jeśli jednak zarządzasz tylko jedną dzierżawą pulpitu wirtualnego systemu Windows, koncepcja grupy dzierżawy nie ma zastosowania i możesz nadal obsługiwać dzierżawę i zarządzać nią, która istnieje w domyślnej grupie dzierżawy.

## <a name="end-users"></a>Użytkownicy końcowi

Po przypisaniu użytkowników do grup aplikacji mogą oni połączyć się z wdrożeniem pulpitu wirtualnego systemu Windows z dowolnym klientem pulpitu wirtualnego systemu Windows.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o dostępie delegowanym i przypisywaniu ról użytkownikom w [programie Delegowany dostęp na pulpicie wirtualnym systemu Windows](delegated-access-virtual-desktop.md).

Aby dowiedzieć się, jak skonfigurować dzierżawę pulpitu wirtualnego systemu Windows, zobacz [Tworzenie dzierżawy na pulpicie wirtualnym systemu Windows](tenant-setup-azure-active-directory.md).

Aby dowiedzieć się, jak połączyć się z pulpitem wirtualnym systemu Windows, zobacz jeden z następujących artykułów:

- [Łączenie z systemu Windows 10 lub Windows 7](connect-windows-7-and-10.md)
- [Łączenie z przeglądarki internetowej](connect-web.md)
