---
title: Usługa Azure Security Center — często zadawane pytania (FAQ) | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące usługi Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2018
ms.author: rkarlin
ms.openlocfilehash: 382f85c268b2e21a780756057f4bf78c41c791c2
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283507"
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Często zadawane pytania dotyczące usługi Azure Security Center
Często zadawane pytania dotyczące usługi Azure Security Center, to usługa, która pomaga zapobiegać zagrożeniom, wykrywanie i odpowiadanie na nie dzięki lepszemu wglądowi w i kontroli nad ich zabezpieczeniami zasobami Microsoft Azure.

> [!NOTE]
> Począwszy od początku czerwca 2017 roku, usługa Security Center będzie używać programu Microsoft Monitoring Agent do gromadzenia i przechowywania danych. Aby dowiedzieć się więcej, zobacz [migracja platformy Centrum zabezpieczeń Azure](security-center-platform-migration.md). Informacje przedstawione w tym artykule reprezentują funkcję Security Center po przejściu do programu Microsoft Monitoring Agent.
>
>

## <a name="general-questions"></a>Pytania ogólne
### <a name="what-is-azure-security-center"></a>Co to jest Centrum zabezpieczeń Azure?
Centrum zabezpieczeń Azure ułatwia zapobieganie zagrożeniom, ich wykrywanie i reagowanie na nie, a przy tym zapewnia lepszy wgląd i większą kontrolę w zakresie bezpieczeństwa zasobów na platformie Azure. Umożliwia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami dla wszystkich subskrypcji, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

### <a name="how-do-i-get-azure-security-center"></a>Jak uzyskać usługę Azure Security Center?
Usługa Azure Security Center jest włączone w ramach subskrypcji Microsoft Azure i dostępne w [witryny Azure portal](https://azure.microsoft.com/features/azure-portal/). ([Zaloguj się do portalu](https://portal.azure.com), wybierz opcję **Przeglądaj**i przewiń do **usługi Security Center**).  

## <a name="billing"></a>Rozliczenia
### <a name="how-does-billing-work-for-azure-security-center"></a>Jak działa rozliczanie usługi Azure Security Center?
Usługa Security Center jest oferowana w dwóch warstwach:

**w warstwie bezpłatna** zapewnia wgląd w stan zabezpieczeń zasobów platformy Azure, podstawowe zasady zabezpieczeń, zaleceń dotyczących zabezpieczeń i integrację z produktów i usług zabezpieczeń oferowanych przez partnerów.

**w warstwie standardowa** dodaje zaawansowanych zagrożeń możliwości wykrywania, w tym zagrożeń analizy, analizę behawioralną, wykrywanie anomalii, zdarzenia związane z bezpieczeństwem i zagrożeń: uznanie autorstwa raportów. Korzystanie z warstwy Standardowa jest bezpłatne przez pierwszych 60 dni. Należy wybrać w dalszym ciągu używać usługi dłużej niż 60 dni, automatycznie będą naliczane za usługę.  Aby przeprowadzić uaktualnienie, wybierz [warstwy cenowej](https://docs.microsoft.com/azure/security-center/security-center-pricing) w zasadach zabezpieczeń.

## <a name="permissions"></a>Uprawnienia
Azure Security Center korzysta z [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md), który zapewnia [wbudowane role](../role-based-access-control/built-in-roles.md) które można przypisać do użytkowników, grup i usług na platformie Azure.

Usługa Security Center ocenia konfigurację zasobów, aby zidentyfikować problemy dotyczące zabezpieczeń i luki w zabezpieczeniach. W usłudze Security Center widoczne są tylko informacje związane z zasobem, gdy masz przypisaną rolę właściciela, współautora lub czytelnika subskrypcji lub grupy zasobów, do której należy zasób.

Zobacz [uprawnień w usłudze Azure Security Center](security-center-permissions.md) Aby dowiedzieć się więcej na temat ról i dozwolonych akcji w usłudze Security Center.

## <a name="data-collection"></a>Zbieranie danych
Usługa Security Center zbiera dane z maszyn wirtualnych (VM) i komputerów spoza platformy Azure do monitorowania pod kątem luk w zabezpieczeniach i zagrożeń. Dane są zbierane za pomocą programu Microsoft Monitoring Agent, który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z maszyn oraz kopiuje dane do Twojego obszaru roboczego na potrzeby analizy.

### <a name="how-do-i-disable-data-collection"></a>Jak wyłączyć zbieranie danych?
Automatyczna aprowizacja jest domyślnie wyłączona. Można wyłączyć automatyczną aprowizację zasobów w dowolnym momencie przez wyłączenie tego ustawienia w zasadach zabezpieczeń. Automatyczna aprowizacja zdecydowanie zalecane jest aby uzyskać alerty zabezpieczeń i zalecenia dotyczące aktualizacji systemu, luk w zabezpieczeniach systemu operacyjnego i programu endpoint protection.

Aby wyłączyć zbieranie danych [Zaloguj się do witryny Azure portal](https://portal.azure.com), wybierz opcję **Przeglądaj**, wybierz opcję **usługi Security Center**i wybierz **wybierz zasady**. Wybierz subskrypcję, dla której chcesz wyłączyć automatyczną aprowizację. Po wybraniu subskrypcji **zasady zabezpieczeń — zbierania danych** zostanie otwarty. W obszarze **automatycznej aprowizacji**, wybierz opcję **poza**.

### <a name="how-do-i-enable-data-collection"></a>Jak włączyć zbieranie danych?
Można włączyć zbierania danych dla subskrypcji platformy Azure w ramach zasad zabezpieczeń. Aby włączyć zbieranie danych. [Zaloguj się do witryny Azure portal](https://portal.azure.com), wybierz opcję **Przeglądaj**, wybierz opcję **usługi Security Center**i wybierz **zasady zabezpieczeń**. Wybierz subskrypcję, której chcesz włączyć automatyczną aprowizację. Po wybraniu subskrypcji **zasady zabezpieczeń — zbierania danych** zostanie otwarty. W obszarze **automatycznej aprowizacji**, wybierz opcję **na**.

### <a name="what-happens-when-data-collection-is-enabled"></a>Co się stanie, gdy jest włączone zbieranie danych?
Po włączeniu automatycznej aprowizacji Security Center aprowizuje program Microsoft Monitoring Agent na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i wszelkie nowe, które są tworzone. Automatycznej aprowizacji jest zdecydowanie zalecane, ale jest również dostępna ręcznej instalacji agenta. [Dowiedz się, jak można zainstalować rozszerzenia Microsoft Monitoring Agent](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Agent umożliwia zdarzeń tworzenia procesu 4688 i *CommandLine* pola wewnątrz zdarzeń 4688. Nowe procesy utworzone na maszynie Wirtualnej są rejestrowane w dzienniku zdarzeń i monitorowana przez Centrum zabezpieczeń usługi wykrywania. Instrukcje dotyczące szczegółów dla każdego nowego procesu, zobacz [pola Opis 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Agent również zbiera dane zdarzeń 4688, utworzony na maszynie Wirtualnej i przechowuje je w polu wyszukiwania.

Agent umożliwia również zbieranie danych dla [adaptacyjnego sterowania aplikacjami](security-center-adaptive-application.md), usługa Security Center konfiguruje lokalne zasady funkcji AppLocker w trybie inspekcji, aby zezwolić na wszystkie aplikacje. To spowoduje, że zasady ograniczeń oprogramowania do wygenerowania zdarzenia, które następnie są pobierane i wykorzystywane przez usługę Security Center. Należy zauważyć, że te zasady nie zostaną skonfigurowane na maszynach, na których jest już skonfigurowane zasady funkcji AppLocker. 

Gdy usługa Security Center wykrywa podejrzane działania na maszynie Wirtualnej, powiadomienia klienta za pośrednictwem poczty e-mail, gdy [zabezpieczeń informacji kontaktowych](security-center-provide-security-contact-details.md) zostało podane. Alert jest również widoczne w pulpicie nawigacyjnym alerty zabezpieczeń Centrum zabezpieczeń.



### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Czy Monitoring Agent wpływ na wydajność moich serwerów?
Agent korzysta nominalna ilość zasobów systemowych i powinno mieć większego wpływu na wydajność. Aby uzyskać więcej informacji na temat wpływu na wydajność i agent i rozszerzenia, zobacz [przewodnik planowania i obsługi](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Gdzie są przechowywane moje dane?
Dane zbierane z tego agenta są przechowywane w istniejącego obszaru roboczego usługi Log Analytics skojarzonych z subskrypcją lub nowy obszar roboczy. Aby uzyskać więcej informacji, zobacz [bezpieczeństwo danych](security-center-data-security.md).

## <a name="using-azure-security-center"></a>Za pomocą usługi Azure Security Center
### <a name="what-is-a-security-policy"></a>Co to są zasady zabezpieczeń?
Zasady zabezpieczeń określają zestaw mechanizmów kontrolnych, które są zalecane dla zasobów w ramach określonej subskrypcji. W usłudze Azure Security Center można zdefiniować zasady dla subskrypcji platformy Azure zgodnie z wymaganiami w zakresie zabezpieczeń firmy i typem aplikacji oraz poufnością danych w poszczególnych subskrypcjach.

Zasady zabezpieczeń włączone w usłudze Azure Security Center dysku — zalecenia dotyczące zabezpieczeń i monitorowania. Aby dowiedzieć się więcej na temat zasad zabezpieczeń, zobacz [monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Kto może modyfikować zasady zabezpieczeń?
Aby zmodyfikować zasady zabezpieczeń, musi być kontem administratora zabezpieczeń lub właścicielem lub współautorem subskrypcji.

Aby dowiedzieć się, jak skonfigurować zasady zabezpieczeń, zobacz [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-policies.md).

### <a name="what-is-a-security-recommendation"></a>Co to jest zalecana ze względów bezpieczeństwa?
Usługa Azure Security Center analizuje stan zabezpieczeń zasobów platformy Azure. Po zidentyfikowaniu potencjalnych luk w zabezpieczeniach tworzone są zalecenia. Zaleceń prowadzi przez proces konfigurowania wymaganych kontroli. Przykładami są:

* Inicjowanie obsługi ochrony przed złośliwym kodem do identyfikacji i usuwania złośliwego oprogramowania
* [Sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md) i reguł sterujących ruchem do maszyn wirtualnych
* Inicjowanie obsługi administracyjnej zapory aplikacji sieci web, aby pomóc Ci chronić przed atakami przeznaczone dla aplikacji sieci web
* Wdrażanie brakujących aktualizacji systemu
* Modyfikowanie konfiguracji systemu operacyjnego, które odbiegają od zalecanych standardów

Tylko te zalecenia, które są włączone w zasadach zabezpieczeń są wyświetlane w tym miejscu.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Jak wyświetlić bieżący stan zabezpieczeń zasobów platformy Azure, Moje?
**Omówienie usługi Security Center** blok ogólny stan zabezpieczeń środowiska według obliczeniowych, sieci, magazynu i danych i aplikacji. Każdy typ zasobu ma przedstawiający wskaźnik, jeśli zidentyfikowano żadnych potencjalnych luk w zabezpieczeniach. Klikając każdy Kafelek Wyświetla listę problemów z zabezpieczeniami identyfikowane przez usługę Security Center oraz spis zasobów w ramach subskrypcji.

### <a name="what-triggers-a-security-alert"></a>Co wyzwala alert zabezpieczeń?
Usługa Azure Security Center automatycznie zbiera, analizuje i fuses dane dzienników z zasobów platformy Azure, sieci i rozwiązań partnerskich, takich jak ochrony przed złośliwym oprogramowaniem i zapory. Po wykryciu zagrożenia tworzony jest alert zabezpieczeń. Przykłady obejmują wykrywanie:

* Zagrożonych maszyn wirtualnych komunikujących się ze znanymi złośliwymi adresami IP
* Zaawansowanego złośliwego oprogramowania wykrytego za pomocą usługi raportowania błędów Windows
* Ataków siłowych wobec maszyn wirtualnych
* Alerty zabezpieczeń z rozwiązań zabezpieczeń zintegrowanych partnerów, takich jak chroniące przed złośliwym kodem lub zapory aplikacji sieci Web

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Jaka jest różnica między zagrożenia wykryte i alerty o przez program Microsoft Security Response Center i Azure Security Center?
Narzędzie Microsoft Security Response Center (MSRC) wykonuje monitorowania zabezpieczeń wybierz sieć platformy Azure i infrastrukturą i odbiera skarg analizy i nadużyć zagrożenia pochodzące od innych firm. Gdy MSRC uświadamia sobie, że dane klienta były używane na przez nieupoważnione lub lub że korzystanie z platformy Azure przez klienta nie jest zgodne z warunkami dopuszczalne użycie, Menedżer zdarzenia zabezpieczeń powiadamia klienta. Powiadomienia są zazwyczaj występuje, wysyłając wiadomość e-mail do kontaktów zabezpieczeń określone w usłudze Azure Security Center lub właściciel subskrypcji platformy Azure, jeśli nie określono kontaktu zabezpieczeń.

Usługa Security Center jest usługą platformy Azure, która stale monitoruje środowiska platformy Azure przez klienta i stosuje analytics, aby automatycznie wykrywać szeroką gamę potencjalnie złośliwych działań. Wykrywane odmiany są udostępniane jako alerty zabezpieczeń w pulpicie nawigacyjnym usługi Security Center.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Jakie zasoby platformy Azure są monitorowane przez usługę Azure Security Center?
Usługa Azure Security Center monitoruje następujących zasobów platformy Azure:

* Maszyny wirtualne (VM) (w tym [usług w chmurze](../cloud-services/cloud-services-choose-me.md))
* Sieci wirtualne platformy Azure
* Usługa Azure SQL
* Konto usługi Azure Storage
* Aplikacje sieci Web platformy Azure (w [środowiska App Service Environment](../app-service/environment/intro.md))
* Rozwiązań partnerskich zintegrowanych z subskrypcją platformy Azure, takich jak zapory aplikacji sieci web na maszynach wirtualnych i w środowisku App Service Environment

## <a name="virtual-machines"></a>Maszyny wirtualne
### <a name="what-types-of-virtual-machines-are-supported"></a>Jakie typy maszyn wirtualnych są obsługiwane?
Monitorowanie i zalecenia są dostępne dla maszyn wirtualnych (VM) utworzone za pomocą obu [klasycznej sieci wirtualnej i modelem wdrażania usługi Resource Manager](../azure-classic-rm.md).

Zobacz [obsługiwanych platform w usłudze Azure Security Center](security-center-os-coverage.md) Aby uzyskać listę obsługiwanych platform.

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Dlaczego usługa Azure Security Center nie rozpoznaje rozwiązanie chroniące przed złośliwym kodem uruchomiona na maszynie Wirtualnej platformy Azure?
Usługa Azure Security Center ma wgląd we ochrony przed złośliwym kodem zainstalowane za pomocą rozszerzeń platformy Azure. Na przykład usługa Security Center nie jest w stanie wykryć ochrony przed złośliwym kodem, który został wstępnie zainstalowane w obrazie, podane lub jeśli zainstalowano ochrony przed złośliwym oprogramowaniem na maszynach wirtualnych przy użyciu własnych procesów (na przykład systemy zarządzania konfiguracją).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Dlaczego otrzymuję komunikat "Brak danych skanowania" dla mojej maszyny Wirtualnej?
Ten komunikat pojawia się, gdy nie ma żadnych danych skanowania dla maszyny Wirtualnej. Jego może zająć trochę czasu (mniej niż godzinę) dla danych skanowania wypełnić po włączeniu zbierania danych w usłudze Azure Security Center. Po początkowego zapełniania danych skanowania może odebrane tego komunikatu, ponieważ nie ma żadnych danych skanowania w ogóle lub nie ma żadnych ostatnich danych skanowania. Skanowanie nie wypełnić dla maszyny Wirtualnej w stanie zatrzymania. Ten komunikat może również zostać wyświetlony, jeśli danych skanowania nie została wypełniona ostatnio (zgodnie z zasadami przechowywania dla agenta programu Windows, który ma wartość domyślną w ciągu 30 dni).

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Jak często usługa Security Center skanowanie luk w zabezpieczeniach systemu operacyjnego, aktualizacji systemu i problemy z ochroną punktu końcowego?
Czas oczekiwania w usłudze Security Center skanuje pod kątem luk w zabezpieczeniach, aktualizacje, problemów i jest:

- Konfiguracje zabezpieczeń systemu operacyjnego — danych jest aktualizowany w ciągu 48 godzin
- Aktualizacje systemu — danych jest aktualizowany w ciągu 24 godzin
- Problemy z programem Endpoint Protection — dane są aktualizowane w ramach 8 godzin

Usługa Security Center skanuje w poszukiwaniu nowych danych zazwyczaj co godzinę. Powyższe wartości opóźnienia są scenariusz gdzie nie jest ostatnie skanowanie lub skanowania nie powiodło się.

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Dlaczego otrzymuję komunikat "Brak jest Agent maszyny Wirtualnej?"
Musi być zainstalowany Agent maszyny Wirtualnej na maszynach wirtualnych, aby włączyć zbieranie danych. Agent maszyny wirtualnej jest instalowany domyślnie w przypadku maszyn wirtualnych wdrażanych z poziomu portalu Azure Marketplace. Aby uzyskać informacje dotyczące sposobu instalowania agenta maszyny Wirtualnej na innych maszynach wirtualnych, zobacz wpis w blogu [agenta maszyny Wirtualnej i rozszerzenia](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).
