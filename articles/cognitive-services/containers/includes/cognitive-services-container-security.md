---
title: Zabezpieczenia kontenerów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zabezpieczyć kontener
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: fd2a6cdad01302501e30ec60a4d3ccf6efd9c266
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876836"
---
## <a name="azure-cognitive-services-container-security"></a>Zabezpieczenia kontenerów usług Azure Cognitive Services

Bezpieczeństwo powinno być głównym celem podczas tworzenia aplikacji. Znaczenie zabezpieczeń jest metryką sukcesu. Podczas zarządzania rozwiązaniem programowym, które zawiera kontenery usług Cognitive Services, ważne jest, aby zrozumieć ograniczenia i możliwości dostępne dla Ciebie. Aby uzyskać więcej informacji na temat zabezpieczeń sieci, zobacz [Konfigurowanie sieci wirtualnych usług Azure Cognitive Services][az-security].

> [!IMPORTANT]
> Domyślnie nie ma *żadnych zabezpieczeń* w interfejsie API kontenera usług Cognitive Services. Powodem tego jest to, że najczęściej kontener będzie działał jako część zasobnika, który jest chroniony od zewnątrz przez most sieciowy. Możliwe jest jednak włączenie uwierzytelniania, które działa identycznie jak uwierzytelnianie używane podczas uzyskiwania dostępu do [chmurowych usług Cognitive Services.][request-authentication]

Poniższy diagram ilustruje domyślne i **niezabezpieczane** podejście:

![Zabezpieczenia kontenerów](../media/container-security.svg)

Jako alternatywne i *bezpieczne* podejście, konsumenci kontenerów usług Cognitive Services może rozszerzyć kontener z przodu składnika, zachowując punkt końcowy kontenera prywatne. Rozważmy scenariusz, w którym używamy [Istio][istio] jako bramy przychodzącej. Istio obsługuje protokół HTTPS/TLS i uwierzytelnianie certyfikatu klienta. W tym scenariuszu fronton Istio udostępnia dostęp do kontenera, przedstawiając certyfikat klienta, który jest wcześniej umieszczony na białej liście z Istio.

[Nginx][nginx] to kolejny popularny wybór w tej samej kategorii. Zarówno Istio, jak i Nginx działają jako siatka usług i oferują dodatkowe funkcje, takie jak równoważenie obciążenia, routing i kontrola szybkości.

### <a name="container-networking"></a>Sieć kontenerów

Kontenery usług Cognitive Services są zobowiązane do przesyłania informacji o pomiarach do celów rozliczeniowych. Jedynym wyjątkiem są *kontenery w trybie offline,* ponieważ są one zgodne z inną metodologią rozliczeń. Nie można zezwolić na listę różnych kanałów sieciowych, na których opierają się kontenery usług Cognitive Services, uniemożliwi działanie kontenera.

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>Zezwalaj na listy domen i portów usług Cognitive Services

Host powinien zezwolić na port listy **443** i następujące domeny:

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>Wyłączanie inspekcji pakietów głębokich

> [Głęboka inspekcja pakietów](https://en.wikipedia.org/wiki/Deep_packet_inspection) (DPI) to rodzaj przetwarzania danych, który szczegółowo sprawdza dane przesyłane przez sieć komputerową i zwykle podejmuje działania, blokując, ponownie routingu lub rejestrując je odpowiednio.

Wyłącz dpi na bezpiecznych kanałach, które kontenery usług Cognitive Services tworzą na serwerach firmy Microsoft. Niezastosowanie się do tego uniemożliwi prawidłowe działanie kontenera.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../cognitive-services-virtual-networks.md
