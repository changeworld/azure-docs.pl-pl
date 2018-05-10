---
title: Możliwości techniczne zabezpieczeń platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat przetwarzania danych usług w chmurze zawierających szeroką gamę wystąpienia obliczeniowe i usług, które można skalować w górę i w dół automatycznie na potrzeby aplikacji lub przedsiębiorstwa.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 6643db7b732cc5b01ce7602eb3d679c130c46720
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="azure-security-technical-capabilities"></a>Możliwości techniczne zabezpieczeń platformy Azure

Ułatwienie Azure bieżących i przyszłych klientów zrozumieć i korzystanie z różnych funkcji zabezpieczeń dostępnych w i otaczającego platformy Azure, firma Microsoft wprowadziła serii oficjalne dokumenty, omówienie zabezpieczeń, najlepsze rozwiązania i Listy kontrolne. Tematy zakresu pod względem szerokości i głębokość i są okresowo aktualizowane. Ten dokument jest częścią tej serii, zgodnie z opisem w poniższej sekcji abstrakcyjny. Więcej informacji na temat tej serii zabezpieczeń Azure można znaleźć pod adresem (URL).

## <a name="azure-platform"></a>Platforma Azure

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) platformy w chmurze składa się z infrastrukturą i usług aplikacji, z usług zintegrowanych danych i zaawansowane metody analizy i narzędzia deweloperskie i usług, obsługiwana w danych firmy Microsoft w chmurze publicznej centrów. Klienci używać platformy Azure dla wiele różnych możliwości i scenariusze z podstawowych obliczeniowych, sieci i magazynu, dla urządzeń przenośnych i aplikacji usługi sieci web, pełna chmury różnych scenariuszy, takich jak Internet rzeczy i można używany z technologiami typu open source i wdrożony jako chmura hybrydowa lub hostowanych w ramach centrum danych klienta. Platforma Azure udostępnia technologii w chmurze, jak bloki konstrukcyjne w celu ograniczenia kosztów, które ułatwiają innowacji szybko i aktywne zarządzanie systemami. Podczas tworzenia lub migracji zasobów informatycznych do dostawcy usług w chmurze są zależne możliwości Twojej organizacji do ochrony aplikacji i danych z usług i formanty zapewniają do zarządzania zabezpieczeniami elementów zawartości opartej na chmurze.

Microsoft Azure jest to jedyny dostawca obliczeniowych chmury, który oferuje platformę aplikacji bezpiecznego, spójne i infrastruktury jako usługi dla zespołów do pracy w ramach ich skillsets inną chmurę i poziom złożoności projektu, z usługami zintegrowanych danych i analitycznych odkrywanie analizy danych wszędzie tam, gdzie istnieje, przez firmę Microsoft, jak i platform firmy Microsoft, otwórz struktur i narzędzi, zapewniając wyboru do integracji z lokalnymi również wdrażanie usług w chmurze Azure w ramach lokalnych centrów danych chmury. W ramach firmy Microsoft w chmurze zaufane klienci polegać na platformie Azure dla branży zabezpieczeń, zgodności, prywatności i niezawodność sieci przeważająca osób, partnerzy i procesów obsługuje organizacji w chmurze.

Microsoft Azure możesz:

- Przyspieszenie innowacji z chmurą.

- Decyzje biznesowe zasilania i aplikacji z informacjami.

- Za darmo tworzenie i wdrażanie dowolnego miejsca.

- Ochrona firmy.

## <a name="scope"></a>Zakres

Centralny punkt tego dokumentu dotyczy funkcji zabezpieczeń i funkcji pomocniczych — podstawowe składniki Microsoft Azure, czyli [magazyn Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-introduction), [baz danych SQL Azure Microsoft](https://docs.microsoft.com/azure/sql-database/), [modelu maszyny wirtualnej Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/)i infrastruktura i narzędzia zarządzania ich wszystkich. Oficjalny dokument fokus na Microsoft Azure możliwości techniczne dostępne jako klientów do wypełnienia ich rolę w ochronie zabezpieczenia i prywatność danych.

Opis tego modelu udostępnionego odpowiedzialność znaczenie jest niezbędne dla klientów, którzy są przenoszeniu do chmury. Dostawcy chmury oferują znaczące korzyści dla zabezpieczeń i zgodności, ale te zalety zwalnia klienta z ochrony swoich użytkowników, aplikacji i ofert usług.

Rozwiązania IaaS klienta jest odpowiedzialny lub odpowiada udostępnionego do zabezpieczania i zarządzania nimi systemu operacyjnego, konfigurację sieci, aplikacje, tożsamości, klientów i danych.  PaaS kompilacji rozwiązania w przypadku wdrożenia IaaS, klient nadal odpowiada lub ma wspólnej odpowiedzialności za zabezpieczania i zarządzanie aplikacjami, tożsamości, klientów i danych. Rozwiązania SaaS, Nonetheless, klient w dalszym ciągu odpowiada. One zapewnić, że dane są poprawnie klasyfikowane, i ich współodpowiedzialne, do zarządzania ich użytkowników i urządzeń punktu końcowego.

Ten dokument nie zawiera szczegółowych pokrycia dowolnego powiązane składniki platformy Microsoft Azure, takich jak witryny sieci Web platformy Azure, Azure Active Directory, HDInsight, Media Services i innych usług, które są warstwie nad podstawowe składniki. Mimo że podano minimalny poziom informacje ogólne, czytników uznaje, że znasz podstawowe pojęcia Azure zgodnie z opisem w inne odwołania obsługiwane przez firmę Microsoft i zawarte w łączy w tym oficjalnym dokumencie.

## <a name="available-security-technical-capabilities-to-fulfil-user-customer-responsibility---big-picture"></a>Możliwości techniczne zabezpieczeń dostępne do wypełnienia odpowiedzialności użytkowników (klienta) - duży obraz

Microsoft Azure oferuje usługi, które mogą pomóc klientów do potrzeb zabezpieczeń, prywatności i zgodności. Na poniższej ilustracji pomaga opisano różne usługi platformy Azure dostępna dla użytkowników do utworzenia infrastruktury aplikacji bezpieczne i zgodne, oparte na standardach.

![Techniczne obraz Big możliwości dostępnych zabezpieczeń](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access-protect"></a>Zarządzanie i sterowanie tożsamości i dostęp użytkownika (Chroń)

Azure pomaga chronić działalności biznesowej i informacje osobiste, należy włączyć zarządzanie tożsamościami użytkowników i poświadczeń i kontroli dostępu.

### <a name="azure-active-directory"></a>Usługa Azure Active Directory

Pomocy firmy Microsoft tożsamościami i dostępem zarządzania rozwiązań IT ochrony dostępu do aplikacji i zasobów w firmowym centrum danych i w chmurze, włączanie dodatkowe poziomy sprawdzania poprawności, takich jak uwierzytelnianie wieloskładnikowe i zasady dostępu warunkowego. Monitorowania podejrzanych działań przez zaawansowane zabezpieczenia raportowania, inspekcji i alerty, pomaga ograniczyć potencjalne problemy. [Azure Active Directory Premium](https://docs.microsoft.com/azure/active-directory/active-directory-editions) zapewnia jednokrotnego do tysięcy chmury aplikacji (SaaS) i dostęp do aplikacji sieci web, możesz uruchomić lokalnie.

Korzyści w zakresie zabezpieczeń usługi Azure Active Directory (Azure AD) mogą być następujące:

- Utwórz i Zarządzaj jednej tożsamości dla każdego użytkownika w przedsiębiorstwie hybrydowego Synchronizacja użytkowników, grup i urządzeń.

- Podaj pojedynczy logowania jednokrotnego dostęp do aplikacji w tym tysięcy wstępnie zintegrowanych aplikacji SaaS.

- Włącz zabezpieczenia dostępu do aplikacji, wymuszając uwierzytelnianie wieloskładnikowe opartych na regułach, które zarówno lokalnie i aplikacji w chmurze.

- Udostępnić bezpieczny dostęp zdalny do lokalnych aplikacji sieci web za pośrednictwem serwera Proxy aplikacji usługi Azure AD.

[Portalu usługi Azure Active Directory](http://aad.portal.azure.com/) jest dostępna części portalu Azure. Z tego pulpitu nawigacyjnego zapoznaj się z omówieniem stanu Twojej organizacji i Poznaj łatwe zarządzanie katalogu, użytkowników lub dostęp do aplikacji.

![Usługa Azure Active Directory](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig2.png)

Poniżej przedstawiono podstawowe możliwości zarządzania Azure tożsamości:

- Logowanie jednokrotne

- Uwierzytelnianie wieloskładnikowe

- Monitorowanie zabezpieczeń, alertów i raportów na podstawie learning maszyny

- Zarządzanie tożsamościami i dostępem klientów

- Rejestracja urządzenia

- Zarządzanie tożsamościami uprzywilejowanymi

- Ochrona tożsamości

#### <a name="single-sign-on"></a>Logowanie jednokrotne

[Logowanie jednokrotne (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) oznacza dostępowi do wszystkich aplikacji i zasobów potrzebnych do prowadzenia działalności, logując się tylko raz przy użyciu jednego konta użytkownika. Po zalogowaniu możesz dostęp do wszystkich aplikacji, należy nie są wymagane do uwierzytelniania (na przykład wpisz hasło) po raz drugi.

W wielu organizacjach opierają się na oprogramowania jako aplikacje usługi (SaaS), takich jak Office 365, pole i Salesforce na produktywność użytkownika końcowego. W przeszłości personel działu informatycznego jest niezbędne do indywidualnie tworzenie i aktualizowanie kont użytkowników w każdej aplikacji SaaS, a użytkownicy będą musieli Zapamiętaj hasło dla każdej aplikacji SaaS.

[Usługi Azure AD rozszerza lokalnej usługi Active Directory do chmury](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis), umożliwiając użytkownikom używanie ich podstawowego konta organizacyjnego, nie tylko Zaloguj się do swoich urządzeń dołączonych do domeny i zasobów firmy, ale również wszystkich aplikacji sieci web i aplikacji SaaS potrzebne do ich zadania.

Nie tylko użytkowników nie trzeba zarządzać wiele zestawów nazwy użytkowników i hasła, dostęp do aplikacji mogą być automatycznie udostępnione lub cofnąć elastycznie na podstawie grupy organizacyjne i ich stan jako pracownika. [Usługi Azure AD wprowadza formanty Zarządzanie zabezpieczeniami i dostępem](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) umożliwiające centralne zarządzanie dostępem użytkowników do aplikacji SaaS.

#### <a name="multi-factor-authentication"></a>Uwierzytelnianie wieloskładnikowe

[Usługa Azure Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) jest metoda uwierzytelniania, która wymaga użycia więcej niż jednej metody weryfikacji i dodaje kluczową drugą warstwę zabezpieczeń do logowania użytkowników i transakcji. [MFA ułatwia zabezpieczenie](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works) dostęp do danych i aplikacji, spełniając zapotrzebowanie na prosty proces logowania. Zapewnia silne uwierzytelnianie za pomocą różnych opcji weryfikacji — połączenie telefoniczne, wiadomość tekstowa lub aplikacja mobilna weryfikacji lub powiadamiania o kodzie i innych firm tokenów OAuth.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitorowanie zabezpieczeń, alertów i raportów na podstawie learning maszyny

Monitorowanie zabezpieczeń i alertów i machine learning raportów na podstawie identyfikujące niespójne wzorce dostępu ułatwia ochronę firmy. Dostęp do usługi Azure Active Directory i raporty użycia umożliwia wgląd we integralności i bezpieczeństwa katalogu organizacji. Dzięki tym informacjom administratora katalogu można lepiej określić, gdzie może znajdować się możliwe zagrożenia bezpieczeństwa, tak aby ich odpowiednio zaplanować ich eliminowania.

W portalu Azure lub za pomocą [portalu usługi Azure Active Directory](http://aad.portal.azure.com/), [raporty](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) są podzielone na następujące sposoby:

- Raporty anomalii — zawiera zdarzenia znajdujące się nietypowe logowania. Naszym celem jest uświadomić możesz takiego działania i umożliwiają zdecydować, czy jest podejrzane zdarzenia.

- Raporty aplikacji zintegrowanego — wgląd w sposób aplikacji w chmurze są używane w organizacji. Usługa Azure Active Directory oferuje integrację z tysiącami aplikacji w chmurze.

- Raporty o błędach — wskazują błędy, które mogą wystąpić podczas inicjowania obsługi administracyjnej kont do aplikacji zewnętrznych.

- Raporty dotyczące użytkownika — wyświetlać urządzenia/symbol w dane o aktywności dla określonego użytkownika.

- Dzienniki aktywności — zawiera rekord wszystkich zdarzeń inspekcji w ostatnich 24 godzinach, ostatnich 7 dni lub ostatnich 30 dni i grupy działania zmiany i działanie resetowania i rejestracji hasła.

#### <a name="consumer-identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem klientów

[Usługa Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) jest tożsamość wysokiej dostępności, globalnych, zarządzanie usługą w aplikacjach użytkownika, która może obsłużyć setki milionów tożsamości. Można ją łatwo integrować z platformami mobilnymi i platformami sieci Web. Konsumenci mogą logować się do wszystkich aplikacji przy użyciu w pełni dostosowywanego procesu, używając istniejących kont sieci społecznościowych lub tworząc nowe poświadczenia.

W przeszłości, deweloperzy aplikacji, którzy chcieli [i zalogują się konsumentów](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview) w swoich aplikacjach, musieli napisać własny kod. Musieli korzystać z lokalnych baz danych lub systemów do przechowywania nazw i haseł użytkowników. Usługa Azure Active Directory B2C oferuje organizacji lepszy sposób integracji zarządzania tożsamością użytkowników w aplikacjach za pomocą bezpiecznej, spełniającej standardy platformy i dużego zestawu rozszerzalnych zasad.

Gdy używasz usługi Azure Active Directory B2C użytkownicy mogą rejestrować dla aplikacji, przy użyciu istniejących kont społecznościowych (Facebook, Google, Amazon, LinkedIn) lub tworząc nowe poświadczenia (adres e-mail i hasło lub nazwa użytkownika i hasło).

#### <a name="device-registration"></a>Rejestracja urządzenia

[Rejestracja urządzenia w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/device-management-introduction) stanowi podstawę opartego na urządzeniach [dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-on-premises-setup) scenariuszy. Po zarejestrowaniu urządzenia rejestracji urządzeń usługi Azure AD zapewnia urządzenia przy użyciu tożsamości, który służy do uwierzytelniania urządzenia podczas logowania się użytkownika. Uwierzytelnionego urządzenia i atrybutów urządzenia można następnie użyć do wymuszania zasad dostępu warunkowego dla aplikacji hostowanych w chmurze i lokalnych.

W połączeniu z [zarządzania urządzeniami przenośnymi (MDM)](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) rozwiązania, takie jak usługi Intune, atrybuty urządzenia w usłudze Azure Active Directory są aktualizowane przy użyciu dodatkowych informacji o urządzeniu. Umożliwia to tworzenie reguł dostępu warunkowego, które wymuszają dostęp z urządzeń spełniających określone standardy zabezpieczeń i zgodności.

#### <a name="privileged-identity-management"></a>Zarządzanie tożsamościami uprzywilejowanymi

[Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) umożliwia zarządzanie, kontrolowania i monitorowanie tożsamości uprzywilejowanych oraz uzyskać dostęp do zasobów w usłudze Azure AD, jak również inne usługi online firmy Microsoft, takich jak usługi Office 365 lub Microsoft Intune.

Czasami użytkownicy muszą wykonać operacje uprzywilejowane w zasobów platformy Azure lub usługi Office 365 lub innych aplikacji SaaS. Często oznacza to, że organizacje mają nadanie im stałe uprzywilejowanego dostępu w usłudze Azure AD. Jest to zagrożenie bezpieczeństwa rosnącym zasobów hostowanych w chmurze, ponieważ organizacje wystarczająco nie można monitorować, co robią tych użytkowników, używając swoich uprawnień administratora. Ponadto w przypadku złamania zabezpieczeń konta użytkownika z dostępem uprzywilejowanym tego naruszenia co może mieć wpływ na ich ogólne bezpieczeństwo chmury. Azure AD Privileged Identity Management pomaga rozwiązywać to ryzyko.

Azure AD Privileged Identity Management umożliwia:

- Zobacz użytkowników, którzy są administratorami usługi Azure AD

- Włącz na żądanie "just in time" dostęp administracyjny do usługi Online firmy Microsoft, takich jak Office 365 i Intune

- Uzyskaj raporty dotyczące historii dostępu administratora i zmian w przypisaniach administratora

- Uzyskiwanie alertów dotyczących dostępu do ról uprzywilejowanych

#### <a name="identity-protection"></a>Ochrona tożsamości

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) to usługa zabezpieczeń, która udostępnia skonsolidowany wgląd w zdarzenia o podwyższonym ryzyku i potencjalnych luk w zabezpieczeniach wpływających na tożsamości organizacji. Identity Protection korzysta z możliwości wykrywania anomalii istniejących Azure usługi Active Directory (dostępne za pośrednictwem usługi Azure AD nietypowe działanie raportów) i wprowadza nowe typy zdarzeń ryzyka, które można wykrywania anomalii w czasie rzeczywistym.

## <a name="secured-resource-access-in-azure"></a>Dostęp do zabezpieczonych zasobów na platformie Azure

Kontrola dostępu na platformie Azure rozpoczyna się z punktu widzenia rozliczeń. Właściciel konta platformy Azure, dostępne po przejściu na stronę [Centrum konta platformy Azure](https://account.windowsazure.com/subscriptions), jest kontem administratora (AA). Subskrypcje są kontener rozliczeń, ale również działają jako granic zabezpieczeń: Każda subskrypcja ma usługi administratora kto dodawania, usuwania i modyfikowania zasobów platformy Azure w ramach tej subskrypcji przy użyciu portalu Azure. SA domyślne nowej subskrypcji jest AA, ale AA można zmienić SA w Centrum konta platformy Azure.

![Dostęp do zabezpieczonych zasobów na platformie Azure](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig3.png)

Subskrypcje mają również skojarzenie z katalogiem. Katalog definiuje zestaw użytkowników. Mogą to być użytkownikom pracy lub nauki, który utworzył katalog lub można je użytkowników zewnętrznych (to znaczy Accounts firmy Microsoft). Subskrypcje są dostępne dla podzbioru użytkowników katalogu, na których został przypisany jako usługi administratora lub administratora współpracującego (CA); Jedynym wyjątkiem jest w starszej wersji ze względu na Accounts Microsoft (dawniej identyfikator Windows Live ID) można przypisać jako administratora systemu lub urzędu certyfikacji nie jest obecny w katalogu.

Nastawionych zabezpieczeń należy skoncentrować się na zapewniając pracownikom dokładne uprawnienia potrzebne. Za dużo uprawnienia mogą uwidaczniać konta na ataki. Za mało uprawnienia oznacza, że pracownicy nie można pobrać ich pracować wydajnie. [Azure opartej na rolach kontroli dostępu (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) pomaga rozwiązać ten problem, oferując precyzyjne zarządzanie dostępem dla platformy Azure.

![Dostęp do zabezpieczonych zasobów ](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig4.png)

Przy użyciu funkcji RBAC, można rozdzielenie obowiązków w obrębie organizacji i udzielić tylko takiego dostępu dla użytkowników, które są niezbędne do wykonywania swoich zadań. Zamiast nadanie każdy nieograniczonych uprawnień w Twojej subskrypcji platformy Azure lub zasobów, można zezwolić tylko pewne akcje. Na przykład użycie funkcji RBAC, aby umożliwić jednego pracownika zarządzać maszyn wirtualnych w ramach subskrypcji, gdy inny można zarządzać baz danych w ramach tej samej subskrypcji.

![Dostęp do zabezpieczonych zasobów Azure (RBAC)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="azure-data-security-and-encryption-protect"></a>Bezpieczeństwo danych platformy Azure i szyfrowania (Ochrona)

Jeden z kluczy do ochrony danych w chmurze jest księgowanie możliwe stany, w których może wystąpić danych oraz kontrolki są dostępne dla tego stanu. Dla danych Azure zabezpieczeń i szyfrowania najlepsze rozwiązania zalecenia można wokół stanów następujące dane.

- W pozostałych: W tym wszystkie informacje, które typy, statycznie występujących na nośnik fizyczny, kontenerów i obiektów magazynu można go magnetyczne lub optyczne dysku.

- Podczas przesyłania: Podczas transferu danych między składnikami, lokalizacji lub programy, takie jak przez sieć, przez usługi service bus (z lokalnymi do chmury i odwrotnie, łącznie z połączeń hybrydowych, takie jak ExpressRoute) lub w trakcie operacji wejścia/wyjścia on jest traktować jako w ruchu.

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

Aby osiągnąć szyfrowania magazynowane, wykonaj następujące czynniki:

Obsługuje co najmniej jeden z modeli zalecane szyfrowania szczegółowo w poniższej tabeli do szyfrowania danych.

| Modele szyfrowania |  |  |  |
| ----------------  | ----------------- | ----------------- | --------------- |
| Szyfrowanie serwera | Szyfrowanie serwera | Szyfrowanie serwera | Szyfrowanie klienta
| Przy użyciu usługi zarządzane klucze szyfrowania po stronie serwera | W usłudze Azure Key Vault za pomocą Customer-Managed klucze szyfrowania po stronie serwera | Przy użyciu lokalnego klienta zarządzane klucze szyfrowania po stronie serwera |
| • Dostawców zasobów azure wykonywać operacje szyfrowania i odszyfrowywania <br> • Firma Microsoft zarządza kluczy <br>• Chmury pełna funkcjonalność | • Dostawców zasobów azure wykonywać operacje szyfrowania i odszyfrowywania<br>• Klient kontroluje klucze za pomocą usługi Azure Key Vault<br>• Chmury pełna funkcjonalność | • Dostawców zasobów azure wykonywać operacje szyfrowania i odszyfrowywania <br>• Klient kontroluje klucze lokalnego <br> • Chmury pełna funkcjonalność| • Usług azure nie widzi odszyfrowane dane <br>• Klienci przechowywać klucze lokalnie lub w innych secure magazynów. Klucze nie są dostępne do usług platformy Azure <br>• Zmniejszona funkcji chmury|

### <a name="enabling-encryption-at-rest"></a>Włączenie szyfrowania magazynowane

**Zidentyfikuj wszystkie lokalizacje magazyny danych**

Celem szyfrowanie magazynowanych jest do szyfrowania wszystkich danych. W ten sposób unika się Brak ważnych danych lub wszystkich lokalizacji utrwalonych. Wyświetla wszystkie dane przechowywane przez aplikację. 

> [!Note] 
> Nie tylko "application data" lub "dane osobowe", ale wszystkie dane związane z tym aplikacji konta metadanych (mapowania subskrypcji, informacje kontraktu danych osobowych).

Należy wziąć pod uwagę jakie magazynów jest używany do przechowywania danych. Na przykład:

- Magazynu zewnętrznego (na przykład SQL Azure, bazie danych dokumentów, HDInsights, Data Lake, itp.)

- Magazyn tymczasowy (żadnych lokalnej pamięci podręcznej zawierającego dane dzierżawcy)

- W pamięci podręcznej (mogą znajdować się w pliku stronicowania.)

### <a name="leverage-the-existing-encryption-at-rest-support-in-azure"></a>Wykorzystywanie istniejących szyfrowania w witrynie pomocy technicznej rest na platformie Azure

Dla każdego używanego sklepu wykorzystać istniejące szyfrowania w witrynie pomocy technicznej Rest.

- Magazyn Azure: Zobacz [szyfrowanie usługi Magazyn Azure dla danych magazynowanych](https://docs.microsoft.com/azure/storage/storage-service-encryption),

- Usługi SQL Azure: Zobacz [przezroczystego szyfrowania danych (funkcji TDE), zawsze zaszyfrowane programu SQL](https://msdn.microsoft.com/library/mt163865.aspx)

- Na dysku magazynu maszyny Wirtualnej & lokalny ([szyfrowania dysków Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption))

Dla maszyny Wirtualnej i lokalny magazyn dyskowy Użyj szyfrowania dysków Azure w przypadku, gdy obsługiwane:

#### <a name="iaas"></a>IaaS

Usługi z maszyn wirtualnych IaaS (Windows lub Linux) powinny używać [szyfrowania dysków Azure](https://microsoft.sharepoint.com/teams/AzureSecurityCompliance/Security/SitePages/Azure%20Disk%20Encryption.aspx) do szyfrowania woluminy zawierające dane klienta.

#### <a name="paas-v2"></a>PaaS w wersji 2

Usługi uruchomione na PaaS v2 przy użyciu usługi Service Fabric szyfrowania dysków Azure dla zestawu skalowania maszyn wirtualnych [VMSS] do szyfrowania można używać ich maszyny wirtualne w wersji 2 PaaS.

#### <a name="paas-v1"></a>PaaS v1

Szyfrowanie dysków Azure aktualnie nie jest obsługiwane na PaaS v1. W związku z tym należy użyć do szyfrowania danych magazynowanych: szyfrowanie na poziomie aplikacji.  Obejmuje, ale nie jest ograniczona do danych aplikacji, plików tymczasowych, dzienniki i zrzuty awaryjne.

Większość usług powinien próbować korzystać z szyfrowania dostawcy zasobów magazynu. Niektóre usługi konieczne przeprowadzenie jawne szyfrowania, na przykład żadnego utrwalonego materiału klucza (certyfikaty, głównego / wzorca kluczy) musi być przechowywany w magazynie kluczy.

Jeśli szyfrowanie po stronie serwera za pomocą kluczy zarządzany przez klienta musi istnieć sposób dla klienta pobrać klucz do nas. Obsługiwane i zalecany sposób dzięki integracji z magazynu kluczy Azure (AKV). W takim przypadku klientów, można dodać i zarządzać swoimi kluczami w usłudze Azure Key Vault. Klienta można dowiedzieć się, jak używać AKV za pośrednictwem [wprowadzenie do korzystania z usługi Key Vault](http://go.microsoft.com/fwlink/?linkid=521402).

Aby zintegrować z usługą Azure Key Vault, należy dodać kodu poprosić klucz AKV, gdy są potrzebne do odszyfrowywania.

- Zobacz [usługi Azure Key Vault — krok po kroku](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step/) uzyskać informacje dotyczące sposobu integracji z AKV.

Jeśli obsługuje klientów zarządzanych kluczy, musisz podać UX dla klienta, aby określić magazynu kluczy (lub identyfikator URI magazynu klucza) do użycia.

Szyfrowanie magazynowanych obejmuje szyfrowania hosta, infrastruktury i dzierżawcy danych, utratę klucze z powodu awarii systemu lub złośliwych działań może oznaczać wszystkie zaszyfrowane dane zostaną utracone. W związku z tym jest krytyczny, że szyfrowania rozwiązania Rest ma scenariusza odzyskiwania po awarii kompleksowe odporność na awarie systemu i złośliwych działań.

Zazwyczaj są nadal podatne na kluczy szyfrowania usług, które implementuje szyfrowanie przechowywanych lub danych pozostaje w postaci niezaszyfrowanej na dysku hosta (na przykład w pliku stronicowania systemu operacyjnego hosta.) W związku z tym usługi należy zapewnić, że wolumin hosta dla swoich usług są szyfrowane. Ułatwia to obliczeń zespołu włączył wdrożenia szyfrowania hosta, który używa [funkcji Bitlocker](https://technet.microsoft.com/library/dn306081.aspx) NKP i rozszerzeń usługi DCM i agenta do szyfrowania woluminu hosta.

Większość usług są implementowane na maszynach wirtualnych Azure standard. Tych usług należy uzyskać [szyfrowania hosta](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) automatycznie podczas obliczeń włączy ją. Dla usług uruchomionych w obliczeń zarządzane klastry hosta szyfrowanie jest włączane automatycznie, zgodnie z systemu Windows Server 2016 jest wdrażana.

### <a name="encryption-in-transit"></a>Szyfrowanie podczas przesyłania danych

Ochrona danych podczas przesyłania powinien być integralną część strategii ochrony danych. Ponieważ dane są przenoszone i z powrotem w wielu lokalizacjach, ogólne zalecenie jest zawsze używają protokołów SSL/TLS do wymiany danych w różnych lokalizacjach. W niektórych sytuacjach można odizolować kanału całej komunikacji między lokalnymi i w chmurze infrastruktury przy użyciu wirtualnej sieci prywatnej (VPN).

Przenoszenie między lokalną infrastrukturą i Azure danych należy rozważyć odpowiednie zabezpieczenia, takie jak HTTPS lub sieci VPN.

Dla organizacji, które trzeba bezpieczny dostęp z wielu stacji roboczych lokalnego do platformy Azure, użyj [Azure VPN lokacja lokacja](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create).

Dla organizacji, które trzeba bezpieczny dostęp z jednej stacji roboczej znajdujących się lokalnie do platformy Azure, użyj [punkt-lokacja sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create).

Większych zestawów danych można przenieść za pośrednictwem dedykowanej o dużej szybkości łącza sieci WAN takich jak [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Jeśli chcesz używać usługi ExpressRoute, można także szyfrowanie danych na poziomie aplikacji przy użyciu [SSL/TLS](https://support.microsoft.com/kb/257591) lub innymi protokołami zapewnia dodatkową ochronę.

Jeśli użytkownik korzysta z usługi Azure Storage za pośrednictwem portalu Azure, wszystkich transakcji jest realizowana za pośrednictwem protokołu HTTPS. [Interfejs API REST magazynu](https://msdn.microsoft.com/library/azure/dd179355.aspx) over HTTPS można również wchodzić w interakcje z [usługi Azure Storage](https://azure.microsoft.com/services/storage/) i [bazy danych SQL Azure](https://azure.microsoft.com/services/sql-database/).

Organizacje, które się nie powieść, aby chronić przesyłane dane są bardziej narażony na [ataków man-in--middle](https://technet.microsoft.com/library/gg195821.aspx), [podsłuchiwaniu](https://technet.microsoft.com/library/gg195641.aspx)i przejęcie kontroli sesji. Tego rodzaju ataki może być pierwszym krokiem w uzyskiwaniu dostępu do poufnych danych.

Możesz można dowiedzieć się więcej o sieci VPN platformy Azure opcja przeczytaj artykuł [planowania i projektowania dla bramy sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

### <a name="enforce-file-level-data-encryption"></a>Wymuszanie szyfrowania danych na poziomie plików

[Usługa Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) używa zasad szyfrowania, tożsamości i autoryzacji, aby ułatwić zabezpieczanie plików i wiadomości e-mail. Usługa Azure RMS działa na wielu urządzeniach — telefonach, tablety i komputery, aby chronić w obrębie organizacji i poza organizacją. Ta funkcja jest możliwa, ponieważ usługa Azure RMS dodaje poziom ochrony, która jest powiązana z danymi nawet wtedy, gdy opuszczą teren organizacji.

Jeśli używasz usługi Azure RMS do ochrony plików za pomocą branżowego standardu kryptografii pełnej obsługi [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). Podczas ochrony danych należy korzystać z usługi Azure RMS, masz gwarantują, że ta ochrona pozostanie z plikiem, nawet jeśli zostanie skopiowany do magazynu, które nie są pod kontrolą IT, takich jak Usługa magazynu w chmurze. Taki sam występuje w przypadku plików udostępnianych za pośrednictwem poczty e-mail, plik jest chroniony jako załącznik do wiadomości e-mail z instrukcjami sposobu otwierania chronionego załącznika.
Podczas planowania wdrożenia usługi Azure RMS firma Microsoft zaleca następujące czynności:

- Zainstaluj [aplikacji RMS sharing](https://technet.microsoft.com/library/dn339006.aspx). Ta aplikacja jest zintegrowany z pakietu Office aplikacji przez zainstalowanie pakietu Office dodatek tak, aby użytkownikom ochronę plików bezpośrednio.

- Skonfiguruj aplikacje i usługi obsługują usługę Azure RMS

- Utwórz [szablonów niestandardowych](https://technet.microsoft.com/library/dn642472.aspx) która odzwierciedla wymagania firmy. Na przykład: szablon do górnej poufne dane, które powinny być stosowane w wszystkich ściśle tajne związane z wiadomości e-mail.

Organizacje, które są słabe na [klasyfikacji danych](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) i ochrona plików może być bardziej narażony na wycieku danych. Bez ochrony prawidłowego pliku organizacji nie będzie można uzyskiwać cenne informacje biznesowe, monitorować nadużycia i uniemożliwić nieautoryzowanym dostępem do plików.

> [!Note]
> Dodatkowe informacje na temat usługi Azure RMS od przeczytania artykułu [wprowadzenie do korzystania z usługi Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).

## <a name="secure-your-application-protect"></a>Zabezpieczanie aplikacji (Ochrona)
Gdy platforma Azure jest odpowiedzialny za bezpieczeństwo infrastruktury i platformy, która aplikacja działa na, jest obowiązek secure samej aplikacji. Innymi słowy musisz tworzenie, wdrażanie i zarządzanie kodem aplikacji i zawartości w bezpieczny sposób. W przeciwnym razie z kodu aplikacji lub zawartość nadal może być narażony na zagrożenia.

### <a name="web-application-firewall-waf"></a>Zapora aplikacji sieci Web
[Zapora aplikacji sieci Web (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) jest funkcją [brama aplikacji w](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) zapewnia scentralizowane ochrony aplikacji sieci web z najczęściej luki w zabezpieczeniach i luk w zabezpieczeniach.

Zapora aplikacji sieci Web zapewnia ochronę na podstawie reguł z [podstawowych zestawów reguł OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) w wersji 3.0 lub 2.2.9. Aplikacje sieci Web coraz częściej stają się obiektami złośliwych ataków wykorzystujących znane luki w zabezpieczeniach. Wśród nich często zdarzają się np. ataki polegające na iniekcji SQL i ataki z użyciem skryptów wykorzystywanych w wielu witrynach. Zapobieganie takim atakom z poziomu kodu aplikacji może być trudne. Może też wymagać rygorystycznego przestrzegania harmonogramu konserwacji, poprawek i monitorowania na poziomie wielu warstw topologii aplikacji. Scentralizowana zapora aplikacji sieci Web ułatwia zarządzanie zabezpieczeniami oraz zapewnia lepszą ochronę administratorów aplikacji przed zagrożeniami i intruzami. Zapora aplikacji sieci Web może reagować na zagrożenia bezpieczeństwa szybciej — poprzez wdrażanie poprawek zapobiegających wykorzystaniu znanych luk w zabezpieczeniach w centralnej lokalizacji zamiast w poszczególnych aplikacjach sieci Web. Istniejące bramy Application Gateway można łatwo przekonwertować na bramę Application Gateway obsługującą zaporę aplikacji sieci Web.

Oto niektóre typowe luki w zabezpieczeniach sieci Web, przed którymi chroni zapora aplikacji sieci Web:

- Ochrona przed atakami polegającymi na iniekcji SQL

- Ochrona przed atakami z użyciem skryptów wykorzystywanych w obrębie wielu witryn

- Częste ataki w ramach sieci Web polegające na iniekcji poleceń, przemycaniu żądań HTTP, rozdzielaniu odpowiedzi HTTP i zdalnym dołączaniu plików

- Ochrona przed naruszeniami protokołu HTTP

- Ochrona przed nieprawidłowościami protokołu HTTP, takimi jak brakujące powiązania agenta i użytkownika hosta oraz akceptowanie nagłówków

- Zapobieganie atakom z użyciem robotów, przeszukiwarek i skanerów

- Wykrycie typowych błędów konfiguracji aplikacji (czyli Apache usług IIS, itp.)

> [!Note]
> Aby bardziej szczegółową listę reguł i ich ochrony, zobacz następujące [podstawowe zestawy reguł](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview#core-rule-sets):

Platforma Azure udostępnia również kilka funkcji łatwy w użyciu w celu zabezpieczania ruchu przychodzącego i wychodzącego dla aplikacji. Azure również pomaga klientom zabezpieczyć ich kodu aplikacji przez zapewnienie zewnętrznie realizować funkcje do skanowania luk w zabezpieczeniach aplikacji sieci web.

- [Konfiguracja uwierzytelniania aplikacji usługi Azure Active Directory](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [Bezpieczny ruch do aplikacji przez włączenie Transport Layer Security (TLS/SSL) - HTTPS](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)

  - [Wymuś cały ruch przychodzący za pośrednictwem połączenia HTTPS](http://microsoftazurewebsitescheatsheet.info/)

  - [Włącz zabezpieczenie Transport ograniczeniami (HSTS)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [Ograniczanie dostępu do aplikacji za pomocą adresu IP klienta](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Ograniczanie dostępu do aplikacji przez zachowanie klienta - częstotliwość żądania i współbieżność](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Skanowanie kodu aplikacji sieci web dla luk w zabezpieczeniach za pomocą skanowania przy użyciu usługi Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [Skonfiguruj uwierzytelnianie wzajemne TLS, aby wymagać certyfikaty klienta do nawiązania połączenia aplikacji sieci web](https://docs.microsoft.com/azure/app-service/app-service-web-configure-tls-mutual-auth)

- [Skonfiguruj certyfikat klienta do użycia z aplikacji w celu nawiązania bezpiecznego połączenia z zasobów zewnętrznych](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Usuń nagłówków standard server, aby uniknąć narzędzi z odcisków aplikacji](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Bezpieczne łączenie aplikacji z zasobami w sieci prywatnej przy użyciu sieci VPN typu punkt-lokacja](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)

- [Bezpieczne łączenie z zasobami w sieci prywatnej przy użyciu połączeń hybrydowych aplikacji](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)

Usługa aplikacji Azure korzysta z tego samego rozwiązania chroniące przed złośliwym kodem przez usług Azure Cloud Services i maszyny wirtualne. Aby dowiedzieć się więcej o tym odwoływać się do naszej [dokumentacji ochrony przed złośliwym kodem](https://docs.microsoft.com/azure/security/azure-security-antimalware).

## <a name="secure-your-network-protect"></a>Zabezpieczenia sieci (Ochrona)
Microsoft Azure obejmuje to niezawodna infrastruktura sieci do obsługi aplikacji i wymaganiami dotyczącymi łączności usługi. Łączność sieciowa będzie możliwe między zasobami znajdującymi się na platformie Azure, między lokalnymi i Azure hostowanych zasobów oraz do i z Internetu i Azure.

[Infrastruktury sieci platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) umożliwia bezpieczne łączenie z zasobów platformy Azure za pomocą [sieci wirtualnych (sieci wirtualne)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Sieci wirtualnej jest odzwierciedla w chmurze Twoją sieć. Sieci wirtualnej jest logiczną izolacją sieci chmury Azure przeznaczoną dla Twojej subskrypcji. Możesz połączyć sieci wirtualnych do sieci lokalnej.

![Zabezpieczenia sieci (Ochrona)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig6.png)

Jeśli potrzebujesz kontroli dostępu na poziomie sieci podstawowej (na podstawie adresu IP i protokoły TCP lub UDP), a następnie można użyć [grup zabezpieczeń sieci](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg). Grupy zabezpieczeń sieci (NSG) jest filtrowanie Zapora podstawowa pakietów stanowe i umożliwia kontrolowanie dostępu na podstawie [5-elementowej](https://www.techopedia.com/definition/28190/5-tuple).

Sieć platformy Azure obsługuje możliwość dostosowania zachowania routingu ruchu w sieci dla sieci wirtualne platformy Azure. Można to zrobić przez skonfigurowanie [trasy zdefiniowane przez użytkownika](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) na platformie Azure.

[Wymuszone tunelowanie](https://www.petri.com/azure-forced-tunneling) mechanizm służy do zapewnienia usług nie są dozwolone do nawiązania połączenia z urządzeniami przez Internet.

Obsługuje platformy Azure w wersji dedykowanej łącza sieci WAN łączność z siecią lokalną i sieci wirtualnej platformy Azure z [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). Powiązanie Azure i witryny sieci Web używa dedykowane połączenie, które nie przechodzi przez publicznej sieci Internet. Jeśli aplikacja Azure jest uruchomiona w wielu centrach danych, możesz użyć [usługi Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) rozsyłanie żądań od użytkowników inteligentnie między wystąpieniami aplikacji. Można również kierować ruchem do usługi nie są uruchomione na platformie Azure, jeśli są one dostępne z Internetu.

## <a name="virtual-machine-security-protect"></a>Zabezpieczeń maszyny wirtualnej (Ochrona)

[Maszyny wirtualne platformy Azure](https://docs.microsoft.com/azure/virtual-machines/) umożliwia wdrażanie szeroką gamę rozwiązań opartych na przetwarzaniu w sposób elastyczne. Dzięki obsłudze rozwiązań Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM i SAP oraz usługi BizTalk Services na platformie Azure możesz wdrożyć dowolne obciążenie w dowolnym języku i w prawie każdym systemie operacyjnym.

Przy użyciu platformy Azure, można użyć [ochrony przed złośliwym oprogramowaniem](https://docs.microsoft.com/azure/security/azure-security-antimalware) od dostawców zabezpieczeń, takie jak Microsoft, firmy Symantec, Trend Micro i Kaspersky, aby chronić maszyny wirtualne z złośliwych plików, adware i innymi zagrożeniami.

Antimalware firmy Microsoft dla usług Azure Cloud Services i maszyn wirtualnych jest możliwość ochrony w czasie rzeczywistym, która pomaga w identyfikacji i usuwania wirusy, programy szpiegujące lub inne złośliwe oprogramowanie. Microsoft Antimalware udostępnia można skonfigurować alerty, gdy wiadomo, że złośliwego lub niechcianego oprogramowania próbuje się zainstalować lub uruchomić w systemie Azure.

[Kopia zapasowa Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) jest skalowalna, która chroni dane aplikacji z zero inwestycji kapitału i minimalne kosztów operacyjnych. Błędy aplikacji mogą powodować uszkodzenia danych, a błędy użytkowników — usterki aplikacji. Kopia zapasowa Azure maszynach wirtualnych z systemem Windows i Linux są chronione.

[Usługa Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) pomaga organizowania replikacji, trybu failover i odzyskiwania obciążeń i aplikacji, tak aby były dostępne z lokalizacji dodatkowej jeśli spadnie do lokalizacji głównej.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist-protect"></a>Zapewnianie zgodności: usługi powodu starannością listy kontrolnej w chmurze (Ochrona)

Microsoft opracowała [z chmury usługi z powodu starannością listy kontrolnej](https://aka.ms/cloudchecklist.download) może pomóc organizacji korzystają z powodu starannością zgodnie z ich należy wziąć pod uwagę przeniesienie do chmury. Zapewnia to struktura dla organizacji, rozmiar i typ — prywatnej firmy i organizacje sektora publicznego, w tym dla instytucji rządowych na wszystkich poziomach oraz organizacjom — Aby zidentyfikować ich wydajności, usługi, zarządzanie danymi i ładu cele i wymagania. Pozwala na porównanie oferowanych przez różnych dostawców usługi w chmurze ostatecznie stanowiący podstawę umowy usługi chmury.

Lista kontrolna zawiera platforma, która wyrównuje klauzuli przez klauzulę o nowy standard międzynarodowych umów usługi chmury, ISO/IEC 19086. Ten standard udostępnia zestaw połączonych uwagi dla organizacji ułatwić im podejmowania decyzji o przyjęciu chmury, a następnie utwórz podstawą typowe porównanie ofert usług w chmurze.

Lista kontrolna zamienia dokładnie sprawdzonych i doświadczonych przeniesienia do chmury, podając wskazówki strukturalnych i podejście spójne i powtarzalnej Wybieranie dostawcy usług w chmurze.

Wdrożenia chmury nie jest już po prostu decyzji technologii. Ponieważ lista kontrolna wymagania touch na wszystkie aspekty organizacji, udostępniają zwołać wszystkich kluczy wewnętrzny-podejmujące — CIO i CISO, jak również prawnych, ryzyka specjalistom ds. zarządzania, nabywania i zgodności. Powoduje to zwiększenie wydajności proces podejmowania decyzji i podstaw decyzji w rozsądkiem dźwięku, co zmniejsza prawdopodobieństwo nieprzewidzianego roadblocks do przyjęcia.

Ponadto lista kontrolna:

- Udostępnia tematy dyskusji klucza dla decydentów na początku procesu wdrażania chmury.

- Obsługuje dokładnego firm dyskusji związanych z przepisami i cele w organizacji prywatności, dane osobowe (dane osobowe) i bezpieczeństwo danych.

- Pomaga organizacjom Zidentyfikuj potencjalne problemy, które mogą wpłynąć na projekt w chmurze.

- Zapewnia spójny zestaw pytań, samych warunków, definicje, metryki i materiałów dla każdego dostawcy, aby uprościć proces porównanie ofert od dostawcy usług w innej chmurze.

## <a name="azure-infrastructure-and-application-security-validation-detect"></a>Azure walidacji infrastruktury i aplikacji (wykrywa)

[Azure bezpieczeństwa operacyjnego](https://docs.microsoft.com/azure/security/azure-operational-security) odwołuje się do usług, formanty i funkcje dostępne dla użytkowników do ochrony danych, aplikacji i innych zasobów na platformie Microsoft Azure.

![Sprawdzanie poprawności zabezpieczeń (wykrywa)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig7.png)

Operacyjne zabezpieczeń platformy Azure w oparciu o strukturę, która zawiera wiedzę za pośrednictwem różnych funkcji, które są unikatowe dla firmy Microsoft, w tym Microsoft Security Development Lifecycle (SDL), program Microsoft Security odpowiedzi Centrum i głębokie pogłębianie wiedzy na temat zagrożeń bezpieczeństwa.

### <a name="microsoft-operations-management-suiteoms"></a>Microsoft operations management suite(OMS)

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) rozwiązanie do zarządzania IT dla chmur hybrydowych. Użyte bez parametrów lub rozszerzyć Twoje istniejące wdrożenie programu System Center, OMS umożliwia maksymalną elastyczność i kontroli do zarządzania infrastruktury w chmurze.

![Microsoft operations management suite(OMS)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig8.png)

Dzięki OMS mogą zarządzać wystąpienie w żadną chmurą, tym lokalnymi, Azure AWS, Windows Server, Linux, VMware i OpenStack, na niższe koszty niż konkurencyjnych rozwiązania. Utworzony na świecie pierwszy chmury, OMS oferuje nowe podejście do zarządzania oznacza to najszybsze i najbardziej ekonomiczny sposób spełniają wyzwania biznesowe i uwzględnić nowe obciążenia, aplikacji i środowisk chmury przedsiębiorstwa.

### <a name="log-analytics"></a>Log Analytics

Usługa [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) umożliwia monitorowanie pakietu OMS przez zbieranie danych z zarządzanych zasobów w centralnym repozytorium. Te dane mogą obejmować zdarzenia, dane wydajności i niestandardowe dane dostarczane przez interfejs API. Zebrane dane są dostępne na potrzeby alertów, analizy i eksportu.

![Log Analytics](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig9.png)

Ta metoda umożliwia konsolidowanie danych z różnych źródeł, więc można połączyć dane z usługami Azure z istniejącą lokalnego środowiska. Ponadto wprowadza wyraźny podział między zbieraniem danych a akcjami wykonanymi na tych danych, tak aby wszystkie akcje były dostępne dla wszystkich typów danych.

### <a name="azure-security-center"></a>Azure Security Center

Usługa [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) ułatwia zapobieganie zagrożeniom, ich wykrywanie i reagowanie na nie, a przy tym zapewnia lepszy wgląd i większą kontrolę w zakresie bezpieczeństwa zasobów na platformie Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań z zakresu zabezpieczeń.

Centrum zabezpieczeń analizuje stan zabezpieczeń zasobów platformy Azure w celu identyfikowania potencjalnych luk w zabezpieczeniach. Lista zaleceń prowadzi użytkownika przez proces konfigurowania wymaganych elementów sterujących.

Przykłady obejmują:

- Inicjowanie ochrony przed złośliwym oprogramowaniem w celu identyfikacji i usuwania złośliwego oprogramowania

- Konfigurowanie grup zabezpieczeń sieci i reguł sterujących ruchem do maszyn wirtualnych

- Inicjowanie zapór aplikacji sieci web pomagających chronić przed atakami na aplikacje sieci web użytkownika

- Wdrażanie brakujących aktualizacji systemu

- Modyfikowanie konfiguracji systemu operacyjnego, które odbiegają od zalecanych standardów

Centrum zabezpieczeń automatycznie gromadzi, analizuje i integruje dane dzienników z zasobów platformy Azure, sieci i rozwiązań partnerskich, takich jak programy chroniące przed złośliwym oprogramowaniem i zapory. Po wykryciu zagrożenia tworzony jest alert zabezpieczeń. Przykłady obejmują wykrywanie:

- Złamany maszyny wirtualnej komunikowania się ze znanymi złośliwymi adresami IP

- Zaawansowanego złośliwego oprogramowania wykrytego za pomocą funkcji raportowania błędów systemu Windows

- Ataków siłowych wobec maszyn wirtualnych

- Alerty zabezpieczeń ze zintegrowanych programów chroniących przed złośliwym oprogramowaniem i zapór

### <a name="azure-monitor"></a>Monitor systemu Azure

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) zawiera wskaźniki do informacje dotyczące określonych typów zasobów. Zapewnia ona wizualizacji, zapytania, routingu, alerty, automatyczne skalowanie i automatyzacji na dane zarówno z infrastrukturą systemu Azure (dziennik) i każdego pojedynczego zasobu platformy Azure (dzienników diagnostycznych).

Aplikacje w chmurze są złożonych z wielu części ruchu. Monitorowanie zawiera danych, aby upewnić się, że aplikacja pozostaje w górę i działa w dobrej kondycji. Pomaga również umożliwia stave potencjalne problemy i rozwiązywanie problemów w przeszłości te.

![Azure Monitor](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig10.png) Ponadto dane monitorowania można użyć w celu uzyskania szczegółowych informacji o aplikacji. Wiedzy może pomóc zwiększyć wydajność aplikacji lub utrzymania lub automatyzować czynności, które w przeciwnym razie wymagają ręcznej interwencji.

Inspekcja zabezpieczeń sieci jest niezbędne do wykrycia luk w zabezpieczeniach sieci i zapewniania zgodności z przepisami ładu modelu i zabezpieczeń IT. Widok grupy zabezpieczeń można pobrać skonfigurowanej grupy zabezpieczeń sieci i reguły zabezpieczeń, a także reguły zabezpieczeń skuteczne. Z listą zasady zastosowane należy określić się, że porty są otwarte i ss luki w zabezpieczeniach sieci.

### <a name="network-watcher"></a>Obserwator sieci

[Monitor sieci](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) to regionalnych usługa, która umożliwia monitorowanie i diagnozowanie warunki na poziomie sieci w, do i z platformy Azure. Diagnostyka sieci i narzędzi wizualizacji dostępnych z obserwatora sieciowego pomagają zrozumieć, diagnozowanie i Uzyskaj wgląd do sieci na platformie Azure. Ta usługa obejmuje przechwytywania pakietów, następnego przeskoku, przepływ IP Sprawdź widok grupy zabezpieczeń, dzienniki przepływu NSG. Scenariusz poziomu monitorowania udostępnia widok pełnego zasobów sieciowych, w przeciwieństwie do monitorowania zasobów poszczególnych sieci.

### <a name="storage-analytics"></a>Analityka magazynu

[Analityka magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) może przechowywać metryki, które obejmują statystyk i pojemności dane zagregowane transakcji dotyczące żądań do usługi magazynu. Transakcje są raportowane zarówno na poziomie operacji interfejsu API, a także na poziomie usługi magazynu, a pojemność jest zgłaszany na poziomie usługi magazynu. Dane metryk można analizować wykorzystanie usługi magazynu, diagnozowanie problemów z żądań wysyłanych z usługą magazynu i poprawić wydajność aplikacji, które używają usługi.

### <a name="application-insights"></a>Application Insights

[Usługa Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) jest rozszerzalną usługę zarządzania wydajności aplikacji (APM) dla deweloperów sieci web na wielu platformach. Użyj tej usługi do monitorowania aplikacji sieci Web na żywo. Usługa automatycznie wykryje nieprawidłowości w zakresie wydajności. Zawiera on zaawansowane analizy narzędzia ułatwiające diagnozowanie problemów i zrozumieć, co zrobić użytkownicy, z aplikacją. Usługa ta pomaga w ciągłym udoskonalaniu wydajności i użyteczności tworzonych rozwiązań. Działa w przypadku aplikacji na różnych platformach, w tym .NET, Node.js i J2EE, hostowanych lokalnie lub w chmurze. Integruje się z procesu opracowywania oprogramowania, a ma punkty połączenia do różnych narzędzi programistycznych.

Monitoruje ona:

- **Liczby żądań, czasy reakcji i współczynniki błędów** — dowiedz się, które strony są najbardziej popularne, o jakiej porze dnia i gdzie są Twoi użytkownicy. Zobacz, które strony działają najlepiej. Jeśli Twoje czasy odpowiedzi i częstotliwości awarii są duże, gdy jest więcej żądań, być może masz problem z zasobami.

- **Współczynniki zależności, czasy reakcji i współczynniki błędów** — dowiedz się, czy usługi zewnętrzne nie spowalniają pracy.

- **Wyjątki** — analizy zagregowanych danych statystycznych, lub wybierz określone wystąpienia i przejść do szczegółów w powiązanych żądań i ślad stosu. Są zgłaszane zarówno wyjątki serwera, jak i przeglądarki.

- **Wydajność ładowania i wyświetleń stron** — zgłoszona przez przeglądarki użytkowników.

- **Wywołania AJAX ze stron sieci web** -szybkość, czas reakcji i awariami.

- **Liczba użytkowników i sesji.**

- **Liczniki wydajności** z serwerów systemu Windows lub Linux, takie jak użycie procesora CPU, pamięci i sieci.

- **Diagnostyka hosta** z platformy Docker lub Azure.

- **Diagnostyczne dzienniki śledzenia** z Twojej aplikacji — dzięki temu możesz skorelować zdarzenia śledzenia z żądaniami.

- **Niestandardowe zdarzenia i metryki** zapisu samodzielnie w kodzie serwera lub klienta, do śledzenia zdarzeń biznesowych, takich jak towarów sprzedanych, lub gry won.

Infrastruktura aplikacji zwykle obejmuje wiele składników — może to być maszyna wirtualna, konto magazynu i sieć wirtualna albo aplikacja sieci Web, baza danych, serwer bazy danych i usługi zewnętrzne. Te składniki nie są widoczne jako osobne jednostki, tylko jako powiązane i zależne od siebie nawzajem części jednej całości. Dlatego najlepiej wdrażać i monitorować je oraz zarządzać nimi grupowo. [Usługa Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) umożliwia pracę z zasobami w rozwiązaniu jako grupa.

Wszystkie zasoby danego rozwiązania można wdrożyć, zaktualizować lub usunąć w ramach jednej skoordynowanej operacji. Wdrażanie wykonuje się przy użyciu szablonu, którego można następnie używać w różnych środowiskach (testowanie, etap przejściowy i produkcja). Usługa Resource Manager zapewnia funkcje zabezpieczeń, inspekcji i znakowania ułatwiające zarządzanie zasobami po wdrożeniu.

**Korzyści wynikające ze stosowania usługi Resource Manager**

Usługa Resource Manager zapewnia kilka korzyści:

- Możliwość grupowego wdrożenia i monitorowania wszystkich zasobów w ramach rozwiązania oraz zarządzania nimi (zamiast obsługiwania zasobów pojedynczo).

- Możliwość wielokrotnego wdrażania rozwiązania w całym cyklu programistycznym z gwarancją spójnego stanu zasobów po każdym wdrożeniu.

- Możliwość zarządzania infrastrukturą przy użyciu szablonów deklaratywnych zamiast skryptów.

- Możliwość definiowania zależności między zasobami, aby wdrażać je w odpowiedniej kolejności.

- Możliwość stosowania kontroli dostępu do wszystkich usług w grupie zasobów dzięki natywnej integracji funkcji kontroli dostępu na podstawie ról z platformą zarządzania.

- Możliwość dodawania tagów do zasobów w celu logicznego uporządkowania wszystkich zasobów w ramach subskrypcji.

- Możliwość wyjaśniania rozliczeń w organizacji przez wyświetlanie kosztów dla grupy zasobów korzystających z tego samego tagu.

> [!Note]
> Usługa Resource Manager udostępnia nową metodę wdrażania rozwiązań i zarządzania nimi. Jeśli używasz wcześniejszy model wdrażania i chcesz, aby dowiedzieć się więcej o zmianach, zobacz [wdrożenia Understanding Resource Manager oraz wdrażania klasycznego](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o zabezpieczeń, odczytując niektóre nasze tematy szczegółowe zabezpieczeń:

- [Inspekcja i rejestrowanie](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [Wzrost cyberprzestępczości](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [Projektowanie i bezpieczeństwa operacyjnego](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [Szyfrowanie](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [Zarządzanie tożsamościami i dostępem](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [Bezpieczeństwo sieci](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [Zarządzanie zagrożeniami](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)
