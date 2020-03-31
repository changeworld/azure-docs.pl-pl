---
title: Hybrydowy projekt tożsamości — strategia ochrony danych Azure | Dokumenty firmy Microsoft
description: Zdefiniujesz strategię ochrony danych dla rozwiązania tożsamości hybrydowej, aby spełnić zdefiniowane wymagania biznesowe.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109373"
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Definiowanie strategii ochrony danych dla rozwiązania tożsamości hybrydowej
W tym zadaniu zdefiniujesz strategię ochrony danych dla rozwiązania tożsamości hybrydowej, aby spełnić wymagania biznesowe zdefiniowane w:

* [Określanie wymogów ochrony danych](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [Określanie wymagań dotyczących zarządzania zawartością](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Określanie wymagań dotyczących kontroli dostępu](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Określanie wymagań dotyczących odpowiadania na zdarzenia](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Definiowanie opcji ochrony danych
Jak wyjaśniono w [określić wymagania dotyczące synchronizacji katalogów,](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)usługa Microsoft Azure AD może synchronizować się z lokalnymi usługami domenowymi Active Directory (AD DS). Ta integracja umożliwia organizacjom korzystanie z usługi Azure AD do weryfikacji poświadczeń użytkowników podczas próby uzyskania dostępu do zasobów firmowych. Można to zrobić w obu scenariuszach: dane w spoczynku lokalnie i w chmurze. Dostęp do danych w usłudze Azure AD wymaga uwierzytelniania użytkownika za pośrednictwem usługi tokenu zabezpieczającego (STS).

Po uwierzytelnieniu główna nazwa użytkownika (UPN) jest odczytywana z tokenu uwierzytelniania. Następnie system autoryzacji określa replikowaną partycję i kontener odpowiadający domenie użytkownika. Informacje o istnieniu użytkownika, stan włączone i roli następnie pomaga system autoryzacji określić, czy dostęp do dzierżawy docelowej jest autoryzowany dla użytkownika w tej sesji. Niektóre autoryzowane akcje (w szczególności tworzenie resetowania użytkowników i hasła) tworzą dziennik inspekcji, który administrator dzierżawy następnie używa do zarządzania wysiłkami lub dochodzeniami dotyczącymi zgodności.

Przenoszenie danych z lokalnego centrum danych do usługi Azure Storage za pośrednictwem połączenia internetowego może nie zawsze być wykonalne ze względu na ilość danych, dostępność przepustowości lub inne zagadnienia. [Usługa importowania/eksportowania usługi Azure Storage](../../storage/common/storage-import-export-service.md) udostępnia opcję sprzętową do umieszczania/pobierania dużych ilości danych w magazynie obiektów blob. Umożliwia wysyłanie zaszyfrowanych dysków twardych [zlockiem BitLocker](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) bezpośrednio do centrum danych platformy Azure, w którym operatorzy chmury przekazują zawartość do konta magazynu lub mogą pobrać dane platformy Azure na dyski, aby powrócić do Ciebie. Tylko zaszyfrowane dyski są akceptowane dla tego procesu (przy użyciu klucza funkcji BitLocker generowanego przez samą usługę podczas konfigurowania zadania). Klucz funkcji BitLocker jest dostarczany do platformy Azure oddzielnie, zapewniając w ten sposób udostępnianie kluczy poza pasmem.

Ponieważ przesyłane dane mogą odbywać się w różnych scenariuszach, jest również istotne, aby wiedzieć, że platforma Microsoft Azure używa [sieci wirtualnej](https://azure.microsoft.com/documentation/services/virtual-network/) do izolowania ruchu dzierżawców od siebie, stosując środki, takie jak zapory na poziomie hosta i gościa, filtrowanie pakietów IP, blokowanie portów i punkty końcowe HTTPS. Jednak większość komunikacji wewnętrznej platformy Azure, w tym infrastruktury do infrastruktury i infrastruktury do klienta (lokalnie), są również szyfrowane. Innym ważnym scenariuszem jest komunikacja w centrach danych platformy Azure; Firma Microsoft zarządza sieciami, aby upewnić się, że żadna maszyna wirtualna nie może personifikować ani podsłuchiwać adresu IP innego adresu. TLS/SSL jest używany podczas uzyskiwania dostępu do usługi Azure Storage lub baz danych SQL lub podczas łączenia się z usługami w chmurze. W takim przypadku administrator klienta jest odpowiedzialny za uzyskanie certyfikatu TLS/SSL i wdrożenie go w infrastrukturze dzierżawy. Ruch danych przemieszczających się między maszynami wirtualnymi w tym samym wdrożeniu lub między dzierżawcami w jednym wdrożeniu za pośrednictwem sieci wirtualnej platformy Microsoft Azure może być chroniony za pomocą zaszyfrowanych protokołów komunikacyjnych, takich jak HTTPS, SSL/TLS lub innych.

W zależności od tego, jak odpowiedziałeś na pytania w [określić wymagania dotyczące ochrony danych,](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)powinieneś być w stanie określić, w jaki sposób chcesz chronić swoje dane i jak rozwiązanie tożsamości hybrydowej może pomóc w tym procesie. W poniższej tabeli przedstawiono opcje obsługiwane przez platformę Azure, które są dostępne dla każdego scenariusza ochrony danych.

| Opcje ochrony danych | W spoczynku w chmurze | W spoczynku na miejscu | Przesyłanie |
| --- | --- | --- | --- |
| Szyfrowanie dysków funkcją BitLocker |X |X | |
| SQL Server do szyfrowania baz danych |X |X | |
| Szyfrowanie maszyn wirtualnych do maszyny wirtualnej | | |X |
| SSL/TLS | | |X |
| Sieć VPN | | |X |

> [!NOTE]
> Przeczytaj [zgodność z funkcją](https://azure.microsoft.com/support/trust-center/services/) w [Centrum zaufania platformy Microsoft Azure,](https://azure.microsoft.com/support/trust-center/) aby dowiedzieć się więcej o certyfikatach, z którymi każda usługa platformy Azure jest zgodna.
> Ponieważ opcje ochrony danych używają podejścia wielowarstwowego, porównanie tych opcji nie ma zastosowania do tego zadania. Upewnij się, że wykorzystujesz wszystkie opcje dostępne dla każdego stanu danych.
>
>

## <a name="define-content-management-options"></a>Definiowanie opcji zarządzania zawartością

Jedną z zalet korzystania z usługi Azure AD do zarządzania infrastrukturą tożsamości hybrydowej jest to, że proces jest w pełni przejrzysty z punktu widzenia użytkownika końcowego. Użytkownik próbuje uzyskać dostęp do zasobu udostępnionego, zasób wymaga uwierzytelniania, użytkownik musi wysłać żądanie uwierzytelnienia do usługi Azure AD w celu uzyskania tokenu i uzyskania dostępu do zasobu. Cały ten proces odbywa się w tle, bez interakcji z użytkownikiem. 

Organizacje, które obawiają się o prywatność danych, zwykle wymagają klasyfikacji danych dla swojego rozwiązania. Jeśli ich bieżąca infrastruktura lokalna jest już przy użyciu klasyfikacji danych, można użyć usługi Azure AD jako głównego repozytorium tożsamości użytkownika. Typowe narzędzie, które jest używane lokalnie do klasyfikacji danych jest nazywany [Data Classification Toolkit](https://msdn.microsoft.com/library/Hh204743.aspx) dla systemu Windows Server 2012 R2. To narzędzie może pomóc w identyfikowaniu, klasyfikowaniu i ochronie danych na serwerach plików w chmurze prywatnej. Istnieje również możliwość użycia [automatycznej klasyfikacji plików](https://technet.microsoft.com/library/hh831672.aspx) w systemie Windows Server 2012, aby wykonać to zadanie.

Jeśli twoja organizacja nie ma klasyfikacji danych, ale musi chronić poufne pliki bez dodawania nowych serwerów lokalnie, może korzystać z [usługi Microsoft Azure Rights Management Service.](https://technet.microsoft.com/library/JJ585026.aspx)  Usługa Azure RMS używa zasad szyfrowania, tożsamości i autoryzacji w celu zabezpieczenia plików i poczty e-mail oraz działa na wielu urządzeniach — telefonach, tabletach i komputerach. Ponieważ usługa Azure RMS jest usługą w chmurze, nie ma potrzeby jawnego konfigurowania relacji zaufania z innymi organizacjami, zanim będzie można udostępnić im chroniona zawartość. Jeżeli korzystają już one z usługi Office 365 lub katalogu usługi Azure AD, współpraca między organizacjami jest obsługiwana automatycznie. Można również zsynchronizować tylko atrybuty katalogu, które usługa Azure RMS musi obsługiwać wspólną tożsamość dla lokalnych kont usługi Active Directory przy użyciu usług synchronizacji usługi Azure Active Directory (AAD Sync) lub usługi Azure AD Connect.

Istotną częścią zarządzania zawartością jest zrozumienie, kto uzyskuje dostęp do którego zasobu, dlatego rozbudowa możliwości rejestrowania jest ważna dla rozwiązania do zarządzania tożsamościami. Usługa Azure AD udostępnia dziennik przez 30 dni, w tym:

* Zmiany w członkostwie roli (np. użytkownik dodany do roli Administratora globalnego)
* Aktualizacje poświadczeń (np. zmiany hasła)
* Zarządzanie domeną (np. weryfikowanie domeny niestandardowej, usuwanie domeny)
* Dodawanie lub usuwanie aplikacji
* Zarządzanie użytkownikami (np. dodawanie, usuwanie, aktualizowanie użytkownika)
* Dodawanie lub usuwanie licencji

> [!NOTE]
> Przeczytaj artykuł [Microsoft Azure Security and Audit Log Management,](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) aby dowiedzieć się więcej o możliwościach rejestrowania na platformie Azure.
> W zależności od tego, jak odpowiedziałeś na pytania w [określaniu wymagań dotyczących zarządzania zawartością,](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)powinieneś być w stanie określić sposób zarządzania zawartością w rozwiązaniu tożsamości hybrydowej. Podczas gdy wszystkie opcje udostępniane w tabeli 6 są w stanie zintegrować z usługą Azure AD, ważne jest, aby zdefiniować, który jest bardziej odpowiedni dla potrzeb biznesowych.
>
>

| Opcje zarządzania treścią | Zalety | Wady |
| --- | --- | --- |
| Scentralizowana lokalna (serwer zarządzania prawami dostępu do usługi Active Directory) |Pełna kontrola nad infrastrukturą serwerów odpowiedzialną za klasyfikację danych <br> Wbudowana funkcja w systemie Windows Server, brak potrzeby dodatkowej licencji lub subskrypcji <br> Można zintegrować z usługą Azure AD w scenariuszu hybrydowym <br> Obsługuje funkcje zarządzania prawami do informacji (IRM) w usługach Microsoft Online, takich jak Exchange Online i SharePoint Online, a także w usłudze Office 365 <br> Obsługuje lokalne produkty serwerowe firmy Microsoft, takie jak Exchange Server, SharePoint Server i serwery plików z systemem Windows Server i infrastrukturą klasyfikacji plików (FCI). |Wyższa konserwacja (bądź na bieżąco z aktualizacjami, konfiguracją i potencjalnymi aktualizacjami), ponieważ IT jest właścicielem serwera <br> Wymagaj infrastruktury serwera lokalnie<br> Nie korzysta z funkcji platformy Azure natywnie |
| Scentralizowana w chmurze (Azure RMS) |Łatwiejsze zarządzanie w porównaniu z rozwiązaniem lokalnym <br> Możliwość integracji z usługami AD DS w scenariuszu hybrydowym <br>  Pełna integracja z usługą Azure AD <br> Nie wymaga serwera lokalnego w celu wdrożenia usługi <br> Obsługuje lokalne produkty serwerowe firmy Microsoft, takie jak Exchange Server, SharePoint, Server i serwery plików z systemem Windows Server i klasyfikacją plików, infrastrukturą (FCI) <br> IT, może mieć pełną kontrolę nad kluczem dzierżawy z możliwością BYOK. |Twoja organizacja musi mieć subskrypcję w chmurze obsługującą usługę RMS <br> Twoja organizacja musi mieć katalog usługi Azure AD do obsługi uwierzytelniania użytkowników dla usługi RMS |
| Hybrydowy (usługa Azure RMS zintegrowana z lokalnym serwerem zarządzania prawami dostępu do usługi Active Directory) |W tym scenariuszu gromadzi zalety zarówno, scentralizowane lokalnie i w chmurze. |Twoja organizacja musi mieć subskrypcję w chmurze obsługującą usługę RMS <br> Twoja organizacja musi mieć katalog usługi Azure AD do obsługi uwierzytelniania użytkowników dla programu RMS, <br> Wymaga połączenia między usługą chmury platformy Azure a infrastrukturą lokalną |

## <a name="define-access-control-options"></a>Definiowanie opcji kontroli dostępu
Korzystając z funkcji uwierzytelniania, autoryzacji i kontroli dostępu dostępnych w usłudze Azure AD, można włączyć firmę do korzystania z centralnego repozytorium tożsamości, umożliwiając jednocześnie użytkownikom i partnerom korzystanie z logowania jednokrotnego (SSO), jak pokazano na poniższym rysunku:

![scentralizowane zarządzanie](./media/plan-hybrid-identity-design-considerations/centralized-management.png)

Scentralizowane zarządzanie i pełna integracja z innymi katalogami

Usługa Azure Active Directory zapewnia logowanie jednokrotne do tysięcy aplikacji SaaS i lokalnych aplikacji sieci Web. Zobacz [listę zgodności federacji usługi Azure Active Directory: dostawcy tożsamości innych firm, których można użyć do zaimplementowania artykułu logowania jednokrotnego, aby](how-to-connect-fed-compatibility.md) uzyskać więcej informacji na temat innych firm jednokrotnych, które zostały przetestowane przez firmę Microsoft. Ta funkcja umożliwia organizacji do zaimplementowania różnych scenariuszy B2B przy jednoczesnym zachowaniu kontroli zarządzania tożsamością i dostępem. Jednak podczas procesu projektowania B2B, ważne jest, aby zrozumieć metodę uwierzytelniania, która jest używana przez partnera i sprawdź poprawność, jeśli ta metoda jest obsługiwana przez platformę Azure. Obecnie następujące metody są obsługiwane przez usługę Azure AD:

* Język znaczników oświadczeń zabezpieczeń (SAML)
* OAuth
* Kerberos
* Tokeny
* Certyfikaty

> [!NOTE]
> przeczytaj [protokoły uwierzytelniania usługi Azure Active Directory,](https://msdn.microsoft.com/library/azure/dn151124.aspx) aby dowiedzieć się więcej szczegółów na temat każdego protokołu i jego możliwości na platformie Azure.
>
>

Korzystając z pomocy technicznej usługi Azure AD, aplikacje biznesowe korzystające z tych samych usług przenośnych mogą korzystać z tego samego środowiska łatwego uwierzytelniania usług przenośnych, aby umożliwić pracownikom logowanie się do aplikacji mobilnych przy użyciu poświadczeń firmowej usługi Active Directory. Dzięki tej funkcji usługa Azure AD jest obsługiwana jako dostawca tożsamości w usługach mobilnych wraz z innymi już obsługiwanymi dostawcami tożsamości (które obejmują konta Microsoft, identyfikator Facebooka, identyfikator Google i identyfikator Twitter). Jeśli aplikacje lokalne używają poświadczeń użytkownika znajdujących się w usługach AD DS firmy, dostęp od partnerów i użytkowników pochodzących z chmury powinien być przezroczysty. Kontrola dostępu warunkowego użytkownika do (opartych na chmurze) aplikacji internetowych, interfejsu API sieci Web, usług w chmurze firmy Microsoft, aplikacji SaaS innych firm i natywnych (mobilnych) aplikacji klienckich oraz ma zalety bezpieczeństwa, inspekcji, raportowania w jednym Miejsce. Zaleca się jednak, aby sprawdzić poprawność implementacji w środowisku nieprodukcyjnym lub z ograniczoną liczbą użytkowników.

> [!TIP]
> Należy wspomnieć, że usługa Azure AD nie ma zasad grupy, jak ad ds. Aby wymusić zasady dotyczące urządzeń, potrzebne jest rozwiązanie do zarządzania urządzeniami przenośnymi, takie jak [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx).
>
>

Gdy użytkownik jest uwierzytelniony przy użyciu usługi Azure AD, ważne jest, aby ocenić poziom dostępu, który ma użytkownik. Poziom dostępu, który użytkownik ma za zasób może się różnić. Usługa Azure AD może dodać dodatkową warstwę zabezpieczeń, kontrolując dostęp do niektórych zasobów, należy pamiętać, że sam zasób może mieć również własną listę kontroli dostępu oddzielnie, na przykład kontrolę dostępu dla plików znajdujących się na serwerze plików. Na poniższym rysunku podsumowano poziomy kontroli dostępu, które można mieć w scenariuszu hybrydowym:

![kontrola dostępu](./media/plan-hybrid-identity-design-considerations/accesscontrol.png)

Każda interakcja na diagramie pokazano na rysunku X reprezentuje jeden scenariusz kontroli dostępu, które mogą być objęte usługi Azure AD. Poniżej znajduje się opis każdego scenariusza:

1. Dostęp warunkowy do aplikacji hostowanych lokalnie: można używać zarejestrowanych urządzeń z zasadami dostępu dla aplikacji skonfigurowanych do używania usług AD FS w systemie Windows Server 2012 R2.

2. Kontrola dostępu do witryny Azure portal: Platforma Azure umożliwia również kontrolowanie dostępu do portalu przy użyciu kontroli dostępu opartej na rolach (RBAC)). Ta metoda umożliwia firmie ograniczyć liczbę operacji, które dana osoba może wykonać w witrynie Azure portal. Za pomocą RBAC kontrolować dostęp do portalu, administratorzy IT można delegować dostęp przy użyciu następujących metod zarządzania dostępem:

   - Przypisanie ról oparte na grupach: można przypisać dostęp do grup usługi Azure AD, które można synchronizować z lokalnej usługi Active Directory. Dzięki temu można wykorzystać istniejące inwestycje, które organizacja poczyniła w narzędzia i procesy zarządzania grupami. Można również użyć funkcji zarządzania delegowanymi grupami usługi Azure AD Premium.
   - Użyj wbudowanych ról na platformie Azure: można użyć trzech ról — właściciel, współautor i czytelnik, aby upewnić się, że użytkownicy i grupy mają uprawnienia do wykonywania tylko zadań, których potrzebują do wykonywania swoich zadań.
   -  Szczegółowy dostęp do zasobów: można przypisać role do użytkowników i grup dla określonej subskrypcji, grupy zasobów lub pojedynczego zasobu platformy Azure, takiego jak witryna sieci Web lub baza danych. W ten sposób można zapewnić, że użytkownicy mają dostęp do wszystkich zasobów, których potrzebują i nie ma dostępu do zasobów, które nie muszą zarządzać.

   > [!NOTE]
   > Jeśli tworzenie aplikacji i chcesz dostosować kontrolę dostępu dla nich, jest również możliwe użycie ról aplikacji usługi Azure AD do autoryzacji. Przejrzyj ten [przykład WebApp-RoleClaims-DotNet,](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) jak utworzyć aplikację, aby korzystać z tej funkcji.


3. Dostęp warunkowy dla aplikacji usługi Office 365 z usługą Microsoft Intune: administratorzy IT mogą aprowizować zasady urządzeń dostępu warunkowego w celu zabezpieczenia zasobów firmowych, jednocześnie umożliwiając pracownikom informacji na zgodnych urządzeniach dostęp do usług. 
  
4. Dostęp warunkowy dla aplikacji Saas: [Ta funkcja](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work/) umożliwia konfigurowanie reguł dostępu do uwierzytelniania wieloskładnikowego dla aplikacji i możliwość blokowania dostępu dla użytkowników nie w zaufanej sieci. Reguły uwierzytelniania wieloskładnikowego można zastosować do wszystkich użytkowników przypisanych do aplikacji lub tylko dla użytkowników w określonych grupach zabezpieczeń. Użytkownicy mogą zostać wykluczeni z wymogu uwierzytelniania wieloskładnikowego, jeśli uzyskują dostęp do aplikacji z adresu IP znajdującego się wewnątrz sieci organizacji.

Ponieważ opcje kontroli dostępu używają podejścia wielowarstwowego, porównanie tych opcji nie ma zastosowania do tego zadania. Upewnij się, że wykorzystujesz wszystkie opcje dostępne dla każdego scenariusza, który wymaga kontrolowania dostępu do zasobów.

## <a name="define-incident-response-options"></a>Definiowanie opcji reagowania na zdarzenia
Usługa Azure AD może pomóc it do tożsamości potencjalnych zagrożeń bezpieczeństwa w środowisku, monitorując aktywność użytkownika. Firma IT może używać raportów usługi Azure AD Access i Usage w celu uzyskania wglądu w integralność i bezpieczeństwo katalogu organizacji. Dzięki tym informacjom administrator IT może lepiej określić, gdzie mogą leżeć możliwe zagrożenia bezpieczeństwa, aby mogli odpowiednio zaplanować ograniczenie tych zagrożeń.  [Subskrypcja usługi Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) zawiera zestaw raportów zabezpieczeń, które umożliwiają it uzyskanie tych informacji. [Raporty usługi Azure AD](../reports-monitoring/overview-reports.md) są podzielone na następujące kategorie:

* **Raporty anomalii:** zawierają zdarzenia logowania, które okazały się nietypowe. Celem jest uświadomienie sobie takiej aktywności i umożliwienie ci ustalenia, czy zdarzenie jest podejrzane.
* **Raport zintegrowanych aplikacji:** zapewnia wgląd w sposób, w jaki aplikacje w chmurze są używane w organizacji. Usługa Azure Active Directory oferuje integrację z tysiącami aplikacji w chmurze.
* **Raporty o błędach:** wskaż błędy, które mogą wystąpić podczas inicjowania obsługi administracyjnej kont do aplikacji zewnętrznych.
* **Raporty specyficzne dla użytkownika**: Wyświetlanie danych aktywności urządzenia/logowania dla określonego użytkownika.
* **Dzienniki aktywności:** zawierają rekord wszystkich skontrolowanych zdarzeń w ciągu ostatnich 24 godzin, ostatnich 7 dni lub ostatnich 30 dni, a także zmiany aktywności w grupie oraz resetowanie hasła i rejestrowanie aktywności.

> [!TIP]
> Innym raportem, który może również pomóc zespołowi reagowania na incydenty pracującemu nad sprawą, jest użytkownik z raportem [o wycieku poświadczeń.](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials/) Ten raport powierzchni wszelkie dopasowania między listy nie przeciekły poświadczeń i dzierżawy.
>


Inne ważne wbudowane raporty w usłudze Azure AD, które mogą być używane podczas badania odpowiedzi na incydenty i są:

* **Działanie resetowania hasła:** zapewnij administratorowi wgląd w sposób aktywnego resetowania hasła w organizacji.
* **Działanie rejestracji resetowania hasła:** zapewnia wgląd w to, którzy użytkownicy zarejestrowali swoje metody resetowania hasła i jakie metody wybrali.
* **Działanie grupy:** zawiera historię zmian w grupie (np. użytkowników dodanych lub usuniętych), które zostały zainicjowane w Panelu dostępu.

Oprócz podstawowych możliwości raportowania usługi Azure AD Premium, których można używać podczas procesu badania odpowiedzi na incydenty, it może również korzystać z raportu inspekcji w celu uzyskania informacji, takich jak:

* Zmiany w członkostwie roli (na przykład użytkownik dodany do roli Administratora globalnego)
* Aktualizacje poświadczeń (na przykład zmiany hasła)
* Zarządzanie domeną (na przykład weryfikowanie domeny niestandardowej, usuwanie domeny)
* Dodawanie lub usuwanie aplikacji
* Zarządzanie użytkownikami (na przykład dodawanie, usuwanie, aktualizowanie użytkownika)
* Dodawanie lub usuwanie licencji

Ponieważ opcje reagowania na zdarzenia używają podejścia wielowarstwowego, porównanie tych opcji nie ma zastosowania do tego zadania. Upewnij się, że wykorzystujesz wszystkie opcje dostępne dla każdego scenariusza, który wymaga użycia funkcji raportowania usługi Azure AD w ramach procesu reagowania na incydenty w firmie.

## <a name="next-steps"></a>Następne kroki
[Określanie zadań zarządzania tożsamościami hybrydowymi](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień projektowych](plan-hybrid-identity-design-considerations-overview.md)
