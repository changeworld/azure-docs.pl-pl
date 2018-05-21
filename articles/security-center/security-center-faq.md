---
title: Centrum zabezpieczeń Azure — często zadawane pytania (FAQ) | Dokumentacja firmy Microsoft
description: Często zadawane pytania odpowiedzi na pytania dotyczące Centrum zabezpieczeń Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2018
ms.author: terrylan
ms.openlocfilehash: e46c2ad30b578b0642ee7b541ea003ed67c6a7f5
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Często zadawane pytania dotyczące usługi Azure Security Center
Często zadawane pytania odpowiedzi na pytania dotyczące Centrum zabezpieczeń Azure to usługa, która pomaga zapobiec, wykrywania i reagowania na zagrożenia lepszy wgląd w i kontroli w zakresie bezpieczeństwa zasobów na platformie Microsoft Azure.

> [!NOTE]
> Począwszy od początku czerwca 2017 roku, usługa Security Center będzie używać programu Microsoft Monitoring Agent do gromadzenia i przechowywania danych. Aby dowiedzieć się więcej, zobacz [migracji Platform Centrum zabezpieczeń Azure](security-center-platform-migration.md). Informacje przedstawione w tym artykule reprezentują funkcję Security Center po przejściu do programu Microsoft Monitoring Agent.
>
>

## <a name="general-questions"></a>Pytania ogólne
### <a name="what-is-azure-security-center"></a>Co to jest Centrum zabezpieczeń Azure?
Centrum zabezpieczeń Azure ułatwia zapobieganie zagrożeniom, ich wykrywanie i reagowanie na nie, a przy tym zapewnia lepszy wgląd i większą kontrolę w zakresie bezpieczeństwa zasobów na platformie Azure. Umożliwia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami dla wszystkich subskrypcji, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

### <a name="how-do-i-get-azure-security-center"></a>Jak uzyskać Centrum zabezpieczeń Azure?
Centrum zabezpieczeń Azure jest włączone w ramach subskrypcji Microsoft Azure i są dostępne w [portalu Azure](https://azure.microsoft.com/features/azure-portal/). ([Zaloguj się do portalu](https://portal.azure.com), wybierz pozycję **Przeglądaj**, a Przewiń **Centrum zabezpieczeń**).  

## <a name="billing"></a>Rozliczenia
### <a name="how-does-billing-work-for-azure-security-center"></a>Jak działa rozliczeń dla Centrum zabezpieczeń Azure?
Centrum zabezpieczeń jest oferowana w dwóch warstw:

**Warstwę bezpłatna** zapewnia wgląd w stan zabezpieczeń zasobów platformy Azure, zasady zabezpieczeń podstawowych, zalecenia dotyczące zabezpieczeń i integracja z produktów i usług zabezpieczeń z partnerami.

**Warstwy standardowa** dodaje zagrożeń zaawansowane możliwości wykrywania, takie jak zagrożenia analizy, analizy behawioralnej, wykrywania anomalii, przypadki naruszenia zabezpieczeń i zagrożenia autorstwa raportów. Korzystanie z warstwy Standardowa jest bezpłatne przez pierwszych 60 dni. Należy wybrać w dalszym ciągu korzystać z usługi ponad 60 dni, firma Microsoft automatycznie uruchomić do obciążania dla usługi.  Aby przeprowadzić uaktualnienie, wybierz [warstwy cenowej](https://docs.microsoft.com/azure/security-center/security-center-pricing) w zasadach zabezpieczeń.

## <a name="permissions"></a>Uprawnienia
Centrum zabezpieczeń Azure używa [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md), która zapewnia [wbudowane role](../role-based-access-control/built-in-roles.md) który można przypisać do użytkowników, grup i usług Azure.

Centrum zabezpieczeń ocenia konfiguracji zasobów, aby zidentyfikować problemy z zabezpieczeniami i luk w zabezpieczeniach. W Centrum zabezpieczeń widoczne są tylko informacje związane z zasobem, jeśli przypisano rolę właściciela, współautora lub czytelnika subskrypcji lub grupy zasobów, do której należy zasób.

Zobacz [uprawnienia w Centrum zabezpieczeń Azure](security-center-permissions.md) Aby dowiedzieć się więcej o rolach i akcji dozwolonych w Centrum zabezpieczeń.

## <a name="data-collection"></a>Zbieranie danych
Centrum zabezpieczeń zbiera dane z maszyn wirtualnych platformy Azure (maszyny wirtualne) i komputerów z systemem innym niż Azure monitorowanie luk w zabezpieczeniach i zagrożeń. Dane są zbierane za pomocą programu Microsoft Monitoring Agent, który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z maszyn oraz kopiuje dane do Twojego obszaru roboczego na potrzeby analizy.

### <a name="how-do-i-disable-data-collection"></a>Jak wyłączyć zbieranie danych?
Automatyczne inicjowanie obsługi administracyjnej jest domyślnie wyłączone. Możesz wyłączyć automatyczne Inicjowanie obsługi administracyjnej z zasobów w dowolnym momencie przez wyłączenie tego ustawienia w zasadach zabezpieczeń. Automatyczne udostępnianie zdecydowanie zalecane jest aby uzyskać alerty zabezpieczeń i zaleceń dotyczących aktualizacji systemu, luk w zabezpieczeniach systemu operacyjnego i programu endpoint protection.

Aby wyłączyć zbieranie danych [Zaloguj się do portalu Azure](https://portal.azure.com), wybierz pozycję **Przeglądaj**, wybierz pozycję **Centrum zabezpieczeń**i wybierz **wybierz zasady**. Wybierz subskrypcję, dla której chcesz wyłączyć automatyczną aprowizację. Po wybraniu subskrypcji **zasady zabezpieczeń — zbieranie danych** otwiera. W obszarze **automatycznego inicjowania obsługi administracyjnej**, wybierz pozycję **poza**.

### <a name="how-do-i-enable-data-collection"></a>Jak włączyć zbieranie danych?
Zbieranie danych można włączyć dla Twojej subskrypcji platformy Azure w zasadach zabezpieczeń. Aby włączyć zbieranie danych. [Zaloguj się do portalu Azure](https://portal.azure.com), wybierz pozycję **Przeglądaj**, wybierz pozycję **Centrum zabezpieczeń**i wybierz **zasady zabezpieczeń**. Wybierz subskrypcję, która ma zostać włączone automatyczne udostępnianie. Po wybraniu subskrypcji **zasady zabezpieczeń — zbieranie danych** otwiera. W obszarze **automatycznego inicjowania obsługi administracyjnej**, wybierz pozycję **na**.

### <a name="what-happens-when-data-collection-is-enabled"></a>Co się stanie po włączeniu funkcji zbierania danych?
Automatyczne inicjowanie obsługi administracyjnej jest włączona, przepisy Centrum zabezpieczeń firmy Microsoft Monitoring Agent na wszystkich obsługiwanych maszyn wirtualnych platformy Azure i nowe pliki, które są tworzone. Automatyczne udostępnianie zdecydowanie zaleca się, ale ręcznej instalacji agenta jest również dostępna. [Dowiedz się, jak zainstalować rozszerzenie programu Microsoft Monitoring Agent](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension).

Agent umożliwia zdarzeń tworzenia procesu 4688 i *CommandLine* pole wewnątrz zdarzenia 4688. Nowe procesy utworzone na maszynie Wirtualnej są rejestrowane w dzienniku zdarzeń i monitorowane przez Centrum zabezpieczeń wykrywania usługi. Aby uzyskać informacje dotyczące szczegółów zarejestrowanych dla każdego nowego procesu, zobacz [pola Opis 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Agent również zbiera dane zdarzeń 4688 utworzone na maszynie Wirtualnej i przechowuje je w obszarze wyszukiwania.

Jeśli Centrum zabezpieczeń wykryje podejrzanych działań na maszynie Wirtualnej, w przypadku klienta jest powiadamiany za pośrednictwem poczty e-mail [informacje kontaktowe zabezpieczeń](security-center-provide-security-contact-details.md) podano. Alert jest również widoczne na pulpicie nawigacyjnym alerty zabezpieczeń Centrum zabezpieczeń.

### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Agenta monitorowania na wydajność serwerów?
Agent wykorzystuje nominalnego ilość zasobów systemowych, a powinien mieć mały wpływ na wydajność. Aby uzyskać więcej informacji dotyczących wpływu na wydajność i agent i rozszerzenia, zobacz [przewodnik dotyczący planowania i operacje](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Gdzie są przechowywane moje dane?
Dane zbierane z tego agenta są przechowywane w istniejącym obszarem roboczym analizy dzienników skojarzonych z Twoją subskrypcją lub nowego obszaru roboczego. Aby uzyskać więcej informacji, zobacz [bezpieczeństwo danych](security-center-data-security.md).

## <a name="using-azure-security-center"></a>Korzystanie z Centrum zabezpieczeń Azure
### <a name="what-is-a-security-policy"></a>Co to jest zasady zabezpieczeń?
Zasady zabezpieczeń określają zestaw kontrolek, które są zalecane dla zasobów w określonej subskrypcji. W Centrum zabezpieczeń Azure można zdefiniować zasady dla subskrypcji platformy Azure zgodnie z wymaganiami firmy dotyczącymi zabezpieczeń i typem aplikacji oraz poufności danych w każdej subskrypcji.

Zasady zabezpieczeń włączone w Centrum zabezpieczeń Azure dysku — zalecenia dotyczące zabezpieczeń i monitorowania. Aby dowiedzieć się więcej na temat zasad zabezpieczeń, zobacz [monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Kto może modyfikować zasady zabezpieczeń?
Aby zmodyfikować zasady zabezpieczeń, musi być administratorem zabezpieczeń lub właścicielem lub współautorem subskrypcji.

Aby dowiedzieć się, jak skonfigurować zasady zabezpieczeń, zobacz [Ustawianie zasad zabezpieczeń w Centrum zabezpieczeń Azure](security-center-policies.md).

### <a name="what-is-a-security-recommendation"></a>Jaka jest zalecana ze względów bezpieczeństwa?
Centrum zabezpieczeń Azure analizuje stan zabezpieczeń zasobów platformy Azure. Po potencjalnych luk w zabezpieczeniach, tworzone są zalecenia. Zalecenia ułatwiają konfigurowanie potrzebnego formantu. Przykłady to:

* Inicjowanie obsługi administracyjnej ochrony przed złośliwym kodem do identyfikacji i usuwania złośliwego oprogramowania
* Konfigurowanie [grup zabezpieczeń sieci](../virtual-network/virtual-networks-nsg.md) i reguł sterujących ruchem do maszyn wirtualnych
* Aprowizowanie zapory aplikacji sieci web pomagających chronić przed atakami przeznaczonych dla aplikacji sieci web
* Wdrażanie brakujących aktualizacji systemu
* Modyfikowanie konfiguracji systemu operacyjnego, które odbiegają od zalecanych standardów

Tylko te zalecenia, które są włączone w zasadach zabezpieczeń są wyświetlane tutaj.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Jak wyświetlić bieżący stan zabezpieczeń Zasoby platformy Azure?
**Omówienie Centrum zabezpieczeń** bloku przedstawia ogólny stan zabezpieczeń środowiska według obliczeniowych, sieci, magazynu i danych i aplikacji. Każdy typ zasobu ma przedstawiający wskaźnika, jeśli zidentyfikowano wszelkich potencjalnych luk w zabezpieczeniach. Kliknięcie każdego kafelka powoduje wyświetlenie listy problemów dotyczących zabezpieczeń, zidentyfikowane przez Centrum zabezpieczeń, razem ze spisem zasobów w ramach subskrypcji.

### <a name="what-triggers-a-security-alert"></a>Co to jest wyzwalane alert zabezpieczeń?
Centrum zabezpieczeń Azure automatycznie gromadzi, analizuje i fuses dane dzienników z zasobów platformy Azure, sieci i rozwiązań partnerskich, takich jak ochrony przed złośliwym oprogramowaniem i zapory. Po wykryciu zagrożenia tworzony jest alert zabezpieczeń. Przykłady obejmują wykrywanie:

* Zagrożonych maszyn wirtualnych komunikujących się ze znanymi złośliwymi adresami IP
* Zaawansowanego złośliwego oprogramowania wykrytego za pomocą raportowanie błędów systemu Windows
* Ataków siłowych wobec maszyn wirtualnych
* Alerty zabezpieczeń ze zintegrowanych zabezpieczeń rozwiązań partnerskich, takich jak przed złośliwym oprogramowaniem i zapór aplikacji sieci Web

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Jaka jest różnica między zagrożenia wykryte i alerty o przez Microsoft Security Response Center lub Centrum zabezpieczeń Azure?
MSRC Microsoft Security Response Center () wykonuje monitorowanie zabezpieczeń wybierz sieć platformy Azure i infrastruktury i odbiera zagrożeń analizy i nadużycia utrudnień od osób trzecich. Gdy MSRC świadomość, że dane klienta uzyskaniu przez stronę bezprawnego lub nieautoryzowane lub czy używanie klienta usługi Azure nie spełnia warunków do użycia dopuszczalne, Menedżer zdarzenia zabezpieczeń powiadamia klienta. Powiadomień zwykle występuje, wysyłając wiadomość e-mail do kontaktów zabezpieczeń określonych w Centrum zabezpieczeń Azure lub właściciela subskrypcji platformy Azure, jeśli nie określono kontaktu zabezpieczeń.

Centrum zabezpieczeń jest usługą platformy Azure, która stale monitoruje klienta środowiska platformy Azure i stosuje analytics, aby automatycznie wykrywać szeroką gamę potencjalnie złośliwych działań. Wykrywane odmiany są udostępniane jako alerty zabezpieczeń w pulpicie nawigacyjnym Centrum zabezpieczeń.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Zasoby platformy Azure są monitorowane przez Centrum zabezpieczeń Azure?
Centrum zabezpieczeń Azure monitorowanie następujących zasobów platformy Azure:

* Maszynach wirtualnych (VM) (w tym [usługi w chmurze](../cloud-services/cloud-services-choose-me.md))
* Sieci wirtualne platformy Azure
* Usługi SQL Azure
* Konto usługi Azure Storage
* Aplikacje sieci Web Azure (w [środowiska usługi aplikacji](../app-service/environment/intro.md))
* Rozwiązań partnerskich zintegrowanych z subskrypcją platformy Azure, takich jak Zapora aplikacji sieci web na maszynach wirtualnych i środowiska usługi aplikacji

## <a name="virtual-machines"></a>Maszyny wirtualne
### <a name="what-types-of-virtual-machines-are-supported"></a>Jakie typy maszyn wirtualnych są obsługiwane?
Monitorowanie i zalecenia są dostępne dla maszyn wirtualnych (VM) utworzone za pomocą obu [klasycznego i modeli wdrażania usługi Resource Manager](../azure-classic-rm.md).

Zobacz [obsługiwanych platform w Centrum zabezpieczeń Azure](security-center-os-coverage.md) listę obsługiwanych platform.

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Dlaczego nie rozpoznaje ochrony przed złośliwym kodem działającej na maszynie Wirtualnej platformy Azure w Centrum zabezpieczeń Azure?
Centrum zabezpieczeń Azure ma wgląd w ochrony przed złośliwym oprogramowaniem, zainstalowanych przy użyciu rozszerzeń Azure. Na przykład Centrum zabezpieczeń nie jest w stanie wykryć ochrony przed złośliwym kodem, który został wstępnie zainstalowany na podane obrazu lub jeśli ochrony przed złośliwym kodem jest zainstalowany na maszynach wirtualnych przy użyciu własnych procesów (takich jak systemy zarządzania konfiguracją).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Dlaczego jest wyświetlany komunikat "Brakujących danych skanowania" Moje maszyny wirtualnej?
Ten komunikat jest wyświetlany, gdy nie ma żadnych danych skanowania dla maszyny Wirtualnej. Może upłynąć trochę czasu (mniej niż godzinę), zanim dane skanowania do wypełnienia po włączeniu funkcji zbierania danych w Centrum zabezpieczeń Azure. Po początkowej populacji danych skanowania ponieważ nie ma żadnych danych skanowania w ogóle lub nie ma żadnych ostatnich danych skanowania może zostać wyświetlony ten komunikat. Skanowanie nie należy wypełniać dla maszyny Wirtualnej w stanie zatrzymania. Ten komunikat może również zostać wyświetlony, jeśli dane skanowania nie został wypełniony ostatnio (zgodnie z zasadami przechowywania dla agenta systemu Windows, który ma wartość domyślną w ciągu 30 dni).

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Jak często Centrum zabezpieczeń skanowania w poszukiwaniu luk w zabezpieczeniach systemu operacyjnego, aktualizacji systemu i problemy z ochroną punktu końcowego?
Czas oczekiwania w Centrum zabezpieczeń skanowania pod kątem luk w zabezpieczeniach, aktualizacje i problemów:

- Konfiguracje zabezpieczeń systemu operacyjnego — danych jest aktualizowany w ciągu 48 godzin
- Aktualizacje systemu — danych jest aktualizowany w ciągu 24 godzin
- Problemy z ochroną punktu końcowego — dane są aktualizowane w ramach 8 godzin

Centrum zabezpieczeń zwykle skanowania pod kątem nowych danych co godzinę. Wartości opóźnienia powyżej są scenariusz gdzie nie jest ostatnie skanowanie lub skanowania nie powiodło się.

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Dlaczego jest wyświetlany komunikat "Brak jest Agent maszyny Wirtualnej?"
Agent maszyny Wirtualnej musi być zainstalowany na maszynach wirtualnych, aby włączyć zbieranie danych. Agent maszyny wirtualnej jest instalowany domyślnie w przypadku maszyn wirtualnych wdrażanych z poziomu portalu Azure Marketplace. Aby uzyskać informacje dotyczące sposobu instalowania agenta maszyny Wirtualnej na innych maszynach wirtualnych, zobacz we wpisie blogu [agenta maszyny Wirtualnej i rozszerzenia](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).
