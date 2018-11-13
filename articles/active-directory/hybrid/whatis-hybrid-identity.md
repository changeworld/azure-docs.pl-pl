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
ms.date: 11/02/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2aca42c23cc213d5d7e451105052d5d5d697b77d
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979475"
---
# <a name="hybrid-identity-and-microsoft-identity-solutions"></a>Rozwiązania tożsamości hybrydowej i tożsamości firmy Microsoft
Rozwiązania do obsługi tożsamości hybrydowych usługi [Microsoft Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) umożliwiają synchronizowanie lokalnych obiektów katalogu z usługą Azure AD bez utraty możliwości zarządzania użytkownikami lokalnymi. Pierwszą decyzją, którą należy podjąć podczas planowania synchronizacji lokalnej usługi Active Directory systemu Windows Server z usługą Azure AD, jest określenie, czy chcesz używać tożsamości zarządzanych, czy też tożsamości federacyjnej. 

- **Tożsamości zarządzane** — grupy i konta użytkowników, które są synchronizowane z lokalnej usługi Active Directory, a uwierzytelnianie użytkowników jest zarządzane przez platformę Azure.   
- **Tożsamości federacyjne** umożliwiają większą kontrolę nad użytkownikami przez oddzielenie uwierzytelniania użytkownika od platformy Azure i delegowanie uwierzytelniania do zaufanego, lokalnego dostawcy tożsamości. 

Dostępnych jest kilka opcji konfigurowania tożsamości hybrydowej. Po ustaleniu, który model tożsamości najlepiej odpowiada wymaganiom Twojej organizacji, należy również wziąć pod uwagę czas, istniejącą infrastrukturę, złożoność i koszt. Te czynniki są różne w różnych organizacjach i mogą się zmieniać wraz z upływem czasu. Jeśli jednak wymagania naprawdę ulegną zmianie, można przełączyć się do innego modelu tożsamości.

## <a name="managed-identity"></a>Tożsamość zarządzana 

Tożsamość zarządzana to najprostszy sposób synchronizowania lokalnych obiektów katalogu (użytkowników i grup) z usługą Azure AD. 

![Zsynchronizowana tożsamość hybrydowa](./media/whatis-hybrid-identity/managed.png)

Mimo że tożsamość zarządzana jest najprostszą i najszybszą metodą, użytkownicy nadal muszą mieć oddzielne hasła na potrzeby zasobów w chmurze. Aby tego uniknąć, możesz również (opcjonalnie) [zsynchronizować skróty haseł użytkowników](how-to-connect-password-hash-synchronization.md) z katalogiem usługi Azure AD. Synchronizacja skrótów haseł pozwala użytkownikom na logowanie się do zasobów organizacji opartych na chmurze za pomocą takiej samej nazwy użytkownika i hasła, jakich używają lokalnie. Narzędzie Azure AD Connect okresowo sprawdza lokalny katalog pod kątem zmian i na bieżąco synchronizuje katalog usługi Azure AD. Jeśli atrybut użytkownika lub hasło zostaną zmienione w lokalnej usłudze Active Directory, te elementy są automatycznie aktualizowane w usłudze Azure AD. 

W przypadku większości organizacji, w których istotne jest tylko umożliwienie użytkownikom zalogowania się do usługi Office 365, aplikacji SaaS i innych zasobów bazujących na usłudze Azure AD, zalecana jest domyślna opcja synchronizacji skrótów haseł. Jeśli ta opcja nie jest odpowiednia dla Twojej organizacji, konieczne będzie wybranie między uwierzytelnianiem przekazywanym i usługami AD FS.

> [!TIP]
> Hasła użytkowników są przechowywane w lokalnej usłudze Active Directory systemu Windows Server w postaci wartości skrótów, które reprezentują rzeczywiste hasła użytkowników. Wartość skrótu jest wynikiem jednokierunkowej funkcji matematycznej (algorytmu wyznaczania wartości skrótu). Nie ma możliwości przywrócenia wyniku tej jednokierunkowej funkcji z powrotem do hasła w postaci zwykłego tekstu. Skrótu hasła nie można użyć w celu zalogowania się do sieci lokalnej. Jeśli wybierzesz opcję synchronizacji haseł, program Azure AD Connect wyodrębnia skróty haseł z lokalnej usługi Active Directory i stosuje do nich dodatkowe środki zabezpieczeń przed ich zsynchronizowaniem z usługą Azure AD. Synchronizacja skrótów haseł może być również stosowana razem z zapisywaniem zwrotnym haseł, aby umożliwić samoobsługowe resetowanie haseł w usłudze Azure AD. Ponadto dla użytkowników pracujących na komputerach przyłączonych do domeny, które są połączone z siecią firmową, można włączyć logowanie jednokrotne (SSO). Użytkownicy z włączoną funkcją logowania jednokrotnego muszą tylko raz wprowadzać swoją nazwę użytkownika, aby móc bezpiecznie uzyskiwać dostęp do zasobów w chmurze. 
>

## <a name="pass-through-authentication"></a>Uwierzytelnianie przekazywane

[Uwierzytelnianie przekazywane usługi Azure AD](how-to-connect-pta.md) udostępnia proste rozwiązanie do weryfikacji haseł dla usług bazujących na usłudze Azure AD, które wykorzystuje lokalną usługę Active Directory. Jeśli zasady zabezpieczeń i zgodności w Twojej organizacji nie zezwalają na wysyłanie haseł użytkowników (nawet w formie skrótów) i potrzebujesz tylko obsługi klasycznego logowania jednokrotnego dla urządzeń przyłączonych do domeny, zaleca się przeprowadzenie oceny możliwości korzystania z uwierzytelniania przekazywanego. Uwierzytelnianie przekazywane nie wymaga wdrażania żadnych elementów w strefie DMZ, co upraszcza infrastrukturę wdrażania w porównaniu z usługami AD FS. Gdy użytkownicy logują się za pomocą usługi Azure AD, ta metoda uwierzytelniania weryfikuje ich hasła bezpośrednio w lokalnej usłudze Active Directory.

![Uwierzytelnianie przekazywane](./media/whatis-hybrid-identity/pass-through-authentication.png)

W przypadku korzystania z uwierzytelniania przekazywanego nie ma potrzeby tworzenia złożonej infrastruktury sieciowej i nie trzeba przechowywać haseł lokalnych w chmurze. W połączeniu z logowaniem jednokrotnym uwierzytelnianie przekazywane zapewnia naprawdę zintegrowane środowisko logowania się do usługi Azure AD lub innych usług w chmurze.

Uwierzytelnianie przekazywane jest konfigurowane za pomocą narzędzia Azure AD Connect, które używa prostego lokalnego agenta nasłuchującego żądań sprawdzania poprawności haseł. Agenta można łatwo wdrożyć na wielu komputerach w celu zapewnienia wysokiej dostępności i równoważenia obciążenia. Ponieważ cała komunikacja dotyczy tylko ruchu wychodzącego, nie jest wymagane instalowanie łącznika w strefie DMZ. Łącznik ma następujące wymagania dotyczące komputera serwera:

- Windows Server 2012 R2 lub nowszy
- Przyłączony do domeny w lesie, za pomocą którego użytkownicy są weryfikowani

## <a name="federated-identity-ad-fs"></a>Tożsamość federacyjna (AD FS)

Aby uzyskać większą kontrolę nad sposobem, w jaki użytkownicy uzyskują dostęp do usługi Office 365 i innych usług w chmurze, można skonfigurować synchronizację katalogów za pomocą logowania jednokrotnego (SSO) w taki sposób, aby w tym celu były używane usługi [Active Directory Federation Services (AD FS)](how-to-connect-fed-whatis.md). Federowanie logowań użytkowników z usługami AD FS powoduje delegowanie uwierzytelniania do serwera lokalnego, który weryfikuje poświadczenia użytkowników. W tym modelu lokalne poświadczenia usługi Active Directory nigdy nie są przekazywane do usługi Azure AD.

![Tożsamość federacyjna](./media/whatis-hybrid-identity/federated-identity.png)

Ta metoda logowania, nazywana również federacją tożsamości, zapewnia, że całe uwierzytelnianie użytkowników jest kontrolowane lokalnie, i umożliwia administratorom implementowanie bardziej rygorystycznych poziomów kontroli dostępu. Federacja tożsamości z usługami AD FS jest najbardziej złożoną opcją i wymaga wdrożenia dodatkowych serwerów w środowisku lokalnym. Federacja tożsamości zobowiązuje również do zapewnienia całodobowej pomocy technicznej dla infrastruktury usług Active Directory i AD FS. Taki wysoki poziom pomocy technicznej jest konieczny, ponieważ jeśli Twoje lokalne serwery dostępu do Internetu, kontrolera domeny lub usług AD FS będą niedostępne, użytkownicy nie będą mogą się logować do usług w chmurze.

> [!TIP]
> Jeśli zdecydujesz się na użycie federacji z usługami Active Directory Federation Services (AD FS), możesz opcjonalnie skonfigurować synchronizację skrótów haseł jako kopię zapasową na wypadek awarii infrastruktury usług AD FS.
>

## <a name="common-scenarios-and-recommendations"></a>Typowe scenariusze i zalecenia

Poniżej przedstawiono kilka typowych scenariuszy związanych z tożsamościami hybrydowymi i zarządzaniem dostępem wraz z zaleceniami co do tego, która opcja (lub opcje) obsługi tożsamości hybrydowych może być najbardziej odpowiednia w danym przypadku.

|Wymagana funkcjonalność:|PHS i SSO<sup>1</sup>| PTA i SSO<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|Automatyczne synchronizowanie z chmurą nowych kont użytkowników, kontaktów i grup utworzonych w lokalnej usłudze Active Directory.|![Zalecane](./media/whatis-hybrid-identity/ic195031.png)| ![Zalecane](./media/whatis-hybrid-identity/ic195031.png) |![Zalecane](./media/whatis-hybrid-identity/ic195031.png)|
|Konfigurowanie dzierżawy na potrzeby scenariuszy hybrydowych usługi Office 365.|![Zalecane](./media/whatis-hybrid-identity/ic195031.png)| ![Zalecane](./media/whatis-hybrid-identity/ic195031.png) |![Zalecane](./media/whatis-hybrid-identity/ic195031.png)|
|Umożliwienie użytkownikom logowania się i uzyskiwania dostępu do usług w chmurze przy użyciu swojego lokalnego hasła.|![Zalecane](./media/whatis-hybrid-identity/ic195031.png)| ![Zalecane](./media/whatis-hybrid-identity/ic195031.png) |![Zalecane](./media/whatis-hybrid-identity/ic195031.png)|
|Zaimplementowanie logowania jednokrotnego przy użyciu poświadczeń firmowych.|![Zalecane](./media/whatis-hybrid-identity/ic195031.png)| ![Zalecane](./media/whatis-hybrid-identity/ic195031.png) |![Zalecane](./media/whatis-hybrid-identity/ic195031.png)|
|Zagwarantowanie, że żadne skróty haseł nie będą przechowywane w chmurze.| |![Zalecane](./media/whatis-hybrid-identity/ic195031.png)|![Zalecane](./media/whatis-hybrid-identity/ic195031.png)|
|Umożliwienie działania rozwiązań obsługujących uwierzytelnianie wieloskładnikowe w chmurze.| |![Zalecane](./media/whatis-hybrid-identity/ic195031.png)|![Zalecane](./media/whatis-hybrid-identity/ic195031.png)|
|Umożliwienie działania rozwiązań obsługujących lokalne uwierzytelnianie wieloskładnikowe.| | |![Zalecane](./media/whatis-hybrid-identity/ic195031.png)|
|Obsługa uwierzytelniania użytkowników za pomocą kart inteligentnych<sup>4</sup>| | |![Zalecane](./media/whatis-hybrid-identity/ic195031.png)|
|Wyświetlanie powiadomień dotyczących wygasania haseł w portalu pakietu Office i na pulpicie systemu Windows 10.| | |![Zalecane](./media/whatis-hybrid-identity/ic195031.png)|

> <sup>1</sup> Synchronizacja skrótów haseł za pomocą logowania jednokrotnego.
>
> <sup>2</sup> Uwierzytelnianie przekazywane i logowanie jednokrotne. 
>
> <sup>3</sup> Federacyjne logowanie jednokrotne z użyciem usług AD FS.
>
> <sup>4</sup> Usługi AD FS można zintegrować z infrastrukturą kluczy publicznych przedsiębiorstwa, aby umożliwić logowanie przy użyciu certyfikatów. Mogą to być certyfikaty programowe wdrażane za pośrednictwem zaufanych kanałów aprowizowania, takich jak zarządzanie danymi głównymi lub obiekt zasad grupy, lub certyfikaty na kartach inteligentnych (w tym na kartach PIV/CAC) albo w usłudze Hello dla firm (zaufanie certyfikatu). Aby uzyskać więcej informacji na temat obsługi uwierzytelniania za pomocą kart inteligentnych, zobacz [ten blog](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/).
>

## <a name="what-is-azure-ad-connect"></a>Co to jest program Azure AD Connect?

Azure AD Connect to narzędzie firmy Microsoft, które umożliwia spełnienie wymagań związanych z tożsamością hybrydową.  Dzięki temu użytkownicy mogą posługiwać się wspólną tożsamością dla usługi Office 365, platformy Azure i aplikacji SaaS zintegrowanych z usługą Azure AD.  Oferuje ono następujące funkcje:
    
- [Synchronizacja](how-to-connect-sync-whatis.md) — ten składnik odpowiada za tworzenie użytkowników, grup i innych obiektów. Odpowiada także za zapewnienie zgodności informacji o tożsamości lokalnych użytkowników i grup z informacjami w chmurze.  Służy on też do synchronizowania skrótów haseł z usługą Azure AD.
- [Synchronizacja skrótów haseł](how-to-connect-password-hash-synchronization.md) — opcjonalny składnik, który pozwala użytkownikom na używanie tego samego hasła lokalnie i w chmurze dzięki zsynchronizowaniu skrótów haseł użytkowników z usługą Azure AD.
-   [AD FS i integracja federacyjna](how-to-connect-fed-whatis.md) — usługi federacyjne to opcjonalny składnik programu Azure AD Connect, za pomocą którego można skonfigurować środowisko hybrydowe przy użyciu lokalnej infrastruktury usług AD FS. Składnik ten udostępnia również funkcje zarządzania usługami AD FS, takie jak odnawianie certyfikatów i dodatkowe wdrożenia serwera usług AD FS.
-   [Uwierzytelnianie przekazywane](how-to-connect-pta.md) — opcjonalny składnik, który pozwala użytkownikom używać tego samego hasła w infrastrukturze lokalnej i w chmurze, ale nie wymaga dodatkowej infrastruktury w środowisku federacyjnym.
-   [Integracja serwera PingFederate i federacji](how-to-connect-install-custom.md#configuring-federation-with-pingfederate) — inna opcja federacji, która umożliwia korzystanie z serwera PingFederate jako dostawcy tożsamości.
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









