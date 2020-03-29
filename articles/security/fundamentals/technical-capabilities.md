---
title: Funkcje techniczne zabezpieczeń na platformie Azure — Microsoft Azure
description: Wprowadzenie do usług zabezpieczeń na platformie Azure, które pomagają chronić dane, zasoby i aplikacje w chmurze.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2019
ms.author: TomSh
ms.openlocfilehash: 61afad1d9994fd703bd8df047d1861baddeae997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845345"
---
# <a name="azure-security-technical-capabilities"></a>Możliwości techniczne zabezpieczeń platformy Azure
Ten artykuł zawiera wprowadzenie do usług zabezpieczeń na platformie Azure, które pomagają chronić dane, zasoby i aplikacje w chmurze i spełniać potrzeby firmy w zakresie zabezpieczeń.

## <a name="azure-platform"></a>Platforma Azure

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) to platforma chmurowa składająca się z usług infrastrukturalnych i aplikacji, ze zintegrowanymi usługami danych i zaawansowaną analizą oraz narzędziami i usługami deweloperskimi, hostowanymi w centrach danych chmury publicznej firmy Microsoft. Klienci korzystają z platformy Azure w wielu różnych pojemnościach i scenariuszach, od podstawowych obliczeń, sieci i pamięci masowej, przez usługi mobilne i aplikacje internetowe, po pełne scenariusze chmury, takie jak Internet rzeczy, i mogą być używane z technologiami open source i wdrażane jako chmura hybrydowa lub hostowane w centrum danych klienta. Platforma Azure udostępnia technologię chmury jako elementy składowe, które pomagają firmom oszczędzać koszty, szybko wprowadzać innowacje i proaktywnie zarządzać systemami. Podczas tworzenia lub migracji zasobów IT do dostawcy chmury polegasz na zdolnościach tej organizacji do ochrony aplikacji i danych za pomocą usług i formantów, które zapewniają w celu zarządzania bezpieczeństwem zasobów chmurowych.

Microsoft Azure jest jedynym dostawcą przetwarzania w chmurze, który oferuje bezpieczną, spójną platformę aplikacji i infrastrukturę jako usługę dla zespołów do pracy w ramach różnych umiejętności w chmurze i poziomów złożoności projektu, ze zintegrowanymi usługami danych i analizy, które odkrywają dane z danych, gdziekolwiek istnieją, zarówno na platformach Microsoft, jak i innych firm, otwarte struktury i narzędzia, zapewniające możliwość integracji chmury z lokalnymi, a także wdrażanie usług w chmurze platformy Azure w ramach lokalnych centrów danych. W ramach zaufanej chmury firmy Microsoft klienci polegają na platformie Azure pod względem bezpieczeństwa, niezawodności, zgodności, prywatności oraz rozległej sieci osób, partnerów i procesów wspierających organizacje w chmurze.

Dzięki platformie Microsoft Azure możesz:

- Przyspiesz innowacje dzięki chmurze.

- Zasąduj decyzje biznesowe & aplikacje za pomocą szczegółowych informacji.

- Twórz swobodnie i wdrażaj w dowolnym miejscu.

- Chroń swoją działalność.

## <a name="security-technical-capabilities-to-fulfil-your-responsibility"></a>Zabezpieczenia techniczne, aby wypełnić swoją odpowiedzialność

Platforma Microsoft Azure oferuje usługi, które pomogą Ci spełnić twoje potrzeby w zakresie bezpieczeństwa, prywatności i zgodności. Poniższy obraz pomaga wyjaśnić różne usługi platformy Azure dostępne do tworzenia bezpiecznej i zgodnej infrastruktury aplikacji w oparciu o standardy branżowe.

![Dostępne funkcje techniczne zabezpieczeń - Duży obraz](./media/technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access"></a>Zarządzanie tożsamością i dostępem użytkowników oraz kontrolowanie ich

Platforma Azure pomaga chronić informacje biznesowe i osobiste, umożliwiając zarządzanie tożsamościami użytkowników i poświadczeniami oraz kontrolowanie dostępu.

### <a name="azure-active-directory"></a>Usługa Azure Active Directory

Rozwiązania firmy Microsoft do zarządzania tożsamościami i dostępem pomagają działowi IT chronić dostęp do aplikacji i zasobów w firmowym centrum danych i w chmurze, umożliwiając dodatkowe poziomy sprawdzania poprawności, takie jak uwierzytelnianie wieloskładnikowe i dostęp warunkowy Zasady działalności. Monitorowanie podejrzanej aktywności przy użyciu zaawansowanych raportów zabezpieczeń, inspekcji i alertów umożliwia rozwiązywanie potencjalnych problemów z zabezpieczeniami. [Usługa Azure Active Directory Premium](../../active-directory/active-directory-whatis.md) zapewnia logowanie jednokrotne tysięcy aplikacji w chmurze i dostęp do aplikacji sieci Web uruchamianych lokalnie.

Korzyści z zabezpieczeń usługi Azure Active Directory (Azure AD) obejmują możliwość:

- Utwórz jedną tożsamość dla każdego użytkownika w całym przedsiębiorstwie hybrydowym i zarządzaj nią, synchronizując użytkowników, grupy i urządzenia.

- Zapewnij dostęp do logowania jednokrotnego do aplikacji, w tym tysięcy wstępnie zintegrowanych aplikacji SaaS.

- Zabezpieczyć dostęp do aplikacji przez wymuszenie uwierzytelniania wieloskładnikowego na podstawie zasad zarówno dla aplikacji lokalnych, jak i dla aplikacji w chmurze.

- Aprowizuj bezpieczny dostęp zdalny do lokalnych aplikacji sieci web za pośrednictwem serwera proxy aplikacji usługi Azure AD.

Portal [usługi Azure Active Directory](https://aad.portal.azure.com/) jest dostępny w ramach witryny Azure portal. Na tym pulpicie nawigacyjnym można uzyskać przegląd stanu organizacji i łatwo zarządzać katalogiem, użytkownikami lub dostępem do aplikacji.

![Usługa Azure Active Directory](./media/technical-capabilities/azure-security-technical-capabilities-fig2.png)

Poniżej przedstawiono podstawowe funkcje zarządzania tożsamościami platformy Azure:

- Logowanie jednokrotne

- Uwierzytelnianie wieloskładnikowe

- Monitorowanie zabezpieczeń, alerty i raporty oparte na uczeniu maszynowym

- Zarządzanie tożsamością i dostępem konsumentów

- Rejestracja urządzenia

- Privileged Identity Management

- Ochrona tożsamości

#### <a name="single-sign-on"></a>Logowanie jednokrotne

[Logowanie jednokrotne (Logowanie jednokrotne)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) oznacza możliwość uzyskania dostępu do wszystkich aplikacji i zasobów potrzebnych do prowadzenia działalności gospodarczej, logując się tylko raz przy użyciu jednego konta użytkownika. Po zalogowaniu można uzyskać dostęp do wszystkich potrzebnych aplikacji bez konieczności uwierzytelniania (na przykład wpisywać hasło) po raz drugi.

Wiele organizacji korzysta z aplikacji typu oprogramowanie jako usługa (SaaS), takich jak Office 365, Box i Salesforce, aby zwiększyć produktywność użytkowników końcowych. W przeszłości pracownicy IT musieli indywidualnie tworzyć i aktualizować konta użytkowników w każdej aplikacji SaaS, a użytkownicy musieli zapamiętać hasło dla każdej aplikacji SaaS.

Usługa Azure AD rozszerza lokalną usługę [Active Directory na chmurę,](../../active-directory/manage-apps/what-is-single-sign-on.md)umożliwiając użytkownikom korzystanie z podstawowego konta organizacyjnego w celu nie tylko logowania się do urządzeń i zasobów firmy przyłączonych do domeny, ale także wszystkich aplikacji sieci Web i SaaS potrzebnych do wykonywania zadań.

Użytkownicy nie tylko nie muszą zarządzać wieloma zestawami nazw użytkowników i haseł, ale także mogą być automatycznie aprowizowani lub usuwani z obsługi administracyjnej na podstawie grup organizacyjnych i ich statusu jako pracownika. [Usługa Azure AD wprowadza mechanizmy kontroli nadzoru zabezpieczeń i dostępu,](../../active-directory/active-directory-enterprise-apps-manage-sso.md) które umożliwiają centralne zarządzanie dostępem użytkowników w aplikacjach SaaS.

#### <a name="multi-factor-authentication"></a>Uwierzytelnianie wieloskładnikowe

[Uwierzytelnianie wieloskładnikowe platformy Azure (MFA)](../../active-directory/authentication/multi-factor-authentication.md) to metoda uwierzytelniania, która wymaga użycia więcej niż jednej metody weryfikacji i dodaje krytyczną drugą warstwę zabezpieczeń do logowania i transakcji użytkowników. [Usługa MFA pomaga chronić](../../active-directory/authentication/concept-mfa-howitworks.md) dostęp do danych i aplikacji, spełniając jednocześnie zapotrzebowanie użytkowników na prosty proces logowania. Zapewnia silne uwierzytelnianie za pomocą wielu opcji weryfikacji — połączenia telefonicznego, wiadomości tekstowej lub powiadomienia lub kodu weryfikacyjnego aplikacji mobilnej oraz tokenów OAuth innych firm.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitorowanie zabezpieczeń, alerty i raporty oparte na uczeniu maszynowym

Monitorowanie zabezpieczeń i alerty oraz raporty oparte na uczeniu maszynowym, które identyfikują niespójne wzorce dostępu, mogą pomóc w ochronie firmy. Raporty dostępu i użycia usługi Azure Active Directory umożliwiają uzyskanie wglądu w integralność i bezpieczeństwo katalogu organizacji. Dzięki tym informacjom administrator katalogu może lepiej określić, gdzie mogą leżeć możliwe zagrożenia bezpieczeństwa, aby mogli odpowiednio zaplanować ograniczenie tych zagrożeń.

W witrynie Azure portal lub za pośrednictwem [portalu usługi Azure Active Directory](https://aad.portal.azure.com/) [raporty](../../active-directory/active-directory-reporting-azure-portal.md) są podzielone na następujące kategorie:

- Raporty anomalii — zawierają zdarzenia logowania, które okazały się nietypowe. Naszym celem jest uświadomienie Ci takiej aktywności i umożliwienie Ci decydowania o tym, czy zdarzenie jest podejrzane.

- Zintegrowane raporty aplikacji — zapewniają wgląd w sposób, w jaki aplikacje w chmurze są używane w organizacji. Usługa Azure Active Directory oferuje integrację z tysiącami aplikacji w chmurze.

- Raporty o błędach — wskazują błędy, które mogą wystąpić podczas inicjowania obsługi administracyjnej kont do aplikacji zewnętrznych.

- Raporty specyficzne dla użytkownika — wyświetlanie urządzenia i logowania danych aktywności dla określonego użytkownika.

- Dzienniki aktywności — zawierają rekord wszystkich skontrolowanych zdarzeń w ciągu ostatnich 24 godzin, ostatnich 7 dni lub ostatnich 30 dni oraz zmiany aktywności grupy oraz resetowanie hasła i rejestrowanie aktywności.

#### <a name="consumer-identity-and-access-management"></a>Zarządzanie tożsamością i dostępem konsumentów

[Usługa Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) to wysoce dostępna, globalna usługa zarządzania tożsamościami dla aplikacji przeznaczonych dla konsumentów, która skaluje się do setek milionów tożsamości. Można ją łatwo integrować z platformami mobilnymi i platformami sieci Web. Konsumenci mogą logować się do wszystkich aplikacji przy użyciu w pełni dostosowywanego procesu, używając istniejących kont sieci społecznościowych lub tworząc nowe poświadczenia.

W przeszłości deweloperzy aplikacji, którzy chcieli [zarejestrować się i zalogować konsumentów](../../active-directory-b2c/overview.md) do swoich aplikacji, napisaliby własny kod. Musieli korzystać z lokalnych baz danych lub systemów do przechowywania nazw i haseł użytkowników. Usługa Azure Active Directory B2C oferuje twojej organizacji lepszy sposób integracji zarządzania tożsamościami konsumentami z aplikacjami za pomocą bezpiecznej platformy opartej na standardach i dużego zestawu rozszerzalnych zasad.

Korzystając z usługi Azure Active Directory B2C, konsumenci mogą zarejestrować się w aplikacjach przy użyciu istniejących kont społecznościowych (Facebook, Google, Amazon, LinkedIn) lub tworząc nowe poświadczenia (adres e-mail i hasło lub nazwę użytkownika i hasło).

#### <a name="device-registration"></a>Rejestracja urządzenia

[Rejestracja urządzeń usługi Azure AD](../../active-directory/devices/overview.md) jest podstawą scenariuszy dostępu [warunkowego](../../active-directory/devices/overview.md) opartego na urządzeniach. Po zarejestrowaniu urządzenia rejestracja urządzenia usługi Azure AD udostępnia urządzeniu tożsamość, która jest używana do uwierzytelniania urządzenia, gdy użytkownik się zaloguje. Uwierzytelnione urządzenie i atrybuty urządzenia mogą być następnie używane do wymuszania zasad dostępu warunkowego dla aplikacji hostowanych w chmurze i lokalnie.

W połączeniu z rozwiązaniem [do zarządzania urządzeniami przenośnymi (MDM),](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) takim jak Intune, atrybuty urządzenia w usłudze Azure Active Directory są aktualizowane o dodatkowe informacje o urządzeniu. Dzięki temu można utworzyć reguły dostępu warunkowego, które wymuszają dostęp z urządzeń, aby spełnić standardy zabezpieczeń i zgodności.

#### <a name="privileged-identity-management"></a>Privileged Identity Management

[Zarządzanie tożsamościami uprzywilejowanymi usługi Azure Active Directory (AD)](../../active-directory/privileged-identity-management/pim-configure.md) umożliwia zarządzanie uprzywilejowanymi tożsamościami i dostępem do zasobów w usłudze Azure AD, a także innych usług online firmy Microsoft, takich jak Office 365 lub Microsoft Intune.

Czasami użytkownicy muszą wykonywać uprzywilejowane operacje w zasobach platformy Azure lub Office 365 lub innych aplikacji SaaS. Często oznacza to, że organizacje muszą przyznać im stały uprzywilejowany dostęp w usłudze Azure AD. Jest to rosnące zagrożenie bezpieczeństwa dla zasobów hostowanych w chmurze, ponieważ organizacje nie mogą wystarczająco monitorować, co ci użytkownicy robią z ich uprawnieniami administratora. Ponadto jeśli konto użytkownika z uprzywilejowanym dostępem zostanie naruszone, to jedno naruszenie może mieć wpływ na ich ogólne zabezpieczenia w chmurze. Zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD pomaga rozwiązać to ryzyko.

Zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD umożliwia:

- Zobacz, którzy użytkownicy są administratorami usługi Azure AD

- Włączanie dostępu administracyjnego na żądanie i "just in time" do usług Online Services firmy Microsoft, takich jak Office 365 i Intune

- Uzyskiwanie raportów dotyczących historii dostępu administratora i zmian w przypisaniach administratorów

- Uzyskiwanie alertów o dostępie do roli uprzywilejowanej

#### <a name="identity-protection"></a>Ochrona tożsamości

[Usługa Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) to usługa zabezpieczeń, która zapewnia skonsolidowany widok wykrywania ryzyka i potencjalnych luk w zabezpieczeniach mających wpływ na tożsamości organizacji. Usługa Identity Protection korzysta z istniejących funkcji wykrywania anomalii usługi Azure Active Directory (dostępnych za pośrednictwem raportów o nietypowych działaniach usługi Azure AD) i wprowadza nowe typy wykrywania ryzyka, które mogą wykrywać anomalie w czasie rzeczywistym.

## <a name="secure-resource-access"></a>Bezpieczny dostęp do zasobów

Kontrola dostępu na platformie Azure rozpoczyna się od punktu rozliczeniowego. Właścicielem konta platformy Azure, do które uzyskuje się dostęp, odwiedzając [Centrum kont platformy Azure,](https://account.windowsazure.com/subscriptions)jest administratorem konta (AA). Subskrypcje są kontenerem do rozliczeń, ale działają również jako granica zabezpieczeń: każda subskrypcja ma administratora usługi (SA), który może dodawać, usuwać i modyfikować zasoby platformy Azure w tej subskrypcji przy użyciu witryny Azure portal. Domyślnym sa nowej subskrypcji jest AA, ale usługa AA można zmienić sa w Centrum kont platformy Azure.

![Bezpieczny dostęp do zasobów na platformie Azure](./media/technical-capabilities/azure-security-technical-capabilities-fig3.png)

Subskrypcje mają również skojarzenia z katalogiem. Katalog definiuje zestaw użytkowników. Mogą to być użytkownicy z pracy lub szkoły, która utworzyła katalog, lub mogą być użytkownikami zewnętrznymi (czyli kontami Microsoft). Subskrypcje są dostępne dla podzbiór tych użytkowników katalogu, którzy zostali przypisani jako administrator usługi (SA) lub współadministrator (CA); jedynym wyjątkiem jest to, że ze względu na starsze przyczyny konta Microsoft (dawniej Identyfikator Windows Live) mogą być przypisane jako SA lub CA bez obecności w katalogu.

Firmy zorientowane na bezpieczeństwo powinny skupić się na zapewnieniu pracownikom dokładnych uprawnień, których potrzebują. Zbyt wiele uprawnień może udostępnić konto atakującym. Zbyt mało uprawnień oznacza, że pracownicy nie mogą wykonywać swojej pracy wydajnie. [Kontrola dostępu oparta na rolach platformy Azure (RBAC)](../../role-based-access-control/overview.md) pomaga rozwiązać ten problem, oferując szczegółowe zarządzanie dostępem dla platformy Azure.

![Bezpieczny dostęp do zasobów](./media/technical-capabilities/azure-security-technical-capabilities-fig4.png)

Przy użyciu kontroli dostępu opartej na rolach można przeprowadzić segregowanie zadań w ramach zespołu i nadać użytkownikom tylko takie uprawnienia dostępu, które są im niezbędne do wykonywania zadań. Zamiast udzielać wszystkim nieograniczonych uprawnień w subskrypcji platformy Azure lub zasobów, można zezwolić tylko na niektóre akcje. Na przykład użyj RBAC, aby jeden pracownik zarządzał maszynami wirtualnymi w ramach subskrypcji, podczas gdy inny może zarządzać bazami danych SQL w ramach tej samej subskrypcji.

![Bezpieczny dostęp do zasobów na platformie Azure (RBAC)](./media/technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="data-security-and-encryption"></a>Bezpieczeństwo i szyfrowanie danych

Jednym z kluczy do ochrony danych w chmurze jest księgowanie możliwych stanów, w których mogą wystąpić dane i jakie formanty są dostępne dla tego stanu. W przypadku zabezpieczeń danych platformy Azure i najlepszych rozwiązań dotyczących szyfrowania zalecenia dotyczące następujących stanów danych.

- W spoczynku: Obejmuje to wszystkie obiekty magazynu informacji, kontenery i typy, które istnieją statycznie na nośnikach fizycznych, czy to na dysku magnetycznym, czy optycznym.

- Przesyłanie: Gdy dane są przesyłane między składnikami, lokalizacjami lub programami, takimi jak za pośrednictwem sieci, przez magistralę usług (z lokalnego do chmury i odwrotnie, w tym połączenia hybrydowe, takie jak Usługa ExpressRoute), lub podczas procesu wprowadzania/przesyłania danych, uważa się, że są w ruchu.

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

Aby uzyskać szyfrowanie w spoczynku, wykonaj każdy z następujących czynności:

Obsługa co najmniej jednego z zalecanych modeli szyfrowania wyszczególnionych w poniższej tabeli do szyfrowania danych.

| Modele szyfrowania |  |  |  |
| ----------------  | ----------------- | ----------------- | --------------- |
| Szyfrowanie serwera | Szyfrowanie serwera | Szyfrowanie serwera | Szyfrowanie klienta
| Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych usługi | Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault | Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez klienta lokalnego |
| • Dostawcy zasobów platformy Azure wykonują operacje szyfrowania i odszyfrowywania <br> • Firma Microsoft zarządza kluczami <br>• Pełna funkcjonalność chmury | • Dostawcy zasobów platformy Azure wykonują operacje szyfrowania i odszyfrowywania<br>• Klient steruje kluczami za pośrednictwem usługi Azure Key Vault<br>• Pełna funkcjonalność chmury | • Dostawcy zasobów platformy Azure wykonują operacje szyfrowania i odszyfrowywania <br>• Klient steruje kluczami lokalnymi <br> • Pełna funkcjonalność chmury| • Usługi platformy Azure nie widzą odszyfrowanych danych <br>• Klienci przechowują klucze lokalnie (lub w innych bezpiecznych sklepach). Klucze nie są dostępne dla usług platformy Azure <br>• Zmniejszona funkcjonalność chmury|

### <a name="enabling-encryption-at-rest"></a>Włączanie szyfrowania w spoczynku

**Identyfikowanie wszystkich lokalizacji, w których przechowujesz dane**

Celem szyfrowania w spoczynku jest szyfrowanie wszystkich danych. Eliminuje to możliwość utraty ważnych danych lub wszystkich utrwalonych lokalizacji. Wylicz wszystkie dane przechowywane przez aplikację.

> [!Note]
> Nie tylko "dane aplikacji" lub "DANE UMOŻLIWIAJĄCE", ale wszelkie dane związane z aplikacją, w tym metadane konta (mapowania subskrypcji, informacje o umowie, informacje umożliwiające identyfikację użytkownika).

Zastanów się, jakie magazyny są używane do przechowywania danych. Przykład:

- Magazyn zewnętrzny (na przykład SQL Azure, Document DB, HDInsights, Data Lake itp.)

- Magazyn tymczasowy (dowolna lokalna pamięć podręczna zawierająca dane dzierżawy)

- Pamięć podręczna w pamięci (można umieścić w pliku stronicowa).

### <a name="leverage-the-existing-encryption-at-rest-support-in-azure"></a>Skorzystaj z istniejącej obsługi szyfrowania w spoczynku na platformie Azure

Dla każdego używanego sklepu użyj istniejącej obsługi szyfrowania w spoczynku.

- Usługa Azure Storage: zobacz [Szyfrowanie usługi Azure Storage w celu uzyskania danych w spoczynku](../../storage/common/storage-service-encryption.md),

- SQL Azure: Zobacz [przezroczyste szyfrowanie danych (TDE), SQL zawsze szyfrowane](https://msdn.microsoft.com/library/mt163865.aspx)

- Wirtualna & magazyn dysków lokalnych[(szyfrowanie dysków azure)](../azure-security-disk-encryption-overview.md)

W przypadku maszyn wirtualnych i magazynu dysków lokalnych użyj szyfrowania dysków platformy Azure, gdzie jest obsługiwana:

#### <a name="iaas"></a>IaaS

Usługi z maszynami wirtualnymi IaaS (Windows lub Linux) powinny używać [szyfrowania dysków platformy Azure](https://microsoft.sharepoint.com/teams/AzureSecurityCompliance/Security/SitePages/Azure%20Disk%20Encryption.aspx) do szyfrowania woluminów zawierających dane klienta.

#### <a name="paas-v2"></a>PaaS v2

Usługi uruchomione w paaS w wersji 2 przy użyciu sieci szkieletowej usług mogą używać szyfrowania dysku platformy Azure dla zestawu skalowania maszyny wirtualnej [VMSS] do szyfrowania maszyn wirtualnych PaaS w wersji 2.

#### <a name="paas-v1"></a>PaaS v1

Szyfrowanie dysków platformy Azure obecnie nie jest obsługiwane w paas v1. W związku z tym należy użyć szyfrowania na poziomie aplikacji do szyfrowania utrwalonych danych w spoczynku.  Obejmuje to między innymi dane aplikacji, pliki tymczasowe, dzienniki i zrzuty awaryjne.

Większość usług powinna próbować wykorzystać szyfrowanie dostawcy zasobów magazynu. Niektóre usługi muszą wykonywać jawne szyfrowanie, na przykład wszelkie utrwalone materiały kluczy (certyfikaty, klucze główne / główne) muszą być przechowywane w magazynie kluczy.

Jeśli obsługujesz szyfrowanie po stronie usługi za pomocą kluczy zarządzanych przez klienta, klient musi mieć sposób, aby uzyskać klucz do nas. Obsługiwany i zalecany sposób, aby to zrobić, integrując się z usługi Azure Key Vault (AKV). W takim przypadku klienci mogą dodawać klucze i zarządzać nimi w usłudze Azure Key Vault. Klient może dowiedzieć się, jak korzystać z AKV za pośrednictwem [wprowadzenie do usługi Key Vault](https://go.microsoft.com/fwlink/?linkid=521402).

Aby zintegrować się z usługą Azure Key Vault, należy dodać kod, aby zażądać klucza od AKV, gdy jest to potrzebne do odszyfrowania.

- Zobacz [Azure Key Vault — krok po kroku, aby](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step/) uzyskać informacje na temat integracji z AKV.

Jeśli obsługujesz klucze zarządzane przez klienta, musisz podać środowisko użytkownika dla klienta, aby określić, który magazyn kluczy (lub identyfikator URI usługi Key Vault) ma być używany.

Ponieważ szyfrowanie w spoczynku obejmuje szyfrowanie danych hosta, infrastruktury i dzierżawy, utrata kluczy z powodu awarii systemu lub złośliwej aktywności może oznaczać utratę wszystkich zaszyfrowanych danych. Dlatego bardzo ważne jest, aby rozwiązanie Szyfrowanie w spoczynku miało kompleksową historię odzyskiwania po awarii odporną na awarie systemu i złośliwą aktywność.

Usługi implementujące szyfrowanie w spoczynku są zwykle nadal podatne na klucze szyfrowania lub dane pozostawione w stanie niezaszyfrowanym na dysku hosta (na przykład w pliku stronicowa hosta systemu operacyjnego). W związku z tym usługi muszą upewnić się, że wolumin hosta dla swoich usług jest szyfrowany. Aby ułatwić ten zespół obliczeniowy, umożliwił wdrożenie szyfrowania hosta, które używa [funkcji BitLocker](https://technet.microsoft.com/library/dn306081.aspx) NKP i rozszerzeń usługi DCM i agenta do szyfrowania woluminu hosta.

Większość usług są implementowane na standardowych maszyn wirtualnych platformy Azure. Takie usługi powinny automatycznie uzyskać [szyfrowanie hosta,](../azure-security-disk-encryption-overview.md) gdy umożliwia to compute. W przypadku usług uruchomionych w klastrach zarządzanych obliczeń szyfrowanie hosta jest włączane automatycznie po włączeniu systemu Windows Server 2016.

### <a name="encryption-in-transit"></a>Szyfrowanie podczas przesyłania

Ochrona przesyłanych danych powinna być istotną częścią strategii ochrony danych. Ponieważ dane są przemieszczane tam iz powrotem z wielu lokalizacji, ogólne zalecenie jest, że zawsze używasz protokołów SSL/TLS do wymiany danych w różnych lokalizacjach. W niektórych okolicznościach można odizolować cały kanał komunikacji między infrastrukturą lokalną i chmurową przy użyciu wirtualnej sieci prywatnej (VPN).

W przypadku przenoszenia danych między infrastrukturą lokalną a platformą Azure należy wziąć pod uwagę odpowiednie zabezpieczenia, takie jak HTTPS lub VPN.

W przypadku organizacji, które muszą zabezpieczyć dostęp z wielu stacji roboczych zlokalizowanych lokalnie na platformie Azure, użyj [sieci VPN lokacja-lokacja platformy Azure.](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)

W przypadku organizacji, które muszą zabezpieczyć dostęp z jednej stacji roboczej znajdującej się lokalnie na platformie Azure, należy użyć [sieci VPN typu punkt-lokacja](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Większe zestawy danych można przenosić za na dedykowane szybkie łącze WAN, takie jak [ExpressRoute.](https://azure.microsoft.com/services/expressroute/) Jeśli zdecydujesz się korzystać z usługi ExpressRoute, można również zaszyfrować dane na poziomie aplikacji przy użyciu [protokołu SSL/TLS](https://support.microsoft.com/kb/257591) lub innych protokołów w celu dodatkowej ochrony.

Jeśli wchodzisz w interakcję z usługą Azure Storage za pośrednictwem witryny Azure Portal, wszystkie transakcje występują za pośrednictwem protokołu HTTPS. [Interfejs API REST magazynu](https://msdn.microsoft.com/library/azure/dd179355.aspx) za pośrednictwem protokołu HTTPS może być również używany do interakcji z [usługą Azure Storage](https://azure.microsoft.com/services/storage/) i [bazą danych SQL Azure.](https://azure.microsoft.com/services/sql-database/)

Organizacje, które nie chronią danych w tranzycie, są bardziej podatne na [ataki typu man-in-the-middle,](https://technet.microsoft.com/library/gg195821.aspx) [podsłuchiwanie](https://technet.microsoft.com/library/gg195641.aspx)i porwanie sesji. Takie ataki mogą być pierwszym krokiem do uzyskania dostępu do poufnych danych.

Więcej informacji na temat opcji sieci VPN platformy Azure można dowiedzieć się, czytając artykuł [Planowanie i projektowanie bramy sieci VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="enforce-file-level-data-encryption"></a>Wymuszanie szyfrowania danych na poziomie pliku

[Usługa Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) używa zasad szyfrowania, tożsamości i autoryzacji w celu zabezpieczenia plików i poczty e-mail. Usługa Azure RMS działa na wielu urządzeniach — telefonach, tabletach i komputerach, chroniąc je zarówno w organizacji, jak i poza nią. Ta funkcja jest możliwa, ponieważ usługa Azure RMS dodaje poziom ochrony, który pozostaje w przypadku danych, nawet jeśli opuści granice organizacji.

Korzystając z usługi Azure RMS do ochrony plików, używasz kryptografii standardowej w branży z pełną obsługą [FIPS 140-2](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf). Korzystając z usługi Azure RMS w celu ochrony danych, masz pewność, że ochrona pozostaje z plikiem, nawet jeśli jest kopiowany do magazynu, który nie jest pod kontrolą IT, takich jak usługa magazynu w chmurze. To samo dotyczy plików udostępnionych za pośrednictwem poczty e-mail, plik jest chroniony jako załącznik do wiadomości e-mail, z instrukcjami, jak otworzyć chroniony załącznik.
Podczas planowania wdrażania usługi Azure RMS zaleca się następujące:

- Zainstaluj [aplikację do udostępniania programu RMS](https://technet.microsoft.com/library/dn339006.aspx). Ta aplikacja integruje się z aplikacjami pakietu Office, instalując dodatek pakietu Office, dzięki czemu użytkownicy mogą łatwo chronić pliki bezpośrednio.

- Konfigurowanie aplikacji i usług do obsługi usługi Azure RMS

- Twórz [szablony niestandardowe,](https://technet.microsoft.com/library/dn642472.aspx) które odzwierciedlają wymagania biznesowe. Na przykład: szablon dla ściśle tajnych danych, które powinny być stosowane we wszystkich ściśle tajnych powiązanych wiadomości e-mail.

Organizacje, które są słabe w [klasyfikacji danych](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) i ochrony plików mogą być bardziej podatne na wyciek danych. Bez odpowiedniej ochrony plików organizacje nie będą w stanie uzyskać szczegółowych informacji biznesowych, monitorować pod kątem nadużyć i zapobiegać złośliwemu dostępowi do plików.

> [!Note]
> Więcej informacji na temat usługi Azure RMS można dowiedzieć się, czytając artykuł [Wprowadzenie do usługi Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).

## <a name="secure-your-application"></a>Zabezpiecz swoją aplikację
Platforma Azure jest odpowiedzialna za zabezpieczenie infrastruktury i platformy, na których działa aplikacja, należy zabezpieczyć samą aplikację. Innymi słowy należy opracować, wdrożyć i zarządzać kodem aplikacji i zawartości w bezpieczny sposób. Bez tego kod aplikacji lub zawartość nadal mogą być narażone na zagrożenia.

### <a name="web-application-firewall"></a>Zapora aplikacji internetowej
[Zapora aplikacji sieci Web (WAF)](../../application-gateway/waf-overview.md) jest funkcją [bramy aplikacji,](../../application-gateway/overview.md) która zapewnia scentralizowaną ochronę aplikacji internetowych przed typowymi exploitami i lukami w zabezpieczeniach.

Zapora aplikacji internetowej zapewnia ochronę na podstawie reguł z [podstawowych zestawów reguł OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) w wersji 3.0 lub 2.2.9. Aplikacje internetowe coraz częściej stają się obiektami złośliwych ataków wykorzystujących znane luki w zabezpieczeniach. Wśród nich często zdarzają się np. ataki polegające na iniekcji SQL i ataki z użyciem skryptów wykorzystywanych w wielu witrynach. Zapobieganie takim atakom z poziomu kodu aplikacji może być trudne. Może też wymagać rygorystycznego przestrzegania harmonogramu konserwacji, poprawek i monitorowania na poziomie wielu warstw topologii aplikacji. Scentralizowana zapora aplikacji internetowej ułatwia zarządzanie zabezpieczeniami oraz zapewnia lepszą ochronę administratorów aplikacji przed zagrożeniami i intruzami. Zapora aplikacji internetowej może reagować na zagrożenia bezpieczeństwa szybciej — poprzez wdrażanie poprawek zapobiegających wykorzystaniu znanych luk w zabezpieczeniach w centralnej lokalizacji zamiast w poszczególnych aplikacjach internetowych. Istniejące bramy Application Gateway można łatwo przekonwertować na bramę Application Gateway obsługującą zaporę aplikacji internetowej.

Oto niektóre typowe luki w zabezpieczeniach w Internecie, przed którymi chroni zapora aplikacji internetowej:

- Ochrona przed atakami polegającymi na iniekcji SQL

- Ochrona przed atakami z użyciem skryptów wykorzystywanych w obrębie wielu witryn

- Częste ataki w ramach sieci Web polegające na iniekcji poleceń, przemycaniu żądań HTTP, rozdzielaniu odpowiedzi HTTP i zdalnym dołączaniu plików

- Ochrona przed naruszeniami protokołu HTTP

- Ochrona przed nieprawidłowościami protokołu HTTP, takimi jak brakujące powiązania agenta i użytkownika hosta oraz akceptowanie nagłówków

- Zapobieganie atakom z użyciem robotów, przeszukiwarek i skanerów

- Wykrywanie typowych błędnych konfiguracji aplikacji (czyli Apache, IIS itp.)

> [!Note]
> Aby uzyskać bardziej szczegółową listę reguł i ich zabezpieczeń, zobacz następujące [podstawowe zestawy reguł:](../../application-gateway/waf-overview.md)

Platforma Azure udostępnia również kilka łatwych w użyciu funkcji ułatwiające zabezpieczenie ruchu przychodzącego i wychodzącego dla aplikacji. Platforma Azure pomaga również klientom zabezpieczyć kod aplikacji, udostępniając funkcje dostarczone zewnętrznie w celu skanowania aplikacji sieci web w poszukiwaniu luk w zabezpieczeniach.

- [Konfigurowanie uwierzytelniania usługi Azure Active Directory dla aplikacji](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [Zabezpieczanie ruchu w aplikacji przez włączenie zabezpieczeń warstwy transportu (TLS/SSL) — HTTPS](../../app-service/configure-ssl-bindings.md)

  - [Wymuszanie całego ruchu przychodzącego za pośrednictwem połączenia HTTPS](http://microsoftazurewebsitescheatsheet.info/)

  - [Włącz ścisłe zabezpieczenia transportu (HSTS)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [Ograniczanie dostępu do aplikacji według adresu IP klienta](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Ograniczanie dostępu do aplikacji przez zachowanie klienta — częstotliwość żądań i współbieżność](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Skanowanie kodu aplikacji internetowej w poszukiwaniu luk w zabezpieczeniach przy użyciu skanowania zabezpieczeń folii aluminiowej](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [Konfigurowanie wzajemnego uwierzytelniania TLS w celu wymagania certyfikatów klienta do łączenia się z aplikacją sieci Web](../../app-service/app-service-web-configure-tls-mutual-auth.md)

- [Konfigurowanie certyfikatu klienta do bezpiecznego łączenia się z zasobami zewnętrznymi](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Usuwanie standardowych nagłówków serwera w celu uniknięcia pobierania odcisków palców od aplikacji przez narzędzia](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Bezpieczne łączenie aplikacji z zasobami w sieci prywatnej za pomocą sieci VPN typu point-to-site](../../app-service/web-sites-integrate-with-vnet.md)

- [Bezpieczne łączenie aplikacji z zasobami w sieci prywatnej przy użyciu połączeń hybrydowych](../../app-service/app-service-hybrid-connections.md)

Usługa Azure App Service używa tego samego rozwiązania ochrony przed złośliwym oprogramowaniem, używanego przez usługi w chmurze i maszyny wirtualne platformy Azure. Aby dowiedzieć się więcej na ten temat, zapoznaj się z naszą [dokumentacją ochrony przed złośliwym oprogramowaniem.](antimalware.md)

## <a name="secure-your-network"></a>Zabezpiecz swoją sieć
Platforma Microsoft Azure zawiera niezawodną infrastrukturę sieciową obsługującą wymagania dotyczące łączności aplikacji i usług. Łączność sieciowa jest możliwa między zasobami znajdującymi się na platformie Azure, między zasobami lokalnymi i hostowanymi na platformie Azure oraz z Internetem i platformą Azure.

[Infrastruktura sieci platformy Azure](../../virtual-machines/windows/infrastructure-example.md) umożliwia bezpieczne łączenie zasobów platformy Azure ze sobą za pomocą [sieci wirtualnych .](../../virtual-network/virtual-networks-overview.md) Sieć wirtualna jest reprezentacją własnej sieci w chmurze. Sieć wirtualna jest logiczną izolacją sieci w chmurze platformy Azure dedykowaną do subskrypcji. Sieci wirtualne można połączyć z sieciami lokalnymi.

![Zabezpiecz sieć (ochrona)](./media/technical-capabilities/azure-security-technical-capabilities-fig6.png)

Jeśli potrzebna jest podstawowa kontrola dostępu na poziomie sieci (oparta na adresie IP i protokołach TCP lub UDP), możesz użyć [sieciowych grup zabezpieczeń](../../virtual-network/virtual-network-vnet-plan-design-arm.md). Network Security Group (NSG) jest podstawową zaporą filtrowania pakietów stanowych i umożliwia kontrolowanie dostępu na podstawie [5-krotki.](https://www.techopedia.com/definition/28190/5-tuple)

Sieci platformy Azure obsługuje możliwość dostosowania zachowania routingu dla ruchu sieciowego w sieci wirtualnej platformy Azure. Można to zrobić, konfigurując [trasy zdefiniowane przez użytkownika na](../../virtual-network/virtual-networks-udr-overview.md) platformie Azure.

[Wymuszone tunelowanie](https://www.petri.com/azure-forced-tunneling) to mechanizm, którego można użyć, aby upewnić się, że usługi nie mogą inicjować połączenia z urządzeniami w Internecie.

Platforma Azure obsługuje dedykowaną łączność łącza sieci WAN z siecią lokalną i siecią wirtualną platformy Azure za pomocą [usługi ExpressRoute.](../../expressroute/expressroute-introduction.md) Łącze między platformą Azure a witryną używa dedykowanego połączenia, które nie przechodzi przez publiczny Internet. Jeśli aplikacja platformy Azure jest uruchomiona w wielu centrach danych, można użyć [usługi Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) do inteligentnego kierowania żądań od użytkowników między wystąpieniami aplikacji. Można również kierować ruch do usług nie uruchomionych na platformie Azure, jeśli są one dostępne z Internetu.

## <a name="virtual-machine-security"></a>Zabezpieczenia maszyn wirtualnych

[Maszyny wirtualne platformy Azure](../../virtual-machines/index.yml) umożliwia elastyczne wdrażanie szerokiej gamy rozwiązań obliczeniowych. Dzięki obsłudze systemów Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP i Azure BizTalk Services można wdrożyć dowolne obciążenie i dowolny język w prawie każdym systemie operacyjnym.

Korzystając z platformy Azure, możesz używać [oprogramowania chroniącego przed złośliwym oprogramowaniem](antimalware.md) od dostawców zabezpieczeń, takich jak Microsoft, Symantec, Trend Micro i Kaspersky, aby chronić maszyny wirtualne przed złośliwymi plikami, adware i innymi zagrożeniami.

Ochrona przed złośliwym oprogramowaniem firmy Microsoft dla usług w chmurze i maszyn wirtualnych platformy Azure to funkcja ochrony w czasie rzeczywistym, która pomaga identyfikować i usuwać wirusy, programy szpiegujące i inne złośliwe oprogramowanie. Ochrona przed złośliwym oprogramowaniem firmy Microsoft udostępnia konfigurowalne alerty, gdy znane złośliwe lub niechciane oprogramowanie próbuje zainstalować się lub uruchomić w systemach platformy Azure.

[Usługa Azure Backup](../../backup/backup-overview.md) to skalowalne rozwiązanie, które chroni dane aplikacji przy zerowym kapitale i minimalnych kosztach operacyjnych. Błędy aplikacji mogą uszkodzić dane, a błędy ludzkie mogą powodować błędy w aplikacjach. Dzięki usłudze Azure Backup maszyny wirtualne z systemem Windows i Linux są chronione.

[Usługa Azure Site Recovery](../../site-recovery/site-recovery-overview.md) ułatwia organizowanie replikacji, pracy awaryjnej i odzyskiwania obciążeń i aplikacji, dzięki czemu są one dostępne z lokalizacji dodatkowej, jeśli lokalizacja podstawowa ustępuje.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist"></a>Zapewnienie zgodności: lista kontrolna należytej staranności usług w chmurze

Firma Microsoft [opracowała listę kontrolną należytej staranności usług w chmurze,](https://aka.ms/cloudchecklist.download) aby pomóc organizacjom w doczesnym wykonywaniu należytej staranności, gdy rozważają przejście do chmury. Zapewnia strukturę organizacji dowolnej wielkości i rodzaju — prywatnych firm i organizacji sektora publicznego, w tym rządu na wszystkich szczeblach i w organizacjach non-profit — w celu określenia własnych celów w zakresie wydajności, usług, zarządzania danymi i zarządzania oraz Wymagania. Dzięki temu mogą porównywać oferty różnych dostawców usług w chmurze, co ostatecznie stanowi podstawę umowy o świadczenie usług w chmurze.

Lista kontrolna zawiera ramy, które dostosowują klauzulę po klauzuli do nowego międzynarodowego standardu dla umów serwisowych w chmurze, ISO/IEC 19086. Ten standard oferuje ujednolicony zestaw zagadnień dla organizacji, aby pomóc im w podejmowaniu decyzji dotyczących wdrażania chmury i stworzyć wspólną płaszczyznę do porównywania ofert usług w chmurze.

Lista kontrolna promuje gruntownie sprawdzone przejście do chmury, zapewniając ustrukturyzowane wskazówki i spójne, powtarzalne podejście do wyboru dostawcy usług w chmurze.

Wdrożenie chmury nie jest już po prostu decyzją technologiczną. Ponieważ wymagania dotyczące listy kontrolnej dotyczą każdego aspektu organizacji, służą one do zwołania wszystkich kluczowych wewnętrznych decydentów — CIO i CISO, a także specjalistów od prawników, zarządzania ryzykiem, zamówień i zgodności. Zwiększa to skuteczność procesu decyzyjnego i podejmowania decyzji w zakresie rzetelnego rozumowania, zmniejszając w ten sposób prawdopodobieństwo nieprzewidzianych przeszkód do przyjęcia.

Ponadto lista kontrolna:

- Udostępnia kluczowe tematy dyskusji dla decydentów na początku procesu wdrażania chmury.

- Obsługuje szczegółowe dyskusje biznesowe na temat przepisów i własnych celów organizacji dotyczących prywatności, informacji umożliwiających identyfikację użytkownika i bezpieczeństwa danych.

- Pomaga organizacjom identyfikować wszelkie potencjalne problemy, które mogą mieć wpływ na projekt w chmurze.

- Zapewnia spójny zestaw pytań, z tymi samymi terminami, definicjami, metrykami i rezultatami dla każdego dostawcy, aby uprościć proces porównywania ofert od różnych dostawców usług w chmurze.

## <a name="azure-infrastructure-and-application-security-validation"></a>Sprawdzanie poprawności zabezpieczeń infrastruktury i aplikacji platformy Azure

[Usługa Azure Operational Security](operational-security.md) odnosi się do usług, formantów i funkcji dostępnych dla użytkowników w celu ochrony ich danych, aplikacji i innych zasobów na platformie Microsoft Azure.

![walidacja zabezpieczeń (wykrywanie)](./media/technical-capabilities/azure-security-technical-capabilities-fig7.png)

Usługa Azure Operational Security jest oparta na platformie wykorzystującej wiedzę zdobytą za pośrednictwem różnych funkcji unikatowych dla firmy Microsoft, w tym cyklu microsoft security development lifecycle (SDL), programu Microsoft Security Response Centre i głęboką świadomość krajobrazu zagrożeń dla cyberbezpieczeństwa.

### <a name="microsoft-azure-monitor"></a>Microsoft Azure Monitor

[Usługa Azure Monitor](../../azure-monitor/index.yml) to rozwiązanie do zarządzania it dla chmury hybrydowej. Dzienniki usługi Azure Monitor, używane samodzielnie lub rozszerzające istniejące wdrożenie w usłudze System Center, zapewniają maksymalną elastyczność i kontrolę nad zarządzaniem infrastrukturą w chmurze.

![Azure Monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig8.png)

Za pomocą usługi Azure Monitor możesz zarządzać dowolnym wystąpieniem w dowolnej chmurze, w tym lokalnie, platformie Azure, AWS, Windows Server, Linux, VMware i OpenStack, po niższych kosztach niż rozwiązania konkurencyjne. Stworzony z myślą o świecie opartym na chmurze monitor Azure Monitor oferuje nowe podejście do zarządzania przedsiębiorstwem, które jest najszybszym i najbardziej opłacalnym sposobem sprostania nowym wyzwaniom biznesowym i uwzględnienia nowych obciążeń, aplikacji i środowisk w chmurze.

### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

[Dzienniki usługi Azure Monitor](https://azure.microsoft.com/documentation/services/log-analytics) zapewniają usługi monitorowania, zbierając dane z zasobów zarządzanych do centralnego repozytorium. Te dane mogą obejmować zdarzenia, dane wydajności i niestandardowe dane dostarczane przez interfejs API. Zebrane dane są dostępne na potrzeby alertów, analizy i eksportu.

![Dzienniki usługi Azure Monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig9.png)

Ta metoda umożliwia konsolidację danych z różnych źródeł, dzięki czemu można łączyć dane z usług platformy Azure z istniejącym środowiskiem lokalnym. Ponadto wprowadza wyraźny podział między zbieraniem danych a akcjami wykonanymi na tych danych, tak aby wszystkie akcje były dostępne dla wszystkich typów danych.

### <a name="azure-security-center"></a>Azure Security Center

Usługa [Azure Security Center](../../security-center/security-center-intro.md) ułatwia zapobieganie zagrożeniom, ich wykrywanie i reagowanie na nie, a przy tym zapewnia lepszy wgląd i większą kontrolę w zakresie bezpieczeństwa zasobów na platformie Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań z zakresu zabezpieczeń.

Centrum zabezpieczeń analizuje stan zabezpieczeń zasobów platformy Azure w celu identyfikowania potencjalnych luk w zabezpieczeniach. Lista zaleceń prowadzi użytkownika przez proces konfigurowania wymaganych elementów sterujących.

Przykłady:

- Inicjowanie ochrony przed złośliwym oprogramowaniem w celu identyfikacji i usuwania złośliwego oprogramowania

- Konfigurowanie sieciowych grup zabezpieczeń i reguł do kontrolowania ruchu na maszynach wirtualnych

- Inicjowanie zapór aplikacji internetowych pomagających chronić przed atakami na aplikacje internetowe użytkownika

- Wdrażanie brakujących aktualizacji systemu

- Modyfikowanie konfiguracji systemu operacyjnego, które odbiegają od zalecanych standardów

Centrum zabezpieczeń automatycznie gromadzi, analizuje i integruje dane dzienników z zasobów platformy Azure, sieci i rozwiązań partnerskich, takich jak programy chroniące przed złośliwym oprogramowaniem i zapory. Po wykryciu zagrożenia tworzony jest alert zabezpieczeń. Przykłady obejmują wykrywanie:

- Zagrożone maszyny wirtualne komunikujące się ze znanymi złośliwymi adresami IP

- Zaawansowanego złośliwego oprogramowania wykrytego za pomocą funkcji raportowania błędów systemu Windows

- Brutalne ataki siłowe na maszyny wirtualne

- Alerty zabezpieczeń ze zintegrowanych programów chroniących przed złośliwym oprogramowaniem i zapór

### <a name="azure-monitor"></a>Monitor platformy Azure

[Usługa Azure Monitor](../../azure-monitor/overview.md) udostępnia wskaźniki do informacji na temat określonych typów zasobów. Oferuje wizualizację, kwerendy, routing, alerty, automatyczne skalowanie i automatyzację na danych zarówno z infrastruktury platformy Azure (dziennik aktywności), jak i każdego pojedynczego zasobu platformy Azure (dzienniki diagnostyczne).

Aplikacje w chmurze są złożone z wieloma ruchomymi częściami. Monitorowanie zapewnia dane, aby upewnić się, że aplikacja pozostaje w stanie dobrej kondycji. Pomaga również zażegnać potencjalne problemy lub rozwiązać poprzednie.

![Usługa](./media/technical-capabilities/azure-security-technical-capabilities-fig10.png) Azure Monitor Ponadto można użyć danych monitorowania, aby uzyskać szczegółowe informacje na temat aplikacji. Ta wiedza może pomóc w zwiększce wydajności aplikacji lub łatwości konserwacji lub zautomatyzowaniu działań, które w przeciwnym razie wymagałyby ręcznej interwencji.

Inspekcja zabezpieczeń sieci ma kluczowe znaczenie dla wykrywania luk w zabezpieczeniach sieci i zapewnienia zgodności z modelem bezpieczeństwa IT i nadzoru regulacyjnego. W widoku Grupa zabezpieczeń można pobrać skonfigurowane sieciowe grupy zabezpieczeń i reguły zabezpieczeń, a także skuteczne reguły zabezpieczeń. Po zastosowaniu listy reguł można określić porty, które są otwarte i lukę w zabezpieczeniach sieci SS.

### <a name="network-watcher"></a>Obserwator sieciowy

[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) to usługa regionalna, która umożliwia monitorowanie i diagnozowanie warunków na poziomie sieci w, do i z platformy Azure. Narzędzia diagnostyczne i wizualizacji sieci dostępne za pomocą obserwatora sieci pomagają zrozumieć, zdiagnozować i uzyskać wgląd w sieć na platformie Azure. Ta usługa obejmuje przechwytywanie pakietów, następny przeskok, weryfikację przepływu IP, widok grupy zabezpieczeń, dzienniki przepływu sieciowej grupy zabezpieczeń. Monitorowanie na poziomie scenariusza zapewnia widok end-to-end zasobów sieciowych w przeciwieństwie do monitorowania poszczególnych zasobów sieciowych.

### <a name="storage-analytics"></a>Analityka magazynu

[Usługa Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) może przechowywać dane, które zawierają zagregowane statystyki transakcji i dane pojemności dotyczące żądań do usługi magazynu. Transakcje są zgłaszane zarówno na poziomie operacji interfejsu API, jak i na poziomie usługi magazynu, a pojemność jest raportowana na poziomie usługi magazynu. Dane metryki mogą służyć do analizowania użycia usługi magazynu, diagnozowania problemów z żądaniami dla usługi magazynu i poprawy wydajności aplikacji korzystających z usługi.

### <a name="application-insights"></a>Application Insights

[Usługa Application Insights](../../azure-monitor/app/app-insights-overview.md) to usługa zarządzania wydajnością aplikacji rozszerzalna (APM) dla deweloperów sieci web na wielu platformach. Użyj tej usługi do monitorowania aplikacji internetowej na żywo. Usługa automatycznie wykryje nieprawidłowości w zakresie wydajności. Zawiera zaawansowane narzędzia analityczne ułatwiające diagnozowanie problemów i zrozumienie, co użytkownicy robią z aplikacją. Usługa ta pomaga w ciągłym udoskonalaniu wydajności i użyteczności tworzonych rozwiązań. Działa dla aplikacji na wielu różnych platformach, w tym .NET, Node.js i Java EE, hostowanych lokalnie lub w chmurze. Integruje się z procesem devOps i ma punkty połączenia z różnymi narzędziami programistycznymi.

Monitoruje ona:

- **Liczby żądań, czasy reakcji i współczynniki błędów** — dowiedz się, które strony są najbardziej popularne, o jakiej porze dnia i gdzie są Twoi użytkownicy. Zobacz, które strony działają najlepiej. Jeśli Twoje czasy odpowiedzi i częstotliwości awarii są duże, gdy jest więcej żądań, być może masz problem z zasobami.

- **Współczynniki zależności, czasy reakcji i współczynniki błędów** — dowiedz się, czy usługi zewnętrzne nie spowalniają pracy.

- **Wyjątki** — analizowanie zagregowanych danych statystycznych lub wybieranie określonych wystąpień i przechodzenie do szczegółów śladu stosu i powiązanych żądań. Są zgłaszane zarówno wyjątki serwera, jak i przeglądarki.

- **Wydajność ładowania i wyświetleń stron** — zgłoszona przez przeglądarki użytkowników.

- **Połączenia AJAX ze stron internetowych** - wskaźniki, czasy odpowiedzi i wskaźniki niepowodzeń.

- **Liczy się użytkownik i sesja.**

- **Liczniki wydajności** z serwerów systemu Windows lub Linux, takie jak użycie procesora CPU, pamięci i sieci.

- **Diagnostyka hosta** z platformy Docker lub Azure.

- **Diagnostyczne dzienniki śledzenia** z Twojej aplikacji — dzięki temu możesz skorelować zdarzenia śledzenia z żądaniami.

- **Niestandardowe zdarzenia i metryki,** które piszesz samodzielnie w kodzie klienta lub serwera, aby śledzić zdarzenia biznesowe, takie jak sprzedane przedmioty lub wygrane gry.

Infrastruktura aplikacji zwykle obejmuje wiele składników — może to być maszyna wirtualna, konto magazynu i sieć wirtualna albo aplikacja internetowa, baza danych, serwer bazy danych i usługi zewnętrzne. Te składniki nie są widoczne jako osobne jednostki, tylko jako powiązane i zależne od siebie nawzajem części jednej całości. Dlatego najlepiej wdrażać i monitorować je oraz zarządzać nimi grupowo. [Usługa Azure Resource Manager](../../azure-resource-manager/management/overview.md) umożliwia pracę z zasobami w rozwiązaniu jako grupa.

Wszystkie zasoby danego rozwiązania można wdrożyć, zaktualizować lub usunąć w ramach jednej skoordynowanej operacji. Wdrażanie wykonuje się przy użyciu szablonu, którego można następnie używać w różnych środowiskach (testowanie, etap przejściowy i produkcja). Usługa Resource Manager zapewnia funkcje zabezpieczeń, inspekcji i tagowania ułatwiające zarządzanie zasobami po wdrożeniu.

**Zalety korzystania z usługi Resource Manager**

Usługa Resource Manager zapewnia kilka korzyści:

- Możliwość grupowego wdrożenia i monitorowania wszystkich zasobów w ramach rozwiązania oraz zarządzania nimi (zamiast obsługiwania zasobów pojedynczo).

- Możliwość wielokrotnego wdrażania rozwiązania w całym cyklu programistycznym z gwarancją spójnego stanu zasobów po każdym wdrożeniu.

- Możliwość zarządzania infrastrukturą przy użyciu szablonów deklaratywnych zamiast skryptów.

- Można zdefiniować zależności między zasobami, więc są one wdrażane w odpowiedniej kolejności.

- Możliwość stosowania kontroli dostępu do wszystkich usług w grupie zasobów dzięki natywnej integracji funkcji kontroli dostępu na podstawie ról z platformą zarządzania.

- Możliwość dodawania tagów do zasobów w celu logicznego uporządkowania wszystkich zasobów w ramach subskrypcji.

- Możliwość wyjaśniania rozliczeń w organizacji przez wyświetlanie kosztów dla grupy zasobów korzystających z tego samego tagu.

> [!Note]
> Usługa Resource Manager udostępnia nową metodę wdrażania rozwiązań i zarządzania nimi. Jeśli używasz wcześniejszego modelu wdrażania i chcesz dowiedzieć się więcej o zmianach, zobacz [Opis wdrażania Menedżera zasobów i wdrożenia klasycznego](../../azure-resource-manager/management/deployment-models.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o bezpieczeństwie, czytając niektóre z naszych szczegółowych tematów bezpieczeństwa:

- [Inspekcja i rejestrowanie](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [Cyberprzestępczości](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [Projektowanie i bezpieczeństwo operacyjne](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [Szyfrowania](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [Zarządzanie tożsamościami i dostępem](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [Bezpieczeństwo sieci](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [Zarządzanie zagrożeniami](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)
