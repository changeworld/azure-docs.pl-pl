---
title: Środowisko Windows wirtualnego pulpitu (wersja zapoznawcza) — platformy Azure
description: Podstawowe elementy w środowisku Windows wirtualnego Desktop w wersji zapoznawczej.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: ceed6a8bb74206b7c6689ce542482148800e4ba9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403514"
---
# <a name="windows-virtual-desktop-preview-environment"></a>Środowisko Windows wirtualnego pulpitu (wersja zapoznawcza)

Windows wirtualnego pulpitu (wersja zapoznawcza) to usługa, która zapewnia użytkownikom łatwy i bezpieczny dostęp do ich pulpity nawigacyjne i wirtualizacją RemoteApps. Ten temat informuje o nieco więcej informacji na temat ogólną strukturę środowiska wirtualnego pulpitu Windows.

## <a name="tenants"></a>Dzierżaw

Dzierżawa Windows pulpitu wirtualnego jest podstawowym interfejsem do zarządzania środowiskiem Windows pulpitu wirtualnego. Każda dzierżawa Windows pulpitu wirtualnego musi być skojarzony z usługą Azure Active Directory zawierającą użytkowników, którzy będą Zaloguj się do środowiska. W dzierżawie pulpitu wirtualnego Windows możesz przystąpić do tworzenia pul hosta do uruchamiania obciążeń użytkowników.

## <a name="host-pools"></a>Pule hosta

Pula hosta jest kolekcją maszyn wirtualnych platformy Azure, które rejestrują się pulpitu wirtualnego Windows jako hosty sesji podczas uruchamiania agenta Windows pulpitu wirtualnego. Wszystkie maszyny wirtualne hosta sesji w puli hosta powinien źródło za pomocą tego samego obrazu dla spójne środowisko użytkownika.

Może to być jeden z dwóch typów puli hosta:

- Osobisty, kontekstowy, gdzie każdy host sesji jest przypisane do poszczególnych użytkowników.
- W puli, gdzie hosty sesji może akceptować połączeń z każdego użytkownika uprawnień do grupy aplikacji w ramach puli hosta.

Możesz ustawić dodatkowe właściwości puli hosta na zmianę zachowania równoważenia obciążenia, ile sesji, każdy host sesji może trwać, i co użytkownik może zrobić, aby hosty sesji w puli hosta, gdy użytkownik jest zalogowany celu ich sesji pulpitu wirtualnego Windows. Możesz kontrolować zasoby opublikowane dla użytkowników za pomocą grup aplikacji.

## <a name="app-groups"></a>Grupy aplikacji

Grupy aplikacji jest logicznym grupowaniem aplikacji zainstalowanych na hostach sesji w puli hosta. Grupa aplikacji może być jeden z dwóch typów:

- Połączenia programów RemoteApp, gdzie użytkownicy uzyskują dostęp do RemoteApps indywidualnie wybierz i opublikować w grupie aplikacji
- Pulpitu, w którym użytkownicy uzyskują dostęp do pełnego pulpitu

Domyślnie grupy aplikacji komputerowej (o nazwie "Grupa aplikacji pulpitu") jest tworzony automatycznie podczas tworzenia puli hosta. W dowolnym momencie można usunąć tej grupy aplikacji. Jednak nie można utworzyć innej grupy aplikacji klasycznej w puli hosta, gdy istnieje grupa aplikacji klasycznej. Aby opublikować RemoteApps, należy utworzyć grupę aplikacji programów RemoteApp. Można utworzyć wiele grup aplikacji programów RemoteApp scenariuszy innego procesu roboczego. Może również zawierać nakładających się RemoteApps różnych grup aplikacji programów RemoteApp.

Do publikowania zasobów dla użytkowników, należy je przypisać do grup aplikacji. Podczas przypisywania użytkowników do grupy aplikacji, należy wziąć pod uwagę następujące elementy:

- Nie można przypisać użytkownika do grupy aplikacji komputerowej i programów RemoteApp grupy aplikacji w tej samej puli hosta.
- Użytkownika można przypisać do wielu grup aplikacji w ramach tej samej puli hosta, a ich źródło danych będzie nagromadzenia obu grup aplikacji.

## <a name="tenant-groups"></a>Dzierżawa grupy

W Windows pulpitu wirtualnego dzierżawy Windows pulpitu wirtualnego jest, gdzie się dzieje w większości instalacji i konfiguracji. Dzierżawy pulpitu wirtualnego Windows zawiera pule hosta, grupy aplikacji i przypisań użytkowników grupy aplikacji. Jednak może być pewne sytuacje, gdzie trzeba zarządzać wieloma dzierżawami pulpitu wirtualnego Windows jednocześnie, szczególnie w przypadku dostawcy usług chmury (CSP) albo jako partnera hostingu. W takich sytuacjach można użyć niestandardową grupę dzierżawy pulpitu wirtualnego Windows umieszczenie wszystkich klientów pulpitu wirtualnego Windows dzierżaw i centralnie zarządzać dostępem. Jednak jeśli zarządzasz pojedynczej dzierżawy Windows pulpitu wirtualnego, pojęcie grup dzierżawy nie ma zastosowania i nadal działać i zarządzanie dzierżawą, która znajduje się w grupie domyślnej dzierżawy.

## <a name="end-users"></a>Użytkownicy końcowi

Po przypisaniu użytkowników do ich grup aplikacji, można nawiązać wdrożenie pulpitu wirtualnego Windows z któregokolwiek z klientów Windows pulpitu wirtualnego.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o delegowany dostęp i jak przypisać role do użytkowników w [delegowany dostęp w wersji zapoznawczej pulpitu wirtualnego Windows](delegated-access-virtual-desktop.md).

Aby dowiedzieć się, jak skonfigurować swoją dzierżawę, pulpitu wirtualnego Windows, zobacz [utworzyć dzierżawę w wersji zapoznawczej pulpitu wirtualnego Windows](tenant-setup-azure-active-directory.md).

Aby dowiedzieć się, jak nawiązać połączenie z pulpitem wirtualnym Windows, zobacz jeden z następujących artykułów:

- [Nawiązać połączenie z klienta pulpitu zdalnego na Windows 7 i Windows 10](connect-windows-7-and-10.md)
- [Nawiązywać połączenia z klientem w sieci web Windows wirtualnego pulpitu (wersja zapoznawcza)](connect-web.md)
