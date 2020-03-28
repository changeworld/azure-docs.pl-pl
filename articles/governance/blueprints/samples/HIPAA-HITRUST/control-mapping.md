---
title: Hipaa HITRUST plan kontroli przykładowych
description: Mapowanie sterowania przykładami planu HITRUST HIPAA. Każdy formant jest mapowany na jedną lub więcej zasad platformy Azure, które pomagają w ocenie.
ms.date: 11/26/2019
ms.topic: sample
ms.openlocfilehash: c6680055cfe41a2f893d46cba797783913d880ce
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75472310"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>Mapowanie sterowania przykładem planu HITRUST HIPAA

W poniższym artykule opisano, jak plan ochrony konta AZURE PLANY HIPAA HITRUST przykładowe mapy do formantów HITRUST HIPAA. Aby uzyskać więcej informacji na temat kontrolek, zobacz [HIPAA HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Poniższe mapowania są do formantów **HITRUST HIPAA.** Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania sterowania. Wiele mapowanych formantów są implementowane za pomocą inicjatywy [azure policy.](../../../policy/overview.md) Aby przejrzeć pełną inicjatywę, otwórz **policy** w witrynie Azure portal i wybierz stronę **Definicje.** Następnie znajdź i wybierz ** \[podgląd\]: Inspekcja kontroli HIPAA HITRUST wbudowanej** inicjatywy zasad.

> [!IMPORTANT]
> Każdy formant poniżej jest skojarzony z jedną lub więcej definicji [zasad platformy Azure.](../../../policy/overview.md) Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolą; jednak często nie ma 1:1 lub pełne dopasowanie między formantem i jedną lub więcej zasad. W związku z tym **zgodne** w usłudze Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to pełnej zgodności ze wszystkimi wymaganiami formantu. Ponadto standard zgodności zawiera formanty, które nie są objęte żadnych definicji zasad platformy Azure w tej chwili. W związku z tym zgodność w zasadach platformy Azure jest tylko częściowy widok ogólnego stanu zgodności. Skojarzenia między formantami i definicjami zasad platformy Azure dla tego przykładu planu zgodności mogą się zmieniać wraz z czasem. Aby wyświetlić historię zmian, zobacz [historię zatwierdzania gitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md).

## <a name="control-against-malicious-code"></a>Kontrola przed złośliwym kodem

Ten plan ułatwia zarządzanie ochroną punktów końcowych, w tym ochronę przed złośliwym kodem, przez przypisanie definicji [zasad platformy Azure,](../../../policy/overview.md) które monitorują brak ochrony punktów końcowych na maszynach wirtualnych w usłudze Azure Security Center i wymuszają rozwiązanie ochrony przed złośliwym oprogramowaniem firmy Microsoft na maszynach wirtualnych systemu Windows.

- Wdrażanie domyślnego rozszerzenia ochrony przed złośliwym oprogramowaniem firmy Microsoft IaaS dla serwera systemu Windows
- Dzienniki diagnostyczne na kontach usługi Batch powinny być włączone
- Należy zainstalować aktualizacje systemu na maszynach


## <a name="management-of-removable-media"></a>Zarządzanie nośnikami wymiennymi

Organizacja, na podstawie poziomu klasyfikacji danych, rejestruje nośniki (w tym laptopy) przed użyciem, nakłada uzasadnione ograniczenia dotyczące sposobu korzystania z takich nośników i zapewnia odpowiedni poziom ochrony fizycznej i logicznej (w tym szyfrowania) dla zawierających zakryte informacje do momentu prawidłowego zniszczenia lub odkażania.

- Wymagaj szyfrowania na kontach usługi Data Lake Store
- Ochrona TDE wystąpienia zarządzanego SQL powinna być szyfrowana za pomocą własnego klucza
- Szyfrowanie dysku powinno być stosowane na maszynach wirtualnych
- Należy włączyć przezroczyste szyfrowanie danych w bazach danych SQL


## <a name="information-exchange-policies-and-procedures"></a>Zasady i procedury wymiany informacji

Dostawcy usług w chmurze korzystają z uznanej w branży platformy wirtualizacji i standardowych formatów wirtualizacji (np. w formacie otwartej wirtualizacji, OVF), aby zapewnić interoperacyjność, oraz udokumentowali niestandardowe zmiany wprowadzone w dowolnym używanym hipernadzorze i haki wirtualizacji specyficzne dla rozwiązania dostępne do przeglądu klienta.

- Wdrażanie wymagań wstępnych w celu inspekcji maszyn wirtualnych systemu Windows, które nie mają zainstalowanych określonych aplikacji

## <a name="control-of-operational-software"></a>Kontrola oprogramowania operacyjnego 

Organizacja identyfikuje nieautoryzowane oprogramowanie w systemie informacyjnym, w tym serwery, stacje robocze i laptopy, stosuje politykę zezwalania na wszystko i odmowę przez wyjątek, aby zakazać wykonywania znanego nieautoryzowanego oprogramowania w systemie informacyjnym, oraz przegląda i aktualizuje listę nieautoryzowanego oprogramowania okresowo, ale nie mniej niż co roku.

- \[Podgląd\] Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych systemu Windows w "Opcje zabezpieczeń-Inspekcja"
- \[Podgląd\] Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych systemu Windows w "Zasady inspekcji systemu - zarządzanie kontem"

## <a name="change-control-procedures"></a>Procedury kontroli zmian

Integralność wszystkich obrazów maszyn wirtualnych jest zawsze zapewniona przez rejestrowanie i wywoływanie alertów o wszelkich zmianach wprowadzonych w obrazach maszyn wirtualnych oraz udostępnianie ich właścicielom firm i/lub klientom za pomocą metod elektronicznych (np. portali lub alertów) wyników zmiany lub przeniesienia oraz późniejszej weryfikacji integralności obrazu.

- \[Podgląd\] Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych systemu Windows w "Zasady inspekcji systemu — szczegółowe śledzenie"

## <a name="inventory-of-assets"></a>Inwentaryzacja aktywów 

Utrzymuje się spis aktywów i usług

- Dzienniki diagnostyczne w usługach wyszukiwania powinny być włączone.
- \[Podgląd\] wymagań wdrażania w celu inspekcji konfiguracji maszyn wirtualnych systemu Windows w "Opcje zabezpieczeń — serwer sieciowy firmy Microsoft"
- \[Podgląd\] wymagania dotyczące wdrażania w celu inspekcji konfiguracji maszyn wirtualnych systemu Windows w "Szablony administracyjne — sieć"

## <a name="control-of-technical-vulnerabilities"></a>Kontrola luk technicznych 

Dla wszystkich składników systemu i sieci istnieje wzmocniony standard konfiguracji.

- Inspekcja maszyn wirtualnych bez skonfigurowania odzyskiwania po awarii
- Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL
- Luka w zabezpieczeniach powinna zostać naprawiona przez rozwiązanie do oceny luk w zabezpieczeniach

## <a name="segregation-in-networks"></a>Segregacja w sieciach

Bramy zabezpieczeń organizacji (np. zapory) wymuszają zasady zabezpieczeń i są skonfigurowane do filtrowania ruchu między domenami, blokowania nieautoryzowanego dostępu i służą do utrzymywania segregacji między wewnętrznymi przewodowymi, wewnętrznymi sieciami bezprzewodowymi i zewnętrznymi segmentów sieci (np. Internetu), w tym dmz i wymuszania zasad kontroli dostępu dla każdej z domen.

- Automatyczne inicjowanie obsługi administracyjnej agenta monitorowania zabezpieczeń
- Wdrażanie obserwatora sieci podczas tworzenia sieci wirtualnych

## <a name="input-data-validation"></a>Sprawdzanie poprawności danych wejściowych

W przypadku wszystkich publicznych aplikacji sieci Web zapory na poziomie aplikacji są implementowane w celu kontrolowania ruchu. W przypadku aplikacji publicznych, które nie są oparte na sieci Web, organizacja zaimplementowała zaporę sieciową specyficzną dla typu aplikacji. Jeśli ruch do aplikacji publicznej jest szyfrowany, urządzenie znajduje się za szyfrowaniem lub jest w stanie odszyfrować ruch przed analizą.

- \[Podgląd\] Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych systemu Windows w "Właściwości Zapory systemu Windows"


## <a name="network-connection-control"></a>Kontrola połączenia sieciowego

Ruch sieciowy jest kontrolowany zgodnie z zasadami kontroli dostępu organizacji za pośrednictwem zapory i innych ograniczeń związanych z siecią dla każdego punktu dostępu do sieci lub interfejsu zarządzanego zewnętrznej usługi telekomunikacyjnej.

- Dostęp za pośrednictwem punktów końcowych skierowanych do Internetu powinien być ograniczony
- Ochrona TDE wystąpienia zarządzanego SQL powinna być szyfrowana za pomocą własnego klucza
- Zdalne debugowanie powinno być wyłączone dla aplikacji interfejsu API

## <a name="network-controls"></a>Sterowanie siecią

Organizacja korzysta z zabezpieczonych i zaszyfrowanych kanałów komunikacji podczas migracji serwerów fizycznych, aplikacji lub danych na serwery zwirtualizowane.

- Szyfrowanie dysku powinno być stosowane na maszynach wirtualnych
- Ochrona TDE serwera SQL powinna być szyfrowana za pomocą własnego klucza
- \[Podgląd\] Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych systemu Windows w "Opcje zabezpieczeń - dostęp do sieci"
- Inspekcja nieograniczonego dostępu do kont magazynu
- \[Podgląd\] Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych systemu Windows w "Właściwości zapory systemu Windows"
- Wdrażanie ustawień diagnostyki w sieciowych grupach zabezpieczeń
- Dostęp za pośrednictwem punktów końcowych skierowanych do Internetu powinien być ograniczony

## <a name="sensitive-system-isolation"></a>Czuła izolacja systemu

Udostępnione zasoby systemowe (np. rejestry, pamięć główna, pamięć pomocnicza) są zwalniane z powrotem do systemu, chronione przed ujawnieniem innym systemom/aplikacjom/użytkownikom, a użytkownicy nie mogą celowo lub nieumyślnie uzyskać dostępu do pozostałości informacji.

- Maszyny wirtualne powinny zostać zmigrowane do nowych zasobów usługi Azure Resource Manager

## <a name="security-of-network-services"></a>Bezpieczeństwo usług sieciowych

Uzgodnione usługi świadczone przez dostawcę/menedżera sieci są formalnie zarządzane i monitorowane w celu zapewnienia ich bezpiecznego świadczenia.

- Maszyny wirtualne powinny zostać zmigrowane do nowych zasobów usługi Azure Resource Manager

## <a name="network-routing-control"></a>Sterowanie routingu sieciowego

Kontrolki routingu są implementowane za pośrednictwem bram zabezpieczeń (np. zapór) używanych między sieciami wewnętrznymi i zewnętrznymi (np. internetem i sieciami innych firm).

- Adaptacyjne sterowanie aplikacjami powinno być włączone na maszynach wirtualnych

## <a name="information-exchange-policies-and-procedures"></a>Zasady i procedury wymiany informacji

Organizacja ogranicza korzystanie z kontrolowanych przez organizację przenośnych nośników danych przez upoważnione osoby w zewnętrznych systemach informacyjnych.

- Inspekcja nieograniczonego dostępu do kont magazynu
- Zdalne debugowanie powinno być wyłączone dla aplikacji sieci Web
- Aplikacja APi powinna być dostępna tylko za pośrednictwem protokołu HTTPS

## <a name="electronic-messaging"></a>Wiadomości elektroniczne

Zatwierdzenia są uzyskiwane przed korzystaniem z zewnętrznych usług publicznych, w tym wiadomości błyskawicznych lub udostępniania plików.

- \[Podgląd\] Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Linux, które nie mają uprawnień do pliku hasła ustawionego na 0644

## <a name="on-line-transactions"></a>Transakcje on-line

Organizacja wymaga użycia szyfrowania między i korzystania z podpisów elektronicznych przez każdą ze stron zaangażowanych w transakcję. Organizacja zapewnia, że przechowywanie szczegółów transakcji znajduje się poza publicznie dostępnymi środowiskami (np. na platformie pamięci masowej istniejącej w intranecie organizacji) i nie jest zachowywana i eksponowana na nośniku pamięci masowej bezpośrednio dostępnym z Internetu.W przypadku korzystania z zaufanego organu (np. w celu wydawania i utrzymywania podpisów cyfrowych i/lub certyfikatów cyfrowych), bezpieczeństwo jest zintegrowane i osadzone w całym kompleksowym zarządzaniu certyfikatami/podpisami Proces.

- Szyfrowanie dysku powinno być stosowane na maszynach wirtualnych
- \[Podgląd\] Pokaż wyniki inspekcji z maszyn wirtualnych systemu Windows, które nie zawierają określonych certyfikatów w zaufaniu głównym

## <a name="password-management"></a>Zarządzanie hasłami

Hasła są szyfrowane podczas transmisji i przechowywania na wszystkich komponentach systemu.

- \[Podgląd\] Pokaż wyniki inspekcji z maszyn wirtualnych systemu Windows, które nie mają włączonego ustawienia złożoności hasła

## <a name="user-authentication-for-external-connections"></a>Uwierzytelnianie użytkowników dla połączeń zewnętrznych

Silne metody uwierzytelniania, takie jak wieloczynnikowe, Radius lub Kerberos (dla uprzywilejowanego dostępu) i CHAP (do szyfrowania poświadczeń dla metod telefonicznych) są implementowane dla wszystkich połączeń zewnętrznych z siecią organizacji.

- Usługa MFA powinna być włączona konta z uprawnieniami do zapisu w ramach subskrypcji
- Kontrola dostępu do sieci just in time powinna być stosowana na maszynach wirtualnych

## <a name="user-identification-and-authentication"></a>Identyfikacja i uwierzytelnianie użytkownika

Użytkownicy, którzy pełnili funkcje uprzywilejowane (np. administrowanie systemem) używają oddzielnych kont podczas wykonywania tych uprzywilejowanych funkcji. Metody uwierzytelniania wieloskładnikowego są używane zgodnie z zasadami organizacyjnymi (np. w przypadku zdalnego dostępu do sieci).

- Usługa MFA powinna być włączona konta z uprawnieniami do zapisu w ramach subskrypcji
- Kontrola dostępu do sieci just in time powinna być stosowana na maszynach wirtualnych

## <a name="privilege-management"></a>Zarządzanie uprawnieniami

Dostęp do funkcji zarządzania lub konsol administracyjnych dla systemów obsługujących zwirtualizowane systemy jest ograniczony do personelu w oparciu o zasadę najmniejszych uprawnień i obsługiwany za pomocą kontroli technicznych.

- Kontrola dostępu do sieci just in time powinna być stosowana na maszynach wirtualnych
- \[Kontrola\] dostępu oparta na rolach podglądu (RBAC) powinna być używana w usługach Kubernetes

## <a name="review-of-user-access-rights"></a>Przegląd praw dostępu użytkowników

Organizacja przechowuje udokumentowaną listę autoryzowanych użytkowników zasobów informacyjnych.

- \[Podgląd\] Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych systemu Windows w "Opcje zabezpieczeń - konta"

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Zdalna ochrona portu diagnostycznego i konfiguracyjnego

Porty, usługi i podobne aplikacje zainstalowane na komputerze lub w systemach sieciowych, które nie są specjalnie wymagane dla funkcji biznesowych, są wyłączone lub usunięte.

- Porty zarządzania powinny być zamykane na maszynach wirtualnych
- Luki w konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych powinny zostać naprawione

## <a name="audit-logging"></a>Rejestrowanie inspekcji

Dzienniki wiadomości wysyłanych i odbieranych są przechowywane, w tym data, godzina, miejsce i miejsce docelowe wiadomości, ale nie jej zawartość. Inspekcja jest zawsze dostępna, gdy system jest aktywny i śledzi kluczowe zdarzenia, powodzenie/nieudany dostęp do danych, zmiany konfiguracji zabezpieczeń systemu, uprzywilejowane lub użyteczne korzystanie, wszelkie alarmy podniesione, aktywację i dezaktywację systemów ochrony (np. A/V i IDS), aktywację i dezaktywację mechanizmów identyfikacji i uwierzytelniania oraz tworzenie i usuwanie obiektów na poziomie systemu.

- Dzienniki diagnostyczne w Centrum zdarzeń powinny być włączone
- Należy zainstalować aktualizacje systemu w zestawach skalowania maszyny wirtualnej

## <a name="monitoring-system-use"></a>Wykorzystanie systemu monitorowania

Zautomatyzowane systemy wdrożone w całym środowisku organizacji są używane do monitorowania kluczowych zdarzeń i nietypowej aktywności oraz analizowania dzienników systemowych, których wyniki są regularnie przeglądane. Monitorowanie obejmuje operacje uprzywilejowane, autoryzowany dostęp lub próby nieautoryzowanego dostępu, w tym próby uzyskania dostępu do dezaktywowanych kont oraz alerty lub błędy systemu.

- Dzienniki diagnostyczne w zestawach skalowania maszyny wirtualnej powinny być włączone

## <a name="segregation-of-duties"></a>Podział obowiązków

Rozdzielenie obowiązków służy do ograniczenia ryzyka nieautoryzowanej lub niezamierzonej modyfikacji informacji i systemów. Żadna osoba nie jest w stanie uzyskać dostępu, modyfikować ani korzystać z systemów informatycznych bez autoryzacji lub wykrycia. Dostęp osób odpowiedzialnych za administrowanie i kontrola dostępu jest ograniczony do minimum niezbędnego w oparciu o rolę i obowiązki każdego użytkownika, a osoby te nie mogą uzyskać dostępu do funkcji inspekcji związanych z tymi kontrolami.

- Usługa MFA powinna być włączona konta z uprawnieniami do zapisu w ramach subskrypcji

## <a name="administrator-and-operator-logs"></a>Dzienniki administratora i operatora

Organizacja zapewnia prawidłowe rejestrowanie jest włączona w celu inspekcji działań administratora; i regularnie przegląda dzienniki systemu i operatora.

- Usługa MFA powinna być włączona konta z uprawnieniami do zapisu w ramach subskrypcji

## <a name="identification-of-risks-related-to-external-parties"></a>Identyfikacja ryzyka związanego z podmiotami zewnętrznymi

Połączenia dostępu zdalnego między organizacją a stronami zewnętrznymi są szyfrowane

- Szyfrowanie dysku powinno być stosowane na maszynach wirtualnych

## <a name="business-continuity-and-risk-assessment"></a>Ciągłość działania i ocena ryzyka

Organizacja identyfikuje swoje krytyczne procesy biznesowe i integruje wymagania dotyczące zarządzania bezpieczeństwem informacji dotyczące ciągłości działania z innymi wymogami ciągłości odnoszącymi się do takich aspektów, jak operacje, personel, materiały, transport i obiektów.

- \[Podgląd\] Pokaż wyniki inspekcji z konfiguracji maszyn wirtualnych systemu Windows w "Konsola opcji zabezpieczeń — odzyskiwanie"

> [!NOTE]
> Dostępność określonych definicji zasad platformy Azure może się różnić w przypadku platformy Azure dla instytucji rządowych i innych chmur krajowych. 

## <a name="next-steps"></a>Następne kroki

Przejrzano mapowanie kontroli przykładowego planu HITRUST HIPAA. Następnie odwiedź następujące artykuły, aby dowiedzieć się więcej o omówieniu i sposobie wdrażania tego przykładu:

> [!div class="next step action"]
> [Plan HITRUST HIPAA — przegląd](./control-mapping.md)
> [planu HIPAA HITRUST - Wdrażanie kroków](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Dowiedz się więcej o [cyklu życia planu](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
