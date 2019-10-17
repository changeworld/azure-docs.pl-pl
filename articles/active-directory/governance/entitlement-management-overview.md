---
title: Co to jest zarządzanie upoważnieniami w usłudze Azure AD? (Wersja zapoznawcza) — Azure Active Directory
description: Zapoznaj się z omówieniem zarządzania uprawnieniami Azure Active Directory i sposobami korzystania z niego do zarządzania dostępem do grup, aplikacji i witryn usługi SharePoint Online dla użytkowników wewnętrznych i zewnętrznych.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/03/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: a467856550bf2deaab931b3fe2f54b7986f12f8a
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430295"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>Co to jest zarządzanie upoważnieniami w usłudze Azure AD? (Wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie prawami w usłudze Azure Active Directory (Azure AD) jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pracownicy w organizacjach potrzebują dostępu do różnych grup, aplikacji i witryn w celu wykonywania ich zadań. Zarządzanie tym dostępem jest trudne. W większości przypadków nie ma zorganizowanej listy wszystkich zasobów wymaganych przez użytkownika dla projektu. Menedżer projektu jest dobrym zrozumieniem wymaganych zasobów, osób, których to dotyczy, oraz czasu, przez jaki projekt będzie ostatni. Jednak Menedżer projektu zwykle nie ma uprawnień do zatwierdzania lub udzielania dostępu innym osobom. Ten scenariusz jest bardziej skomplikowany podczas próby pracy z zewnętrznymi osobami lub firmami.

Zarządzanie prawami Azure Active Directory (Azure AD) ułatwia zarządzanie dostępem do grup, aplikacji i witryn usługi SharePoint Online dla użytkowników wewnętrznych, a także użytkowników spoza organizacji.

Ten film wideo zawiera omówienie zarządzania uprawnieniami i jego wartości biznesowej:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="why-use-entitlement-management"></a>Dlaczego warto korzystać z zarządzania prawami?

Organizacje korporacyjne często napotykają problemy związane z zarządzaniem dostępem do zasobów, takich jak:

- Użytkownicy mogą nie wiedzieć, do jakiego dostępu powinni mieć
- Użytkownicy mogą mieć problemy z lokalizowaniem odpowiednich osób lub odpowiednich zasobów
- Gdy użytkownicy znajdą i uzyskają dostęp do zasobu, mogą uzyskać dostęp do niego dłużej niż jest to wymagane do celów służbowych

Te problemy są złożone dla użytkowników, którzy potrzebują dostępu z innego katalogu, takich jak użytkownicy zewnętrzni z organizacji łańcucha dostaw lub innych partnerów firmy. Na przykład:

- Organizacje mogą nie znać wszystkich konkretnych osób w innych katalogach, aby mogli je zapraszać
- Nawet jeśli organizacje mogły zaprosić tych użytkowników, organizacje mogą nie pamiętać, aby zarządzać wszystkimi dostępem użytkownika spójnie

Zarządzanie prawami w usłudze Azure AD może pomóc rozwiązać te wyzwania.

## <a name="what-can-i-do-with-entitlement-management"></a>Co mogę zrobić z zarządzaniem prawami?

Poniżej przedstawiono niektóre możliwości zarządzania uprawnieniami:

- Tworzenie pakietów powiązanych zasobów, które użytkownicy mogą żądać
- Zdefiniuj reguły żądania zasobów i czasu wygaśnięcia dostępu
- Zarządzanie cyklem życia dostępu zarówno dla użytkowników wewnętrznych, jak i zewnętrznych
- Delegowanie zarządzania zasobami
- Wyznaczanie osób zatwierdzających do zatwierdzania żądań
- Tworzenie raportów do śledzenia historii

Aby zapoznać się z omówieniem zarządzania tożsamościami i zarządzaniem prawami, Obejrzyj następujące wideo na konferencji z zapłonem 2018:

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>Jakie zasoby mogę zarządzać?

Poniżej przedstawiono typy zasobów, do których można zarządzać dostępem z zarządzaniem prawami:

- Grupy zabezpieczeń usługi Azure AD
- Grupy usługi Office 365
- Aplikacje dla przedsiębiorstw usługi Azure AD, w tym aplikacja SaaS i aplikacje zintegrowane z niestandardową obsługą Federacji lub aprowizacji
- Zbiory witryn i witryny usługi SharePoint Online

Można również kontrolować dostęp do innych zasobów, które są zależne od grup zabezpieczeń usługi Azure AD lub grup programu Office 365.  Na przykład:

- Można udzielić użytkownikom licencji na Microsoft Office 365 przy użyciu grupy zabezpieczeń usługi Azure AD w pakiecie dostępu i konfigurowania [licencjonowania opartego na grupach](../users-groups-roles/licensing-groups-assign.md) dla tej grupy.
- Można udzielić użytkownikom dostępu do zarządzania zasobami platformy Azure za pomocą grupy zabezpieczeń usługi Azure AD w pakiecie dostępu i tworzenia [przypisania roli platformy Azure](../../role-based-access-control/role-assignments-portal.md) dla tej grupy

## <a name="what-are-access-packages-and-policies"></a>Co to są pakiety i zasady dostępu?

Zarządzanie prawami wprowadza koncepcję *pakietu dostępu*. Pakiet dostępu to pakiet wszystkich zasobów, które użytkownik musi pracować nad projektem lub wykonywać zadania. Zasoby obejmują dostęp do grup, aplikacji lub witryn. Pakiety dostępu są używane do zarządzania dostępem do pracowników wewnętrznych, a także użytkowników spoza organizacji. Pakiety dostępu są definiowane w kontenerach o nazwie *wykazów*.

Pakiety dostępu zawierają również co najmniej jedną *zasadę*. Zasady definiują reguły lub guardrails w celu uzyskania dostępu do pakietu dostępu. Włączenie zasad wymusza, aby tylko Ci użytkownicy mieli dostęp do odpowiednich zasobów i przez właściwy czas.

![Dostęp do pakietu i zasad](./media/entitlement-management-overview/elm-overview-access-package.png)

W przypadku pakietu dostępu i jego zasad Menedżer pakietów dostępu definiuje:

- Zasoby
- Role, których użytkownicy potrzebują dla zasobów
- Użytkownicy wewnętrzni i partnerzy z użytkownikami zewnętrznymi, którzy mają prawo do żądania dostępu
- Proces zatwierdzania i użytkownicy, którzy mogą zatwierdzać lub odmówić dostępu
- Czas trwania dostępu użytkownika

Na poniższym diagramie przedstawiono przykład różnych elementów w usłudze zarządzania uprawnieniami. Przedstawia dwa przykładowe pakiety dostępu.

- **Pakiet Access 1** zawiera pojedynczą grupę jako zasób. Dostęp jest definiowany przy użyciu zasad, które umożliwiają uzyskanie dostępu do zestawu użytkowników w katalogu.
- **Pakiet Access 2** zawiera grupę, aplikację i witrynę usługi SharePoint Online jako zasoby. Dostęp jest definiowany przy użyciu dwóch różnych zasad. Pierwsze zasady umożliwiają zestawowi użytkowników w katalogu zażądanie dostępu. Druga zasada umożliwia użytkownikom w zewnętrznym katalogu zażądanie dostępu.

![Omówienie zarządzania uprawnieniami](./media/entitlement-management-overview/elm-overview.png)

## <a name="terminology"></a>Terminologia

Aby lepiej zrozumieć Zarządzanie uprawnieniami i dokumentację, należy zapoznać się z poniższymi postanowieniami.

| Termin lub pojęcie | Opis |
| --- | --- |
| Zarządzanie prawami | Usługa, która przypisuje, odwołuje i zarządza pakietami dostępu. |
| pakiet dostępu | Zbiór zasobów wymaganych przez zespół lub projekt i podlega zasadom. Pakiet dostępu jest zawsze zawarty w wykazie. |
| żądanie dostępu | Żądanie dostępu do zasobów w pakiecie dostępu. Żądanie zwykle przechodzi przez przepływ pracy. |
| policy | Zestaw reguł definiujących cykl życia dostępu, takich jak użytkownicy uzyskują dostęp, kto może zatwierdzić i jak długo użytkownicy mają dostęp. Przykładowe zasady obejmują dostęp pracownika i dostęp zewnętrzny. |
| pełnotekstowy | Kontener powiązanych zasobów i pakietów dostępu. |
| Wykaz ogólny | Wbudowany wykaz, który jest zawsze dostępny. Aby dodać zasoby do wykazu ogólnego, wymagane są pewne uprawnienia. |
| Zasoby | Zasób lub usługa (na przykład grupa Office, Grupa zabezpieczeń, aplikacja lub witryna usługi SharePoint Online), do której użytkownik może uzyskać uprawnienia. |
| Typ zasobu | Typ zasobu, który obejmuje grupy, aplikacje i witryny usługi SharePoint Online. |
| Rola zasobu | Kolekcja uprawnień skojarzonych z zasobem. |
| Katalog zasobów | Katalog, który ma co najmniej jeden zasób do udostępnienia. |
| przypisani użytkownicy | Przypisanie pakietu dostępu do użytkownika, tak aby użytkownik miał wszystkie role zasobów tego pakietu. |
| mogły | Proces tworzenia pakietu dostępu dostępnego dla użytkowników do żądania. |

## <a name="license-requirements"></a>Wymagania licencyjne

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Wyspecjalizowane chmury, takie jak Azure Government, Azure (Niemcy) i Azure (Chiny), nie są obecnie dostępne do użycia w tej wersji zapoznawczej.

### <a name="which-users-must-have-licenses"></a>Którzy użytkownicy muszą mieć licencje?

Dzierżawa musi mieć co najmniej tyle licencji na Azure AD — wersja Premium P2, ponieważ aktywni użytkownicy będą członkami. Aktywni użytkownicy należący do zarządzania prawami obejmują:

- Użytkownik inicjujący lub zatwierdził żądanie dotyczące pakietu dostępu.
- Użytkownik, któremu przypisano pakiet dostępu. 
- Użytkownik zarządzający pakietami dostępu.

W ramach licencji dla użytkowników należących do członków, można również zezwolić wielu użytkownikom-Gościom na współpracujące z zarządzaniem prawami. Aby uzyskać informacje o tym, jak obliczyć liczbę użytkowników-Gości, których można uwzględnić, zobacz [Azure Active Directory wskazówki dotyczące licencjonowania współpracy B2B](../b2b/licensing-guidance.md).

Aby uzyskać informacje na temat sposobu przypisywania licencji do użytkowników, zobacz [przypisywanie lub usuwanie licencji przy użyciu portalu Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Tworzenie pierwszego pakietu dostępu](entitlement-management-access-package-first.md)
- [Typowe scenariusze](entitlement-management-scenarios.md)
