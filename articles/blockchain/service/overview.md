---
title: Omówienie usługi Azure Blockchain
description: Omówienie usługi Azure Blockchain
ms.date: 03/30/2020
ms.topic: overview
ms.reviewer: ravastra
ms.openlocfilehash: 821bac0da13209e5126f5bab109aa0895ade840a
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529156"
---
# <a name="what-is-azure-blockchain-service"></a>Co to jest usługa Azure Blockchain Service?

Usługa Azure Blockchain to w pełni zarządzana usługa księgi, która umożliwia użytkownikom rozwój i obsługę sieci blockchain na dużą skalę na platformie Azure. Usługa Azure Blockchain Service zapewnia ujednolicenie kontroli zarówno zarządzania infrastrukturą, jak i zarządzania siecią łańcucha bloków:

* Proste wdrażanie sieci i operacje
* Wbudowane zarządzanie konsorcjum
* Opracowywanie inteligentnych kontraktów za pomocą znanych narzędzi programistycznych

Usługa Azure Blockchain jest przeznaczona do obsługi wielu protokołów księgi. Obecnie zapewnia wsparcie dla księgi [Kworum](https://www.goquorum.com/) Ethereum przy użyciu mechanizmu konsensusu [Istanbul Bizantine Fault Tolerance (IBFT).](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus)

Powyższe możliwości prawie w ogóle nie wymagają administracji i są udostępniane bez żadnych dodatkowych kosztów. Możesz skupić się na tworzeniu aplikacji i logice biznesowej, zamiast przydzielać czas i zasoby do zarządzania maszynami wirtualnymi i infrastrukturą. Ponadto możesz nadal rozwijać aplikację za pomocą narzędzi open source i wybranej platformy, aby dostarczać rozwiązania bez konieczności uczenia się nowych umiejętności.

## <a name="network-deployment-and-operations"></a>Wdrażanie sieci i operacje

Wdrażanie usługi Azure Blockchain service odbywa się za pośrednictwem witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub za pomocą kodu programu Visual Studio przy użyciu rozszerzenia Azure Blockchain. Wdrażanie jest uproszczone, w tym inicjowanie obsługi administracyjnej węzłów transakcji i walidatora, sieci wirtualne platformy Azure w celu izolacji zabezpieczeń, a także magazyn zarządzany przez usługę.  Ponadto podczas wdrażania nowego elementu członkowskiego łańcucha bloków użytkownicy tworzą również konsorcjum lub dołączają do niego.  Konsorcja umożliwiają wielu stronom w różnych subskrypcjach platformy Azure bezpieczną komunikację ze sobą za pośrednictwem udostępnionego łańcucha bloków.  To uproszczone wdrożenie zmniejsza wdrażanie sieci blockchain z dni do minut.

### <a name="performance-and-service-tiers"></a>Poziomy wydajności i usług

Usługa Azure Blockchain service oferuje dwie warstwy usług: *Podstawowa* i *Standardowa.* Każda warstwa oferuje różną wydajność i możliwości do obsługi lekkich obciążeń programistów i testowania do masowo skalowane wdrożenia łańcucha bloków produkcyjnych. Użyj warstwy *Podstawowa* do tworzenia, testowania i weryfikacji pojęć. Użyj warstwy *Standardowa* dla wdrożeń na poziomie produkcyjnym. Obie warstwy obejmują co najmniej jeden węzeł transakcji i jeden węzeł walidatora (Podstawowy) lub dwa węzły walidatora (Standard). 

![Warstwy cenowe](./media/overview/pricing-tiers.png)

Oprócz oferowania dwóch węzłów walidatora warstwa *Standardowa* zawiera dwa *pola wirtualne* dla każdej transakcji i węzła walidatora, podczas gdy warstwa *Basic* oferuje konfigurację 1 vCore.  Oferując 2 pola wirtualne dla węzłów transakcji i walidatora, 1 rystrój wirtualny może być dedykowany do księgi kworum, podczas gdy pozostałe 1 rystrój wirtualny może być używany do innych usług związanych z infrastrukturą, zapewniając optymalną wydajność dla obciążeń łańcucha bloków produkcyjnych. Aby uzyskać więcej informacji na temat szczegółów cen, zobacz [Cennik usługi Azure Blockchain Service](https://azure.microsoft.com/pricing/details/blockchain-service).

### <a name="security-and-maintenance"></a>Bezpieczeństwo i konserwacja

Po zainicjowaniu obsługi administracyjnej pierwszego członka łańcucha bloków masz możliwość dodawania dodatkowych węzłów transakcji do członka.  Domyślnie węzły transakcji są zabezpieczone za pomocą reguł zapory i wymagają konfiguracji dostępu.  Ponadto wszystkie węzły transakcji szyfrują dane w ruchu za pośrednictwem protokołu TLS.  Istnieje wiele opcji zabezpieczania dostępu do węzłów transakcji, w tym reguły zapory, uwierzytelnianie podstawowe, klucze dostępu i integracja usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [konfigurowanie węzłów transakcji](configure-transaction-nodes.md) i [konfigurowanie dostępu usługi Azure Active Directory](configure-aad.md).

Jako usługa zarządzana usługa Azure Blockchain Service zapewnia, że węzły członka łańcucha bloków są załatane za pomocą najnowszych aktualizacji systemu operacyjnego hosta i stosu oprogramowania księgi, skonfigurowanych pod kątem wysokiej dostępności (tylko warstwa standardowa), eliminując większość devops wymaganych dla tradycyjnych węzłów łańcucha bloków IaaS.  Aby uzyskać więcej informacji na temat poprawek i aktualizacji, zobacz [obsługiwane wersje księgi usługi Azure Blockchain Service](ledger-versions.md).

### <a name="monitoring-and-logging"></a>Monitorowanie i rejestrowanie

Ponadto usługa Azure Blockchain Service zapewnia zaawansowane metryki za pośrednictwem usługi Azure Monitor, zapewniając wgląd w użycie procesora CPU, pamięci i pamięci węzłów.  Usługa Azure Monitor zapewnia również przydatne informacje na temat aktywności sieci blockchain, takich jak transakcje i bloki wydobywane, głębokość kolejki transakcji i aktywne połączenia.  Metryki można dostosować, aby zapewnić widoki na szczegółowe informacje, które są ważne dla aplikacji blockchain.  Ponadto progi można zdefiniować za pomocą alertów umożliwiających użytkownikom wyzwalanie akcji, takich jak wysyłanie wiadomości e-mail lub wiadomości tekstowych, uruchamianie aplikacji logiki, funkcja platformy Azure lub wysyłanie do niestandardowego elementu webhook.

![Metryki](./media/overview/metrics.png)

Za pośrednictwem usługi Azure Log Analytics użytkownicy mogą wyświetlać dzienniki związane z księgą kworum lub inne ważne informacje, takie jak próby połączeń z węzłami transakcji.

## <a name="built-in-consortium-management"></a>Wbudowane zarządzanie konsorcjum

Wdrażając pierwszego członka łańcucha bloków, dołączasz lub tworzysz nowe konsorcjum.  Konsorcjum jest logiczną grupą używaną do zarządzania i łączności między członkami łańcucha bloków, którzy dokonywają transakcji w procesie wielopartyjnym.  Usługa Azure Blockchain service zapewnia wbudowane mechanizmy kontroli nadzoru za pośrednictwem wstępnie zdefiniowanych umów inteligentnych, które określają, jakie akcje mogą podejmować członkowie konsorcjum.  Te formanty nadzoru mogą być dostosowywane w razie potrzeby przez administratora konsorcjum. Podczas tworzenia nowego konsorcjum, twój członek łańcucha bloków jest domyślnym administratorem konsorcjum, umożliwiając możliwość zapraszania innych stron do przyłączenia się do konsorcjum.  Możesz dołączyć do konsorcjum tylko wtedy, gdy zostałeś wcześniej zaproszony.  Dołączając do konsorcjum, twój członek łańcucha bloków podlega kontroli zarządzania wprowadzonej przez administratora konsorcjum.

![Zarządzanie konsorcjum](./media/overview/consortium.png)

Akcje zarządzania konsorcjum, takie jak dodawanie i usuwanie członków z konsorcjum można uzyskać za pośrednictwem programu PowerShell i interfejsu API REST. Można programowo zarządzać konsorcjum przy użyciu wspólnych interfejsów, a nie modyfikowanie i przesyłanie stałych umów inteligentnych. Aby uzyskać więcej informacji, zobacz [zarządzanie konsorcjum](consortium.md).

## <a name="develop-using-familiar-development-tools"></a>Opracowywanie przy użyciu znanych narzędzi programistycznych

Na podstawie księgi Kworum Ethereum typu open source można tworzyć aplikacje dla usługi Azure Blockchain w taki sam sposób, jak w przypadku istniejących aplikacji Ethereum. Dzięki współpracy z wiodącymi partnerami branżowymi rozszerzenie kodu programu Azure Blockchain Development Kit Visual Studio umożliwia deweloperom korzystanie ze znanych narzędzi, takich jak Truffle Suite, do tworzenia inteligentnych kontraktów. Korzystając z rozszerzenia zestawu Azure Blockchain Development Kit, deweloperzy mogą tworzyć lub łączyć się z istniejącym konsorcjum, dzięki czemu można tworzyć i wdrażać inteligentne kontrakty z jednego środowiska IDE. Za pomocą rozszerzenia kodu programu Azure Blockchain Visual Studio można utworzyć lub połączyć się z istniejącym konsorcjum, dzięki czemu można tworzyć i wdrażać inteligentne kontrakty z jednego IDE. Aby uzyskać więcej informacji, zobacz [Zestaw azure blockchain development kit w portalu VS Code marketplace](https://aka.ms/vscodebcextension) i podręcznik użytkownika zestawu Azure Blockchain Development [Kit.](https://aka.ms/vscodebcextensionwiki)

## <a name="publish-blockchain-data"></a>Publikowanie danych łańcucha bloków

Blockchain Data Manager for Azure Blockchain Service przechwytuje, przekształca i dostarcza dane transakcji usługi Azure Blockchain Service do tematów usługi Azure Event Grid, zapewniając niezawodną i skalowalną integrację księgi blockchain z usługami platformy Azure. Blockchain Data Manager umożliwia integrację aplikacji poza łańcuchem i magazynów danych. Aby uzyskać więcej informacji, zobacz [Blockchain Data Manager dla usługi Azure Blockchain Service](data-manager.md).

## <a name="support-and-feedback"></a>Pomoc techniczna i opinie

Aby uzyskać informacje o technologii Azure Blockchain, odwiedź [blog platformy Azure Blockchain,](https://azure.microsoft.com/blog/topics/blockchain/) aby być na bieżąco z ofertami usług blockchain i informacjami od zespołu inżynierów platformy Azure Blockchain.

Aby przekazać opinię o produkcie lub poprosić o nowe funkcje, po głosowaniu na pomysł za pośrednictwem [forum opinii platformy Azure dla łańcucha bloków.](https://aka.ms/blockchainuservoice)

### <a name="community-support"></a>Pomoc techniczna w społeczności

Nawiąż kontakt z inżynierami firmy Microsoft i ekspertami społeczności Platformy Azure Blockchain.

* [Forum usługi Azure Blockchain MSDN](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain)
* [Społeczność techniczna firmy Microsoft](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/AzureBlockchainService)

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, wypróbuj szybki start lub dowiedz się więcej szczegółów z tych zasobów.
* [Tworzenie członka łańcucha bloków przy użyciu witryny Azure portal](create-member.md) lub [tworzenie członka łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure](create-member-cli.md)
* Aby zapoznać się z porównaniami kosztów i kalkulatorami, zobacz [stronę cennika](https://azure.microsoft.com/pricing/details/blockchain-service).
* Tworzenie pierwszej aplikacji przy użyciu [zestawu Azure Blockchain Development Kit](https://github.com/Azure-Samples/blockchain-devkit)
* [Przewodnik użytkownika](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) rozszerzenia usługi Azure Blockchain VSCode
