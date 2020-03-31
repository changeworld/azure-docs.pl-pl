---
title: Wymagania systemowe usługi Microsoft Azure Data Box Edge| Dokumenty firmy Microsoft
description: Dowiedz się więcej o wymaganiach dotyczących oprogramowania i sieci dla usługi Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/03/2019
ms.author: alkohli
ms.openlocfilehash: 458c062eef011363724cb894ce67ba75181ba8ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260230"
---
# <a name="azure-data-box-edge-system-requirements"></a>Wymagania systemowe usługi Azure Data Box Edge

W tym artykule opisano ważne wymagania systemowe dotyczące rozwiązania usługi Microsoft Azure Data Box Edge i klientów łączących się z usługą Azure Data Box Edge. Przed wdrożeniem usługi Data Box Edge zaleca się uważne zapoznanie się z informacjami. Można odwołać się do tych informacji, w razie potrzeby podczas wdrażania i późniejszej operacji.

Wymagania systemowe dla data box edge obejmują:

- **Wymagania dotyczące oprogramowania dla hostów** — opisuje obsługiwane platformy, przeglądarki dla interfejsu użytkownika konfiguracji lokalnej, klientów SMB i wszelkie dodatkowe wymagania dla klientów, którzy uzyskują dostęp do urządzenia.
- **Wymagania dotyczące sieci dla urządzenia** — zawiera informacje o wszelkich wymaganiach sieciowych dotyczących działania urządzenia fizycznego.

## <a name="supported-os-for-clients-connected-to-device"></a>Obsługiwany system operacyjny dla klientów podłączonych do urządzenia

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Obsługiwane protokoły dla klientów uzyskujących dostęp do urządzenia

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Obsługiwane konta magazynu

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Obsługiwane typy magazynu

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Obsługiwane przeglądarki dla lokalnego interfejsu użytkownika sieci Web

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Wymagania dotyczące portów sieciowych

### <a name="port-requirements-for-data-box-edge"></a>Wymagania dotyczące portów dla krawędzi pola danych

W poniższej tabeli wymieniono porty, które muszą zostać otwarte w zaporze, aby umożliwić ruch SMB, chmura lub zarządzanie. W tej tabeli *in* lub *inbound* odnosi się do kierunku, z którego przychodzący klient żąda dostępu do urządzenia. *Wyjście* lub *wychodzące* odnosi się do kierunku, w którym urządzenie Data Box Edge wysyła dane zewnętrznie, poza wdrożeniem, na przykład wychodzące do Internetu.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Wymagania dotyczące portu dla IoT Edge

Usługa Azure IoT Edge umożliwia komunikację wychodzącą z lokalnego urządzenia edge do chmury platformy Azure przy użyciu obsługiwanych protokołów Usługi IoT Hub. Komunikacja przychodząca jest wymagana tylko w określonych scenariuszach, w których usługa Azure IoT Hub musi wypychać wiadomości na urządzenie usługi Azure IoT Edge (na przykład wiadomości z chmury do urządzenia).

Użyj następującej tabeli dla konfiguracji portów dla serwerów obsługujących środowisko uruchomieniowe usługi Azure IoT Edge:

| Nr portu | Wył. | Zakres portu | Wymagany | Wskazówki |
|----------|-----------|------------|----------|----------|
| Protokół TCP 443 (HTTPS)| Out       | Sieć WAN        | Tak      | Otwarte wychodzące dla inicjowania obsługi administracyjnej ioT Edge. Ta konfiguracja jest wymagana podczas korzystania ze skryptów ręcznych lub usługi inicjowania obsługi administracyjnej urządzeń azure (DPS).|

Aby uzyskać pełne informacje, przejdź do [reguł konfiguracji zapory i portu dla wdrożenia usługi IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>Wzorce adresów URL dla reguł zapory

Administratorzy sieci często mogą konfigurować zaawansowane reguły zapory na podstawie wzorców adresów URL w celu filtrowania ruchu przychodzącego i wychodzącego. Urządzenie data box edge i usługa zależą od innych aplikacji firmy Microsoft, takich jak usługa Azure Service Bus, kontrola dostępu usługi Azure Active Directory, konta magazynu i serwery Usługi Microsoft Update. Wzorce adresów URL skojarzone z tymi aplikacjami mogą służyć do konfigurowania reguł zapory. Ważne jest, aby zrozumieć, że wzorce adresów URL skojarzone z tymi aplikacjami mogą ulec zmianie. Te zmiany wymagają od administratora sieci monitorowania i aktualizowania reguł zapory dla urządzenia Data Box Edge w razie potrzeby.

Zaleca się ustawienie reguł zapory dla ruchu wychodzącego, na podstawie stałych adresów IP data box edge, w większości przypadków. Można jednak użyć poniższych informacji, aby ustawić zaawansowane reguły zapory, które są potrzebne do tworzenia bezpiecznych środowisk.

> [!NOTE]
> - Interfejsy IP urządzenia (źródła) powinny być zawsze ustawione na wszystkie interfejsy sieciowe z obsługą chmury.
> - Docelowe adresy IP powinny być ustawione na [zakresy adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Wzorce adresów URL dla funkcji bramy

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Wzorce adresów URL dla funkcji obliczeniowej

| Wzorzec adresu URL                      | Komponent lub funkcjonalność                     |   
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Rejestr kontenerów firmy Microsoft (wymagany)               |
| https://\*.azurecr.io                     | Rejestry kontenerów osobistych i innych firm (opcjonalnie) | 
| https://\*.azure-devices.net              | Dostęp do usługi IoT Hub (wymagany)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Wzorce adresów URL bramy dla platformy Azure dla instytucji rządowych

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Wzorce adresów URL dla obliczeń dla platformy Azure dla instytucji rządowych

| Wzorzec adresu URL                      | Komponent lub funkcjonalność                     |  
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.com | Rejestr kontenerów firmy Microsoft (wymagany)               |
| https://\*.azure-devices.us              | Dostęp do usługi IoT Hub (wymagany)           |
| https://\*.azurecr.us                    | Rejestry kontenerów osobistych i innych firm (opcjonalnie) | 

## <a name="internet-bandwidth"></a>Przepustowość Internetu

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Zagadnienia dotyczące rozmiaru obliczeń

Korzystaj z doświadczenia podczas opracowywania i testowania rozwiązania, aby upewnić się, że na urządzeniu Data Box Edge jest wystarczająca pojemność i uzyskasz optymalną wydajność urządzenia.

Czynniki, które należy wziąć pod uwagę obejmują:

- Szczegóły kontenera — pomyśl o następujących **tematach.**

    - Ile kontenerów znajduje się w obciążeniu? Możesz mieć wiele lekkich kontenerów w porównaniu z kilkoma zasobochłonnymi kontenerami.
    - Jakie są zasoby przydzielone do tych kontenerów w porównaniu do zasobów, które zużywają?
    - Ile warstw współużytkują kontenery?
    - Czy istnieją nieużywane kontenery? Zatrzymany kontener nadal zajmuje miejsce na dysku.
    - W jakim języku są napisane twoje kontenery?
- **Rozmiar przetwarzanych danych** — ile danych będzie przetwarzać kontenery? Czy te dane zużywają miejsce na dysku lub dane będą przetwarzane w pamięci?
- **Oczekiwana wydajność** — jakie są żądane właściwości użytkowe rozwiązania? 

Aby zrozumieć i udoskonalić wydajność rozwiązania, można użyć:

- Metryki obliczeń dostępne w witrynie Azure portal. Przejdź do zasobu Data Box Edge, a następnie przejdź do **monitorowania > metryki**. Spójrz na **obliczenia krawędzi — użycie pamięci** i obliczenia krawędzi — procent **procesora CPU,** aby zrozumieć dostępne zasoby i jak są zużywane zasoby.
- Polecenia monitorowania dostępne za pośrednictwem interfejsu programu PowerShell urządzenia, takie jak:

    - `dkrdbe stats`, aby uzyskać strumień na żywo statystyk użycia zasobów kontenerów. Polecenie obsługuje procesor, użycie pamięci, limit pamięci i metryki we/wy sieci.
    - `dkrdbe system df`, aby uzyskać informacje dotyczące ilości zużytego miejsca na dysku. 
    - `dkrdbe image prune`, aby wyczyścić nieużywane obrazy i zwolnić miejsce.
    - `dkrdbe ps --size`, aby wyświetlić przybliżony rozmiar uruchomionego kontenera. 

    Aby uzyskać więcej informacji na temat dostępnych poleceń, przejdź do [strony Monitoruj i rozwiązywanie problemów z modułami obliczeniowymi](data-box-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules).

Na koniec upewnij się, że sprawdź poprawność rozwiązania w zestawie danych i kwantyfikuj wydajność na podstawie pola danych przed wdrożeniem w produkcji.


## <a name="next-step"></a>Następny krok

- [Wdrażanie usługi Azure Data Box Edge](data-box-edge-deploy-prep.md)
