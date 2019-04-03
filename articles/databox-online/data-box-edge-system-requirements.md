---
title: Wymagania systemowe krawędź pola danych platformy Azure firmy Microsoft | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o oprogramowaniu i wymagania sieciowe związane z krawędzi pola danych sieci platformy Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/02/2019
ms.author: alkohli
ms.openlocfilehash: 720f4433c1e7b0d271e5729236f439c92a84687e
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884656"
---
# <a name="azure-data-box-edge-system-requirements"></a>Wymagania dotyczące systemu Azure krawędź pola danych

W tym artykule opisano wymagania systemowe dla rozwiązania pole danych platformy Azure przeglądarki Microsoft Edge i klientów nawiązywania połączenia z usługi Azure Data Box Edge. Firma Microsoft zaleca, aby zapoznać się z informacjami dokładnie przed wdrożeniem usługi krawędź pola danych. Można wrócić do tych informacji zgodnie z potrzebami, podczas wdrażania i kolejna operacja.

Wymagania systemowe na krawędzi ramki danych obejmują:

- **Wymagania programowe dotyczące hostów** — zawiera opis obsługiwanych platform, przeglądarki dla interfejsu użytkownika konfiguracji lokalnej, klientów protokołu SMB i wszelkie dodatkowe wymagania dotyczące klientów uzyskujących dostęp do urządzenia.
- **Wymagania dotyczące sieci dla urządzenia** — informacje na temat wszelkie wymagania sieciowe związane z działaniem urządzenia fizycznego.

## <a name="supported-os-for-clients-connected-to-device"></a>Obsługiwane systemy operacyjne dla klientów podłączonych do urządzenia

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Obsługiwane protokoły dla klientów uzyskujących dostęp do urządzenia

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Konta magazynu obsługiwane

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Obsługiwane typy

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Przeglądarki obsługiwane przez lokalnego internetowego interfejsu użytkownika

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Wymagania dotyczące portów sieciowych

### <a name="port-requirements-for-data-box-edge"></a>Wymagania dotyczące portów dla przeglądarki Microsoft Edge pole danych

Poniższa tabela zawiera listę portów, które muszą być otwarte w zaporze, aby umożliwić SMB, w chmurze lub ruch związany z zarządzaniem. W tej tabeli *w* lub *dla ruchu przychodzącego* odnosi się do kierunku z które dostępu przychodzących żądań klienta do Twojego urządzenia. *Limit* lub *wychodzącego* Określa kierunek, w którym urządzenie brzegowe pole danych wysyła dane zewnętrznie, poza wdrożenia, na przykład, ruch wychodzący do Internetu.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Wymagania dotyczące portów dla usługi IoT Edge

Usługa Azure IoT Edge umożliwia komunikacji wychodzącej z urządzenia usługi Edge środowiska lokalnego do chmury platformy Azure za pomocą obsługiwanych protokołów IoT Hub. Komunikacji przychodzącej jest tylko wymagane dla konkretnych scenariuszy, w którym usługi Azure IoT Hub musi wypchnięta komunikaty do usługi Azure IoT Edge urządzenia (na przykład w chmurze do Device messaging).

Konfiguracja portu dla serwerów obsługujących środowisko uruchomieniowe usługi Azure IoT Edge, skorzystaj z poniższej tabeli:

| Nr portu. | Wewnątrz lub na zewnątrz | Zakres portów | Wymagane | Wskazówki |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Wyjście       | Sieć WAN        | Yes      | Otwórz ruchu wychodzącego dla usługi IoT Edge inicjowania obsługi administracyjnej. Ta konfiguracja jest wymagana w przypadku korzystania z ręcznego skryptów lub usługi Azure IoT urządzenia inicjowania obsługi usługi (DPS).|

Aby uzyskać pełne informacje, przejdź do [Zapora i reguły konfiguracji portów dla wdrożenia usługi IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>Wzorce adresów URL dla reguł zapory

Administratorzy sieci często można skonfigurować reguły zapory zaawansowane, na podstawie wzorców adresów URL, do filtrowania przychodzącego i wychodzącego ruchu. Urządzenie brzegowe pola danych i usługi są zależne od innych aplikacji firmy Microsoft, takich jak usługi Azure Service Bus, Azure Active Directory Access Control, kont magazynu i serwerami usługi Microsoft Update. Wzorce adresów URL skojarzone z tymi aplikacjami umożliwia konfigurowanie reguł zapory. Jest ważne dowiedzieć się, że wzorce adresów URL skojarzone z tymi aplikacjami można zmienić. Te zmiany wymagają administratorem sieci, aby monitorować i aktualizować reguły zapory na krawędzi pola danych, sieci jako i potrzebny.

Firma Microsoft zaleca ustawienie reguły zapory dla ruchu wychodzącego, oparte na stałe adresy IP, liberally, w większości przypadków krawędź pola danych. Jednak można użyć poniższych informacji do ustawiania reguły zapory zaawansowanych, które są potrzebne do tworzenia bezpiecznych środowisk.

> [!NOTE]
> - Urządzenia (źródło) adresy IP powinny być zawsze ustawiony wszystkie interfejsy sieciowe z obsługą chmury.
> - Miejsce docelowe adresy IP powinien być ustawiony na [zakresy IP centrów danych platformy Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Wzorce adresów URL dla funkcji bramy

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Wzorce adresów URL dla funkcji obliczeniowych

| Wzorzec URL                      | Składnik lub funkcja                     |   |
|----------------------------------|---------------------------------------------|---|
| `https://mcr.microsoft.com`<br></br>https://\*.cdn.mscr.io | Rejestr kontenerów firmy Microsoft (wymagane)               |   |
| https://\*.azurecr.io                     | Rejestry kontenerów osobistych, jak i innych firm, (opcjonalnie) |   |
| https://\*.azure-devices.net              | Dostęp do usługi IoT Hub (wymagane)                             |   |

### <a name="url-patterns-for-azure-government"></a>Wzorce adresów URL dla platformy Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Przepustowością Internetu

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Następny krok

- [Wdrażanie na krawędzi sieci usługi Azure Data Box](data-box-edge-deploy-prep.md)
