---
title: Projektowania tożsamości hybrydowej — strategii ochrony danych Azure | Dokumentacja firmy Microsoft
description: Można zdefiniować strategię ochrony danych dla Twojego rozwiązania z tożsamością hybrydową pod kątem wymagań biznesowych, które zostały zdefiniowane.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0186d862968259aae73071cfecd7d62443d0256
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109373"
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Definiowanie strategii ochrony danych dla rozwiązania tożsamości hybrydowej
W tym zadaniu należy zdefiniować strategię ochrony danych do hybrydowego tożsamości rozwiązania w celu spełnienia wymagań biznesowych zdefiniowaną w:

* [Określanie wymagań dotyczących ochrony danych](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [Określanie wymagań dotyczących zarządzania zawartością](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Określić wymagania dotyczące kontroli dostępu](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Określanie wymagań dotyczących odpowiedzi na zdarzenia](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Zdefiniuj opcje ochrony danych
Jak wyjaśniono w [określić wymagania dotyczące synchronizacji katalogu](plan-hybrid-identity-design-considerations-directory-sync-requirements.md), Microsoft Azure AD mogą wykonywać synchronizację z sieci lokalnej Active Directory Domain Services (AD DS). Ta integracja umożliwia organizacjom użycia usługi Azure AD w celu zweryfikowania poświadczeń użytkowników, podczas dostępu do zasobów firmy. Można to zrobić w obu scenariuszach: dane podczas rest w środowisku lokalnym i w chmurze. Dostęp do danych w usłudze Azure AD wymaga uwierzytelniania użytkowników za pomocą usługi tokenu zabezpieczającego (STS).

Po uwierzytelnieniu główna nazwa użytkownika (UPN) są odczytywane z tokenu uwierzytelniania. Następnie system autoryzacji określa, replikowany partycji i kontenera odpowiadający domena użytkownika. Informacje na istnienie, włączona i roli użytkownika następnie pomagają system autoryzacja Określa, czy dostęp do dzierżawy docelowej jest autoryzowany w tej sesji użytkownika. Niektóre działania autoryzowanych (w szczególności utworzyć użytkownika i resetowaniem hasła) tworząc dziennik inspekcji, który administrator dzierżawy używa do zarządzania zachowania zgodności lub badania.

Przenosi dane z centrum danych lokalnych do usługi Azure Storage za pośrednictwem połączenia internetowego mogą nie być możliwe ze względu na ilość danych, dostępnej przepustowości lub inne zagadnienia. [Usługi Import/Eksport usługi Azure Storage](../../storage/common/storage-import-export-service.md) zapewnia oparte na sprzęcie opcję dla umieszczenia/pobieranie dużych ilości danych w magazynie obiektów blob. Umożliwi to wysyłanie [zaszyfrowane przez funkcję BitLocker](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) dyski twarde bezpośrednio do centrum danych platformy Azure gdzie operatorom chmury Przekaż zawartość do swojego konta magazynu lub będą oni mogli pobrać Twoje dane platformy Azure do dysków w taki sposób, aby powrócić do Ciebie. Tylko zaszyfrowane dyski są akceptowane dla tego procesu (przy użyciu klucza funkcji BitLocker, generowane przez usługę podczas konfigurowania zadania). Klucz funkcji BitLocker znajduje się na platformie Azure oddzielnie, zapewniając w ten sposób poza klucza udostępniania poza pasmem.

Ponieważ dane przesyłane może odbywać się w różnych scenariuszach, ma również zastosowanie wiedzieć, że Microsoft Azure używa [sieci wirtualne](https://azure.microsoft.com/documentation/services/virtual-network/) do izolacji ruchu dzierżawców od siebie nawzajem zatrudniających środków, takich jak poziom hosta i gościa zapory, filtrowanie pakietów IP, blokowanie portów i punktów końcowych HTTPS. Jednak większość komunikacji wewnętrznej platformy Azure, w tym infrastruktury do infrastruktury i infrastruktury do klienta (lokalnie), również są szyfrowane. Inny scenariusz ważne jest komunikacja w centrach danych platformy Azure; Firma Microsoft zarządza sieci, aby mieć pewność, że nie maszyny Wirtualnej można personifikacji lub podsłuchiwać adresu IP innego. Protokoły TLS/SSL jest używany podczas uzyskiwania dostępu do usługi Azure Storage lub baz danych SQL lub podczas nawiązywania połączenia z usługami w chmurze. W takim przypadku administrator klient jest odpowiedzialny za uzyskanie certyfikatu TLS/SSL i wdrażania infrastruktury dzierżawy. Przenoszenie ruch danych między maszynami wirtualnymi w tym samym wdrożeniu lub dzierżaw w ramach jednego wdrożenia za pomocą programu Microsoft Azure Virtual Network mogą być chronione za pomocą protokołów komunikacji szyfrowanej, takich jak HTTPS, SSL/TLS lub inne osoby.

W zależności od sposobu odpowiedzi na pytania w [określić wymagania dotyczące ochrony danych](plan-hybrid-identity-design-considerations-dataprotection-requirements.md), powinno być możliwe do określenia sposobu ochrony danych i sposobu rozwiązania tożsamości hybrydowej mogą pomóc w ten proces. W poniższej tabeli przedstawiono opcje obsługiwane przez platformę Azure, które są dostępne dla każdego scenariusza ochrony danych.

| Opcje ochrony danych | Magazynowane w chmurze | W pozostałych w środowisku lokalnym | W drodze |
| --- | --- | --- | --- |
| Szyfrowanie dysków funkcją BitLocker |X |X | |
| SQL Server w celu zaszyfrowania baz danych |X |X | |
| Szyfrowanie maszyny Wirtualnej do maszyny Wirtualnej | | |X |
| SSL/TLS | | |X |
| Sieć VPN | | |X |

> [!NOTE]
> Odczyt [zgodność według funkcji](https://azure.microsoft.com/support/trust-center/services/) na [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) Aby dowiedzieć się więcej o certyfikatach, które poszczególnych usługach platformy Azure jest zgodna z.
> Ponieważ opcji ochrony danych używa wielowarstwowego podejścia, porównania między te opcje nie są stosowane dla tego zadania. Upewnij się, że wszystkie opcje dostępne dla każdego stanu danych korzysta.
>
>

## <a name="define-content-management-options"></a>Definiowanie opcji zarządzania zawartością

Jedną z zalet Zarządzanie hybrydowej infrastruktury tożsamości za pomocą usługi Azure AD jest, że proces jest całkowicie przezroczyste z perspektywy użytkownika końcowego. Użytkownik próbuje uzyskać dostęp do udostępnionego zasobu, zasób wymaga uwierzytelnienia, użytkownik musi wysłać żądanie uwierzytelnienia do usługi Azure AD w celu uzyskania tokenu i uzyskać dostęp do zasobu. Ta cały proces przebiega w tle, bez interakcji z użytkownikiem. 

Organizacje, które są zazwyczaj obawy dotyczące prywatności danych wymagają klasyfikacji danych dla ich rozwiązania. Jeśli bieżącą infrastrukturę lokalną już używa klasyfikacji danych, jest możliwe do użycia usługi Azure AD jako głównego repozytorium tożsamości użytkowników. Typowe narzędzia, że jest używane w środowisku lokalnym, klasyfikacja danych jest nazywana [Data Classification Toolkit](https://msdn.microsoft.com/library/Hh204743.aspx) dla systemu Windows Server 2012 R2. To narzędzie może pomóc zidentyfikować, klasyfikować i chronić dane na serwerach plików w chmurze prywatnej. Istnieje również możliwość użycia [automatycznej klasyfikacji plików](https://technet.microsoft.com/library/hh831672.aspx) w systemie Windows Server 2012, aby wykonać to zadanie.

Jeśli Twoja organizacja nie ma klasyfikacji danych w miejscu, ale trzeba chronić poufne pliki bez dodawania nowych serwerów w środowisku lokalnym, mogą używać programu Microsoft [usługi Azure Rights Management](https://technet.microsoft.com/library/JJ585026.aspx).  Usługa Azure RMS używa szyfrowania, tożsamości i zasad autoryzacji, aby ułatwić zabezpieczanie plików oraz wiadomości e-mail i działa na wielu urządzeniach — telefonach, tabletach i komputerach. Ponieważ Azure RMS to usługa w chmurze, nie ma konieczności jawnego konfigurowania relacji zaufania z innymi organizacjami przed udostępnieniem chronionej zawartości z nimi. Jeśli ich jeszcze z usługi Office 365 lub katalogu usługi Azure AD, współpraca między organizacjami jest obsługiwana automatycznie. Możesz także zsynchronizować wyłącznie atrybutów katalogu wymaganych przez usługę Azure RMS do obsługi tożsamości wspólnych dla lokalnych kont usługi Active Directory za pomocą usług Azure do synchronizacji Active Directory (AAD Sync) lub Azure AD Connect.

Jest to ważna część zarządzania zawartością, aby dowiedzieć się, kto ma dostęp do zasobu, który, w związku z tym funkcja Zaawansowane rejestrowanie jest ważne w przypadku rozwiązanie do zarządzania tożsamościami. Usługa Azure AD zapewnia dziennika ponad 30 dni, w tym:

* Zmiany członkostwa w roli (np: użytkownik dodany do roli Administrator globalny)
* Poświadczenia aktualizacji (np: zmiany hasła)
* Zarządzanie domenami (np: weryfikowanie domeny niestandardowej, usunięcie domeny)
* Dodawanie lub usuwanie aplikacji
* Zarządzanie użytkownikami (np: Dodawanie, usuwanie, aktualizowanie użytkownika)
* Dodawanie lub usuwanie licencji

> [!NOTE]
> Odczyt [zabezpieczeń platformy Microsoft Azure i zarządzanie dziennikiem inspekcji](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) Aby dowiedzieć się więcej na temat możliwości rejestrowania na platformie Azure.
> W zależności od sposobu odpowiedzi na pytania w [określić wymagania związane z zarządzaniem zawartością](plan-hybrid-identity-design-considerations-contentmgt-requirements.md), powinno być możliwe ustalenie, jak zawartości, które mają być zarządzane w danym rozwiązaniu tożsamości hybrydowej. Mimo że wszystkie opcje dostępne w tabeli 6 możliwość integracji z usługą Azure AD, należy zdefiniować, co jest bardziej odpowiednia dla potrzeb biznesowych.
>
>

| Opcje zarządzania zawartością | Zalety | Wady |
| --- | --- | --- |
| Scentralizowanie lokalne (Active Directory Rights Management Server) |Pełną kontrolę nad infrastruktury serwera, które odpowiada klasyfikacji danych <br> Wbudowana funkcja w systemie Windows Server, nie ma potrzeby dodatkowych licencji lub subskrypcji <br> Można zintegrować z usługą Azure AD w scenariuszu hybrydowym <br> Obsługuje informacji na temat usługi rights management (IRM) funkcji w usługach Online firmy Microsoft, takich jak Exchange Online i SharePoint Online, a także usługi Office 365 <br> Obsługuje lokalne produkty serwerowe firmy Microsoft, takich jak Exchange Server, SharePoint Server i serwery plików, z systemem Windows Server i infrastrukturą klasyfikacji plików (FCI). |Wyższe konserwacji (trzymać się z aktualizacji, konfiguracji i potencjalne uaktualnień), ponieważ IT jest właścicielem serwera <br> Wymaga infrastruktury serwera w środowisku lokalnym<br> Nie natywnie korzystać z możliwości platformy Azure |
| Scentralizowanie w chmurze (Azure RMS) |Łatwiejsze zarządzanie w porównaniu z rozwiązaniem w firmie <br> Można zintegrować z usługami AD DS w scenariuszu hybrydowym <br>  W pełni zintegrowana z usługą Azure AD <br> Nie wymaga serwera lokalnego w celu wdrożenia usługi <br> Obsługuje produkty serwerowe firmy Microsoft, takich jak Exchange Server, SharePoint, serwer i serwery plików, z systemem Windows Server i pliku klasyfikacji infrastruktury (FCI) lokalnych <br> IT, może mieć pełną kontrolę nad kluczem swojej dzierżawy za pomocą funkcji BYOK. |Organizacja musi mieć subskrypcję chmury, która obsługuje usługę RMS <br> Organizacja musi mieć katalog usługi Azure AD, aby obsługiwać uwierzytelnianie użytkowników dla usług RMS |
| Hybrydowe (Azure RMS, zintegrowana z usługą, On-Premises Active Directory Rights Management Server) |W tym scenariuszu gromadzi zalety, scentralizowane lokalnie i w chmurze. |Organizacja musi mieć subskrypcję chmury, która obsługuje usługę RMS <br> Organizacja musi mieć katalog usługi Azure AD, aby obsługiwać uwierzytelnianie użytkowników dla usług RMS, <br> Wymagane jest połączenie między usługą w chmurze platformy Azure i w obrębie lokalnej infrastruktury |

## <a name="define-access-control-options"></a>Zdefiniuj opcje kontroli dostępu
Dzięki wykorzystaniu uwierzytelniania, autoryzacji i kontroli dostępu, których dostępne w usłudze Azure AD umożliwia firmie używać repozytorium tożsamości centralnej, pozwalając użytkownikom i partnerami, aby użyć jednego logowania jednokrotnego (SSO) jak pokazano na poniższej ilustracji:

![scentralizowane zarządzanie](./media/plan-hybrid-identity-design-considerations/centralized-management.png)

Scentralizowane zarządzanie i w pełni integrację z innych katalogów

Usługa Azure Active Directory umożliwia logowanie jednokrotne do tysięcy aplikacji SaaS i aplikacje sieci web w środowisku lokalnym. Zobacz [listę zgodności federacyjnych usługi Azure Active Directory: dostawcy tożsamości innych firm, które mogą służyć do implementowania logowania jednokrotnego](how-to-connect-fed-compatibility.md) artykuł, aby uzyskać więcej szczegółów na temat logowania jednokrotnego innej firmy, które zostały przetestowane przez firmę Microsoft. Ta funkcja umożliwia organizacji do implementacji różnych scenariuszy B2B przy jednoczesnym zachowaniu kontroli zarządzania tożsamościami i dostępem. Jednak w trakcie B2B Projektowanie procesu, jest ważne poznać metodę uwierzytelniania, która jest używana przez partnera i sprawdź, czy ta metoda jest obsługiwana przez platformę Azure. Obecnie następujące metody są obsługiwane przez usługę Azure AD:

* Security Assertion Markup Language (SAML)
* OAuth
* Protokołu Kerberos
* Tokeny
* Certyfikaty

> [!NOTE]
> Przeczytaj [protokoły uwierzytelniania usługi Azure Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx) Aby dowiedzieć się więcej na temat każdego protokołu i jego możliwości na platformie Azure.
>
>

Dzięki obsłudze usługi Azure AD, aplikacje biznesowe mobilnych, można użyć tego samego łatwa obsługa uwierzytelniania usług Mobile Services sposób umożliwić pracownikom zalogować się do swoich aplikacji dla urządzeń przenośnych za pomocą firmowych poświadczeń usługi Active Directory. Dzięki tej funkcji usługi Azure AD jest obsługiwany jako dostawcy tożsamości w usługach Mobile Services wraz z innych dostawców tożsamości już obsługiwane (w tym Accounts firmy Microsoft, identyfikator Facebook, Google identyfikator i identyfikator usługi Twitter). Aplikacje lokalne, użycie poświadczeń użytkownika znajdujący się w usługach AD DS w firmie, dostęp z partnerów i użytkowników w chmurze powinny być przezroczyste. Zarządzanie kontrolą dostępu warunkowego użytkownika do aplikacji sieci web (opartych na chmurze), internetowy interfejs API, usług w chmurze firmy Microsoft, aplikacji SaaS innych firm i aplikacje klienta natywnego (urządzenia przenośne) i mają korzyści z zabezpieczeń, inspekcji, raportowania wszystko w jednym Umieść. Jednak zaleca się sprawdzanie poprawności wdrożenia w środowisku nieprodukcyjnym lub z ograniczoną liczbę użytkowników.

> [!TIP]
> warto wspomnieć, że usługa Azure AD nie ma zasad grupy ma usług AD DS. Aby wymusić zasady dla urządzeń, należy to rozwiązanie do zarządzania urządzeniami przenośnymi, takie jak [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx).
>
>

Po użytkownik jest uwierzytelniany przy użyciu usługi Azure AD, jest ważne dla oceny poziomu dostępu, których użytkownik ma. Poziom dostępu, których użytkownik ma wobec zasobu mogą się różnić. Gdy usługa Azure AD można dodać jako dodatkowa warstwa zabezpieczeń poprzez kontrolowanie dostępu do niektórych zasobów, należy pamiętać, że samego zasobu może również mieć swój własny listy kontroli dostępu oddzielnie, takich jak kontrola dostępu do plików znajdujących się na serwerze plików. Poniższa ilustracja zawiera podsumowanie poziomów kontroli dostępu, który może mieć w scenariuszu hybrydowym:

![Kontrola dostępu](./media/plan-hybrid-identity-design-considerations/accesscontrol.png)

Każdy interakcja w diagramie pokazano na rysunku x reprezentuje jeden scenariusz kontroli dostępu, który może być objętych przez usługę Azure AD. Poniżej są opis poszczególnych scenariuszy:

1. Dostęp warunkowy do aplikacji, które są hostowane lokalnie: Zarejestrowane urządzenia można używać z zasadami dostępu dla aplikacji, które są skonfigurowane do korzystania z usług AD FS w systemie Windows Server 2012 R2.

2. Kontrola dostępu do witryny Azure portal:  Platforma Azure umożliwia również kontrolować dostęp do portalu przy użyciu kontroli dostępu opartej na rolach (RBAC)). Ta metoda umożliwia firmie ograniczyć liczbę operacji, które osoba można wykonać w witrynie Azure portal. Za pomocą funkcji RBAC do kontrolowania dostępu do portalu, Administratorzy IT mogą delegować dostęp przy użyciu następujących metod zarządzania dostępu:

   - Przypisanie roli oparte na grupach: Dostęp można przypisać do grup usługi Azure AD, które mogą być synchronizowane z lokalnej usługi Active Directory. Dzięki temu można wykorzystać istniejące inwestycje, których organizacja wprowadziła narzędzia i procesy do zarządzania grupami. Można również użyć funkcji zarządzania delegowanej grupy usługi Azure AD Premium.
   - Użyj wbudowanych ról na platformie Azure: Można użyć trzech ról — właściciela, współautora i czytelnika, upewnij się, że użytkownicy i grupy mają uprawnienia do wykonania zadania, których potrzebują do wykonania swoich zadań.
   -  Szczegółowe dostęp do zasobów: Role można przypisać do użytkowników i grup dla określonej subskrypcji, grupy zasobów lub poszczególnych zasobów platformy Azure, takich jak witryny sieci Web lub bazy danych. W ten sposób możesz upewnij się, że użytkownicy mają dostęp do wszystkich zasobów, które są im potrzebne i Brak dostępu do zasobów, które ich nie trzeba zarządzać.

   > [!NOTE]
   > Jeśli tworzysz aplikacje, aby dostosować kontroli dostępu dla nich istnieje również możliwość role aplikacji w usłudze Azure AD na potrzeby autoryzacji. Przejrzyj ten [przykład aplikacji sieci Web-RoleClaims-DotNet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) na temat tworzenia aplikacji, aby używać tej funkcji.


3. Dostęp warunkowy dla aplikacji usługi Office 365 z usługą Microsoft Intune:  Administratorzy IT mogą aprowizować zasady dostępu warunkowego urządzeń, aby zabezpieczyć zasoby firmowe, w tym samym czasie, dzięki czemu pracownicy przetwarzający informacje na zgodnych urządzeniach dostęp do usług. 
  
4. Dostęp warunkowy dla aplikacji Saas: [Ta funkcja](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work/) umożliwia skonfigurowanie reguł dostępu dla aplikacji usługi Multi-Factor authentication i możliwość blokowania dostępu dla użytkowników, nie w zaufanej sieci. Reguły uwierzytelniania wieloskładnikowego można zastosować do wszystkich użytkowników, które są przypisane do aplikacji lub tylko dla użytkowników z określonych grup zabezpieczeń. Użytkownicy mogą być wyłączone z wymagania dotyczące uwierzytelniania wieloskładnikowego, jeśli uzyskują dostęp do aplikacji z adresu IP, który w wewnątrz organizacji sieci.

Ponieważ opcje kontroli dostępu korzysta wielowarstwowego podejścia, porównania między te opcje nie są stosowane dla tego zadania. Upewnij się, że są wykorzystaniu wszystkie opcje dostępne dla każdego scenariusza, który wymaga do kontrolowania dostępu do zasobów.

## <a name="define-incident-response-options"></a>Zdefiniuj opcje odpowiedzi na zdarzenia
Usługa Azure AD może pomóc zespołowi IT tożsamości potencjalne zagrożenia bezpieczeństwa w środowisku przez monitorowanie aktywności użytkownika. IT może używać dostępu usługi Azure AD i raporty użycia, aby uzyskać wgląd w integralność i bezpieczeństwo katalogu organizacji. Dzięki tym informacjom administrator IT może lepiej zidentyfikować potencjalne zagrożenia bezpieczeństwa może być tak, aby ich odpowiednio zaplanować ograniczyć te zagrożenia.  [Subskrypcję usługi Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) ma zestaw raportów dotyczących zabezpieczeń, które można włączyć IT, aby uzyskać te informacje. [Raporty usługi Azure AD](../reports-monitoring/overview-reports.md) są podzielone na następujące kategorie:

* **Raporty anomalii**: Zawiera zdarzenia logowania, które zostały odnalezione jako nietypowe. Celem jest każą pamiętać o tych działań i pozwalają umożliwiają określenie, czy zdarzenie jest podejrzane.
* **Raport aplikacji zintegrowanych**: Zapewnia wgląd w sposób są używane w aplikacjach w chmurze w Twojej organizacji. Usługa Azure Active Directory oferuje integrację z tysięcy aplikacji w chmurze.
* **Raporty o błędach**: Wskazują błędy, które mogą wystąpić podczas aprowizowania kont do aplikacji zewnętrznych.
* **Raporty dotyczące poszczególnych użytkowników**: Wyświetl urządzenia/logowanie w dane aktywności dla określonego użytkownika.
* **Dzienniki aktywności**: Zawiera rekord wszystkich zdarzeń inspekcji w ciągu ostatnich 24 godzinach, ostatnich 7 dni, lub ostatnich 30 dni, a także działania zmiany w grupie i działanie resetowania i rejestracji hasła.

> [!TIP]
> Inny raport, który może również pomóc zespół reagowania na zdarzenia, pracujący nad przypadkiem jest [użytkownik z ujawnionymi poświadczeniami](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials/) raportu. Ten raport prezentuje dopasowań między listą ujawnione poświadczenia i dzierżawy.
>


Inne ważne wbudowanych raportów w usłudze Azure AD, które mogą być używane podczas analizy reakcji na zdarzenia i są:

* **Aktywność resetowania haseł**: Podaj administrator wgląd w sposób aktywnie resetowania hasła jest używana w organizacji.
* **Aktywność rejestracji resetowania haseł**: udostępnia szczegółowe informacje, w których użytkowników, którzy zarejestrowali swoje metody podczas resetowania hasła i metody, które zostały wybrane.
* **Grupa działań**: zapewnia historię zmian do grupy (np: użytkowników dodawanych lub usuwanych), zostały zainicjowane w panelu dostępu.

Oprócz podstawowych funkcji raportowania usługi Azure AD Premium, którego można użyć w procesie reagowania na zdarzenia badania dział IT może również korzystać z raportów inspekcji, aby uzyskać informacje, takie jak:

* Zmiany członkostwa w roli (na przykład użytkownik dodany do roli Administrator globalny)
* Poświadczenia aktualizacji (na przykład zmiany hasła)
* Zarządzanie domenami (na przykład weryfikowanie domeny niestandardowej, usunięcie domeny)
* Dodawanie lub usuwanie aplikacji
* Zarządzanie użytkownikami (na przykład dodawania, usuwania, aktualizowania użytkownika)
* Dodawanie lub usuwanie licencji

Ponieważ opcje w celu reagowania na użyć wielowarstwowego podejścia, porównanie tych opcji nie ma zastosowania dla tego zadania. Upewnij się, że są wykorzystaniu wszystkie opcje dostępne dla każdego scenariusza, który wymaga użycia funkcji raportowania usługi Azure AD w ramach procesu reagowania na zdarzenia w firmie.

## <a name="next-steps"></a>Kolejne kroki
[Określić zadania związane z zarządzaniem tożsamości hybrydowej](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](plan-hybrid-identity-design-considerations-overview.md)
