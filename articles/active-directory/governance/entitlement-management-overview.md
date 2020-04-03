---
title: Co to jest zarządzanie upoważnieniami? - Usługa Azure AD
description: Zapoznaj się z omówieniem zarządzania uprawnieniami usługi Azure Active Directory i sposobu zarządzania dostępem do grup, aplikacji i witryn usługi SharePoint Online dla użytkowników wewnętrznych i zewnętrznych.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65100e8584eba5c31edc2f9fd8c57ad8bd14c0d1
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582539"
---
# <a name="what-is-azure-ad-entitlement-management"></a>Co to jest zarządzanie upoważnieniami w usłudze Azure AD?

Zarządzanie uprawnieniami usługi Azure Active Directory (Azure AD) to funkcja [nadzoru tożsamości,](identity-governance-overview.md) która umożliwia organizacjom zarządzanie tożsamością i cyklem życia dostępu na dużą skalę, automatyzując przepływy pracy żądania dostępu, przydziały dostępu, przeglądy i wygaśnięcie.

Pracownicy w organizacjach potrzebują dostępu do różnych grup, aplikacji i lokacji, aby wykonywać swoje zadania. Zarządzanie tym dostępem jest trudne, ponieważ zmieniają się wymagania — dodawane są nowe aplikacje lub użytkownicy potrzebują dodatkowych praw dostępu.  Ten scenariusz staje się bardziej skomplikowany podczas współpracy z organizacjami zewnętrznymi — możesz nie wiedzieć, kto w innej organizacji potrzebuje dostępu do zasobów organizacji, a oni nie będą wiedzieć, z jakich aplikacji, grup lub witryn korzysta organizacja.

Zarządzanie uprawnieniami usługi Azure AD może pomóc w bardziej efektywnym zarządzaniu dostępem do grup, aplikacji i witryn usługi SharePoint Online dla użytkowników wewnętrznych, a także dla użytkowników spoza organizacji, którzy potrzebują dostępu do tych zasobów.

## <a name="why-use-entitlement-management"></a>Dlaczego warto korzystać z zarządzania uprawnieniami?

Organizacje korporacyjne często borykają się z wyzwaniami związanymi z zarządzaniem dostępem pracowników do zasobów, takich jak:

- Użytkownicy mogą nie wiedzieć, jaki dostęp powinni mieć, a nawet jeśli to zrobią, mogą mieć trudności ze znalezieniem odpowiednich osób do zatwierdzenia ich dostępu
- Gdy użytkownicy znajdą i otrzymają dostęp do zasobu, mogą mieć dostęp dłużej niż jest to wymagane do celów biznesowych

Te problemy są potęgowane dla użytkowników, którzy potrzebują dostępu z innej organizacji, takich jak użytkownicy zewnętrzni z organizacji łańcucha dostaw lub innych partnerów biznesowych. Przykład:

- Nikt nie może znać wszystkich konkretnych osób w katalogach innych organizacji, aby móc ich zaprosić
- Nawet jeśli byli w stanie zaprosić tych użytkowników, nikt w tej organizacji nie może pamiętać, aby zarządzać wszystkimi dostępami użytkownika konsekwentnie

Zarządzanie uprawnieniami usługi Azure AD może pomóc w rozwiązaniu tych problemów.  Aby dowiedzieć się więcej o tym, jak klienci korzystali z zarządzania uprawnieniami usługi Azure AD, możesz przeczytać [studium przypadku firmy Avanade](https://customers.microsoft.com/story/avanade-professional-services-azure-canada) i [studium przypadku firmy Centrica.](https://customers.microsoft.com/story/757467-centrica-energy-azure)  Ten film wideo zawiera omówienie zarządzania uprawnieniami i jego wartości:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>Co mogę zrobić z zarządzaniem uprawnieniami?

Oto niektóre z możliwości zarządzania uprawnieniami:

- Delegować do innych administratorów możliwość tworzenia pakietów dostępu. Te pakiety dostępu zawierają zasoby, których użytkownicy mogą żądać, a menedżerowie pakietów dostępu delegowanego mogą definiować zasady z regułami, o które użytkownicy mogą żądać, którzy muszą zatwierdzić ich dostęp i po wygaśnięciu dostępu.
- Wybierz połączone organizacje, których użytkownicy mogą żądać dostępu.  Gdy użytkownik, który nie jest jeszcze w katalogu żąda dostępu i jest zatwierdzony, są automatycznie zapraszani do katalogu i przypisywany dostęp.  Po wygaśnięciu ich dostępu, jeśli nie mają innych przypisań pakietów dostępu, ich konto B2B w katalogu może zostać automatycznie usunięte.

Możesz rozpocząć pracę z naszego [samouczka, aby utworzyć swój pierwszy pakiet dostępu](entitlement-management-access-package-first.md). Możesz również przeczytać [typowe scenariusze](entitlement-management-scenarios.md)lub obejrzeć filmy, w tym

- [Jak wdrożyć zarządzanie uprawnieniami usługi Azure AD w organizacji](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Jak monitorować i skalować korzystanie z zarządzania uprawnieniami usługi Azure AD](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [Jak delegować w zarządzaniu uprawnieniami](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>Co to są pakiety dostępu i jakie zasoby można zarządzać z nimi?

Zarządzanie uprawnieniami wprowadza do usługi Azure AD koncepcję *pakietu dostępu*. Pakiet dostępu to pakiet wszystkich zasobów z dostępem, którego użytkownik potrzebuje do pracy nad projektem lub wykonania zadania. Pakiety dostępu są używane do regulowaniu dostępu dla wewnętrznych pracowników, a także użytkowników spoza organizacji.

 Oto typy zasobów, do których użytkownik może zarządzać za pomocą zarządzania uprawnieniami:

- Członkostwo w grupach zabezpieczeń usługi Azure AD
- Członkostwo w grupach i zespołach usługi Office 365
- Przypisywanie do aplikacji dla przedsiębiorstw usługi Azure AD, w tym aplikacji SaaS i aplikacji zintegrowanych na zamówienie, które obsługują federacje/logowanie jednokrotne i/lub inicjowanie obsługi administracyjnej
- Członkostwo w witrynach usługi SharePoint Online

Można również kontrolować dostęp do innych zasobów, które opierają się na grupach zabezpieczeń usługi Azure AD lub grupach usługi Office 365.  Przykład:

- Licencje na usługę Microsoft Office 365 można przyznać użytkownikom przy użyciu grupy zabezpieczeń usługi Azure AD w pakiecie dostępu i konfigurowaniu [licencjonowania opartego na grupach](../users-groups-roles/licensing-groups-assign.md) dla tej grupy
- Możesz dać użytkownikom dostęp do zarządzania zasobami platformy Azure przy użyciu grupy zabezpieczeń usługi Azure AD w pakiecie dostępu i tworzeniu [przypisania roli platformy Azure](../../role-based-access-control/role-assignments-portal.md) dla tej grupy

## <a name="how-do-i-control-who-gets-access"></a>Jak kontrolować, kto uzyskuje dostęp?

W pakiecie dostępu administrator lub menedżer pakietów dostępu delegowanego wyświetla listę zasobów (grup, aplikacji i witryn) oraz ról, których użytkownicy potrzebują dla tych zasobów.

Pakiety dostępu zawierają również jedną lub więcej *zasad.* Zasady definiuje reguły lub barierki dla przypisania do pakietu dostępu. Każda zasada może służyć do zapewnienia, że tylko odpowiedni użytkownicy są w stanie zażądać dostępu, że istnieją osoby zatwierdzające dla ich żądania i że ich dostęp do tych zasobów jest ograniczony w czasie i wygaśnie, jeśli nie zostanie odnowiony.

![Pakiet dostępu i zasady dostępu](./media/entitlement-management-overview/elm-overview-access-package.png)

W ramach każdej zasady administrator lub menedżer pakietów dostępu definiuje

- Albo już istniejący użytkownicy (zazwyczaj pracownicy lub już zaproszeni goście) lub organizacje partnerskie użytkowników zewnętrznych, które mogą zażądać dostępu
- Proces zatwierdzania i użytkownicy, którzy mogą zatwierdzać lub odmawiać dostępu
- Czas trwania przypisania dostępu użytkownika, po zatwierdzeniu, przed wygaśnięciem przypisania

Na poniższym diagramie przedstawiono przykład różnych elementów w zarządzaniu uprawnieniami. Pokazuje jeden katalog z dwoma przykładowymi pakietami dostępu.

- **Pakiet dostępu 1** zawiera jedną grupę jako zasób. Program Access jest definiowany za pomocą zasad, które umożliwiają zestawowi użytkowników w katalogu żądanie dostępu.
- **Pakiet programu Access 2** zawiera grupę, aplikację i witrynę usługi SharePoint Online jako zasoby. Dostęp jest zdefiniowany za pomocą dwóch różnych zasad. Pierwsza zasada umożliwia zestaw użytkowników w katalogu, aby zażądać dostępu. Druga zasada umożliwia użytkownikom w katalogu zewnętrznym żądanie dostępu.

![Omówienie zarządzania uprawnieniami](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>Kiedy należy używać pakietów dostępu?

Pakiety dostępu nie zastępują innych mechanizmów przypisywania dostępu.  Są one najbardziej odpowiednie w sytuacjach takich jak:

- Pracownicy potrzebują ograniczonego w czasie dostępu do określonego zadania.  Na przykład można użyć licencjonowania opartego na grupach i grupy dynamicznej, aby upewnić się, że wszyscy pracownicy mają skrzynkę pocztową usługi Exchange Online, a następnie użyć pakietów dostępu w sytuacjach, w których pracownicy potrzebują dodatkowego dostępu, na przykład do odczytu zasobów działów z innego działu.
- Dostęp musi być zatwierdzony przez kierownika pracownika lub inne wyznaczone osoby.
- Działy chcą zarządzać własnymi zasadami dostępu dla swoich zasobów bez udziału działu IT.  
- Co najmniej dwie organizacje współpracują nad projektem, w związku z czym wielu użytkowników z jednej organizacji będzie musiało zostać wniesionych za pośrednictwem usługi Azure AD B2B, aby uzyskać dostęp do zasobów innej organizacji.

## <a name="how-do-i-delegate-access"></a>Jak delegować dostęp?

 Pakiety dostępu są definiowane w kontenerach *nazywanych katalogami*.  Możesz mieć jeden katalog dla wszystkich pakietów dostępu lub można wyznaczyć osoby do tworzenia i posiadania własnych katalogów. Administrator może dodawać zasoby do dowolnego katalogu, ale nie-administrator może dodać do katalogu tylko zasoby, które są jego własnością. Właściciel katalogu może dodawać innych użytkowników jako współwłaścicieli katalogu lub jako menedżerów pakietów dostępu.  Te scenariusze są opisane w dalszej części delegowania artykułu [i ról w zarządzania uprawnieniami usługi Azure AD](entitlement-management-delegate.md).

## <a name="summary-of-terminology"></a>Podsumowanie terminologii

Aby lepiej zrozumieć zarządzanie uprawnieniami i jego dokumentację, można odwołać się do poniższej listy terminów.

| Termin | Opis |
| --- | --- |
| pakiet dostępu | Pakiet zasobów, które zespół lub projekt potrzebuje i jest regulowany z zasadami. Pakiet dostępu jest zawsze zawarty w katalogu. Należy utworzyć nowy pakiet dostępu dla scenariusza, w którym użytkownicy muszą zażądać dostępu.  |
| żądanie dostępu | Żądanie dostępu do zasobów w pakiecie dostępu. Żądanie zazwyczaj przechodzi przez przepływ pracy zatwierdzania.  Jeśli użytkownik żądający otrzyma przypisanie pakietu dostępu. |
| Przypisania | Przypisanie pakietu dostępu do użytkownika zapewnia, że użytkownik ma wszystkie role zasobów tego pakietu dostępu.  Przypisania pakietów dostępu zazwyczaj mają limit czasu przed ich wygaśnięciem. |
| Katalog | Kontener powiązanych zasobów i pakietów dostępu.  Katalogi są używane do delegowania, dzięki czemu nie-administratorzy mogą tworzyć własne pakiety dostępu. Właściciele katalogu mogą dodawać zasoby, których są właścicielami, do katalogu. |
| twórca katalogu | Kolekcja użytkowników, którzy są autoryzowani do tworzenia nowych katalogów.  Gdy użytkownik niebędący administratorem, który jest upoważniony do bycia twórcą katalogu, automatycznie staje się właścicielem tego katalogu. |
| połączona organizacja | Zewnętrzny katalog lub domena usługi Azure AD, z którą masz relację. Użytkownicy z połączonej organizacji mogą być określone w zasadach jako mogą żądać dostępu. |
| policy | Zestaw reguł definiujących cykl życia dostępu, na przykład sposób uzyskiwania dostępu przez użytkowników, osoby, które mogą zatwierdzać i jak długo użytkownicy mają dostęp za pośrednictwem przypisania. Zasady są połączone z pakietem dostępu. Na przykład pakiet dostępu może mieć dwie zasady — jedną dla pracowników, aby zażądać dostępu, a drugą dla użytkowników zewnętrznych, aby zażądać dostępu. |
| zasób | Zasób, taki jak grupa pakietu Office, grupa zabezpieczeń, aplikacja lub witryna usługi SharePoint Online, z rolą, do którą użytkownik może uzyskać uprawnienia. |
| katalog zasobów | Katalog, który ma jeden lub więcej zasobów do udostępnienia. |
| rola zasobu | Kolekcja uprawnień skojarzonych z zasobem i zdefiniowanych przez zasób. Grupa ma dwie role — członka i właściciela. Witryny programu SharePoint mają zazwyczaj 3 role, ale mogą mieć dodatkowe role niestandardowe. Aplikacje mogą mieć role niestandardowe. |


## <a name="license-requirements"></a>Wymagania licencyjne

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Wyspecjalizowane chmury, takie jak Azure Germany i Azure China 21Vianet, nie są obecnie dostępne do użycia.

### <a name="how-many-licenses-must-you-have"></a>Ile masz licencji?

Upewnij się, że twój katalog ma co najmniej tyle licencji usługi Azure AD Premium P2, jak masz:

- Użytkownicy członkowskie, którzy **mogą zażądać** pakietu dostępu.
- Użytkownicy członkowskie i goście, którzy zażądają pakietu dostępu.
- Użytkownicy członkowskie i goście, którzy zatwierdzają żądania pakietu dostępu.
- Użytkownicy członkowskie i goście, którzy mają bezpośrednie przypisanie do pakietu dostępu.

Licencje usługi Azure AD Premium P2 **nie** są wymagane do następujących zadań:

- Żadne licencje nie są wymagane dla użytkowników z rolą Administratora globalnego, którzy skonfigurują początkowe katalogi, pakiety dostępu i zasady i delegują zadania administracyjne innym użytkownikom.
- Żadne licencje nie są wymagane dla użytkowników, którym delegowano zadania administracyjne, takie jak twórca katalogu, właściciel katalogu i menedżer pakietów dostępu.
- Nie są wymagane żadne licencje dla gości, którzy **mogą zażądać** pakietów dostępu, ale **nie** żądają pakietu dostępu.

Dla każdej płatnej licencji usługi Azure AD Premium P2 zakupionej dla użytkowników członkowskich (pracowników) można użyć usługi Azure AD B2B, aby zaprosić maksymalnie 5 użytkowników-gości. Ci użytkownicy-goście mogą również korzystać z funkcji usługi Azure AD Premium P2. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące licencjonowania współpracy usługi Azure AD B2B](../b2b/licensing-guidance.md).

Aby uzyskać więcej informacji o licencjach, zobacz [Przypisywanie lub usuwanie licencji za pomocą portalu usługi Azure Active Directory](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Przykładowe scenariusze licencji

Oto kilka przykładowych scenariuszy licencji ułatwiające określenie liczby licencji, które muszą mieć.

| Scenariusz | Obliczenia | Liczba licencji |
| --- | --- | --- |
| Globalny administrator w Woodgrove Bank tworzy początkowe katalogi i deleguje zadania administracyjne do 6 innych użytkowników. Jedna z zasad określa, że **wszyscy pracownicy** (2000 pracowników) mogą żądać określonego zestawu pakietów dostępu. 150 pracowników żąda pakietów dostępu. | 2000 pracowników, którzy **mogą zażądać** pakietów dostępu | 2000 |
| Globalny administrator w Woodgrove Bank tworzy początkowe katalogi i deleguje zadania administracyjne do 6 innych użytkowników. Jedna z zasad określa, że **wszyscy pracownicy** (2000 pracowników) mogą żądać określonego zestawu pakietów dostępu. Inna zasada określa, że niektórzy **użytkownicy z użytkowników z partnera Contoso** (goście) mogą żądać tych samych pakietów dostępu podlegających zatwierdzeniu. Contoso ma 30 000 użytkowników. 150 pracowników żąda pakietów dostępu i 10 500 użytkowników z contoso żądania dostępu. | 2000 pracowników + 500 użytkowników-gości firmy Contoso, którzy przekraczają współczynnik 1:5 (10 500 - (2000 * 5)) | 2500 |

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Tworzenie pierwszego pakietu dostępu](entitlement-management-access-package-first.md)
- [Typowe scenariusze](entitlement-management-scenarios.md)
