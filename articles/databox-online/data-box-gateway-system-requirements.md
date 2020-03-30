---
title: Wymagania systemowe usługi Microsoft Azure Data Box Gateway| Dokumenty firmy Microsoft
description: Dowiedz się więcej o wymaganiach dotyczących oprogramowania i sieci bramy usługi Azure Data Box Gateway
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 05/08/2019
ms.author: alkohli
ms.openlocfilehash: e8932097bcdef782b1a551d386c2872e02d8abfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260191"
---
# <a name="azure-data-box-gateway-system-requirements"></a>Wymagania systemowe usługi Azure Data Box Gateway

W tym artykule opisano ważne wymagania systemowe dotyczące rozwiązania bramy usługi Microsoft Azure Data Box i klientów łączących się z bramą usługi Azure Data Box. Zaleca się dokładne przejrzenie informacji przed wdrożeniem bramy pola danych, a następnie odwołać się do niej w razie potrzeby podczas wdrażania i późniejszej operacji.

Wymagania systemowe dla urządzenia wirtualnego Bramy pól danych obejmują:

- **Wymagania dotyczące oprogramowania dla hostów** — opisuje obsługiwane platformy, przeglądarki dla interfejsu użytkownika konfiguracji lokalnej, klientów SMB i wszelkie dodatkowe wymagania dla hostów łączących się z urządzeniem.
- **Wymagania dotyczące sieci dla urządzenia** - zawiera informacje o wszelkich wymaganiach sieciowych dla działania urządzenia wirtualnego.


## <a name="specifications-for-the-virtual-device"></a>Dane techniczne urządzenia wirtualnego

Podstawowy system hosta bramy pól danych może przeznaczyć następujące zasoby na aprowizację urządzenia wirtualnego:

| Specyfikacje                                          | Opis              |
|---------------------------------------------------------|--------------------------|
| Procesory wirtualne (rdzenie)   | Minimum 4 |
| Memory (Pamięć)  | Minimum 8 GB|
| Dostępność|Jeden węzeł|
| Dyski| Dysk systemu operacyjnego: 250 GB <br> Dysk danych: minimum 2 TB, alokowany elastycznie, wymagane dyski SSD|
| Interfejsy sieciowe|Co najmniej 1 wirtualny interfejs sieciowy|


## <a name="supported-os-for-clients-connected-to-device"></a>Obsługiwany system operacyjny dla klientów podłączonych do urządzenia

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Obsługiwane protokoły dla klientów uzyskujących dostęp do urządzenia

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-virtualization-platforms-for-device"></a>Obsługiwane platformy wirtualizacji dla urządzeń

| **System operacyjny/platforma**  |**Wersje**   |**Uwagi**  |
|---------|---------|---------|
|Funkcja Hyper-V  |  2012 R2 <br> 2016 <br> 2019 |         |
|VMware ESXi     | 6.0 <br> 6.5 <br> 6.7       |Narzędzia VMware nie są obsługiwane.         |


## <a name="supported-storage-accounts"></a>Obsługiwane konta magazynu

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]


## <a name="supported-storage-types"></a>Obsługiwane typy magazynu

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Obsługiwane przeglądarki dla lokalnego interfejsu użytkownika sieci Web

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Wymagania dotyczące portów sieciowych

W poniższej tabeli wymieniono porty, które muszą zostać otwarte w zaporze, aby umożliwić ruch SMB, chmura lub zarządzanie. W tej tabeli *in* lub *inbound* odnosi się do kierunku, z którego przychodzący klient żąda dostępu do urządzenia. *Wyjście* lub *wychodzące* odnosi się do kierunku, w którym urządzenie bramy data box wysyła dane zewnętrznie, poza wdrożeniem: na przykład wychodzących do Internetu.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="url-patterns-for-firewall-rules"></a>Wzorce adresów URL dla reguł zapory

Administratorzy sieci często mogą konfigurować zaawansowane reguły zapory na podstawie wzorców adresów URL w celu filtrowania ruchu przychodzącego i wychodzącego. Urządzenie bramy pola danych i usługa Brama pola danych zależą od innych aplikacji firmy Microsoft, takich jak usługa Azure Service Bus, kontrola dostępu usługi Azure Active Directory, konta magazynu i serwery Usługi Microsoft Update. Wzorce adresów URL skojarzone z tymi aplikacjami mogą służyć do konfigurowania reguł zapory. Ważne jest, aby zrozumieć, że wzorce adresów URL skojarzone z tymi aplikacjami mogą ulec zmianie. To z kolei będzie wymagać administrator sieci do monitorowania i aktualizowania reguł zapory dla bramy pola danych w razie potrzeby.

Zaleca się ustawienie reguł zapory dla ruchu wychodzącego na podstawie stałych adresów IP usługi Data Box Gateway, w większości przypadków obficie. Można jednak użyć poniższych informacji, aby ustawić zaawansowane reguły zapory, które są potrzebne do tworzenia bezpiecznych środowisk.

> [!NOTE]
> - Interfejsy IP urządzenia (źródła) powinny być zawsze ustawione na wszystkie interfejsy sieciowe z obsługą chmury.
> - Docelowe adresy IP powinny być ustawione na [zakresy adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-azure-government"></a>Wzorce adresów URL dla platformy Azure dla instytucji rządowych

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Przepustowość Internetu

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Następny krok

* [Wdrażanie bramy skrzynki danych platformy Azure](data-box-gateway-deploy-prep.md)

