---
title: Ochrona danych osobowych dokumentu przy użyciu narzędzi do raportowania platformy Azure | Dokumentacja firmy Microsoft
description: Umożliwia Azure usługi reporting services, aby chronić prywatność danych osobowych w inicjatyw zgodne z ogólnych danych ochrony rozporządzenia (GDPR).
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.openlocfilehash: fc859f3aa34531133b7a21e36591b011c63d511c
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="document-protection-of-personal-data-with-azure-reporting-tools"></a>Ochrona danych osobowych dokumentu przy użyciu narzędzi do raportowania platformy Azure

W tym artykule będzie omawiać temat Użyj raportowania usług platformy Azure i technologie, aby lepiej chronić prywatność danych osobowych. Te informacje można użyć w celu zapewnienia przestrzegania przepisów branżowych i rządowych takich jak ogólne dane ochrony rozporządzenia (GDPR).
## <a name="scenario"></a>Scenariusz

Firma rejs dużych, siedzibą w Stanach Zjednoczonych rozwija operacjach oferowanie trasy w DS, Adriatyku i Bałtyckiego mórz, jak również brytyjskich. Aby ułatwić tych działań, ustawił mniejszych rejs wiersze na podstawie we Włoszech, Niemczech, Dania i Zjednoczone Królestwo

Przez firmę Microsoft Azure do przetwarzania i przechowywania danych firmowych. Dotyczy to również dane osobowe, takich jak nazwy, adresy, numery telefonów i informacje o karcie kredytowej z jej klientów globalnych. Zawiera także tradycyjnych informacje kadr, takie jak adresy, numery telefonów, numery identyfikacyjne podatku i inne informacje dotyczące pracowników firmy we wszystkich lokalizacjach. Wiersz rejs zachowuje również dużej bazy danych elementów członkowskich programu osób trzecich i lojalność zawierający informacje osobiste, aby śledzić relacje z bieżących i starszych klientów.

Pracownikom firmy dostępu do sieci w oddziałach firmy i podróży agentów na całym świecie mają dostęp do niektórych zasobów firmy.

## <a name="problem-statement"></a>Opis problemu

Firmy muszą chronić prywatność pracowników i klientów i danych osobowych za pośrednictwem korzystającego z platformy Azure zarządzania i zabezpieczeń strategii zabezpieczeń wielowarstwowy funkcji nakładanie ścisłą kontrolę dostępu do i przetwarzania danych osobowych i musi mieć możliwość wykazać jego środków ochronnych audytorów wewnętrznych i zewnętrznych.

## <a name="company-goal"></a>Celem firmy

W ramach jego strategii zabezpieczeń obrony zabezpieczeń jest celem firmy do śledzenia dostępu do i przetwarzania danych osobowych i upewnij się, że dokumentacji odpowiedniej ochrony danych osobowych w miejscu i działa.

## <a name="solutions"></a>Rozwiązania

Microsoft Azure oferuje kompleksowe monitorowanie, rejestrowania i diagnostyki narzędzia służące do śledzenia i rejestrowania działań i zdarzenia związane z dostęp i przetwarzania danych osobowych, geograficzne przepływu danych i innych firm dostępu do danych osobowych. Ponieważ bezpieczeństwo danych osobowych w chmurze jest udostępniony odpowiedzialność, firma Microsoft udostępnia również klientom:

- Szczegółowe informacje o jego własnej przetwarzania danych klientów

- Środki bezpieczeństwa, zarządzane przez firmę Microsoft

- Jak i gdzie wysyłania danych klientów

- Szczegółowe informacje o procesie przeglądami ochrony prywatności firmy Microsoft

### <a name="azure-active-directory"></a>Usługa Azure Active Directory

[Usługa Azure Active Directory](https://azure.microsoft.com/services/active-directory/) jest oparta na chmurze, wielodostępne katalogami i tożsamościami zarządzania usługi Microsoft. Usługa logowania i raporty dotyczące możliwości inspekcji zapewniają szczegółowe logowania i informacje o aktywności użycia aplikacji ułatwiających monitorowanie i zapewnić prawidłowy dostęp do danych osobowych pracowników i klientów.

Istnieją dwa typy raportów działania:

- [Inspekcji dzienników raporty aktywności](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) zapewniają szczegółowe rekord zadania działania systemu

- [Dziennika raport aktywności logowania](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) dowiesz się, kto wykonał każde działanie wymienione w raporcie inspekcji

Za pomocą dwóch ze sobą, można śledzić historię każdego zadania wykonywane i kto wykonał każdego. Obu typów raportów można dostosowywać i mogą być filtrowane.

#### <a name="how-do-i-access-the-audit-and-security-logs"></a>Jak uzyskać dostęp do dzienników inspekcji i zabezpieczeń?

Dzienniki inspekcji i zabezpieczeń są dostępne z portalu usługi Active Directory na trzy sposoby: poprzez **działania** sekcji (wybierz opcję **dzienniki inspekcji** lub **logowania**), lub z **użytkowników i grup** lub **aplikacje dla przedsiębiorstw** w obszarze **Zarządzaj** w usłudze Active Directory. Raporty można również uzyskać dostęp za pośrednictwem usługi Azure Active Directory raportowania interfejsu API. 

1. W portalu Azure wybierz **usługi Azure Active Directory.**

2. W **działania** zaznacz **dzienniki inspekcji.**

    ![](media/protection-personal-data-azure-reporting-tools/image001.png)

3. Dostosowanie widoku listy, klikając **kolumn** na pasku narzędzi.

4.  Wybierz element w widoku listy, aby wyświetlić wszystkie dostępne szczegółowe informacje dotyczące go.

    ![](media/protection-personal-data-azure-reporting-tools/image003.png)

Raportowanie na platformie Azure Active Directory obejmuje również dwa typy raportów zabezpieczeń **użytkownicy oflagowani ryzyka** i **ryzykowne logowania**, który ułatwia monitorowanie potencjalnych zagrożeń w środowisku platformy Azure.

Aby uzyskać więcej informacji na temat usługi raportowania patrz [raportowania usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)

Odwiedź stronę [raporty działania usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal#activity-reports) Aby uzyskać więcej szczegółowych informacji na temat raportów dostępnych w usłudze Azure Active Directory. Ta witryna zawiera więcej informacji o tym, jak można uzyskać dostępu do usługi [dzienniki inspekcji, raporty aktywności](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) i [raporty aktywności logowania](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) w portalu. Zawiera także informacje o [użytkownicy oflagowani ryzyka](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-user-at-risk) i [ryzykowne logowania](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-risky-sign-ins) raporty dotyczące zabezpieczeń.

Odwiedź stronę [inspekcji usługi Azure Active Directory dokumentacja interfejsu API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference) witryny, aby uzyskać więcej informacji na temat połączenia z katalogiem Azure raportowania programowo.

### <a name="log-analytics"></a>Log Analytics

[Dziennika analizy](https://azure.microsoft.com/services/log-analytics/) można [zbierania danych z monitora Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) do skorelowania go z innymi danymi i udostępnia dodatkowe analizy. Azure Monitor zbiera i analizuje dane monitorowania środowiska platformy Azure. 

Narzędzia analizy w analizy dzienników takie jak dziennik wyszukiwania, widoków i rozwiązań działać względem wszystkich zebranych danych, zapewniając scentralizowane analizy całego środowiska. Analiza dzienników można agregować i analizować dzienniki zdarzeń systemu Windows, dzienniki programu IIS i audyt dzienników systemowych, co pomaga wykrywać potencjalnych naruszeń dane osobowe, które można ujawniać dane osobowe nieautoryzowanym użytkownikom.

#### <a name="how-do-i-use-log-analytics"></a>Jak używać Log Analytics?

Analiza dzienników dostępne za pośrednictwem portalu OMS lub w portalu Azure, z dowolnej przeglądarki sieci web. Log Analytics obejmuje język kwerendy szybkie pobieranie i Konsolidowanie danych w obszarze roboczym analizy dzienników. Można utworzyć i zapisać dziennik wyszukiwania bezpośrednio analizowania danych w portalu.

Aby utworzyć obszaru roboczego analizy dzienników w portalu Azure, wykonaj następujące czynności:

1. Wybierz **analizy dzienników** z listy usług w witrynie Marketplace.

2. Wybierz **tworzenia,** następnie określ nazwę obszaru roboczego analizy dzienników, wybierz Twojej subskrypcji, grupy zasobów, lokalizacji i warstwę cenową.

3. Kliknij przycisk **OK** Aby wyświetlić listę obszarów roboczych.

4. Wybierz obszar roboczy, aby wyświetlić jego szczegóły.

    ![](media/protection-personal-data-azure-reporting-tools/image004.png)

Odwiedź stronę [dokumentacji analityka dziennika](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) Aby dowiedzieć się więcej o usłudze.

Odwiedź stronę [Rozpoczynanie pracy z obszaru roboczego analizy dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started) samouczkiem Utwórz obszar roboczy oceny i Poznaj podstawy sposobu korzystania z usługi.

Odwiedź następujących stron sieci web, aby uzyskać bardziej szczegółowe informacje na temat nawiązywania połączenia z analizy dzienników za pomocą dzienników opisane powyżej:

[Źródła danych dzienników zdarzeń systemu Windows w analizy dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)

[Rejestrowania przez usługi IIS w analizy dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs)

[SYSLOG źródeł danych w analizy dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-syslog)

### <a name="azure-monitorazure-activity-log"></a>Dziennik aktywności platformy Azure Monitor/Azure 

[Azure Monitor](https://azure.microsoft.com/services/monitor/) zapewnia na podstawowym poziomie infrastruktury metryki i dzienniki większość usług w systemie Microsoft Azure.
Monitorowania może pomóc w celu uzyskania szczegółowych informacji o aplikacji Azure. Azure Monitor polega na rozszerzenia diagnostyki Azure (Windows lub Linux) do zbierania większości dzienniki i metryki na poziomie aplikacji. [Dziennik aktywności platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) jest jeden z zasobów, można wyświetlić z monitorem Azure. Śledzi każdego wywołania interfejsu API i zapewnia szereg informacji na temat działań, które występują w [usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
Możesz wyszukać dziennik aktywności (wcześniej nazywane Operational lub dzienniki inspekcji) informacji o zasobie widziany przez infrastrukturę platformy Azure. 

Mimo że większość informacje zapisane w dzienniku aktywności dotyczy wydajności i kondycji usługi, dostępna jest również informacje dotyczące ochrony danych. Przy użyciu dziennika aktywności, można określić ", co, kto i kiedy" dla żadnego zapisu (PUT, POST, DELETE) podejmowaną w odniesieniu do zasobów w Twojej subskrypcji platformy Azure.

Na przykład zapewnia rekordu, gdy administrator usuwa sieciową grupę zabezpieczeń, który może mieć wpływ na ochronę danych osobowych. Wpisy dziennika aktywności są przechowywane w monitorze Azure przez 90 dni.

#### <a name="how-do-i-use-the-data-collected-by-azure-monitor"></a>Jak używać danych zbieranych przez Azure Monitor?

Istnieje wiele sposobów, aby użyć danych w dzienniku aktywności i innych zasobów Azure monitora.

- Można przesyłać strumieniowo dane do innych lokalizacji w rzeczywistych wiersza.

- Dane można przechowywać przez czas dłuższy niż domyślne, za pomocą [konto magazynu Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction) i ustawiania zasad przechowywania.

- Można visual danych grafiki i wykresy, za pomocą [portalu Azure](https://azure.microsoft.com/features/azure-portal/), [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), [Microsoft PowerBI](https://powerbi.microsoft.com/), lub narzędzi innych firm wizualizacji.

- Dane przy użyciu monitora interfejsu API REST Azure, polecenia interfejsu wiersza polecenia, można zbadać [PowerShell](https://docs.microsoft.com/powershell/) poleceń cmdlet lub zestawu .NET SDK.

Aby rozpocząć pracę z monitorem Azure, wybierz **wszystkie usługi** w portalu Azure.

1. Przewiń w dół do **Monitor** w **monitorowanie i zarządzanie** sekcji.

    ![](media/protection-personal-data-azure-reporting-tools/image005.png)

2.  Monitor zostanie otwarty w **dziennik aktywności** widoku.

    ![](media/protection-personal-data-azure-reporting-tools/image007.png)

Można utworzyć i zapisać zapytania dla wspólnych filtrów, a następnie przypiąć najważniejsze zapytania do pulpitu nawigacyjnego portalu, dzięki czemu zawsze będzie wiadomo, czy wystąpiły zdarzenia spełniające kryteria użytkownika.

1. Można filtrować widok grupy zasobów, timespan i Kategoria zdarzenia.

    ![](media/protection-personal-data-azure-reporting-tools/image008.png)

2. Następnie możesz przypinać zapytania do pulpitu nawigacyjnego portalu, klikając **numeru Pin** przycisku. Dzięki temu można utworzyć pojedyncze źródło informacji dla danych operacyjnych na usługi. Nazwa zapytania i liczbę wyników będzie wyświetlana na pulpicie nawigacyjnym.

Aby wyświetlić metryki dla wszystkich zasobów platformy Azure, skonfigurować ustawienia diagnostyki i alerty i wyszukaj w dzienniku umożliwia także monitora. Aby uzyskać więcej informacji na temat korzystania z monitora Azure i dziennik aktywności, zobacz [Rozpoczynanie pracy z monitorem Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

### <a name="azure-diagnostics"></a>Diagnostyka Azure 

Funkcja diagnostyki na platformie Azure umożliwia zbieranie danych z wielu źródeł. Dzienniki zdarzeń systemu Windows, które obejmują dziennika zabezpieczeń, może być szczególnie przydatne w śledzenia i dokumentowanie ochrony danych osobowych. W dzienniku zabezpieczeń śledzi zdarzenia sukcesów i niepowodzeń logowania, a także zmiany uprawnień, wykrywania wzorce wskazujące niektóre rodzaje ataków, zmiany zasad związane z zabezpieczeniami, zmian członkostwa w grupach zabezpieczeń i wiele innych.

Na przykład 4695 identyfikator zdarzenia powiadamia o próba unprotection podlegającą inspekcji chronionych danych. Dotyczy to tylko do ochrony danych interfejsu API (DPAPI), który pomaga chronić dane, takie jak klucze prywatne, przechowywane poświadczenia i innych informacji poufnych.

#### <a name="how-do-i-enable-the-diagnostics-extension-for-windows-vms"></a>Jak włączyć rozszerzenia diagnostyki dla maszyn wirtualnych systemu Windows?

Za pomocą programu PowerShell można włączyć rozszerzenia maszyny Wirtualnej systemu Windows, aby zbierać dane dziennika dla diagnostyki. Czynności w ten sposób zależeć na model wdrażania, których używasz (Resource Manager lub Classic). Aby włączyć rozszerzenie diagnostyki na istniejącej maszyny Wirtualnej, który został utworzony za pośrednictwem modelu wdrażania usługi Resource Manager, można użyć [polecenia cmdlet programu PowerShell Set-AzureRMVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmdiagnosticsextension?view=azurermps-4.3.1).

   ![](media/protection-personal-data-azure-reporting-tools/image009.png)

*\$diagnosticsconfig_path* jest ścieżką do pliku, który zawiera konfigurację diagnostyki w kodzie XML. Aby uzyskać szczegółowe instrukcje dotyczące włączania diagnostyki Azure na maszynie Wirtualnej, zobacz [Użyj programu PowerShell w celu włączenia diagnostyki Azure na maszynie wirtualnej z systemem Windows.](https://docs.microsoft.com/azure/virtual-machines/windows/ps-extensions-diagnostics)

Rozszerzenia diagnostyki Azure można przenieść zebranych danych do konta magazynu platformy Azure lub wysyłają je do usług, takich jak usługi Application Insights. Dane można następnie użyć do przeprowadzania inspekcji.

#### <a name="how-do-i-store-and-view-diagnostic-data"></a>Sposób przechowywania i wyświetlać dane diagnostyczne?

Należy pamiętać, że dane diagnostyczne nie są trwale przechowywane, chyba że transfer emulatora magazynu Microsoft Azure lub do magazynu Azure. Aby przechowywać i przeglądać dane diagnostyczne w usłudze Azure Storage, wykonaj następujące kroki:

1. Określ konto magazynu w pliku ServiceConfiguration.cscfg pliku. Diagnostyka Azure można używać usługi obiektów Blob lub usługi tabel, w zależności od typu danych. Dzienniki zdarzeń systemu Windows są przechowywane w formacie tabeli.

2. Transfer danych. Można zażądać na przesyłanie danych diagnostycznych za pomocą pliku konfiguracji. 2.4 zestawu SDK oraz poprzedniej można również ustawić żądania programowo.

3. Wyświetl dane przy użyciu [Eksploratora usługi Storage Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer), [Eksploratora serwera](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) w programie Visual Studio lub [Menedżera diagnostyki Azure](https://www.cerebrata.com/products/azure-diagnostics-manager) w usłudze Azure Management Studio.

Aby uzyskać więcej informacji na temat sposobu wykonywania każdego z tych kroków, zobacz [magazynu i widok danych diagnostycznych w usłudze Azure Storage.](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)

### <a name="azure-storage-analytics"></a>Analityka usługi Azure Storage 

Analityka magazynu rejestruje szczegółowe informacje na temat udane i nieudane żądania do usługi magazynu. Te informacje mogą służyć do monitorowania poszczególnych żądań, które mogą pomóc w dokumentowanie dostęp do osobistych danych przechowywanych w usłudze. Jednak rejestrowanie analityka magazynu nie jest włączone domyślnie dla konta magazynu. Możesz je włączyć w portalu Azure.

#### <a name="how-do-i-configure-monitoring-for-a-storage-account"></a>Jak skonfigurować monitorowanie dla konta magazynu

Aby skonfigurować monitorowanie dla konta magazynu, wykonaj następujące czynności:

1. Wybierz **kont magazynu** w portalu Azure, a następnie wybierz nazwę konta, który chcesz monitorować.

    ![](media/protection-personal-data-azure-reporting-tools/image011.png)

2. W **monitorowanie** zaznacz **diagnostyki.**

3.  Wybierz **typu** metryki danych, który chcesz monitorować dla każdej usługi (pliku Blob, tabeli,). Aby nakazać magazynu Azure do zapisywania dzienników diagnostycznych do odczytu, zapisu i żądań delete służących do obiektu blob, tabel i kolejek usługi, wybierz **dzienniki obiektu Blob, tabela dzienniki** i **kolejka dzienniki.**

    ![](media/protection-personal-data-azure-reporting-tools/image013.png)

4. Za pomocą suwaka, u dołu, ustaw **przechowywania** zasad w dniach (wartość 1 – 365). Domyślą opcją jest siedem dni.

5. Wybierz **zapisać** Aby zastosować ustawienia konfiguracji.

Wpisy dziennika rejestrowania magazynu zawiera następujące informacje dotyczące poszczególnych żądań:

- Czas informacje takie jak czas rozpoczęcia, czas oczekiwania na trasie i opóźnienie serwera.

- Wpisz szczegóły operacji magazynu, takich jak wykonać operację, klucz obiektu magazynu klienta jest dostęp do powodzenie lub Niepowodzenie i zwrócił kod stanu HTTP do klienta.

- Szczegóły uwierzytelniania, takie jak typ uwierzytelniania używany przez klienta.

- Współbieżność informacje takie jak wartość ETag i sygnatura czasowa ostatniej modyfikacji.

- Rozmiary komunikatów żądań i odpowiedzi.

Aby uzyskać szczegółowe instrukcje dotyczące sposobu włączania rejestrowania analityka magazynu, zobacz [monitorować konto magazynu w portalu Azure.](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

### <a name="azure-security-center"></a>Azure Security Center 

[Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/) monitoruje stan zabezpieczeń zasobów platformy Azure, aby zapobiec i wykrywać zagrożenia i podano zalecenia dotyczące odpowiada. Zapewnia kilka sposobów dokumentu z środków bezpieczeństwa, które chronić prywatność danych osobowych.

Monitorowanie kondycji zabezpieczeń pomaga zapewnić zgodność z zasadami zabezpieczeń. Monitorowanie zabezpieczeń jest aktywnej strategii przeprowadzania inspekcji zasobów w celu zidentyfikowania systemów, które nie spełniają standardów organizacji lub najlepszych rozwiązań. Umożliwia monitorowanie stanu zabezpieczeń następujących zasobów:

- Obliczeń (maszyny wirtualne i usługi w chmurze)

- Sieci (sieci wirtualne)

- Magazyn i dane (serwera i bazy danych inspekcji i wykrywania zagrożeń, funkcji TDE, szyfrowanie magazynu)

- Aplikacje (potencjalnych problemów z zabezpieczeniami)

Problemy z zabezpieczeniami w dowolnej z tych kategorii może stanowić zagrożenie dla prywatności danych osobowych.

#### <a name="how-do-i-view-the-security-state-of-my-azure-resources"></a>Jak wyświetlić stan zabezpieczeń Zasoby platformy Azure?

Usługa Security Center okresowo analizuje stan zabezpieczeń zasobów platformy Azure. Możesz wyświetlić wszelkich potencjalnych luk w zabezpieczeniach w **zapobiegania** pulpitu nawigacyjnego.

   ![](media/protection-personal-data-azure-reporting-tools/image014.png)

1. W **zapobiegania** zaznacz **obliczeniowe** kafelka. W tym miejscu zobaczysz **Przegląd,** wraz z **maszyn wirtualnych** lista wszystkich maszyn wirtualnych i ich stanów zabezpieczeń i **usługi w chmurze** lista ról sieci web i proces roboczy monitorowane przez Centrum zabezpieczeń.

2. Na **omówienie** karcie, drugie zalecenie, aby wyświetlić więcej informacji.

3. Na **maszyn wirtualnych** , a następnie wybierz maszynę Wirtualną w celu wyświetlenia dodatkowych szczegółów.

Po włączeniu funkcji zbierania danych w Centrum zabezpieczeń Azure, programu Microsoft Monitoring Agent jest udostępniany automatycznie na wszystkich istniejących i nowych obsługiwanych maszyn wirtualnych, które są wdrożone. Dane zbierane z tego agenta są przechowywane w każdej istniejącej [analizy dzienników](https://azure.microsoft.com/services/log-analytics/) obszaru roboczego skojarzonego z subskrypcją lub nowego obszaru roboczego.

[Raporty analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) są dostarczane przez Centrum zabezpieczeń. Ta funkcja pozwala przydatne informacje dotyczące wykrycia tożsamości osoby atakującej, cele, bieżących i historycznych ataku kampanii i taktyk, narzędzia i procedur. Środki zaradcze i korygowania informacji jest również uwzględniony.

Głównym celem tych raportów zagrożenie jest na reagowanie na bezpośredniego zagrożenia i pomocy środki później w celu złagodzenia problem. Informacje w raportach również mogą być przydatne, gdy dokument Twojej odpowiedzi na zdarzenia do raportowania i inspekcji.

Raporty analizy zagrożeń są prezentowane w. Format PDF, dostępne za pośrednictwem łącza w **raporty** pole **podejrzane proces wykonywany** bloku dla każdego alertu zabezpieczeń w Centrum zabezpieczeń Azure.

Aby uzyskać więcej informacji na temat sposobu wyświetlania i użyć raportu analizy zagrożeń, zobacz [raport analizy zagrożeń z Centrum zabezpieczeń Azure.](https://docs.microsoft.com/azure/security-center/security-center-threat-report)

## <a name="next-steps"></a>Następne kroki:

[Wprowadzenie do korzystania z usługi Azure Active Directory raportowania interfejsu API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

[Co to jest usługa Log Analytics?](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)

[Omówienie monitorowania na platformie Microsoft Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

[Wprowadzenie do dziennika aktywności platformy Azure (klip wideo)](https://azure.microsoft.com/resources/videos/intro-activity-log/)
