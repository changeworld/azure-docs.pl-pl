---
title: Łączenie usługi Active Directory z usługą Azure Active Directory. | Microsoft Docs
description: Program Azure AD Connect umożliwia integrowanie katalogów lokalnych z usługą Azure Active Directory. Dzięki temu można posługiwać się wspólną tożsamością dla usługi Office 365, platformy Azure i aplikacji SaaS zintegrowanych z usługą Azure AD.
keywords: wprowadzenie do programu Azure AD Connect, omówienie programu Azure AD Connect, co to jest program Azure AD Connect, instalowanie usługi Active Directory
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/18/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 40ac3ca92c65607df056b883608dde60d816143e
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181785"
---
# <a name="hybrid-identity-and-microsofts-identity-solutions"></a>Rozwiązania tożsamości hybrydowej i tożsamości firmy Microsoft
Obecnie małe i duże firmy w coraz większym stopniu korzystają zarówno z aplikacji lokalnych, jak i aplikacji w chmurze.  Zapewnienie użytkownikom dostępu do tych aplikacji stało się nie lada wyzwaniem.

Rozwiązania firmy Microsoft do obsługi tożsamości obejmują zarówno funkcje lokalne, jak i chmurowe, tworząc jedną tożsamość użytkownika na potrzeby uwierzytelniania i autoryzacji w kontekście wszystkich zasobów, niezależnie od lokalizacji. Nazywamy to tożsamością hybrydową.

## <a name="what-is-azure-ad-connect"></a>Co to jest program Azure AD Connect?

Azure AD Connect to narzędzie firmy Microsoft, które umożliwia spełnienie wymagań związanych z tożsamością hybrydową.  Dzięki temu użytkownicy mogą posługiwać się wspólną tożsamością dla usługi Office 365, platformy Azure i aplikacji SaaS zintegrowanych z usługą Azure AD.  Oferuje ono następujące funkcje:
    
- [Synchronizacja](how-to-connect-sync-whatis.md) — ten składnik odpowiada za tworzenie użytkowników, grup i innych obiektów. Odpowiada także za zapewnienie zgodności informacji o tożsamości lokalnych użytkowników i grup z informacjami w chmurze.  Służy on też do synchronizowania skrótów haseł z usługą Azure AD.
-   [AD FS i integracja federacyjna](how-to-connect-fed-whatis.md) — usługi federacyjne to opcjonalny składnik programu Azure AD Connect, za pomocą którego można skonfigurować środowisko hybrydowe przy użyciu lokalnej infrastruktury usług AD FS. Składnik ten udostępnia również funkcje zarządzania usługami AD FS, takie jak odnawianie certyfikatów i dodatkowe wdrożenia serwera usług AD FS.
-   [Uwierzytelnianie przekazywane](how-to-connect-pta.md) — opcjonalny składnik, który pozwala użytkownikom używać tego samego hasła w infrastrukturze lokalnej i w chmurze, ale nie wymaga dodatkowej infrastruktury w środowisku federacyjnym.
-   [Monitorowanie kondycji](whatis-hybrid-identity-health.md) — składnik Azure AD Connect Health zapewnia zaawansowane monitorowanie z możliwością wyświetlania aktywności w centralnej lokalizacji w witrynie Azure Portal. 


![Co to jest program Azure AD Connect](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Co to jest program Azure AD Connect Health?

Program Azure Active Directory (Azure AD) Connect Health pomaga monitorować i zdobywać informacje o lokalnej infrastrukturze do obsługi tożsamości oraz usługach synchronizacji. Umożliwia utrzymywanie niezawodnego połączenia z usługami Office 365 i Microsoft Online Services, udostępniając funkcje monitorowania kluczowych składników tożsamości, takich jak serwery usług Active Directory Federation Services (AD FS), serwery programu Azure AD Connect (nazywane także aparatem synchronizacji), kontrolery domeny usługi Active Directory itp. Zapewnia także łatwy dostęp do kluczowych punktów danych dotyczących tych składników, umożliwiając zdobywanie informacji o użyciu i innych istotnych parametrach, co pozwala na świadome podejmowanie decyzji.

Informacje są prezentowane w [portalu programu Azure AD Connect Health](https://aka.ms/aadconnecthealth). Korzystając z portalu programu Azure AD Connect Health, możesz wyświetlać alerty, wyniki monitorowania wydajności, analizy użycia i inne informacje. Program Azure AD Connect Health pokazuje kondycję kluczowych składników tożsamości w jednym miejscu.

![Co to jest program Azure AD Connect Health](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)


Wraz z rozwojem funkcji programu Azure AD Connect Health portal zapewnia wgląd w dodatkowe informacje za pośrednictwem pulpitu nawigacyjnego obsługiwanego przy użyciu tożsamości. Dzięki temu można zapewnić użytkownikom jeszcze bardziej niezawodne, sprawne i zintegrowane środowisko umożliwiające wydajne działanie.


## <a name="why-use-azure-ad-connect"></a>Dlaczego warto korzystać z programu Azure AD Connect?
Zintegrowanie katalogów lokalnych z usługą Azure AD zwiększa produktywność użytkowników, zapewniając wspólną tożsamość na potrzeby dostępu do zasobów, zarówno lokalnych, jak i w chmurze. Użytkownicy i organizacje uzyskują następujące korzyści:

* Użytkownicy mogą korzystać z jednej tożsamości w celu uzyskiwania dostępu do aplikacji lokalnych oraz usług w chmurze, takich jak Office 365.
* Jedno narzędzie zapewniające łatwe w użyciu środowisko wdrażania na potrzeby synchronizacji i logowania.
* Najnowsze możliwości we wszystkich scenariuszach. Program Azure AD Connect zastępuje starsze wersje narzędzi do integracji tożsamości, takie jak DirSync i Azure AD Sync. Aby uzyskać więcej informacji, zobacz [Porównanie narzędzi do integracji katalogów tożsamości hybrydowej](plan-hybrid-identity-design-considerations-tools-comparison.md).

## <a name="why-use-azure-ad-connect-health"></a>Dlaczego warto korzystać z programu Azure AD Connect Health?
Zintegrowanie katalogów lokalnych z usługą Azure AD zwiększa produktywność użytkowników, zapewniając wspólną tożsamość na potrzeby dostępu do zasobów — zarówno lokalnych, jak i w chmurze. Jednak tego rodzaju integracja wiąże się z koniecznością zapewnienia dobrej kondycji środowiska, aby użytkownicy mieli niezawodny dostęp z dowolnego urządzenia do zasobów lokalnych i znajdujących się w chmurze. Program Azure AD Connect Health pomaga w monitorowaniu i zdobywaniu informacji o lokalnej infrastrukturze do obsługi tożsamości, która umożliwia dostęp do usługi Office 365 lub innych aplikacji usługi Azure AD. Wymaga to jedynie zainstalowania agenta na każdym z lokalnych serwerów tożsamości.

### <a name="azure-ad-connect-health-for-ad-fshow-to-connect-health-adfsmd"></a>[Program Azure AD Connect Health dla usług AD FS](how-to-connect-health-adfs.md)
Program Azure AD Connect Health dla usług AD FS obsługuje usługi AD FS 2.0 w systemach Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 i Windows Server 2016. Obsługuje także monitorowanie serwerów proxy usług AD FS lub serwerów proxy aplikacji internetowej, które zapewniają obsługę uwierzytelniania w przypadku dostępu do ekstranetu. Dzięki prostej i szybkiej instalacji agenta kondycji program Azure AD Connect Health dla usług AD FS oferuje zestaw kluczowych funkcji.

#### <a name="key-benefits-and-best-practices"></a>Najważniejsze korzyści i najlepsze rozwiązania

- *Większe bezpieczeństwo*
  - [Trendy związane z blokadą ekstranetu](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)
  - [Raport dotyczący nieudanych logowań](how-to-connect-health-adfs.md#risky-ip-report-public-preview) 
  - Zgodność z [zasadami ochrony prywatności](reference-connect-health-user-privacy.md)    
- *Alerty dotyczące wszystkich [krytycznych problemów z systemem AD FS](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)*
    - Konfiguracja i dostępność serwera 
    - [Wydajność i łączność](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs) 
  - Regularna konserwacja    
- *Łatwe wdrażanie i zarządzanie*
  - Szybka [instalacja agenta](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs) 
  - Automatyczne uaktualnianie agenta do najnowszej wersji 
  - Dane dostępne w portalu w ciągu kilku minut    
- *Zaawansowane [metryki użycia](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)* 
  - Lista najczęściej używanych aplikacji
  - Lokalizacje sieciowe i połączenia TCP
  - Żądania tokenów dla pojedynczych serwerów    
- *Fantastyczne środowisko pracy użytkownika* 
  - Interfejs podobny do pulpitu nawigacyjnego z witryny Azure Portal
  - [Alerty w wiadomościach e-mail](how-to-connect-health-adfs.md#alerts-for-ad-fs)    

#### <a name="feature-highlight"></a>Podsumowanie funkcji

*   Monitorowanie z alertami, które informują, kiedy serwery usług AD FS i serwery proxy usług AD FS nie są w dobrej kondycji
*   Powiadomienia e-mail dotyczące alertów krytycznych
*   Trendy w danych dotyczących wydajności przydatne do planowania wydajności usług AD FS
*   Analizy użycia dotyczące różnych wariantów logowania się do usług AD FS (aplikacje, użytkownicy, lokalizacja sieciowa itp.)
*   Raporty dotyczące usług AD FS, na przykład lista 50 użytkowników, którzy najczęściej nieprawidłowo podawali nazwę użytkownika/hasło, wraz z ostatnim adresem IP
*   Raport ryzykownych adresów IP dla nieudanych logowań do usług AD FS

Tutaj dowiesz się więcej o [używaniu programu Azure AD Connect Health dla usług AD FS](how-to-connect-health-adfs.md)

### <a name="azure-ad-connect-health-for-synchow-to-connect-health-syncmd"></a>[Program Azure AD Connect Health do celów synchronizacji](how-to-connect-health-sync.md)
Program Azure AD Connect Health do celów synchronizacji monitoruje i udostępnia informacje na temat procesów synchronizacji, które mają miejsce między lokalną usługą Active Directory i usługą Azure AD. Program Azure AD Connect Health do celów synchronizacji oferuje następujący zestaw kluczowych funkcji:

* Monitorowanie z alertami, które informują, kiedy serwer programu Azure AD Connect (zwany też aparatem synchronizacji) nie jest w dobrej kondycji
* Powiadomienia e-mail dotyczące alertów krytycznych
* Wgląd w dane operacyjne dotyczące synchronizacji, między innymi wykresy opóźnień operacji synchronizacji i trendy w innych operacjach, takich jak dodawanie, aktualizowanie i usuwanie
* Przegląd informacji na temat właściwości synchronizacji i ostatniego pomyślnego eksportu do usługi Azure AD
* Raporty o błędach synchronizacji na poziomie obiektu \(nie wymagają usługi Azure AD w wersji Premium\)

Tutaj dowiesz się więcej o [używaniu programu Azure AD Connect Health do celów synchronizacji](how-to-connect-health-sync.md)

### <a name="azure-ad-connect-health-for-ad-dshow-to-connect-health-addsmd"></a>[Program Azure AD Connect Health dla usług AD DS](how-to-connect-health-adds.md)
Program Azure AD Connect Health dla usług Active Directory Domain Services (AD DS) oferuje funkcje monitorowania kontrolerów domeny zainstalowanych w systemach Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 i Windows Server 2016. Instalacja agenta kondycji umożliwia monitorowanie lokalnego środowiska usług AD DS z chmury. Program Azure AD Connect Health dla usług AD DS oferuje następujący zestaw kluczowych funkcji:

* Monitorowanie alertów w celu wykrywania złej kondycji kontrolerów domeny wraz z powiadomieniami e-mail w przypadku alertów krytycznych
* Pulpit nawigacyjny Kontrolery domeny, który zapewnia szybki wgląd w kondycję i stan operacyjny kontrolerów domeny
* Pulpit nawigacyjny Stan replikacji zawierający najnowsze informacje o replikacji wraz z linkami do poradników rozwiązywania problemów w przypadku wykrycia błędów
* Szybki dostęp z dowolnego miejsca do wykresów danych wydajności utworzonych na podstawie popularnych liczników wydajności, które są niezbędne do celów monitorowania i rozwiązywania problemów

Tutaj dowiesz się więcej o [używaniu programu Azure AD Connect Health dla usług AD DS](how-to-connect-health-adds.md)

## <a name="next-steps"></a>Następne kroki


- [Sprzęt i wymagania wstępne](how-to-connect-install-prerequisites.md) 
- [Ustawienia ekspresowe](how-to-connect-install-express.md)
- [Ustawienia dostosowane](how-to-connect-install-custom.md)
- [Synchronizacja skrótów haseł](how-to-connect-password-hash-synchronization.md)|
- [Uwierzytelnianie przekazywane](how-to-connect-pta.md)
- [Program Azure AD Connect a federacja](how-to-connect-fed-whatis.md)
- [Instalowanie agentów programu Azure AD Connect Health](how-to-connect-health-agent-install.md) 
- [Synchronizacja programu Azure AD Connect](how-to-connect-sync-whatis.md)
- [Historia wersji](reference-connect-version-history.md)
- [Porównanie narzędzi do integracji katalogów](plan-hybrid-identity-design-considerations-tools-comparison.md)
- [Azure AD Connect — Często zadawane pytania](reference-connect-faq.md)









