---
title: Azure Integration Services — integracja proste Enterprise
description: Architektura referencyjna przedstawiająca sposób Implementowanie wzorca integracji przedsiębiorstwa proste aplikacje logiki platformy Azure i zarządzanie interfejsami API Azure
author: mattfarm
manager: jonfan
editor: ''
services: logic-apps api-management
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/15/2018
ms.author: LADocs; estfan
ms.openlocfilehash: 17f591a470bf65d3c9365bca9c5ae2d5a6c9574f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232435"
---
# <a name="simple-enterprise-integration---reference-architecture"></a>Łatwa integracja przedsiębiorstwa — architektura referencyjna

## <a name="overview"></a>Przegląd

Informacje o architekturze wzorcowej zawiera zestaw sprawdzonych rozwiązań dotyczących integracji aplikacji, która używa usług Azure Integration Services. Taka architektura może służyć jako tej podstawie wzorców innej aplikacji wymagających interfejsy API protokołu HTTP, przepływu pracy i aranżacji.

*Istnieje wiele aplikacji możliwych technologii integracji z prostego punktu do punktu aplikacji usługi service bus pełne przedsiębiorstwa. Ta seria architektura określa części komponentów wielokrotnego użytku do tworzenia aplikacji integracji — architektów należy wziąć pod uwagę składniki, których potrzebują dla aplikacji i infrastruktury.*

   ![Diagram architektury - integracji przedsiębiorstwa proste](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

## <a name="architecture"></a>Architektura

Architektura zawiera następujące składniki:

- Grupy zasobów. Grupa zasobów jest kontenerem logicznym dla zasobów platformy Azure.
- Zarządzanie interfejsami API Azure. Azure API Management jest w pełni zarządzana platforma publikowania, zabezpieczanie i przekształcanie interfejsy API protokołu HTTP.
- Azure API Management Developer Portal. Każde wystąpienie usługi Azure API Management jest dostarczany z Developer Portal zapewniające dostęp do dokumentacji, przykłady kodu i możliwość testowania interfejsu API.
- Aplikacje logiki platformy Azure. Logic Apps to niekorzystającą platforma do tworzenia przepływów pracy przedsiębiorstwa i integracji.
- Łączniki. Łączniki są używane przez aplikacje logiki do łączenia się najczęściej używanymi usługami. Logic Apps ma już setki różnych łączniki, ale ich można również tworzyć przy użyciu niestandardowego łącznika.
- Adres IP. Usługa Azure API Management ma publiczny adres IP i nazwy domeny. Nazwa domeny jest poddomeną azure api.net, takich jak contoso. Azure api.net. Logic Apps również ma publiczny adres IP; Jednak w ramach tej architektury możemy ograniczyć dostęp do wywołania punktów końcowych aplikacji logiki do tylko Zarządzanie adresami IP z interfejsu API (dla zabezpieczeń).
- Usługa DNS platformy Azure. Usługa DNS platformy Azure to Usługa hostingu domen DNS, rozpoznawania nazw przy użyciu infrastruktury Microsoft Azure. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure. Aby użyć niestandardowej nazwy domeny (np. contoso.com) należy utworzyć rekordy DNS, które mapują nazwy domeny niestandardowej z adresem IP. Aby uzyskać więcej informacji zobacz Konfigurowanie niestandardowej nazwy domeny w usłudze Azure API Management.
- Azure Active Directory (Azure AD). Użyj usługi Azure AD lub innego dostawcy tożsamości w celu uwierzytelniania. Usługi Azure AD umożliwia uwierzytelnianie do punktów końcowych dostępu do interfejsu API (przez przekazanie żetonu Web JSON dla interfejsu API zarządzania w celu zweryfikowania) i może uniemożliwić dostęp do interfejsu API zarządzania portalu dla deweloperów (tylko warstwy standardowa i Premium).

Taka architektura ma niektórych podstawowych wzorców w celu jego działania:

1. Interfejsy API protokołu HTTP są publikowane za pośrednictwem interfejsu API zarządzania portalu dla deweloperów, co umożliwia deweloperom istniejącej wewnętrznej bazy danych (albo wewnętrzne dla organizacji, zewnętrzne lub obie) do integracji wywołań do tych interfejsów API w aplikacjach.
2. Złożone interfejsy API są tworzone przy użyciu logiki aplikacji. organizowanie wywołania do systemów SAAS, usług platformy Azure i wszystkie interfejsy API opublikowane na zarządzanie interfejsami API. Aplikacje logiki są też publikowane za pośrednictwem interfejsu API zarządzania Developer Portal.
3. Aplikacje uzyskania tokenu zabezpieczającego OAuth 2.0 niezbędne do uzyskania dostępu do interfejsu API przy użyciu usługi Azure Active Directory.
4. Zarządzanie interfejsami API Azure weryfikuje token zabezpieczający, a następnie przekazuje żądanie do aplikacji interfejsu API/logiki wewnętrznej bazy danych.

## <a name="recommendations"></a>Zalecenia

Wymagania rzeczywiste mogą różnić się od dotyczących opisanej tu architektury. Użyj zaleceń w tej sekcji jako punktu wyjścia.

### <a name="azure-api-management-tier"></a>Azure API Management warstwy

Użyj podstawowego, warstwy standardowa lub Premium, ponieważ oferują produkcyjnej umowy dotyczącej poziomu usług i obsługi skalowania w poziomie w obrębie regionu Azure (liczba jednostek jest zależna od warstwy). Warstwy Premium obsługuje również skalowalnego w poziomie w różnych regionach platformy Azure. Zestaw funkcji, podstawowa wybranej na poziom przepływności wymaganej warstwy. Aby uzyskać więcej informacji, zobacz [cennik usługi API Management](https://azure.microsoft.com/pricing/details/api-management/).

Dla wszystkich wystąpień usługi API Management są naliczane, gdy działają. Jeśli skalowane w górę i nie wymagają tego poziomu wydajności cały czas, należy rozważyć skorzystanie z godzinowe rozliczeń API Management i skali w dół.

### <a name="logic-apps-pricing"></a>Aplikacje logiki ceny

Logic Apps działa jako [niekorzystającą](logic-apps-serverless-overview.md) modelu — karta jest obliczana oparte na wykonanie akcji i łącznik. Zobacz [Logic Apps cennik](https://azure.microsoft.com/pricing/details/logic-apps/) Aby uzyskać więcej informacji. Obecnie nie istnieją żadne uwagi dotyczące warstwy dla usługi Logic Apps.

### <a name="logic-apps-for-asynchronous-api-calls"></a>Logic Apps dla asynchronicznego wywołania interfejsu API

Logic Apps działa najlepiej w scenariusze, które nie wymagają małych opóźnieniach — np. asynchroniczne lub rozdzielana długo działa interfejsu API. Jeśli wymagana jest małe opóźnienia (np. wywołanie, która blokuje interfejsu użytkownika) firma Microsoft zaleca wykonania tej operacji, przy użyciu różnych technologii, np. usługi Azure Functions lub wdrożyć przy użyciu usługi aplikacji interfejsu API sieci Web lub interfejsu API. Nadal zaleca się ten interfejs API fronted przy użyciu interfejsu API zarządzania konsumentom interfejsu API.

### <a name="region"></a>Region

Dostarczać interfejs API zarządzania i aplikacji logiki w tym samym regionie, aby zminimalizować opóźnienie sieci. W większości przypadków należy wybrać region najbliżej Twoich użytkowników.

Grupa zasobów ma również region, który określa, gdzie są przechowywane metadane wdrażania, i w którym szablon wdrożenia jest przeprowadzana z. Umieść grupę zasobów i jej zasoby w tym samym regionie. Może to zwiększyć dostępność podczas wdrażania.

## <a name="scalability-considerations"></a>Zagadnienia dotyczące skalowalności

Administratorzy usługi API Management należy dodać [buforowanie zasad](../api-management/api-management-howto-cache.md) w razie potrzeby zwiększyć skalowalność usługi i zmniejszyć obciążenie swoje usługi wewnętrznej bazy danych.

Azure API Management podstawowa, standardowa i Premium warstw można skalować w poziomie z w regionie Azure, aby oferuje większej pojemności. Administratorzy mogą używać metryki pojemności w menu metryk do analizowania użycie usługi i skalowanie w górę lub skali odpowiednio.

Zalecenia dotyczące skalowania usługi Zarządzanie interfejsami API:

- Skalowanie należy wziąć pod uwagę wzorce ruchu — klienci z bardziej volatile wzorce ruchu będzie mieć większą konieczność zwiększenia pojemności.
- Spójne o pojemności większej niż 66% może wskazywać na potrzebę skalowanie w górę.
- Spójne pojemności niż 20% może wskazywać na możliwość skalowania w dół.
- Zawsze zaleca się załadowanie testów z reprezentatywny obciążenia usługi Zarządzanie interfejsami API przed włączeniem w środowisku produkcyjnym.

Usługi w warstwie Premium można skalować w poziomie w różnych regionach platformy Azure. Wdrażania w ten sposób klienci uzyska wyższej SLA (99,95%, a nie na poziomie 99,9%) i umożliwia obsługę usług się w pobliżu użytkowników w wielu regionach.

Aplikacje logiki modelu niekorzystającą oznacza to, że administratorzy nie muszą wprowadzić dodatkowe brany pod uwagę w przypadku skalowalności usługi; Usługa automatycznie skaluje do zapotrzebowania.

## <a name="availability-considerations"></a>Zagadnienia dotyczące dostępności

W momencie pisania umowę dotyczącą poziomu usług (SLA) dla usługi Azure API Management jest 99,9% w warstwach podstawowa, standardowa i Premium. Konfiguracje warstwy Premium z wdrożeniem co najmniej jedną jednostkę w co najmniej dwóch regionach mają SLA 99,95%.

W momencie pisania umowę dotyczącą poziomu usług (SLA) dla usługi Azure Logic Apps jest 99,9%.

### <a name="backups"></a>Tworzenie kopii zapasowych

Konfiguracja usługi Azure API Management powinien być [regularnie wykonywana kopia zapasowa](../api-management/api-management-howto-disaster-recovery-backup-restore.md) (odpowiednio na podstawie prawidłowości zmian), a pliki kopii zapasowej przechowywanego w lokalizacji lub inny, na którym działa usługa Region platformy Azure. Klientów można następnie wybrać jedną z dwóch opcji dla ich strategii odzyskiwania po awarii:

1. W przypadku odzyskiwania po awarii nowe wystąpienie interfejsu API zarządzania zostanie zainicjowana przywróceniu kopii zapasowej do niego i rekordy DNS są repointed.
2. Klienci zachować kopię pasywną ich usługi w innym regionie Azure (ponoszenia dodatkowych kosztów) kopii zapasowych regularnie zostaną przywrócone do niego. W przypadku odzyskiwania po awarii tylko rekordy DNS muszą być repointed przywrócenia usługi.

Logic Apps można ponownie utworzyć bardzo szybko i będą niekorzystającą, ich kopię zapasową przez zapisanie kopii skojarzone szablonu usługi Azure Resource Manager. Mogą one zostać zapisane do źródła formantu/zintegrowane klientów ciągłej integracji/ciągły proces wdrażania (CI/CD).

Logikę aplikacji, które zostały opublikowane za pośrednictwem interfejsu API zarządzania, należy ich lokalizacje zaktualizowane powinien przechodzą do centrum danych. Można to zrobić za pomocą prostego skryptu PowerShell do aktualizacji właściwości zaplecza interfejsu API.

## <a name="manageability-considerations"></a>Zagadnienia dotyczące możliwości zarządzania

Tworzenie oddzielnych grup zasobów w środowisku produkcyjnym, projektowanie i testowanie środowisk. Ułatwia to zarządzanie wdrożeniami, usuwanie wdrożeń testowych i przypisywanie praw dostępu.
Podczas przydzielania zasobów do grup zasobów rozważ następujące kwestie:

- Cykl życia. Ogólnie rzecz biorąc, umieszczaj zasoby z tym samym cyklem życia w tej samej grupie zasobów.
- Dostęp. Można użyć [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md) (RBAC), aby zastosować zasady dostępu do zasobów w grupie.
- Rozliczenia. Możesz wyświetlać zestawienia kosztów dla grupy zasobów.
- Warstwa cenowa dla interfejsu API zarządzania — zaleca się używanie deweloperów warstwy dla środowisk projektowania i testowania. Do produkcji wstępnej zalecamy wdrażanie repliki środowiska produkcyjnego, testów i zamykanie do minimum kosztów.

Aby uzyskać więcej informacji, zobacz [grupy zasobów](../azure-resource-manager/resource-group-overview.md) omówienie.

### <a name="deployment"></a>Wdrożenie

Firma Microsoft zaleca użycie [szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) do wdrożenia zarówno usługi Azure API Management i usługi Azure Logic Apps. Szablony ułatwiają Automatyzowanie wdrożeń za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure (CLI).

Zaleca się umieszczanie w ich własnych oddzielne szablony Menedżera zasobów Azure API Management i wszystkich poszczególnych aplikacji logiki. Pozwoli to przechowywania ich w systemów kontroli źródła. Szablony te można następnie wdrożyć razem lub oddzielnie w ramach procesu wdrażania ciągłej integracji/ciągłego (CI/CD).

### <a name="versions"></a>Wersje

Zawsze, gdy konfiguracja wybrać aplikacja logiki (lub wdrażania aktualizacji za pomocą szablonu usługi Resource Manager), kopię tej wersji są przechowywane dla Twojej wygody (wszystkie wersje, które mają Historia uruchomień zostaną zachowane). Te wersje służy do śledzenia zmian historycznych i również podwyższyć poziom wersji jako bieżącej konfiguracji aplikacji logiki; ten sposób oznacza użytkownik może efektywnie wycofywania aplikacji logiki, na przykład.

Zarządzanie interfejsami API ma dwa różne (ale bezpłatnych) [pojęcia versioning](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/):

- Wersje zapewnianie przez użytkowników interfejsu API wybór interfejsu API może korzystać z nich na podstawie swoich potrzeb (np. v1, v2 lub w wersji beta, produkcyjnego).
- Poprawki, pozwalając administratorom interfejsu API bezpiecznie zmian do interfejsu API i wdrażać je dla użytkowników z opcjonalny komentarz.

W kontekście wdrażania — wersje interfejsu API zarządzania należy traktować jako sposobu zmiany bezpiecznie, historia zmian i uświadomić konsumentom interfejsu API tych zmian. Zmiany można utworzyć w środowisku projektowym i wdrażać między innymi środowiskami przy użyciu szablonów usługi Resource Manager.

Podczas gdy poprawek można wykorzystywane do testowania interfejsu API, zanim staje się "current" i dostępny dla użytkowników, nie zaleca się przy użyciu ten mechanizm obciążenia i integracji testowania — osobne testu lub środowiskach produkcji wstępnej należy użyć.

### <a name="configuration"></a>Konfigurowanie

Nigdy nie Szukaj hasła, klucze dostępu lub parametry połączenia w celu kontroli źródła. Jeśli jest to konieczne, umożliwia odpowiednie techniki wdrażania i zabezpieczyć te wartości. 

W aplikacjach logiki żadnych poufnych wartości w ramach aplikacji logiki, (do którego nie można utworzyć w formularzu połączenia) należy przechowywane w usłudze Azure Key Vault i określone z szablonem usługi Resource Manager. Zalecamy również przy użyciu parametrów szablonu wdrożenia oraz pliki parametrów dla każdego środowiska. Więcej pomocy na temat [zabezpieczanie parametry i dane wejściowe w przepływie pracy](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

W usłudze API Management kluczy tajnych są zarządzane przy użyciu obiektów o nazwie właściwości wartości o nazwie. Je bezpiecznie przechowywać wartości, które są dostępne w zasadach zarządzania interfejsem API. Zobacz temat jak [Zarządzanie kluczy tajnych w usłudze API Management](../api-management/api-management-howto-properties.md).

### <a name="diagnostics-and-monitoring"></a>Diagnostyka i monitorowanie

Zarówno [zarządzanie interfejsami API](../api-management/api-management-howto-use-azure-monitor.md) i [Logic Apps](logic-apps-monitor-your-logic-apps.md) obsługuje operacyjne monitorowania za pomocą [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Azure Monitor jest domyślnie włączona i przekazuje informacje na podstawie różnych metryk skonfigurowane dla każdej usługi.

Ponadto dostępne są dodatkowe opcje dla każdej usługi:

- Dzienniki aplikacji logiki mogą być wysyłane do [analizy dzienników](logic-apps-monitor-your-logic-apps-oms.md) głębiej analizy i dashboarding.
- Zarządzanie interfejsami API obsługuje konfigurowanie usługi Application Insights dla deweloperów Ops monitorowania.
- Zarządzanie interfejsami API obsługuje [Power BI rozwiązania szablonu niestandardowego interfejsu API analizy](http://aka.ms/apimpbi). Ten szablon rozwiązania umożliwia klientom tworzenie własnych rozwiązań niestandardowych analytics z raportów dostępnych w usłudze Power BI dla użytkowników biznesowych.

## <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami

W tej sekcji przedstawiono zagadnienia dotyczące zabezpieczeń, które są specyficzne dla usług Azure opisano w tym artykule wdrożone w architekturze zgodnie z opisem. Nie jest to pełna lista najlepszych rozwiązań dotyczących zabezpieczeń.

- Użyj kontroli dostępu opartej na rolach (RBAC), aby zapewnić odpowiedni poziom dostępu dla użytkowników.
- Zabezpiecz publiczne punkty końcowe interfejsu API w usłudze API Management przy użyciu IDConnect OAuth lub Open. W tym celu Konfigurowanie dostawcy tożsamości i Dodawanie zasad walidacji tokenu JWT.
- Nawiązywanie połączenia usługi wewnętrznej bazy danych z interfejsu API zarządzania przy użyciu wzajemnymi certyfikatami
- Secure HTTP na podstawie wyzwalacza Logic Apps, tworząc dozwolonych adresów IP wskazuje adres IP zarządzanie interfejsami API. Zapobiega to wywołanie aplikacji logiki z publicznego Internetu bez pośrednictwa usługi API Management.

Informacje o architekturze wzorcowej przedstawiono sposób kompilowania platforma integracji przedsiębiorstwa proste, za pomocą usług integracji Azure.

## <a name="next-steps"></a>Kolejne kroki

* [Integracja przedsiębiorstwa z kolejki i zdarzenia](logic-apps-architectures-enterprise-integration-with-queues-events.md)
