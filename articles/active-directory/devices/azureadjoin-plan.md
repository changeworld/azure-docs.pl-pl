---
title: Jak zaplanować wdrożenie przyłączanie do usługi Azure Active Directory (Azure AD) | Dokumentacja firmy Microsoft
description: Wyjaśniono, że urządzenia przyłączone do kroków, które są wymagane do wdrożenia usługi Azure AD w środowisku.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: devices
ms.assetid: 81d4461e-21c8-4fdd-9076-0e4991979f62
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: fdc8b5db9316e463f8ec46ca5e235ea53cf44265
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275976"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Porady: Planowanie wdrożenia usługi Azure AD join


Przyłączanie do usługi Azure AD można dołączać urządzenia bezpośrednio do usługi Azure AD bez konieczności dołączania do usługi Active Directory w środowisku lokalnym przy jednoczesnym zachowaniu użytkownikom w wydajnej i bezpiecznej pracy.  

W tym artykule założono, masz znajomość z usługą Azure AD i [Stany urządzeń w usłudze Azure AD](overview.md). 

 

## <a name="review-your-scenarios"></a>Przegląd scenariuszy 

Podczas dołączania do usługi Azure AD ma zastosowanie do większości scenariuszy w organizacji ustawienie, może to być niektórych przypadków użycia, w których warto wdrożyć zamiast dołączenie do hybrydowej usługi Azure AD. Azure AD join jest gotowa do użycia w przedsiębiorstwie w przypadku wdrożeń na dużą skalę lub tego samego zakresu. 

 
Należy wziąć pod uwagę usługi Azure AD join na podstawie następujących kryteriów dla Twojej organizacji:  

- Możesz zaplanować albo ma większość urządzeń Windows w systemie Windows 10. 

- Zamierzasz przenieść wdrożenie oparte na chmurze i zarządzanie urządzeniami Windows. 

- Masz ograniczone na infrastrukturę lokalną lub planujesz zmniejszyć Twojej obecności w środowisku lokalnym. 

- Nie masz dużą zależnością zasad grupy do zarządzania urządzeniami. 

- Nie masz starsze aplikacje korzystające z uwierzytelniania komputera usługi Active Directory. 

- Wszystkich lub wybranych użytkowników w organizacji nie ma znajdować się w sieci firmowej, dostęp do niezbędnych aplikacji i zasobów.  

 

Przegląd scenariuszy na podstawie następujących oceny  

 

## <a name="assess-infrastructure"></a>Ocena infrastruktury  

 

### <a name="users"></a>Użytkownicy 

Jeśli użytkownicy są tworzone w usłudze Active Directory w środowisku lokalnym, muszą być synchronizowane z usługą Azure AD za pomocą usługi Azure AD Connect. Aby dowiedzieć się więcej o synchronizowaniu użytkowników, zobacz [co to jest program Azure AD Connect?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity#what-is-azure-ad-connect) 

Jeśli użytkownicy są tworzone bezpośrednio w usłudze Azure AD, żadna dodatkowa konfiguracja jest wymagana 

 

Alternatywnych identyfikatorów logowania nie są obsługiwane na urządzeniach przyłączonych do usługi Azure AD. Użytkownicy powinni mieć prawidłowa nazwa UPN w usłudze Azure AD.  

 

### <a name="identity-infrastructure"></a>Infrastruktury do obsługi tożsamości 

Przyłączanie do usługi Azure AD działa w środowiskach zarządzanych i federacyjnego.  

#### <a name="managed-environment"></a>Zarządzane środowisko 

Zarządzanego środowiska można wdrożyć za pośrednictwem synchronizacji skrótów haseł lub przekazywania uwierzytelniania za pomocą bezproblemowego logowania jednokrotnego. <read more here> 

#### <a name="federated-environment"></a>Środowisku federacyjnym  

Środowisku federacyjnym powinna mieć dostawcę tożsamości, który obsługuje zarówno protokołu WS-Trust i WS-Fed. WS-Fed jest wymagane do dołączania urządzenia do usługi Azure AD i WS-Trust jest wymagana do logowania się na urządzeniu dołączonym do usługi Azure AD. Tak Jeśli dostawca tożsamości nie obsługuje je, dołączania do usługi Azure AD nie będzie działać. 


#### <a name="smartcards-and-certificate-based-authentication"></a>Karty inteligentne i uwierzytelniania opartego na certyfikatach 

 

Obecnie kart inteligentnych i uwierzytelniania opartego na certyfikatach nie są obsługiwane w usłudze Azure AD, dzięki czemu nie będzie działać na urządzeniach przyłączonych do usługi Azure AD 

 

### <a name="devices"></a>Urządzenia 

 

#### <a name="supported-devices"></a>Obsługiwane urządzenia 

Funkcja Azure AD join jest dostępna wyłącznie dla urządzeń z systemem Windows 10. Nie ma zastosowania do poprzednich wersji systemu Windows lub innych systemów operacyjnych. Jeśli nadal masz Windows 7/8.1, musisz uaktualnić do systemu Windows 10 do wdrożenia usługi Azure AD join 

 

Zalecenie: Zawsze używaj najnowszej wersji systemu Windows 10 dla zaktualizowanych funkcji 

 

### <a name="applications--other-resources"></a>Aplikacje i inne zasoby 

Zaleca się, że migracji aplikacji ze środowiska lokalnego do chmury dla wygody użytkowników i kontroli dostępu. Jednak do usługi Azure AD urządzenia mogą bezproblemowo zapewniają dostęp do swoich lokalnych i działających w chmurze. Aby uzyskać więcej informacji, zobacz [jak logowanie Jednokrotne do lokalnych zasobów działa w usłudze Azure AD urządzenia przyłączone do](azuread-join-sso.md).  


Poniżej przedstawiono zagadnienia dotyczące innego typu aplikacji i zasobów 

 

- **Aplikacje oparte na chmurze:** Jeśli aplikacja zostanie dodany do galerii aplikacji Azure AD, urządzenia przyłączone do użytkownicy pobierają logowania jednokrotnego za pośrednictwem usługi Azure AD. Dodatkowa konfiguracja nie jest wymagana 

 

- **Aplikacje sieci web w środowisku lokalnym:** przypadku niestandardowe aplikacje skompilowane i/lub hostowanych lokalnie, należy dodać je do zaufanych witryn w przeglądarce. Spowoduje to włączenie Windows integrated authentication do pracy i zapewnić wiersz nie środowisko logowania jednokrotnego dla użytkowników. Jeśli używasz usług AD FS, zobacz [Sprawdź i zarządzanie nimi logowanie jednokrotne za pomocą usług AD FS](https://docs.microsoft.com/en-us/previous-versions/azure/azure-services/jj151809(v%3dazure.100)) Aby uzyskać więcej informacji. Zalecenie: Należy wziąć pod uwagę hostingu w chmurze (na przykład Azure) oraz integracji z usługą Azure AD, aby uzyskać lepsze efekty. 

- **Opierając się na starszych protokołów aplikacji lokalnych:** użytkownicy pobierają logowania jednokrotnego z usługi Azure AD przyłączonych do urządzeń, jeśli urządzenie ma bezpośredni kontakt z kontrolerem domeny. Zalecenie: Wdrażanie serwera proxy aplikacji usługi Azure AD, aby umożliwić bezpieczny dostęp do tych aplikacji. Aby uzyskać więcej informacji, zobacz [lepszym rozwiązaniem dlaczego jest serwer Proxy aplikacji?](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy#why-is-application-proxy-a-better-solution) 

- **Udziały sieciowe lokalnie:** użytkownicy pobierają logowania jednokrotnego do ich udziałów sieciowych z usługi Azure AD urządzenia przyłączone do, gdy urządzenie ma łączność z kontrolerem domeny. Aby uzyskać więcej informacji, zobacz [jak logowanie Jednokrotne do lokalnych zasobów działa w usłudze Azure AD urządzenia przyłączone do](azuread-join-sso.md).

 

- **Drukarki:** urządzeń przyłączonych do hybrydowej Wdróż cloud print w odniesieniu do wykrywania drukarek z usługi Azure AD. Alternatywnie użytkownicy umożliwia również ścieżkę UNC drukarki dodać bezpośrednio. Drukarki nie może automatycznie odnalezione w środowisku tylko na chmurze. 

 

- **Aplikacje, opierając się na uwierzytelnianie komputera lokalnego:** te aplikacje nie są obsługiwane na urządzeniach przyłączonych do usługi Azure AD. Zalecenie: Należy wziąć pod uwagę wycofania tych aplikacji i przeniesienie do ich nowoczesnych rozwiązań alternatywnych.  

 

### <a name="device-management"></a>Zarządzanie urządzeniami  

 

Zarządzanie urządzeniami dla urządzeń przyłączonych do usługi Azure AD jest przede wszystkim za pośrednictwem zarządzania urządzeniami Przenośnymi platformy (na przykład Intune) i zarządzania urządzeniami Przenośnymi dostawców usług kryptograficznych. Windows 10 ma wbudowane agenta MDM, która współdziała z wszystkie zgodne rozwiązania do zarządzania urządzeniami Przenośnymi.  

 

Zasady grupy nie są obsługiwane na urządzeniach przyłączonych do usługi Azure AD, ponieważ nie są one połączone z usługi Active Directory.  

 

Oceń parzystości zasad zarządzania urządzeniami Przenośnymi przy użyciu zasad grupy przy użyciu [narzędzia do zarządzania urządzeniami Przenośnymi migracji analizy [MMAT](https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/THR3002R). Przejrzyj zasady obsługiwane i nieobsługiwane, aby określić stosowanie za pomocą rozwiązania EMM zamiast zasad grupy. Nieobsługiwana zasad należy wziąć pod uwagę:  

- Są nieobsługiwane zasady niezbędne dla użytkowników lub urządzeń usługi Azure AD join jest wdrażany? 

- Nieobsługiwana zasady mają zastosowanie w chmurze oparte na wdrożenia? 

 

Jeśli rozwiązania do zarządzania urządzeniami Przenośnymi nie jest dostępna za pośrednictwem galerii aplikacji Azure AD, możesz dodać go zgodnie z procedurą opisaną w [integracji usługi Azure Active Directory z zarządzaniem urządzeniami Przenośnymi](https://docs.microsoft.com/windows/client-management/mdm/azure-active-directory-integration-with-mdm).

 

Za pomocą współzarządzania programu SCCM może służyć do zarządzania niektórymi aspektami urządzenia, podczas gdy zasady są dostarczane za pośrednictwem platformy zarządzania urządzeniami Przenośnymi. Microsoft Intune umożliwia współzarządzania wraz z programem SCCM. Aby uzyskać więcej informacji, zobacz [współzarządzania dla systemu Windows 10 urządzenia](https://docs.microsoft.com/sccm/core/clients/manage/co-management-overview). Jeśli używasz produktu EMM innej niż usługi Intune Sprawdź, czy u swojego dostawcy zarządzania urządzeniami Przenośnymi na scenariuszach stosowanych współzarządzania.  

 

Istnieją dwa sposoby szerokiego zarządzania urządzeń przyłączonych do usługi Azure AD: 

 

- **Tylko do zarządzania urządzeniami Przenośnymi** — urządzenie jest wyłącznie zarządzane przez dostawcę zarządzania urządzeniami Przenośnymi, takiej jak Intune. Wszystkie zasady są dostarczane jako część procesu rejestracji zarządzania urządzeniami Przenośnymi. W przypadku klientów usługi Azure AD Premium lub pakietu EMS rejestracji w rozwiązaniu MDM jest automatyczne jako część usługi Azure AD join. 

- **Współzarządzanie** — urządzenie jest zarządzane przez dostawcę zarządzania urządzeniami Przenośnymi i SCCM w jednocześnie. W tym podejściu na urządzenia zarządzane przez system MDM do administrowania niektóre aspekty zainstalowano agenta programu SCCM. 

Zalecenie: Należy wziąć pod uwagę zarządzania urządzeniami Przenośnymi, tylko urządzenia przyłączone do zarządzania dla usługi Azure AD.  

 

## <a name="configuration"></a>Konfigurowanie 

 

Przyłączanie do usługi Azure AD można skonfigurować w portalu usługi Azure AD na podstawie pewnych określonych ustawień. Przejdź do `Devices -> Device settings` i skonfiguruj następujące opcje:   

- Użytkownicy mogą dołączać urządzania do usługi Azure AD: Ustaw tę wartość na "Wszystkie" lub "Wybrane" na podstawie zakresu wdrożenia.  

- Urządzenia przyłączone do dodatkowych administratorów lokalnych w usłudze Azure AD: wartość "Wybrane" i wybiera użytkowników, którym chcesz dodać do grupy administratorów lokalnych na wszystkie usługi Azure AD urządzeń przyłączonych do wdrożonych w organizacji. Aby uzyskać więcej informacji, zobacz [urządzeń przyłączonych do sposobu zarządzania do lokalnej grupy administratorów w usłudze Azure AD](assign-local-admin.md). 

- Wymagaj uwierzytelniania wieloskładnikowego do dołączania urządzeń: wartość "Yes", jeśli możesz wymagać od użytkowników do wykonywania uwierzytelniania Wieloskładnikowego podczas przyłączania urządzenia do usługi Azure AD. Dla użytkownika, który dołącza urządzenia do usługi Azure AD przy użyciu usługi MFA staje się 2nd factor. 

Jeśli chcesz włączyć roaming stanu do usługi Azure AD, dzięki czemu urządzenia można synchronizować swoje ustawienia, zobacz [co stanu dla przedsiębiorstw roamingu?](enterprise-state-roaming-overview.md). Zalecamy włączenie tego ustawienia, nawet w przypadku hybrydowych usługi Azure AD urządzenia przyłączone do 

### <a name="deployment-options"></a>Opcje wdrożenia 

 

Przyłączanie do usługi Azure AD można wdrożyć za pomocą trzech różnych podejść:  

- **Samoobsługowe w trybie OOBE/ustawienia** — w trybie samoobsługi użytkowników z rzeczywistym użyciem za pośrednictwem usługi Azure AD join albo w ramach Windows poza pole środowiska (OOBE) lub z Windows ustawienia procesów.  

- **Rozwiązania Windows Autopilot** — rozwiązania Windows Autopilot umożliwia wstępnej konfiguracji urządzeń płynne w OOBE do wykonania sprzężenia Azure AD.   

- **Rejestracja zbiorcza** — rejestracja zbiorcza umożliwia sprzężenie administratora oparte na usłudze Azure AD za pomocą zbiorczego inicjowania obsługi administracyjnej narzędzia do konfigurowania urządzeń.  


Poniżej znajduje się porównanie tych trzech metod. 

 
||Instalator samoobsługi|Rozwiązania Windows Autopilot|Rejestrowanie zbiorcze|
|---|---|---|---|
|Wymagaj interakcji użytkownika, aby skonfigurować|Yes|Yes|Nie|
|Wymaga IT|Nie|Yes|Yes|
|Zastosowanie przepływów|Ustawienia & OOBE|Tylko w trybie OOBE|Tylko w trybie OOBE|
|Prawa administratora lokalnego do podstawowego użytkownika|Tak, domyślnie|Można konfigurować|Nie|
|Wymagana jest obsługa urządzeń OEM|Nie|Yes|Nie|
|Obsługiwane wersje|w wersji 1511 +|1709 +|1703 +|
 
Wybierz swoje metody wdrożenia lub metody recenzowania powyższej tabeli i przeglądając następujące zagadnienia dotyczące wdrażania każda z tych metod:  

- Twoje tech użytkownicy są pomysłowy przechodzić przez Instalatora samodzielnie? 

    - Samoobsługa można najlepszej metody dla tych użytkowników. Należy wziąć pod uwagę rozwiązania Windows Autopilot, aby ulepszyć środowisko użytkownika.  

- Czy użytkownicy zdalnego lub w ramach lokalnej firmy? 

    - Samoobsługa lub najlepszym rozwiązaniem dla użytkowników zdalnych, w przypadku instalacji Bezproblemowa praca rozwiązania Autopilot. 
 
- Wolisz konfiguracji usługi zarządzane przez administratora lub użytkownika na podstawie? 

    - Rejestracja zbiorcza sprawdzi się najlepiej w administratora oparte na wdrożenia, aby skonfigurować urządzenia przed przekazaniem użytkowników.     

- Czy kupują urządzenia od producentów OEM 1-2, czy masz szeroką dystrybucję OEM urządzenia?  

    - Zakup od producentów OEM ograniczone, którzy obsługują także rozwiązania Autopilot, możesz korzystać z ściślejszą integrację z rozwiązaniem Autopilot. 
 

 


## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanie urządzeniami](overview.md)

