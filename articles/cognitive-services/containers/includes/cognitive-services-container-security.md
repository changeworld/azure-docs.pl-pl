---
title: Zabezpieczenia kontenerów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zabezpieczyć kontener
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: dapine
ms.openlocfilehash: d8d069dddbce6ab6ddb541db460634ad3f6fa067
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70994950"
---
## <a name="azure-cognitive-services-container-security"></a>Zabezpieczenia kontenera Cognitive Services platformy Azure

Bezpieczeństwo powinno być głównym punktem skupienia przy tworzeniu aplikacji. Znaczenie zabezpieczeń jest metryką dla sukcesu. W przypadku tworzenia architektury rozwiązania programowego, które zawiera Cognitive Services kontenerów, ważne jest, aby zrozumieć, jakie ograniczenia i możliwości są dostępne dla użytkownika. Aby uzyskać więcej informacji, zobacz [zabezpieczenia platformy Azure][az-security].

> [!IMPORTANT]
> Domyślnie *nie ma żadnych zabezpieczeń* w interfejsie API kontenera Cognitive Services. Przyczyną jest to, że najczęściej kontener zostanie uruchomiony jako część elementu, który jest chroniony przez mostek sieciowy. Możliwe jest jednak włączenie uwierzytelniania, które działa identycznie z uwierzytelnianiem używanym podczas uzyskiwania dostępu do [Cognitive Services opartego na chmurze][request-authentication].

Na poniższym diagramie przedstawiono domyślne i **niebezpieczne** podejście:

![Zabezpieczenia kontenerów](../media/container-security.svg)

Jako alternatywne i *bezpieczne* podejście, odbiorcy Cognitive Services kontenerów mogą rozszerzyć kontener z składnikiem czołowym, utrzymując punkt końcowy kontenera jako prywatny. Rozważmy scenariusz, w którym korzystamy z [Istio][istio] jako bramy transferu danych przychodzących. Istio obsługuje uwierzytelnianie przy użyciu protokołu HTTPS/SSL i certyfikatu klienta. W tym scenariuszu fronton Istio uwidacznia dostęp do kontenera, który przedstawia certyfikat klienta, który jest listy dozwolonych wcześniej z Istio.

[Nginx][nginx] jest innym popularnym wyborem w tej samej kategorii. Zarówno Istio, jak i Nginx działają jako siatka usługi i oferują dodatkowe funkcje, takie jak równoważenie obciążenia, Routing i sterowanie szybkością.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../../security/fundamentals/overview.md
