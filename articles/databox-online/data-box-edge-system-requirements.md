---
title: Wymagania systemowe krawędź pola danych platformy Azure firmy Microsoft | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o oprogramowaniu i wymagania sieciowe związane z krawędzi pola danych sieci platformy Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/03/2019
ms.author: alkohli
ms.openlocfilehash: 90c60d586d505ca0c9bd787c37e137f7a38ee1f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60756549"
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

| Wzorzec URL                      | Składnik lub funkcja                     |   
|----------------------------------|---------------------------------------------|
| protokół https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Rejestr kontenerów firmy Microsoft (wymagane)               |
| https://\*.azurecr.io                     | Rejestry kontenerów osobistych, jak i innych firm, (opcjonalnie) | 
| https://\*.azure-devices.net              | Dostęp do usługi IoT Hub (wymagane)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Wzorce adresów URL dla bramy dla platformy Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Wzorce adresów URL w usłudze compute dla platformy Azure Government

| Wzorzec URL                      | Składnik lub funkcja                     |  
|----------------------------------|---------------------------------------------|
| protokół https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.com | Rejestr kontenerów firmy Microsoft (wymagane)               |
| https://\*.azure-devices.us              | Dostęp do usługi IoT Hub (wymagane)           |
| https://\*. azurecr.us                    | Rejestry kontenerów osobistych, jak i innych firm, (opcjonalnie) | 

## <a name="internet-bandwidth"></a>Przepustowością Internetu

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Zagadnienia dotyczące ustalania rozmiaru obliczeń

Użyj środowiska podczas tworzenia i testowania rozwiązania, aby zapewnić istnieje wystarczająca ilość miejsca na urządzeniu z systemem krawędź pola danych i uzyskać optymalną wydajność na urządzeniu.

Czynniki, które należy wziąć pod uwagę obejmują:

- **Szczegóły kontenera** -zastanów się, że.

    - Kontenery są obciążenia? Może mieć wiele uproszczonych kontenerów i kilka z nich dużej ilości zasobów.
    - Co to są zasobów przydzielonych do tych kontenerów i jakie są zasoby, które wykorzystują one?
    - Jak wiele warstw korzystają z kontenerów?
    - Czy istnieją nieużywane kontenery? Zatrzymano kontenera nadal zajmuje miejsce na dysku.
    - W jakim języku napisano się kontenerów?
- **Rozmiar danych przetwarzanych** — jak dużo danych będzie kontenerów przetwarzania? Te dane będą korzystać z miejsca na dysku lub danych będą przetwarzane w pamięci?
- **Oczekiwana wydajność** — co to są żądane właściwości działania rozwiązania? 

Aby zrozumieć i popraw wydajność rozwiązania, można użyć:

- Metryki obliczeniowych dostępnych w witrynie Azure portal. Przejdź do zasobu krawędź pola danych, a następnie przejdź do **monitorowanie > metryki**. Przyjrzyj się **obliczeń brzegowych — użycie pamięci** i **obliczeń brzegowych - procentowe użycie procesora CPU** zrozumienie dostępnych zasobów i jak są zasoby pobierania używane.
- Monitorowania polecenia dostępne za pośrednictwem interfejsu programu PowerShell, urządzenia, takie jak:

    - `dkr` statystyki można pobrać strumienia na żywo z kontenerów statystyki użycia zasobów. To polecenie obsługuje procesora CPU, użycie pamięci, limit pamięci i sieci metryk we/wy.
    - `dkr system df` Aby uzyskać informacje dotyczące ilości miejsca na dysku używanego. 
    - `dkr image [prune]` czyszczenie nieużywanych obrazów i zwolnić miejsce.
    - `dkr ps --size` Aby wyświetlić przybliżona liczba uruchomionego kontenera. 

    Aby uzyskać więcej informacji na temat dostępnych poleceń, przejdź do [monitorowanie i rozwiązywanie problemów z modułami obliczeń](data-box-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules).

Na koniec upewnij się, zweryfikuj swoje rozwiązanie na twoim zestawie danych, a następnie określają ilościowo wydajności na krawędzi pola danych przed wdrożeniem w środowisku produkcyjnym.


## <a name="next-step"></a>Następny krok

- [Wdrażanie na krawędzi sieci usługi Azure Data Box](data-box-edge-deploy-prep.md)
