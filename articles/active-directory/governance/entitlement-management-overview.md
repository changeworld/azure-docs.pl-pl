---
title: Co to jest zarządzanie upoważnieniami w usłudze Azure AD? -Azure Active Directory
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
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1c054bf354b5cdc5e0803ba6849af19fab40f3c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174161"
---
# <a name="what-is-azure-ad-entitlement-management"></a>Co to jest zarządzanie upoważnieniami w usłudze Azure AD?

Zarządzanie prawami w usłudze Azure Active Directory (Azure AD) to funkcja zarządzania [tożsamościami](identity-governance-overview.md) , która umożliwia organizacjom Zarządzanie cyklem życia tożsamości i dostępu na dużą skalę przez Automatyzowanie przepływów pracy żądania dostępu, dostęp do przypisań, przeglądów i datę.

Pracownicy w organizacjach potrzebują dostępu do różnych grup, aplikacji i witryn w celu wykonywania ich zadań. Zarządzanie tym dostępem jest trudne, ponieważ zmiany wymagań — nowe aplikacje są dodawane lub użytkownicy potrzebują dodatkowych praw dostępu.  Ten scenariusz jest bardziej skomplikowany podczas współpracy z organizacjami zewnętrznymi — użytkownik może nie wiedzieć, kto w innej organizacji potrzebuje dostępu do zasobów organizacji i nie wie, jakie aplikacje, grupy lub witryny są używane w organizacji.

Zarządzanie prawami w usłudze Azure AD ułatwia wydajne zarządzanie dostępem do grup, aplikacji i witryn usługi SharePoint Online dla użytkowników wewnętrznych, a także dla użytkowników spoza organizacji, którzy potrzebują dostępu do tych zasobów.

## <a name="why-use-entitlement-management"></a>Dlaczego warto korzystać z zarządzania prawami?

Organizacje korporacyjne często napotykają problemy związane z zarządzaniem dostępem pracowników do zasobów, takich jak:

- Użytkownicy mogą nie wiedzieć, jaki ma dostęp, a nawet jeśli tak, mogą mieć problemy z lokalizowaniem właściwych osób w celu zatwierdzenia dostępu
- Gdy użytkownicy znajdą i uzyskają dostęp do zasobu, mogą uzyskać dostęp do niego dłużej niż jest to wymagane do celów służbowych

Te problemy są złożone dla użytkowników, którzy potrzebują dostępu z innej organizacji, takich jak użytkownicy zewnętrzni z organizacji łańcucha dostaw lub innych partnerów firmy. Na przykład:

- Żadna osoba nie może znać wszystkich konkretnych osób w katalogach innych organizacji, aby móc je zapraszać
- Nawet jeśli udało Ci się zaprosić tych użytkowników, nikt w tej organizacji nie może pamiętać o konieczności spójnego zarządzania wszystkimi prawami dostępu użytkownika

Zarządzanie prawami w usłudze Azure AD może pomóc rozwiązać te wyzwania.  Aby dowiedzieć się więcej o tym, jak klienci korzystają z funkcji zarządzania prawami w usłudze Azure AD, można odczytać [analizę przypadku firma Avanade](https://aka.ms/AvanadeELMCase) oraz [analizę przypadku zorientowanego](https://aka.ms/CentricaELMCase)na siebie.  Ten film wideo zawiera omówienie zarządzania uprawnieniami i jego wartości:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>Co mogę zrobić z zarządzaniem prawami?

Poniżej przedstawiono niektóre możliwości zarządzania uprawnieniami:

- Delegowanie do innych niż administratorów możliwości tworzenia pakietów dostępu. Te pakiety dostępu zawierają zasoby, których użytkownicy mogą żądać, a menedżerowie pakietów dostępu delegowanego mogą definiować zasady z regułami, dla których użytkownicy mogą zażądać, którzy muszą zatwierdzić dostęp, a po wygaśnięciu dostępu.
- Wybierz połączone organizacje, których użytkownicy mogą żądać dostępu.  Gdy użytkownik, który nie znajduje się jeszcze w katalogu, zażąda dostępu i jest zatwierdzony, zostaje automatycznie zaproszony do katalogu i przypisany dostęp.  Po wygaśnięciu ich dostępu, jeśli nie mają żadnych innych przypisań pakietów dostępu, konto B2B w katalogu można automatycznie usunąć.

Możesz rozpocząć pracę z naszym [samouczkiem, aby utworzyć pierwszy pakiet dostępu](entitlement-management-access-package-first.md). Możesz również zapoznać się z [typowymi scenariuszami](entitlement-management-scenarios.md)lub obejrzeć wideo, w tym

- [Jak wdrożyć Zarządzanie prawami w usłudze Azure AD w organizacji](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Jak monitorować i skalować korzystanie z funkcji zarządzania prawami usługi Azure AD](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [Jak delegować uprawnienia do zarządzania prawami](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>Jakie są pakiety dostępu i z którymi zasobami mogę nimi zarządzać?

Zarządzanie prawami wprowadza do usługi Azure AD pojęcie *pakietu dostępu*. Pakiet dostępu to pakiet wszystkich zasobów z dostępem, które użytkownik musi pracować nad projektem lub wykonać ich zadanie. Pakiety dostępu są używane do zarządzania dostępem do pracowników wewnętrznych, a także użytkowników spoza organizacji.

 Poniżej przedstawiono typy zasobów, do których można zarządzać dostępem użytkowników za pomocą zarządzania uprawnieniami:

- Członkostwo w grupach zabezpieczeń usługi Azure AD
- Członkostwo w grupach i zespołach z pakietem Office 365
- Przypisywanie do aplikacji usługi Azure AD Enterprise, w tym aplikacji SaaS i aplikacji zintegrowanych niestandardowo obsługujących Federacji/Logowanie jednokrotne i/lub obsługę administracyjną
- Członkostwo w witrynach usługi SharePoint Online

Można również kontrolować dostęp do innych zasobów, które są zależne od grup zabezpieczeń usługi Azure AD lub grup programu Office 365.  Na przykład:

- Można udzielić użytkownikom licencji na Microsoft Office 365 przy użyciu grupy zabezpieczeń usługi Azure AD w pakiecie dostępu i konfigurowania [licencjonowania opartego na grupach](../users-groups-roles/licensing-groups-assign.md) dla tej grupy.
- Można udzielić użytkownikom dostępu do zarządzania zasobami platformy Azure za pomocą grupy zabezpieczeń usługi Azure AD w pakiecie dostępu i tworzenia [przypisania roli platformy Azure](../../role-based-access-control/role-assignments-portal.md) dla tej grupy

## <a name="how-do-i-control-who-gets-access"></a>Jak mogę kontrolować, kto uzyskuje dostęp?

W przypadku pakietu dostępu administrator lub Menedżer pakietów dostępu delegowanego wyświetla listę zasobów (grup, aplikacji i lokacji) oraz role, których użytkownicy potrzebują dla tych zasobów.

Pakiety dostępu zawierają również co najmniej jedną *zasadę*. Zasady definiują reguły lub guardrails na potrzeby przypisywania do pakietu. Każda z tych zasad może służyć do zapewnienia, że tylko Ci użytkownicy będą mogli żądać dostępu, że istnieją osoby zatwierdzające ich żądania oraz że ich dostęp do tych zasobów jest ograniczony do czasu i wygasa, jeśli nie zostanie odnowiony.

![Dostęp do pakietu i zasad](./media/entitlement-management-overview/elm-overview-access-package.png)

W ramach każdej zasady administrator lub Menedżer pakietów dostępu definiuje

- Już istniejący użytkownicy (zazwyczaj pracownicy lub już zapraszani Goście) lub organizacje partnerskie użytkowników zewnętrznych, którzy mają uprawnienia do żądania dostępu
- Proces zatwierdzania i użytkownicy, którzy mogą zatwierdzać lub odmówić dostępu
- Czas trwania przypisania dostępu użytkownika, po zatwierdzeniu, przed wygaśnięciem przypisania

Na poniższym diagramie przedstawiono przykład różnych elementów w usłudze zarządzania uprawnieniami. Pokazuje jeden wykaz z dwoma przykładowymi pakietami dostępu.

- **Pakiet Access 1** zawiera pojedynczą grupę jako zasób. Dostęp jest definiowany przy użyciu zasad, które umożliwiają uzyskanie dostępu do zestawu użytkowników w katalogu.
- **Pakiet Access 2** zawiera grupę, aplikację i witrynę usługi SharePoint Online jako zasoby. Dostęp jest definiowany przy użyciu dwóch różnych zasad. Pierwsze zasady umożliwiają zestawowi użytkowników w katalogu zażądanie dostępu. Druga zasada umożliwia użytkownikom w zewnętrznym katalogu zażądanie dostępu.

![Omówienie zarządzania uprawnieniami](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>Kiedy należy używać pakietów dostępu?

Pakiety dostępu nie zastępują innych mechanizmów do przypisywania dostępu.  Są one najbardziej odpowiednie w sytuacjach, takich jak następujące:

- Pracownicy potrzebują ograniczonego czasu dostępu do określonego zadania.  Na przykład można użyć licencjonowania opartego na grupach i grupy dynamicznej, aby upewnić się, że wszyscy pracownicy mają skrzynkę pocztową usługi Exchange Online, a następnie używają pakietów dostępu dla sytuacji, w których pracownicy potrzebują dodatkowego dostępu, na przykład w celu odczytu zasobów działu z innego Oddział.
- Dostęp musi zostać zatwierdzony przez Menedżera pracownika lub innych wyznaczeniów osób.
- Działy chcą zarządzać własnymi zasadami dostępu dla swoich zasobów bez udziału IT.  
- Co najmniej dwie organizacje współpracują nad projektem i w związku z tym wiele użytkowników z jednej organizacji będzie musiała zostać przeprowadzona za pośrednictwem usługi Azure AD B2B w celu uzyskania dostępu do zasobów innej organizacji.

## <a name="how-do-i-delegate-access"></a>Jak mogę delegować dostęp?

 Pakiety dostępu są definiowane w kontenerach o nazwie *wykazów*.  Istnieje jeden wykaz dla wszystkich pakietów dostępu lub można wyznaczyć poszczególne osoby do tworzenia i posiadania własnych katalogów. Administrator może dodać zasoby do dowolnego wykazu, ale nie administrator może dodać go tylko do katalogu, do którego należą zasoby. Właściciel wykazu może dodawać innych użytkowników jako współwłaścicieli katalogu lub jako menedżerów pakietów dostępu.  Te scenariusze są opisane w temacie [delegowanie i role w temacie Zarządzanie prawami usługi Azure AD](entitlement-management-delegate.md).

## <a name="summary-of-terminology"></a>Podsumowanie terminologii

Aby lepiej zrozumieć Zarządzanie uprawnieniami i jej dokumentację, można odwołać się z powrotem do poniższej listy warunków.

| Okres obowiązywania Umowy | Opis |
| --- | --- |
| pakiet dostępu | Zbiór zasobów wymaganych przez zespół lub projekt i podlega zasadom. Pakiet dostępu jest zawsze zawarty w wykazie. Należy utworzyć nowy pakiet dostępu dla scenariusza, w którym użytkownicy muszą zażądać dostępu.  |
| żądanie dostępu | Żądanie dostępu do zasobów w pakiecie dostępu. Żądanie jest zwykle wykonywane przez przepływ pracy zatwierdzania.  W przypadku zatwierdzenia użytkownik żądający otrzymuje przypisanie do pakietu. |
| wykorzystując | Przypisanie pakietu dostępu do użytkownika gwarantuje, że użytkownik ma wszystkie role zasobów tego pakietu.  Przypisania pakietów dostępu zwykle mają limit czasu, zanim wygasną. |
| pełnotekstowy | Kontener powiązanych zasobów i pakietów dostępu.  Wykazy są używane do delegowania, aby uniemożliwić administratorom tworzenie własnych pakietów dostępu. Właściciele wykazu mogą dodawać zasoby należące do wykazu. |
| Twórca katalogu | Kolekcja użytkowników, którzy mają uprawnienia do tworzenia nowych wykazów.  Gdy użytkownik niebędący administratorem, który jest autoryzowany jako twórca katalogu tworzy nowy wykaz, automatycznie staje się właścicielem tego katalogu. |
| połączona organizacja | Zewnętrzny katalog lub domena usługi Azure AD, z którą istnieje relacja. Użytkownicy z połączonej organizacji mogą być określeni w zasadach jako mogą żądać dostępu. |
| policy | Zestaw reguł definiujących cykl życia dostępu, na przykład informacje o tym, jak użytkownicy uzyskują dostęp, kto może zatwierdzić i jak długo użytkownicy mają dostęp przez przypisanie. Zasady są połączone z pakietem dostępu. Na przykład pakiet dostępu może mieć dwie zasady — jeden dla pracowników do żądania dostępu, a drugi do żądania dostępu przez użytkowników zewnętrznych. |
| Zasoby | Element zawartości, taki jak grupa pakietu Office, Grupa zabezpieczeń, aplikacja lub witryna usługi SharePoint Online, z rolą, do której użytkownik może uzyskać uprawnienia. |
| Katalog zasobów | Katalog, który ma co najmniej jeden zasób do udostępnienia. |
| Rola zasobu | Kolekcja uprawnień skojarzonych z i zdefiniowanych przez zasób. Grupa ma dwie role — element członkowski i właściciel. Witryny programu SharePoint zazwyczaj mają 3 role, ale mogą mieć dodatkowe role niestandardowe. Aplikacje mogą mieć role niestandardowe. |


## <a name="license-requirements"></a>Wymagania licencyjne

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Wyspecjalizowane chmury, takie jak Azure Government, Azure (Niemcy) i Azure (Chiny), nie są obecnie dostępne do użycia.

### <a name="which-users-must-have-licenses"></a>Którzy użytkownicy muszą mieć licencje?

Dzierżawa musi mieć co najmniej tyle Azure AD — wersja Premium licencji P2, ponieważ użytkownicy będą aktywni w usłudze zarządzania uprawnieniami. Aktywni użytkownicy należący do zarządzania prawami obejmują:

- Użytkownik inicjujący lub zatwierdził żądanie dotyczące pakietu dostępu.
- Użytkownik, któremu przypisano pakiet dostępu.
- Użytkownik zarządzający pakietami dostępu.

W ramach licencji dla użytkowników należących do członków, można również zezwolić wielu użytkownikom-Gościom na współpracujące z zarządzaniem prawami. Aby uzyskać informacje o tym, jak obliczyć liczbę użytkowników-Gości, których można uwzględnić, zobacz [Azure Active Directory wskazówki dotyczące licencjonowania współpracy B2B](../b2b/licensing-guidance.md).

Aby uzyskać informacje na temat sposobu przypisywania licencji do użytkowników, zobacz [przypisywanie lub usuwanie licencji przy użyciu portalu Azure Active Directory](../fundamentals/license-users-groups.md). Należy zauważyć, że zarządzanie uprawnieniami obecnie nie wymusza przypisania licencji dla użytkowników.

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Tworzenie pierwszego pakietu dostępu](entitlement-management-access-package-first.md)
- [Typowe scenariusze](entitlement-management-scenarios.md)
