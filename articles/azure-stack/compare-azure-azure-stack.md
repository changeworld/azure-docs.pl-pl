---
title: Porównanie usługi Azure Stack i Azure globalne | Dokumentacja firmy Microsoft
description: Dowiedz się, jak firma Microsoft zapewnia Azure oraz z usługami w ekosystemie platformy Azure co rodziny usługi Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 5e871d467fec476f1dac77fb879d180ea2322c80
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650108"
---
# <a name="differences-between-global-azure-azure-stack-and-azure-stack-hci"></a>Różnice między globalnej platformy Azure, Azure Stack i Azure Stack HCL

Firma Microsoft udostępnia usługi Azure oraz z usługami w ekosystemie platformy Azure co rodziny usługi Azure Stack. Umożliwia ten sam model aplikacji, Samoobsługowe portale i interfejsów API za pomocą usługi Azure Resource Manager oferują oparte na chmurze możliwości, czy firmy korzysta z globalnej platformy Azure lub zasobów lokalnych.

Tym artykule opisano globalne funkcje platformy Azure, Azure Stack i Azure Stack HCl oraz udostępnia typowe zalecenia scenariusz, aby ułatwić dokonanie najlepszego wyboru do dostarczania usług chmurowych firmy Microsoft w swojej organizacji.

![Omówienie ekosystemu platformy Azure](./media/compare-azure-azure-stack/azure-family.png)

## <a name="global-azure"></a>Globalna platforma Azure

Microsoft Azure to ciągle rozwijający się zestaw usług w chmurze, który ułatwia Twojej organizacji sprostanie wyzwaniom biznesowym. Zapewnia swobodę tworzenia i wdrażania aplikacji oraz zarządzania nimi w ogromnej, globalnej sieci przy użyciu Twoich ulubionych narzędzi i struktur.

Globalne platforma Azure oferuje ponad 100 usług dostępnych w 54 regionach na całym świecie. Aby uzyskać najbardziej aktualną listę globalne usługi platformy Azure, zobacz [ *dostępność produktów według regionów*](https://azure.microsoft.com/regions/services). Usługi, które są dostępne na platformie Azure są wymienione według kategorii, a także, czy są ogólnie dostępne lub dostępne w wersji zapoznawczej.

Aby uzyskać więcej informacji na temat globalne usługi platformy Azure, zobacz [Rozpoczynanie pracy z usługą Azure](https://docs.microsoft.com/azure/#pivot=get-started&panel=get-started1).

## <a name="azure-stack"></a>Azure Stack

Usługa Azure Stack to rozszerzenie platformy Azure, która zapewnia elastyczność i innowacyjność chmury obliczeniowej do środowiska lokalnego. Wdrożone w środowisku lokalnym, usługi Azure Stack może służyć do zapewnienia spójnych usług platformy Azure, bez łączności internetowej albo połączenia z Internetem (i platformy Azure) lub w środowiskach rozłączonych. Usługa Azure Stack używa tych samych podstawowych technologii jako globalne platformy Azure, która zawiera podstawowe składniki infrastruktury as-a-Service (IaaS), Software-as-a-Service (SaaS) i opcjonalnie możliwości Platform-as-a-Service (PaaS), w tym:

- Maszyny wirtualne platformy Azure dla Windows i Linux
- Azure Web Apps i Functions
- W usłudze Azure Key Vault
- Azure Resource Manager
- Azure Marketplace
- Containers
- Usługa Azure IoT Hub i Event Hubs
- Narzędzia administracyjne (plany, oferty, RBAC, itp.)

Możliwości PaaS usługi Azure Stack są opcjonalne, ponieważ usługa Azure Stack nie jest świadczona przez firmę Microsoft, jest świadczona przez naszych klientów. Oznacza to, że możesz zaoferować usługa PaaS, niezależnie od użytkowników końcowych chcesz zmienić, jeśli środowisko jest przygotowane do abstrakcyjnej podstawową infrastrukturę i procesy od użytkownika końcowego. Jednakże usługi Azure Stack obejmuje kilka opcjonalnych dostawców usług PaaS w tym usługi App Service, baz danych SQL i baz danych MySQL. Są to działanie jest gwarantowane jako dostawców zasobów, dzięki czemu są one gotowe wielodostępne, zaktualizowane wraz z upływem czasu ze standardem, które aktualizacje usługi Azure Stack, widoczne w portalu usługi Azure Stack i dobrze zintegrowany z usługą Azure Stack.

Oprócz dostawcy zasobów opisanych powyżej, istnieją dodatkowe usługi PaaS dostępne i przetestowane jako [rozwiązania na podstawie szablonu usługi Azure Resource Manager](https://github.com/Azure/AzureStack-QuickStart-Templates) uruchomioną w modelu IaaS, ale jako usługi Azure Stack operator może oferować je jako Usługi PaaS dla użytkowników, w tym:

- Service Fabric
- Kubernetes Container Service
- Centrum IoT i Centrum zdarzeń
- Łańcuch bloków Etherium
- Cloud Foundry

### <a name="example-use-cases-for-azure-stack"></a>Przykładowe przypadki użycia dla usługi Azure Stack:

- Modelowanie finansowe
- Dane kliniczne i oświadczenia
- Analiza urządzenia IoT
- Optymalizacja asortymentu handlu detalicznego
- Optymalizacja łańcucha dostaw
- Przemysłowy Internet rzeczy
- Konserwacja zapobiegawcza
- Inteligentnego miasta
- Zaangażowanie obywateli

Dowiedz się więcej o usłudze Azure Stack w [co to jest usługa Azure Stack](azure-stack-overview.md).

## <a name="azure-stack-hci"></a>Azure Stack HCI 

Rozwiązania platformy Azure Stack HCl pozwalają na uruchamianie maszyn wirtualnych w środowisku lokalnym i łatwe łączenie z platformą Azure za pomocą rozwiązania infrastruktury (HCL) hiperzbieżnego. Twórz i uruchamiaj aplikacje w chmurze za pomocą spójnych lokalnych usług platformy Azure, aby spełniać wymagania techniczne i prawne. Oprócz uruchomione zwirtualizowanych aplikacji lokalnie Azure Stack HCl umożliwia Zastąp i konsolidować infrastruktury serwera przedawnienia i łączenie z platformą Azure dla usług w chmurze przy użyciu Windows Admin Center.

Usługa Azure Stack HCl zapewnia sprawdzone rozwiązania HCl działającemu w funkcji Hyper-V i bezpośrednimi miejscami do magazynowania przy użyciu systemu Windows Server 2019 zdefiniowanego programowo centrum danych (SDDC). Windows Admin Center służy do zarządzania i zintegrowane dostęp do usług Azure, takich jak:

- Azure Backup
- Azure Site Recovery
- Usługa Azure Monitor i aktualizacji

Zaktualizowaną listę Azure usług, które można połączyć z HCl stosu platformy Azure, aby zobaczyć [łączenia z systemu Windows Server do usługi Azure hybrydowe](https://docs.microsoft.com/windows-server/azure-hybrid-services/index).

### <a name="example-use-cases-for-azure-stack-hci"></a>Przykładowe przypadki użycia dla usługi Azure Stack HCL
- Office system lokalizacji zdalnej lub oddziale firmy
- Konsolidacja centrum danych
- Infrastruktura pulpitów wirtualnych
- Krytyczne dla prowadzonej działalności infrastruktury
- Tani Magazyn
- Wysoka dostępność i odzyskiwanie po awarii w chmurze
- Aplikacje przedsiębiorstwa, takich jak program SQL Server

Odwiedź stronę [witryny sieci Web usługi Azure Stack HCl](https://azure.microsoft.com/overview/azure-stack/hci/) do wyświetlania 70 + Azure Stack HCl rozwiązań obecnie dostępnych od partnerów firmy Microsoft.

## <a name="next-steps"></a>Kolejne kroki

[Podstawy administracji w usłudze Azure Stack](azure-stack-manage-basics.md)

[Szybki Start: Użyj portalu administracyjnego usługi Azure Stack](azure-stack-manage-portals.md)
