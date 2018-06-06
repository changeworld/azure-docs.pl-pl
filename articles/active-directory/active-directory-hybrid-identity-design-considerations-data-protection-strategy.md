---
title: Tożsamość hybrydowym - strategii ochrony danych Azure | Dokumentacja firmy Microsoft
description: Należy zdefiniować strategię ochrony danych dla hybrydowych rozwiązań tożsamości w celu spełnienia wymagań biznesowych, zdefiniowanych.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2017
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 71a49b3d1ce22906a534a71f745685981b93f594
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801902"
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Definiowanie strategii ochrony danych dla rozwiązania z tożsamością hybrydową
W tym zadaniu będziesz definiować hybrydowego tożsamości rozwiązanie, aby spełniać wymagania biznesowe zdefiniowaną w strategii ochrony danych:

* [Określenie wymagań dotyczących ochrony danych](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [Określenie wymagań dotyczących zarządzania zawartością](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Określenie wymagań dotyczących kontroli dostępu](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Określenie wymagań dotyczących odpowiedzi na zdarzenia](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Zdefiniuj opcje ochrony danych
Zgodnie z objaśnieniem w [określenie wymagań synchronizacji katalogu](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md), usługi Microsoft Azure AD mogą wykonywać synchronizację z sieci lokalnej usług domenowych Active Directory (AD DS). Integracja ta umożliwia organizacjom użycia usługi Azure AD w celu zweryfikowania poświadczeń użytkowników, podczas dostępu do zasobów firmy. Można to zrobić w obu przypadkach: dane na rest lokalnie i w chmurze. Dostęp do danych w usłudze Azure AD wymaga uwierzytelnienia użytkownika za pośrednictwem usługi tokenu zabezpieczającego (STS).

Po uwierzytelnieniu główna nazwa użytkownika (UPN) są odczytywane z tokenu uwierzytelniania. Następnie system autoryzacji określa replikowanych partycji i kontenera odpowiadający użytkownika domeny. Informacje dotyczące istnienia, włączona i roli użytkownika następnie pomaga systemu autoryzacji określa, czy dostęp do dzierżawy docelowy jest autoryzowany dla użytkownika w tej sesji. Pewne akcje autoryzowanych (w szczególności utworzyć resetowania użytkownika i hasło) Utwórz dziennik inspekcji, które administrator dzierżawy następnie używa do zarządzania wysiłków zgodności lub dochodzenia.

Przenoszenie danych z centrum danych lokalnych w magazynie Azure przez połączenie internetowe mogą nie być możliwe ze względu na ilość danych, dostępnej przepustowości lub innych kwestii. [Usługi Import/Eksport magazynu Azure](../storage/common/storage-import-export-service.md) zapewnia oparte na sprzęcie opcję dla wprowadzania do pobierania dużych ilości danych w magazynie obiektów blob. Umożliwia wysłanie [zaszyfrowane przez funkcję BitLocker](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) dysków twardych bezpośrednio do centrum danych Azure gdzie operatorom chmury przekazać zawartość do konta magazynu lub pobierają dane platformy Azure do dysków w taki sposób, aby powrócić do Ciebie. Tylko dla zaszyfrowanych dysków uznane za zaakceptowane dla tego procesu (przy użyciu klucza funkcji BitLocker, generowane przez usługę podczas konfigurowania zadania). Klucza funkcji BitLocker zapewnia Azure oddzielnie, zapewniając poza pasmem klucza udostępniania.

Ponieważ przesyłanych danych może odbywać się w różnych scenariuszy, ma również zastosowanie dowiedzieć się, że używa programu Microsoft Azure [sieci wirtualne](https://azure.microsoft.com/documentation/services/virtual-network/) do izolacji ruchu dzierżawców od siebie nawzajem stosowania środków, takich jak poziom hosta i gościa zapór, filtrowanie pakietów IP, port blokowania i punktów końcowych HTTPS. Jednak większość komunikacji wewnętrznej platformy Azure, w tym infrastruktury do infrastruktury i infrastruktury do klienta (lokalnego), również są szyfrowane. Inny scenariusz ważne jest komunikacji w centrach danych platformy Azure; Firma Microsoft zarządza sieci, aby mieć pewność, że maszyna wirtualna nie można personifikować lub podsłuchiwać adresu IP innego. Protokoły TLS/SSL jest używany podczas uzyskiwania dostępu do usługi Azure Storage lub baz danych lub podczas nawiązywania połączenia usługi w chmurze. W takim przypadku administrator klienta jest odpowiedzialny za uzyskiwanie certyfikatu TLS/SSL i wdrażania go do infrastruktury dzierżawy. Przenoszenie ruchu danych między maszynami wirtualnymi w tym samym wdrożeniu lub między dzierżawcami w ramach jednego wdrożenia za pośrednictwem sieci wirtualnej Microsoft Azure mogą być chronione przy użyciu protokołów szyfrowaną komunikację HTTPS, SSL/TLS lub innych użytkowników.

W zależności od odpowiedzi na pytania w [ustalenie wymagań dotyczących ochrony danych](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md), powinno być możliwe ustalenie sposobu ochrony danych i sposobu rozwiązania z tożsamością hybrydową mogą pomóc z tego procesu. W poniższej tabeli przedstawiono opcje obsługiwane przez platformę Azure, które są dostępne dla każdego scenariusza ochrony danych.

| Opcje ochrony danych | Magazynowane w chmurze | W pozostałych lokalnymi | W drodze |
| --- | --- | --- | --- |
| Szyfrowanie dysków funkcją BitLocker |X |X | |
| Program SQL Server do szyfrowania bazy danych |X |X | |
| Szyfrowanie maszyny Wirtualnej do maszyny Wirtualnej | | |X |
| SSL/TLS | | |X |
| Sieć VPN | | |X |

> [!NOTE]
> Odczyt [zgodności przez funkcję](https://azure.microsoft.com/support/trust-center/services/) w [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) Aby dowiedzieć się więcej o certyfikatach, które jest zgodne z każdej usługi Azure.
> Ponieważ opcje ochrony danych używa wielowarstwowych podejście, porównanie te opcje nie są stosowane dla tego zadania. Upewnij się, że są wykorzystuje wszystkie opcje dostępne dla każdego stanu danych.
>
>

## <a name="define-content-management-options"></a>Zdefiniuj opcje zarządzania zawartością
Jedną z zalet Zarządzanie hybrydowej infrastruktury tożsamości za pomocą usługi Azure AD jest, że proces jest w pełni przezroczyste z perspektywy użytkownika końcowego. Użytkownik próbuje uzyskać dostęp do udostępnionych zasobów, zasób wymaga uwierzytelnienia, użytkownik musi wysłać żądanie uwierzytelnienia do usługi Azure AD w celu uzyskania tokenu i uzyskać dostęp do zasobu. Ta cały proces przebiega w tle, bez interakcji z użytkownikiem. Istnieje również możliwość udzielenia uprawnienia do [grupy](active-directory-manage-groups.md#getting-started-with-access-management) użytkowników, aby umożliwić im wykonywanie niektórych typowych akcji.

Organizacje, które są obawy dotyczące prywatności danych zwykle wymagają klasyfikacji danych dla ich rozwiązania. Jeśli ich bieżącej infrastruktury lokalnej jest już z klasyfikacji danych, istnieje możliwość użycia usługi Azure AD jako głównym repozytorium dla tożsamości użytkownika. Typowe narzędzia jest używane lokalnymi klasyfikacji danych jest nazywana [Data Classification Toolkit](https://msdn.microsoft.com/library/Hh204743.aspx) dla systemu Windows Server 2012 R2. To narzędzie może pomóc zidentyfikować, klasyfikować i chronić dane na serwerach plików w chmurze prywatnej. Istnieje również możliwość użycia [automatycznej klasyfikacji plików](https://technet.microsoft.com/library/hh831672.aspx) w systemie Windows Server 2012, aby wykonać to zadanie.

Jeśli Twoja organizacja nie ma klasyfikacji danych w miejscu, ale trzeba chronić poufne pliki bez dodawania nowych serwerów lokalnych, może użyć Microsoft [usługi Azure Rights Management](https://technet.microsoft.com/library/JJ585026.aspx).  Usługa Azure RMS używa szyfrowania, tożsamości i zasad autoryzacji, aby ułatwić zabezpieczanie plików i wiadomości e-mail i działa na wielu urządzeniach — telefonach, tablety i komputery. Ponieważ Azure RMS to usługa w chmurze, nie istnieje potrzeba jawnego konfigurowania relacji zaufania z innymi organizacjami przed udostępnieniem chronionej zawartości z nimi. Jeżeli korzystają już one usługi Office 365 lub katalogu usługi Azure AD, współpraca między organizacjami jest obsługiwana automatycznie. Możesz także zsynchronizować wyłącznie atrybutów katalogu usługi Azure RMS potrzebne do obsługi tożsamości wspólnych dla lokalnych kont usługi Active Directory przy użyciu usług Azure do synchronizacji Active Directory (AAD Sync) lub Azure AD Connect.

Jest to ważna część zarządzania zawartością zrozumienie, kto uzyskuje dostęp do zasobów, w związku z tym możliwość rejestrowania sformatowanego jest ważne dla rozwiązania do zarządzania tożsamościami. Usługa Azure AD zapewnia dziennika ponad 30 dni, w tym:

* Zmiany w członkostwo roli (przykład: użytkownik dodany do roli administratora globalnego)
* Poświadczenia aktualizacji (przykład: zmiany hasła)
* Zarządzanie domenami (przykład: weryfikowanie domeny niestandardowej usuwania domeny)
* Dodawanie lub usuwanie aplikacji
* Zarządzanie użytkownikami (przykład: Dodawanie, usuwanie, aktualizowanie użytkownika)
* Dodawanie lub usuwanie licencji

> [!NOTE]
> Odczyt [zabezpieczeń firmy Microsoft Azure i zarządzanie dziennikiem inspekcji](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) Aby dowiedzieć się więcej na temat możliwości rejestrowania zdarzeń w systemie Azure.
> W zależności od odpowiedzi na pytania w [ustalić wymagania dotyczące zarządzania zawartością](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md), powinno być możliwe ustalenie, jak zawartości, które mają być zarządzane w rozwiązaniu tożsamości hybrydowej. Może być zintegrowany z usługą Azure AD są wszystkie opcje widoczne w tabeli 6, ważne jest określenie, które jest bardziej odpowiednie do potrzeb biznesowych.
>
>

| Opcje zarządzania zawartością | Zalety | Wady |
| --- | --- | --- |
| Scentralizowanie lokalne (Active Directory Rights Management Server) |Pełną kontrolę nad infrastruktury serwera odpowiedzialny za klasyfikacji danych <br> Wbudowane możliwości w systemie Windows Server, nie jest konieczne dodatkowe licencji lub subskrypcji <br> Można zintegrować z usługą Azure AD w scenariuszu hybrydowym <br> Obsługuje możliwości zarządzania (IRM) prawa informacji w usługach Online firmy Microsoft, takich jak Exchange Online i SharePoint Online, jak również usługi Office 365 <br> Obsługuje lokalne produkty serwerowe firmy Microsoft, takich jak Exchange Server, SharePoint Server i serwery plików, z systemem Windows Server i infrastrukturą klasyfikacji plików (FCI). |Wyższy konserwacji (trzymać się aktualizacje, konfiguracji i potencjalne uaktualnienia), od momentu IT jest właścicielem serwera <br> Wymaga infrastruktury serwera lokalnego<br> Nie natywnie wykorzystać możliwości platformy Azure |
| Scentralizowanie w chmurze (Azure RMS) |Łatwiejsze zarządzanie w porównaniu z rozwiązania lokalnego <br> Może być zintegrowany z usługami AD DS w scenariuszu hybrydowym <br>  W pełni zintegrowana z usługą Azure AD <br> Nie wymaga serwera lokalnego w celu wdrożenia usługi <br> Obsługuje lokalne produkty serwerowe firmy Microsoft, takich jak Exchange Server, SharePoint Server i serwery plików, z systemem Windows Server i klasyfikacji plików, infrastruktury (FCI) <br> IT, może mieć pełną kontrolę nad kluczem ich dzierżawy z funkcją BYOK. |Organizacja musi mieć subskrypcję chmury, która obsługuje usługę RMS <br> Organizacja musi mieć katalog usługi Azure AD, aby obsługiwać uwierzytelnianie użytkowników dla usług RMS |
| Hybrydowe (zintegrowana z usługi Azure RMS, lokalnymi Active Directory Rights Management Server) |W tym scenariuszu akumuluje korzyści, scentralizowane lokalnie i w chmurze. |Organizacja musi mieć subskrypcję chmury, która obsługuje usługę RMS <br> Organizacja musi mieć katalog usługi Azure AD, aby obsługiwać uwierzytelnianie użytkowników dla usług RMS, <br> Wymaga połączenia między usługą w chmurze platformy Azure i lokalnej infrastruktury |

## <a name="define-access-control-options"></a>Zdefiniuj opcje kontroli dostępu
Dzięki wykorzystaniu uwierzytelniania, autoryzacji i możliwości kontroli dostępu są dostępne w usłudze Azure AD można włączyć firmy do korzystania z repozytorium tożsamości centralnej, zapewniając użytkownikom i partnerami, aby używać jednego logowania jednokrotnego (SSO) jak pokazano na poniższej ilustracji:

![](./media/hybrid-id-design-considerations/centralized-management.png)

Scentralizowane zarządzanie i w pełni integracji z innych katalogów

Azure Active Directory zapewnia jednokrotnego do tysięcy aplikacji SaaS i lokalnej aplikacji sieci web. Zobacz [listę zgodności federacyjnych usługi Azure Active Directory: dostawcy tożsamości innych firm, które mogą służyć do implementowania rejestracji jednokrotnej](https://msdn.microsoft.com/library/azure/jj679342.aspx) artykułu, aby uzyskać więcej informacji o rejestracji Jednokrotnej innej firmy, które zostały przetestowane przez firmę Microsoft. Ta funkcja umożliwia organizacji, aby zaimplementować różne scenariusze B2B przy zachowaniu kontroli nad zarządzania tożsamościami i dostępem. Jednak podczas B2B Projektowanie procesu, ważne jest, aby poznać metodę uwierzytelniania, która jest używana przez partnera i sprawdź, czy ta metoda jest obsługiwana przez platformę Azure. Obecnie następujące metody są obsługiwane przez usługę Azure AD:

* Security Assertion Markup Language (SAML)
* OAuth
* Kerberos
* Tokeny
* Certyfikaty

> [!NOTE]
> Przeczytaj [protokoły uwierzytelniania usługi Active Directory Azure](https://msdn.microsoft.com/library/azure/dn151124.aspx) Aby dowiedzieć się więcej informacji dotyczących każdego protokołu i jego możliwości na platformie Azure.
>
>

Za pomocą techniczną usługi Azure AD, aplikacji mobilnych biznesowych, można użyć tego samego środowiska łatwe uwierzytelnianie usług Mobile Services sposób umożliwić pracownikom do logowania się do swoich aplikacji dla urządzeń przenośnych przy użyciu poświadczeń firmowych usługi Active Directory. W przypadku tej funkcji usługi Azure AD jest obsługiwany jako dostawcy tożsamości w usłudze Mobile Services obok innych dostawców tożsamości już obsługiwane (w tym Accounts firmy Microsoft, identyfikator usługi Facebook, Google identyfikator i identyfikator Twitter). Użycie poświadczeń użytkownika znajduje się w usługach AD DS firmy, aplikacji lokalnych powinny być przezroczyste dostęp z partnerami i użytkowników pochodzących z chmury. Zarządzanie kontrolą dostępu warunkowego użytkownika do aplikacji sieci web (opartych na chmurze), interfejs API sieci web, usług chmurowych firmy Microsoft, aplikacji SaaS innych firm i aplikacji natywnych (klientów urządzeń przenośnych) i korzystnie zabezpieczeń, inspekcje, raportowanie wszystko w jednym miejsce. Jednak zaleca się sprawdzanie poprawności wdrożenia w środowisku produkcyjnym bez lub z ograniczoną liczbą użytkowników.

> [!TIP]
> należy podać, czy usługi Azure AD nie ma zasad grupy ma usług AD DS. Aby wymusić zasady dla urządzeń, należy rozwiązania do zarządzania urządzeniami przenośnymi, takie jak [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx).
>
>

Po uwierzytelnieniu użytkownika przy użyciu usługi Azure AD, należy oszacować poziom dostępu, który użytkownik ma. Poziom dostępu, który ma użytkownik nad zasobem może się różnić. Gdy usługi Azure AD można dodać dodatkowe zabezpieczenia warstwy kontrolowanie dostępu do niektórych zasobów, należy pamiętać, że zasób sam ma także własną listę kontroli dostępu oddzielnie, takich jak kontrola dostępu do plików znajdujących się na serwerze plików. Poniższa ilustracja zawiera podsumowanie poziomy kontroli dostępu, który może mieć w scenariuszu hybrydowym:

![](./media/hybrid-id-design-considerations/accesscontrol.png)

Każdy interakcji na diagramie pokazano na rysunku x reprezentuje jeden scenariusz kontroli dostępu może być objętych przez usługę Azure AD. Poniżej są opis poszczególnych scenariuszy:

1. Dostęp warunkowy do aplikacji, które są hostowane lokalnie: można używać zarejestrowanych urządzeń z zasadami dostępu dla aplikacji, które są skonfigurowane do korzystania z usług AD FS w systemie Windows Server 2012 R2. Aby uzyskać więcej informacji na temat konfigurowania lokalnego dostępu warunkowego, zobacz [Setting up On-premises Conditional Access using Azure Active Directory Device Registration](active-directory-conditional-access-azure-portal.md) (Konfigurowanie lokalnego dostępu warunkowego przy użyciu usługi Rejestracja urządzeń w usłudze Azure Active Directory).

2. Kontrola dostępu do portalu Azure: Azure umożliwia również kontrolować dostęp do portalu przy użyciu kontroli dostępu opartej na rolach (RBAC)). Ta metoda umożliwia firmie, aby ograniczyć liczbę operacji, które osoby mogą wykonywać w portalu Azure. Za pomocą RBAC kontrolować dostęp do portalu, Administratorzy IT mogą delegować dostęp przy użyciu następujących metod zarządzania dostępu:

    * Przypisanie oparte na grupach roli: może przypisać dostęp do grup usługi Azure AD, które można synchronizować z lokalnej usługi Active Directory. Dzięki temu można wykorzystać istniejące inwestycje, wprowadzone w narzędzi i procesów do zarządzania grupami organizacji. Umożliwia także funkcję delegowanej grupy zarządzania programu Azure AD Premium.
    * Użyć wbudowanych ról na platformie Azure: można użyć trzech ról — właściciela, współautora i czytnika, aby upewnić się, że użytkownicy i grupy mają uprawnienia do wykonywania zadań, należy wykonać swoje zadania.
    * Szczegółowe dostęp do zasobów: można przypisać role do użytkowników i grup dla określonej subskrypcji, grupy zasobów lub pojedynczych zasobów platformy Azure, takich jak witryny sieci Web lub bazy danych. W ten sposób można upewnij się, że użytkownicy mają dostęp do wszystkich zasobów, które są im potrzebne i Brak dostępu do zasobów, które nie muszą zarządzać.

> [!NOTE]
> Tworzenia aplikacji, aby dostosować kontroli dostępu dla nich istnieje również możliwość ról aplikacji w usłudze Azure AD na potrzeby autoryzacji. Przejrzyj to [przykład aplikacji sieci Web-RoleClaims-DotNet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) na temat tworzenia aplikacji w taki sposób, aby móc używać tej funkcji.
>
>

3. Dostęp warunkowy dla aplikacji usługi Office 365 w usłudze Microsoft Intune: Administratorzy IT mogą aprowizować zasady dostępu warunkowego urządzeń, aby zabezpieczyć zasoby firmowe, w tym samym czasie, dzięki czemu pracownicy przetwarzający informacje na zgodnych urządzeniach na dostęp do usług. Aby uzyskać więcej informacji, zobacz artykuł [Conditional Access Device Policies for Office 365 services](active-directory-conditional-access-device-policies.md) (Zasady dostępu warunkowego urządzeń dla usług Office 365).

4. Dostęp warunkowy dla aplikacji Saas: [ta funkcja](http://blogs.technet.com/b/ad/archive/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work.aspx) umożliwia konfigurowanie reguł dostępu do poszczególnych aplikacji usługi Multi-Factor authentication i możliwość blokowania dostępu dla użytkowników nie znajduje się w zaufanej sieci. Reguły uwierzytelniania wieloskładnikowego można stosować do wszystkich użytkowników, które są przypisane do aplikacji lub tylko dla użytkowników z określonych grup zabezpieczeń. Użytkownicy mogą być wyłączone z wymaganie uwierzytelniania wieloskładnikowego, jeśli uzyskują dostęp do aplikacji z adresu IP, który w wewnątrz organizacji w sieci.

Ponieważ opcje kontroli dostępu używa wielowarstwowych podejście, porównanie te opcje nie są stosowane dla tego zadania. Upewnij się, że są wykorzystuje wszystkie opcje dostępne dla każdego scenariusza, który należy kontrolować dostęp do zasobów.

## <a name="define-incident-response-options"></a>Zdefiniuj opcje odpowiedzi na zdarzenia
Usługi Azure AD może pomóc IT do tożsamości potencjalne zagrożenia bezpieczeństwa w środowisku przez monitorowanie aktywności użytkownika. IT mogą używać usługi Azure AD dostępu i użycia raporty wgląd we integralności i bezpieczeństwa katalogu organizacji. Dzięki tym informacjom administrator IT może lepiej określić, gdzie może znajdować się możliwe zagrożenia bezpieczeństwa, tak aby ich odpowiednio zaplanować ich eliminowania.  [Subskrypcję usługi Azure AD Premium](active-directory-get-started-premium.md) ma zestaw raporty dotyczące zabezpieczeń, które można włączyć IT w celu uzyskania tych informacji. [Raporty usługi Azure AD](active-directory-view-access-usage-reports.md) są podzielone na następujące kategorie:

* **Raporty anomalii**: zdarzenia logowania, które zostały uznane za nietypowych zawiera. Celem jest uświadomić możesz takiego działania i które umożliwia wprowadzania określenie, czy zdarzenie jest podejrzane.
* **Zintegrowane raport aplikacji**: zapewnia wgląd w sposób aplikacji w chmurze są używane w organizacji. Usługa Azure Active Directory oferuje integrację z tysiącami aplikacji w chmurze.
* **Raporty o błędach**: wskazują błędy, które mogą wystąpić podczas inicjowania obsługi administracyjnej kont do aplikacji zewnętrznych.
* **Raporty dotyczące użytkownika**: wyświetlać urządzenia/symbol w dane o aktywności dla określonego użytkownika.
* **Dzienniki aktywności**: zawiera rekord wszystkich zdarzeń inspekcji w ostatnich 24 godzinach, ostatnich 7 dni lub ostatnich 30 dni, a także grupy działania zmiany i działanie resetowania i rejestracji hasła.

> [!TIP]
> Jest innego raportu, który może również pomóc zespołu reagowania na zdarzenia pracy w przypadku [użytkownik mający poświadczenia ujawnione](http://blogs.technet.com/b/ad/archive/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials.aspx) raportu. Ten raport wyświetla dopasowań między listą ujawnione poświadczenia i dzierżawy.
>
>

Inne ważne wbudowanych raportów w usłudze Azure AD, które mogą być używane podczas analizy odpowiedzi na zdarzenia i są:

* **Aktywność resetowania haseł**: udostępnianie administrator wgląd w sposób aktywnie resetowania hasła jest używana w organizacji.
* **Aktywność rejestracji resetowania haseł**: udostępnia szczegółowe informacje, do których użytkownicy zarejestrowali ich metod w celu resetowania haseł i metod, które zostały wybrane.
* **Grupa działań**: zawiera historię zmian w grupie (ex: użytkowników dodawanych lub usuwanych) który zostały zainicjowane w panelu dostępu.

Oprócz podstawowych możliwości raportowania usługi Azure AD Premium, którego można użyć w procesie dochodzenia reagowania na zdarzenia, dział IT może również korzystać z raportu inspekcji, aby uzyskać informacje, takie jak:

* Zmiany w członkostwo roli (na przykład użytkownik dodany do roli administratora globalnego)
* Poświadczenia aktualizacji (na przykład zmiany hasła)
* Domeny zarządzania (na przykład weryfikowanie domeny niestandardowej usuwania domeny)
* Dodawanie lub usuwanie aplikacji
* Zarządzanie użytkownikami (na przykład dodawanie, usuwanie, aktualizowanie użytkownika)
* Dodawanie lub usuwanie licencji

Ponieważ opcje odpowiedzi na zdarzenia używa wielowarstwowych podejście, porównanie tych opcji nie ma zastosowania do tego zadania. Upewnij się, że są wykorzystuje wszystkie opcje dostępne dla każdego scenariusza, która wymaga użycia możliwości raportowania usługi Azure AD jako część procesu odpowiedzi na zdarzenia w firmie.

## <a name="next-steps"></a>Kolejne kroki
[Określić zadań zarządzania tożsamości hybrydowej](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](active-directory-hybrid-identity-design-considerations-overview.md)
