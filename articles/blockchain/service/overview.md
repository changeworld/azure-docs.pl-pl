---
title: Omówienie usługi Azure Service łańcucha bloków
description: Omówienie usługi Azure Blockchain
services: azure-blockchain
keywords: łańcuch bloków
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: e370916d420a7bc4cd16d021c69a2f8609093d30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544699"
---
# <a name="what-is-azure-blockchain-service"></a>Co to jest usługa Azure Service łańcuch bloków?

Usługa Azure Service łańcuch bloków to usługa Rejestr w pełni zarządzana, która umożliwia użytkownikom możliwość rozwoju i obsługiwanie sieci łańcucha bloków na dużą skalę na platformie Azure. Zapewniając ujednolicone kontroli zarówno zarządzania infrastrukturą, a także zarządzanie sieć łańcucha bloków, Azure Blockchain Service zapewnia:

* Prosta sieć wdrożeniowe i operacyjne
* Wbudowane zarządzanie konsorcjum
* Twórz kontraktów inteligentnych ze znanych Ci narzędzi programistycznych

Usługa Azure Blockchain jest przeznaczony do obsługi wielu protokołów rejestr. Obecnie zapewnia obsługę Ethereum [kworum](https://www.jpmorgan.com/Quorum) rejestr przy użyciu [IBFT](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus) mechanizm zgodne.

Powyższe możliwości prawie w ogóle nie wymagają administracji i są udostępniane bez żadnych dodatkowych kosztów. Możesz skupić się na rozwoju aplikacji oraz logiki biznesowej a nie na poświęcaniu czas i zasoby do zarządzania maszynami wirtualnymi i infrastrukturą. Ponadto można kontynuować do tworzenia aplikacji przy użyciu narzędzi typu open source i dostarczać rozwiązania bez konieczności uczenia się nowych umiejętności dowolnie wybranej platformie.

## <a name="network-deployment-and-operations"></a>Wdrażanie sieci i operacje

Wdrażanie usługi Azure Service łańcuch bloków może odbywać się za pośrednictwem witryny Azure portal, interfejsu wiersza polecenia platformy Azure, a także za pomocą programu Visual Studio code przy użyciu rozszerzenia Azure blockchain zobaczyliśmy.  Wdrażania jest uproszczony tym inicjowania obsługi administracyjnej węzłów transakcji i modułu sprawdzania poprawności, sieciami wirtualnymi platformy Azure dla zabezpieczeń, izolacji, a także zarządzane przez usługę Magazyn.  Ponadto podczas wdrażania nowego członka łańcucha bloków, użytkownicy również utworzyć lub dołączyć konsorcjum.  Konsorcjum włączyć wiele stron w różnych subskrypcjach platformy Azure mogli bezpiecznie komunikować się ze sobą na udostępnionym łańcucha bloków.  To uproszczone wdrażanie zmniejsza wdrażania sieci łańcucha bloków z dni do minut.

### <a name="performance-and-service-tiers"></a>Warstwy wydajności i usługi

Usługa Azure Blockchain oferuje dwie warstwy usług: *Podstawowe* i *standardowa*. Każda warstwa oferuje inną wydajność i funkcje do obsługi uproszczone programowanie i testowanie obciążeń do masowego skalowania wdrożenia łańcucha bloków w środowisku produkcyjnym. Obie warstwy obejmują transakcji co najmniej jeden węzeł i jeden węzeł weryfikacji (Basic) lub dwa węzły modułu sprawdzania poprawności (metoda standardowa).

![Warstwy cenowe](./media/overview/pricing-tiers.png)

Oprócz dwóch węzłów modułu sprawdzania poprawności, *standardowa* warstwa zapewnia 2 *rdzeni wirtualnych* dla każdego węzła transakcji i modułu weryfikacji, natomiast warstwa Basic oferuje 1 konfigurację (rdzeń wirtualny).  Oferując 2 rdzeni wirtualnych dla węzłów transakcji i modułu weryfikacji, 1 rdzeń wirtualny mogą być przeznaczone dla księgi kworum, podczas gdy pozostałe (1 rdzeń wirtualny) może służyć do innych usług związanych z infrastrukturą, zapewniające optymalną wydajność w środowisku produkcyjnym z obciążeniami łańcucha bloków. Aby uzyskać więcej informacji na temat cen szczegółowe informacje, zobacz [cennik usługi Azure Blockchain Service](https://azure.microsoft.com/pricing/details/blockchain-service).

### <a name="security-and-maintenance"></a>Zabezpieczenia i konserwacja

Po zainicjowaniu obsługi administracyjnej Twojego pierwszego elementu członkowskiego łańcucha bloków, masz możliwość dodawania węzłów dodatkowych transakcji do elementów członkowskich.  Domyślnie węzły transakcji są zabezpieczone za pomocą reguł zapory i będzie konieczne jest skonfigurowany do dostępu.  Ponadto wszystkie węzły transakcji szyfrować dane w ruchu za pośrednictwem protokołu TLS.  Istnieje wiele opcji do zabezpieczania transakcji dostęp do węzła, w tym reguły zapory, uwierzytelnianie podstawowe, klucze dostępu, a także Integracja usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [konfiguracji węzłów transakcji](configure-transaction-nodes.md) i [Konfigurowanie dostępu do usługi Azure Active Directory](configure-aad.md).

Jako usługa zarządzana usługa łańcucha bloków Azure gwarantuje, że węzły elementu członkowskiego łańcucha bloków są zastosować poprawki względem jakiegokolwiek z hostem najnowsze operacyjnego systemowe i rejestr stosu aktualizacji, skonfigurowany dla wysokiej dostępności (tylko w warstwie standardowa), eliminując ilości DevOps wymagana w przypadku tradycyjnych węzłów łańcucha bloków IaaS.  Aby uzyskać więcej informacji na temat poprawek i aktualizacji, zobacz [obsługiwane wersje rejestr Azure Blockchain Service](ledger-versions.md).

### <a name="monitoring-and-logging"></a>Monitorowanie i rejestrowanie

Dodatkowo Azure Blockchain Service generuje dane pomiarowe sformatowanego przy użyciu usługi Azure Monitor Service dostarczanie szczegółowych informacji o węzłach użycie procesora CPU, pamięci i magazynu, a także przydatne szczegółowe informacje dotyczące aktywności sieciowej łańcucha bloków, takich jak transakcje i bloków reklamodawcach, głębokość kolejki transakcji, a także aktywnych połączeń.  Metryki można dostosować w taki sposób, aby zapewnić widoków w szczegółowe informacje, które są istotne dla twojej aplikacji łańcucha bloków.  Ponadto można zdefiniować progi za pomocą alertów, umożliwiając użytkownikom wyzwalać akcje, takie jak wysyłanie wiadomości e-mail lub SMS, uruchamiania aplikacji logiki lub funkcji platformy Azure lub wysyłania do elementu webhook niestandardowy.

![Metryki](./media/overview/metrics.png)

Za pomocą usługi Azure Log Analytics użytkownicy mogą wyświetlać dzienniki związane z księgi kworum lub inne połączenia ważne informacje, takie jak próby w węzłach transakcji.

## <a name="built-in-consortium-management"></a>Wbudowane zarządzanie konsorcjum

Podczas wdrażania Twojego pierwszego elementu członkowskiego łańcucha bloków, możesz dołączyć albo utworzyć nowe konsorcjum.  Konsorcjum to logiczna Grupa używana do zarządzania nadzoru i łączność między członków łańcucha bloków, którzy transact wieloosobowa procesu.  Usługa Azure Service łańcuch bloków umożliwia sterowanie wbudowane zarządzanie przy użyciu wstępnie zdefiniowanych kontraktów inteligentnych, które określają, jakie akcje członków konsorcjum może potrwać.  Te kontrolki nadzoru można dostosować przez administratora konsorcjum zgodnie z potrzebami. Podczas tworzenia nowego konsorcjum elementów członkowskich łańcuch bloków to domyślnego administratora consortium, włączenie możliwości można zaprosić innych stron, aby dołączyć swoje konsorcjum.  Możesz dołączyć konsorcjum tylko wtedy, gdy wcześniej zaproszono Cię.  Podczas przyłączania konsorcjum, elementu członkowskiego z łańcucha bloków podlega kontrole nadzoru wprowadzone przez konsorcjum administratora.

![Zarządzanie konsorcjum](./media/overview/consortium.png)

Konsorcjum akcje zarządzania, takie jak dodawanie i usuwanie elementów członkowskich z konsorcjum jest możliwy za pośrednictwem programu PowerShell i interfejsu API REST. Możesz programowo zarządzać konsorcjum za pomocą wspólnych interfejsów, a nie modyfikowanie i przesyłanie trwałość na podstawie kontraktów inteligentnych. Aby uzyskać więcej informacji, zobacz [zarządzania konsorcjum](consortium.md).

## <a name="develop-using-familiar-development-tools"></a>Programowanie przy użyciu znanych Ci narzędzi programistycznych

Oparte na księgowe Ethereum kworum open source, możesz tworzyć aplikacje dla usługi Azure Service łańcucha bloków taki sam sposób jak w przypadku istniejących aplikacji Ethereum. Praca z wiodących partnerów w branży, rozszerzenie Azure Blockchain Development Kit programu Visual Studio Code umożliwia deweloperom korzystanie z dobrze znanych narzędzi, takich jak pakiet Truffle tworzyć kontrakty inteligentne. Korzystania z rozszerzenia Azure Blockchain Development Kit, deweloperzy można utworzyć lub połączyć się z oraz istniejących konsorcjum tak, aby można je tworzyć i wdrażać swoje inteligentnych umów wszystkie w jednym środowisku IDE. Korzystanie z rozszerzenia Azure Blockchain Visual Studio Code, można utworzyć lub nawiązać istniejących konsorcjum tak, aby można je tworzyć i wdrażać kontraktów inteligentnych wszystko w jednym środowisku IDE. Aby uzyskać więcej informacji, zobacz [Azure Blockchain Development Kit w witrynie marketplace programu VS Code](https://aka.ms/vscodebcextension) i [Podręcznik użytkownika usługi Azure Blockchain Development Kit](https://aka.ms/vscodebcextensionwiki ).

## <a name="support-and-feedback"></a>Pomoc techniczna i opinie

Potrzebujesz pomocy lub masz opinię?

* Odwiedź stronę [blog na temat aplikacji Azure Blockchain](https://azure.microsoft.com/blog/topics/blockchain/), [społeczności technicznej firmy Microsoft](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain), i [forum Azure blockchain zobaczyliśmy](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain).
* Aby przekazać opinię lub poprosić o nowe funkcje, utwórz wpis w platformie [UserVoice](https://feedback.azure.com/forums/921130-azure-blockchain-service).

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć pracę, wypróbuj Szybki Start lub uzyskać więcej szczegółowych informacji z tych zasobów.
* [Utwórz element członkowski łańcucha bloków w witrynie Azure portal](create-member.md) lub [utworzyć element członkowski łańcucha bloków przy użyciu wiersza polecenia platformy Azure](create-member-cli.md)
* Dla porównania i kalkulatory kosztów, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/blockchain-service).
* Tworzenie pierwszej aplikacji przy użyciu [Azure Blockchain Development Kit](https://github.com/Azure-Samples/blockchain-devkit)
* Rozszerzenie programu VSCode Azure Blockchain [Podręcznik użytkownika](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki)
