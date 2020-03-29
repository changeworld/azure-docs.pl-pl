---
title: Przewodnik rozwiązywania problemów z usługą Azure Security Center | Microsoft Docs
description: Ten przewodnik jest przeznaczony dla specjalistów IT, analityków zabezpieczeń i administratorów chmury, którzy muszą rozwiązywać problemy związane z usługą Azure Security Center.
services: security-center
author: v-miegge
manager: dcscontentpm
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 1c751fc31ba9066cf49eabbb86d37eda230c9c98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062887"
---
# <a name="azure-security-center-troubleshooting-guide"></a>Przewodnik rozwiązywania problemów z usługą Azure Security Center

Ten przewodnik jest przeznaczony dla specjalistów IT, analityków zabezpieczeń informacji oraz administratorów chmury, których organizacje używają usługi Azure Security Center i muszą rozwiązywać problemy związane z usługą Azure Security Center.

Usługa Security Center używa agenta monitorowania firmy Microsoft do zbierania i przechowywania danych. Aby dowiedzieć się więcej, zobacz [Migracja platformy usługi Azure Security Center](security-center-platform-migration.md). Informacje przedstawione w tym artykule reprezentują funkcję Security Center po przejściu do programu Microsoft Monitoring Agent.

## <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

W tym przewodniku wyjaśniano, jak rozwiązywać problemy związane z usługą Security Center.

Typy alertów:

* Analiza zachowania maszyny wirtualnej (VMBA)
* Analiza sieci
* Analiza bazy danych SQL Database i magazynu SQL Data Warehouse
* Informacje kontekstowe

W zależności od typów alertów klienci mogą zebrać informacje niezbędne do zbadania alertu przy użyciu następujących zasobów:

* Dzienniki zabezpieczeń w podglądzie zdarzeń maszyny wirtualnej w systemie Windows
* Polecenie AuditD w systemie Linux
* Dzienniki aktywności platformy Azure i włączenie dzienników diagnostycznych w zaatakowanym zasobie.

Klienci mogą dzielić się opiniami dotyczącymi opisu i istotności alertu. Przejdź do samego alertu, wybierz przycisk **Czy te informacje były przydatne**, wybierz przyczynę, a następnie wprowadź komentarz, aby uzasadnić opinię. Stale monitorujemy ten kanał opinii, aby ulepszać nasze alerty.

## <a name="audit-log"></a>Dziennik inspekcji

W większości przypadków rozwiązywanie problemów w usłudze Security Center rozpoczyna się od sprawdzenia rekordów [dziennika inspekcji](../azure-monitor/platform/platform-logs-overview.md) składnika, w przypadku którego wystąpił błąd. Za pomocą dzienników inspekcji można określić:

* Operacje, które zostały wykonane
* Użytkownika, który zainicjował operację
* Czas wystąpienia operacji
* Stan operacji
* Wartości innych właściwości, które mogą ułatwić zbadanie operacji

Dziennik inspekcji zawiera informacje o wszystkich operacjach zapisu (PUT, POST, DELETE) wykonywanych względem zasobów, ale nie zawiera informacji dotyczących operacji odczytu (GET).

## <a name="microsoft-monitoring-agent"></a>Microsoft Monitoring Agent

Usługa Security Center używa agenta monitorowania firmy Microsoft — jest to ten sam agent używany przez usługę Azure Monitor — do zbierania danych zabezpieczeń z maszyn wirtualnych platformy Azure. Po włączeniu zbierania danych i poprawnym zainstalowaniu agenta na maszynie docelowej powinny być wykonywane poniższe procesy:

* HealthService.exe

Po otwarciu konsoli zarządzania usługami (services.msc) będzie również widoczna uruchomiona usługa Microsoft Monitoring Agent (jak pokazano poniżej):

![Usługi](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

Aby sprawdzić, która wersja agenta jest używana, otwórz **Menedżera zadań**, na karcie **Procesy** znajdź kartę **Usługa Microsoft Monitoring Agent**, kliknij ją prawym przyciskiem myszy, a następnie kliknij pozycję **Właściwości**. Na karcie **Szczegóły** sprawdź wersję pliku, jak pokazano poniżej:

![Plik](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)

## <a name="microsoft-monitoring-agent-installation-scenarios"></a>Scenariusze instalacji programu Microsoft Monitoring Agent

Istnieją dwa scenariusze instalacji, które mogą wygenerować różne wyniki podczas instalowania programu Microsoft Monitoring Agent na komputerze. Obsługiwane scenariusze są następujące:

* **Agent instalowany automatycznie za pomocą usługi Security Center**: w tym scenariuszu możliwe będzie wyświetlanie alertów w obu lokalizacjach — usłudze Security Center i funkcji przeszukiwania dzienników. Otrzymasz powiadomienia e-mail na adres e-mail skonfigurowany w zasadach zabezpieczeń dla subskrypcji, do której należy zasób.

* **Agent ręcznie zainstalowany na maszynie wirtualnej znajdującej się na platformie Azure: w**tym scenariuszu, jeśli używasz agentów pobranych i zainstalowanych ręcznie przed lutym 2017 r., możesz wyświetlić alerty w portalu Centrum zabezpieczeń tylko wtedy, gdy filtrujesz subskrypcję, do której należy obszar roboczy. Jeśli filtrujesz subskrypcję, do której należy zasób, nie zobaczysz żadnych alertów. Będziesz otrzymywać powiadomienia e-mail na adres e-mail skonfigurowany w zasadach zabezpieczeń dla subskrypcji, do której należy obszar roboczy.

> [!NOTE]
> Aby uniknąć zachowania opisanego w drugim scenariuszu, upewnij się, że pobrano najnowszą wersję agenta.

## <a name="monitoring-agent-health-issues"></a>Monitorowanie problemów dotyczących kondycji agenta <a name="mon-agent"></a>

**Stan monitorowania** definiuje powód, dla którego usługa Security Center nie może prawidłowo monitorować maszyn wirtualnych i komputerów zainicjowanych do automatycznej aprowizacji. Poniższa tabela zawiera wartości, opisy i kroki związane z rozwiązywaniem problemów z opcją **Stan monitorowania**.

| Stan monitorowania | Opis | Kroki rozwiązywania problemów |
|---|---|---|
| Oczekująca instalacja agenta | Instalacja programu Microsoft Monitoring Agent jest nadal uruchomiona.  Instalacja może zająć do kilku godzin. | Poczekaj na zakończenie automatycznej instalacji. |
| Stan zasilania — wyłączone | Maszyna wirtualna została zatrzymana.  Program Microsoft Monitoring Agent można zainstalować tylko na maszynie wirtualnej, która jest uruchomiona. | Uruchom ponownie maszynę wirtualną. |
| Brak agenta maszyny wirtualnej platformy Azure lub jest on nieprawidłowy | Program Microsoft Monitoring Agent nie został jeszcze zainstalowany.  Aby usługa Security Center mogła zainstalować rozszerzenie, wymagany jest prawidłowy agent maszyny wirtualnej platformy Azure. | Zainstaluj, ponownie zainstaluj lub uaktualnij agenta maszyny wirtualnej platformy Azure na maszynie wirtualnej. |
| Maszyna wirtualna nie jest w stanie gotowości do instalacji  | Program Microsoft Monitoring Agent nie jest jeszcze zainstalowany, ponieważ maszyna wirtualna nie jest gotowa do instalacji. Maszyna wirtualna nie jest gotowa do instalacji ze względu na problem związany z agentem maszyny wirtualnej lub aprowizacją maszyny wirtualnej. | Sprawdź stan maszyny wirtualnej. Wróć do opcji **Maszyny wirtualne** w portalu i wybierz maszynę wirtualną, aby uzyskać informacje o stanie. |
|Instalacja nie powiodła się. Wystąpił błąd ogólny | Program Microsoft Monitoring Agent został zainstalowany, ale zakończył się niepowodzeniem ze względu na błąd. | [Zainstaluj rozszerzenie ręcznie](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) lub odinstaluj rozszerzenie, a usługa Security Center spróbuje je zainstalować ponownie. |
| Instalacja nie powiodła się. Agent lokalny jest już zainstalowany | Instalacja programu Microsoft Monitoring Agent zakończyła się niepowodzeniem. Usługa Security Center zidentyfikowała agenta lokalnego (log analytics lub system center operations manager) już zainstalowanego na maszynie wirtualnej. Aby uniknąć konfiguracji wielokrotnej, w której maszyna wirtualna wysyła raporty do dwóch oddzielnych obszarów roboczych, instalacja programu Microsoft Monitoring Agent została zatrzymana. | Istnieją dwa sposoby rozwiązania problemu: [ręczna instalacja rozszerzenia](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) oraz połączenie go z żądanym obszarem roboczym. Lub też ustawienie żądanego obszaru roboczego jako domyślnego obszaru roboczego oraz włączenie automatycznej aprowizacji agenta.  Zobacz [włączanie automatycznej aprowizacji](security-center-enable-data-collection.md). |
| Agent nie może nawiązać połączenia z obszarem roboczym | Program Microsoft Monitoring Agent został zainstalowany, ale jego działanie zakończyło się niepowodzeniem ze względu na łączność sieciową.  Sprawdź, czy masz dostęp do Internetu oraz czy skonfigurowano prawidłowy serwer proxy HTTP dla agenta. | Zobacz wymagania sieciowe agenta monitorowania. |
| Agent podłączony do brakującego lub nieznanego obszaru roboczego | Usługa Security Center zidentyfikowała, że agent monitorujący firmy Microsoft zainstalowany na maszynie wirtualnej jest połączony z obszarem roboczym, do którego nie ma dostępu. | Może do tego dojść w dwóch przypadkach. Obszar roboczy został usunięty i już nie istnieje. Zainstaluj agenta ponownie z prawidłowym obszarem roboczym lub odinstaluj agenta i pozwól usłudze Security Center zakończyć instalację z automatyczną aprowizacją. W drugim przypadku obszar roboczy jest częścią subskrypcji, do której usługa Security Center nie ma uprawnień. Usługa Security Center wymaga od subskrypcji zezwalania dostawcy zasobów usługi Microsoft Security na dostęp. Aby włączyć usługę, zarejestruj subskrypcję u dostawcy zasobów usługi Microsoft Security. Możesz to zrobić przy użyciu interfejsu API, programu PowerShell, portalu lub poprzez odfiltrowanie subskrypcji w pulpicie nawigacyjnym **Przegląd** usługi Security Center. Aby uzyskać więcej informacji, zobacz [Dostawcy zasobów i ich typy](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). |
| Brak identyfikatora agenta lub agent nie odpowiada | Usługa Security Center nie może pobrać danych zabezpieczeń zeskanowanych z maszyny wirtualnej, chociaż agent jest zainstalowany. | Agent nie zgłasza żadnych danych, w tym pulsu. Agent może być uszkodzony lub coś blokuje ruch. Lub agent zgłasza dane, ale brakuje identyfikatora zasobu platformy Azure, więc jest niemożliwe do dopasowania danych do maszyny Wirtualnej platformy Azure. Aby rozwiązać problemy z systemem Linux, zobacz [Przewodnik rozwiązywania problemów dla agenta analizy dzienników dla systemu Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#im-not-seeing-any-linux-data-in-the-oms-portal). Aby rozwiązać problemy w systemie Windows, zobacz [Troubleshooting Windows Virtual Machines (Rozwiązywanie problemów z maszynami wirtualnymi z systemem Windows)](https://github.com/MicrosoftDocs/azure-docs/blob/8c53ac4371d482eda3d85819a4fb8dac09996a89/articles/log-analytics/log-analytics-azure-vm-extension.md#troubleshooting-windows-virtual-machines). |
| Agent nie jest zainstalowany | Zbieranie danych jest wyłączone. | Włącz zbieranie danych w zasadach zabezpieczeń lub ręcznie zainstaluj program Microsoft Monitoring Agent. |

## <a name="troubleshooting-monitoring-agent-network-requirements"></a>Rozwiązywanie problemów z wymaganiami dotyczącymi sieci agenta monitorowania <a name="mon-network-req"></a>

Aby agenci mogli nawiązać połączenie z usługą Security Center i zarejestrować się za jej pomocą, muszą mieć oni dostęp do zasobów sieciowych, w tym numerów portów i adresów URL domeny.

* W przypadku serwerów proxy konieczne jest zapewnienie, że ich odpowiednie zasoby są skonfigurowane w ustawieniach agenta. Przeczytaj ten artykuł, aby uzyskać więcej informacji dotyczących [sposobu zmiany ustawień serwera proxy](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents).
* Zapory ograniczające dostęp do Internetu należy skonfigurować w taki sposób, aby zezwalały na dostęp do usługi Log Analytics. W ustawieniach agenta nie jest wymagana żadna akcja.

W poniższej tabeli przedstawiono zasoby wymagane do komunikacji.

| Zasób agenta | Porty | Obejście inspekcji HTTPS |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Tak |
| *.oms.opinsights.azure.com | 443 | Tak |
| *.blob.core.windows.net | 443 | Tak |
| *.azure-automation.net | 443 | Tak |

Jeśli wystąpią problemy związane z dołączaniem dotyczące agenta, przeczytaj artykuł [How to troubleshoot Operations Management Suite onboarding issues](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues) (Jak rozwiązywać problemy dotyczące przechodzenia do pakietu Operations Management Suite)

## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>Rozwiązywanie problemów z niedziałającą prawidłowo ochroną punktów końcowych

Agent gościa to proces nadrzędny w stosunku do wszystkich czynności wykonywanych przez rozszerzenie [Usługa firmy Microsoft chroniąca przed złośliwym kodem](../security/fundamentals/antimalware.md). Jeśli proces agenta gościa zakończy się niepowodzeniem, usługa firmy Microsoft chroniąca przed złośliwym kodem uruchomiona jako proces podrzędny agenta gościa może również zakończyć się niepowodzeniem.  W przypadku takich scenariuszy zalecamy zweryfikowanie następujących opcji:

* Jeśli docelowa maszyna wirtualna to obraz niestandardowy, a twórca maszyny wirtualnej nigdy nie zainstalował agenta gościa.
* Jeśli cel to maszyna wirtualna z systemem Linux, a nie Windows, instalowanie rozszerzenia usługi firmy Microsoft chroniącej przed złośliwym kodem w wersji dla systemu Windows na maszynie wirtualnej z systemem Linux zakończy się niepowodzeniem. Agent gościa z systemem Linux ma określone wymagania dotyczące wersji systemu operacyjnego i wymaganych pakietów. Jeśli nie zostaną one spełnione, agent maszyny wirtualnej również nie będzie działać.
* Jeśli maszyna wirtualna została utworzona za pomocą starszej wersji agenta gościa. W takiej sytuacji należy pamiętać, że niektórzy starsi agenci nie mogą przeprowadzić automatycznie samodzielnej aktualizacji i może to prowadzić do wystąpienia tego problemu. Zawsze używaj najnowszej wersji agenta gościa podczas tworzenia własnych obrazów.
* Niektóre programy administracyjne innych firm mogą wyłączać agenta gościa lub blokować dostęp do niektórych lokalizacji plików. Jeśli na maszynie wirtualnej zainstalowano programy innych firm, upewnij się, że agent znajduje się na liście wykluczeń.
* Niektóre ustawienia zapory lub grupy zabezpieczeń sieci (NSG) mogą blokować ruch sieciowy do i z agenta gościa.
* Niektóre listy kontroli dostępu (ACL) mogą uniemożliwiać dostęp do dysku.
* Brak miejsca na dysku może uniemożliwić prawidłowe działanie agenta gościa.

Domyślnie interfejs użytkownika usługi firmy Microsoft chroniącej przed złośliwym kodem jest wyłączony. Więcej informacji na temat jego włączania w razie potrzeby można znaleźć w temacie [Enabling Microsoft Antimalware User Interface on Azure Resource Manager VMs Post Deployment](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/09/enabling-microsoft-antimalware-user-interface-post-deployment/) (Włączanie interfejsu użytkownika usługi firmy Microsoft chroniącej przed złośliwym kodem po wdrożeniu maszyn wirtualnych usługi Azure Resource Manager).

## <a name="troubleshooting-problems-loading-the-dashboard"></a>Rozwiązywanie problemów z ładowaniem pulpitu nawigacyjnego

Jeśli masz problemy z ładowaniem pulpitu nawigacyjnego usługi Security Center, upewnij się, że użytkownik, który rejestruje subskrypcję usługi Security Center (tj. pierwszy użytkownik, który otworzył usługę Security Center za pomocą subskrypcji), oraz użytkownik, który chce włączyć kolekcję danych, mają uprawnienia *Właściciel* lub *Współautor* w subskrypcji. Od tej pory również użytkownicy z uprawnieniem *Czytelnik* w subskrypcji będą widzieć pulpit nawigacyjny/alerty/rekomendacje/zasady.

## <a name="contacting-microsoft-support"></a>Kontaktowanie się z pomocą techniczną firmy Microsoft

Niektóre problemy można zidentyfikować za pomocą wskazówek znajdujących się w tym artykule. Inne problemy można znaleźć udokumentowane na publicznym [Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter) usługi Security Center. Jeśli jednak potrzebujesz dalszych rozwiązań, możesz otworzyć nowe żądanie pomocy technicznej przy użyciu **witryny Azure portal,** jak pokazano poniżej:

![Pomoc techniczna firmy Microsoft](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)

## <a name="see-also"></a>Zobacz też

W tym dokumencie przedstawiono konfigurowanie zasad zabezpieczeń w Centrum zabezpieczeń Azure. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń Azure, zobacz następujące artykuły:

* [Przewodnik planowania i operacji usługi Azure Security Center](security-center-planning-and-operations-guide.md) — dowiedz się, jak zaplanować i zrozumieć zagadnienia dotyczące projektu, aby przyjąć usługę Azure Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — dowiedz się, jak monitorować kondycję zasobów platformy Azure
* [Zarządzanie alertami zabezpieczeń i odpowiadanie na nie w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — dowiedz się, jak zarządzać alertami zabezpieczeń i odpowiadać na nie
* [Informacje o alertach zabezpieczeń w usłudze Azure Security Center](security-center-alerts-type.md)
* [Samouczek: Reagowanie na zdarzenia związane z zabezpieczeniami](tutorial-security-incident.md)
* [Walidacja alertów w usłudze Azure Security Center](security-center-alert-validation.md)
* [Powiadomienia e-mail w usłudze Azure Security Center](security-center-provide-security-contact-details.md)
* [Obsługa zdarzeń naruszenia zabezpieczeń w usłudze Azure Security Center](security-center-incident.md)
* [Badanie zdarzeń i alertów w usłudze Azure Security Center](security-center-investigation.md)
* [Funkcje wykrywania usługi Azure Security Center](security-center-detection-capabilities.md)
* [Monitorowanie rozwiązań partnerskich za pomocą usługi Azure Security Center](security-center-partner-solutions.md) — dowiedz się, jak monitorować stan kondycji rozwiązań partnerskich.
* [Często zadawane pytania dotyczące usługi Azure Security Center](faq-general.md) — znajdowanie często zadawanych pytań dotyczących korzystania z usługi
* [Blog dotyczący zabezpieczeń platformy Azure](https://blogs.msdn.com/b/azuresecurity/) — znajdowanie wpisów w blogu dotyczących zabezpieczeń i zgodności z przepisami platformy Azure
