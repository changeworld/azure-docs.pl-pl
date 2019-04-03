---
title: Wymagania dotyczące systemu Microsoft Azure Data Box Gateway | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o oprogramowaniu i wymagania sieciowe dla bramy pola danych platformy Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 04/02/2019
ms.author: alkohli
ms.openlocfilehash: cac451634bfa357784f9fd3d3a24e06ef3a4ee19
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58881770"
---
# <a name="azure-data-box-gateway-system-requirements"></a>Wymagania systemowe w usłudze Azure brama pola danych

W tym artykule opisano wymagania systemowe dla rozwiązania Microsoft Azure Data Box Gateway oraz klientów nawiązywania połączenia z bramą pola danych platformy Azure. Firma Microsoft zaleca, aby zapoznać się z informacjami dokładnie przed wdrożeniem bramy pola danych i następnie wrócić do niego zgodnie z potrzebami podczas wdrażania i kolejna operacja.

Wymagania systemowe dla urządzenia wirtualnego bramy pola danych obejmują:

- **Wymagania programowe dotyczące hostów** — zawiera opis obsługiwanych platform, przeglądarki dla interfejsu użytkownika konfiguracji lokalnej, klientów protokołu SMB i wszelkie dodatkowe wymagania dla hostów, które łączą się z urządzeniem.
- **Wymagania dotyczące sieci dla urządzenia** — informacje na temat wszelkie wymagania sieciowe związane z działaniem urządzenia wirtualnego.


## <a name="specifications-for-the-virtual-device"></a>Specyfikacje dotyczące urządzeń wirtualnych

Bazowego systemu hosta dla bramy pole danych jest w stanie dedykować następujących zasobów, aby aprowizować urządzenie wirtualne:

| Specyfikacje                                          | Opis              |
|---------------------------------------------------------|--------------------------|
| Procesory wirtualne (rdzenie)   | Minimum 4 |
| Memory (Pamięć)  | Minimum 8 GB|
| Dostępność|Jeden węzeł|
| Dyski| Dysk systemu operacyjnego: 250 GB <br> Dysk z danymi: minimum 2 TB, alokowany elastycznie, wymagane dyski SSD|
| Interfejsy sieciowe|Co najmniej 1 wirtualny interfejs sieciowy|


## <a name="supported-os-for-clients-connected-to-device"></a>Obsługiwane systemy operacyjne dla klientów podłączonych do urządzenia

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Obsługiwane protokoły dla klientów uzyskujących dostęp do urządzenia

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-virtualization-platforms-for-device"></a>Platformy obsługiwane wirtualizacji dla urządzenia

| **System operacyjny i platformy**  |**Wersje**   |**Uwagi**  |
|---------|---------|---------|
|Funkcja Hyper-V  |  2012 R2 <br> 2016  |         |
|VMware ESXi     | 6.0 <br> 6.5 <br> 6.7       |Narzędzia VMware nie są obsługiwane.         |


## <a name="supported-storage-accounts"></a>Konta magazynu obsługiwane

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]


## <a name="supported-storage-types"></a>Obsługiwane typy

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Przeglądarki obsługiwane przez lokalnego internetowego interfejsu użytkownika

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Wymagania dotyczące portów sieciowych

Poniższa tabela zawiera listę portów, które muszą być otwarte w zaporze, aby umożliwić SMB, w chmurze lub ruch związany z zarządzaniem. W tej tabeli *w* lub *dla ruchu przychodzącego* odnosi się do kierunku z które dostępu przychodzących żądań klienta do Twojego urządzenia. *Limit* lub *wychodzącego* Określa kierunek, w którym urządzenie bramy pola danych wysyła dane zewnętrznie, poza wdrożenia: na przykład, ruch wychodzący do Internetu.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="url-patterns-for-firewall-rules"></a>Wzorce adresów URL dla reguł zapory

Administratorzy sieci często można skonfigurować reguły zapory zaawansowane, na podstawie wzorców adresów URL, do filtrowania przychodzącego i wychodzącego ruchu. Urządzenie Data Box bramy i Usługa bramy pola danych są zależne od innych aplikacji firmy Microsoft, takich jak usługi Azure Service Bus, Azure Active Directory Access Control, kont magazynu i serwerami usługi Microsoft Update. Wzorce adresów URL skojarzone z tymi aplikacjami umożliwia konfigurowanie reguł zapory. Jest ważne dowiedzieć się, że wzorce adresów URL skojarzone z tymi aplikacjami można zmienić. Wymaga to co z kolei administrator sieci monitorować i aktualizować reguły zapory dla bramy pola danych jako i potrzebny.

Firma Microsoft zaleca ustawienie reguły zapory dla ruchu wychodzącego, oparte na stałe adresy IP, liberally, w większości przypadków bramy pola danych. Jednak można użyć poniższych informacji do ustawiania reguły zapory zaawansowanych, które są potrzebne do tworzenia bezpiecznych środowisk.

> [!NOTE]
> - Urządzenia (źródło) adresy IP powinny być zawsze ustawiony wszystkie interfejsy sieciowe z obsługą chmury.
> - Miejsce docelowe adresy IP powinien być ustawiony na [zakresy IP centrów danych platformy Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-azure-government"></a>Wzorce adresów URL dla platformy Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Przepustowością Internetu

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Następny krok

* [Wdrażanie bramy usługi Azure Data Box](data-box-gateway-deploy-prep.md)

