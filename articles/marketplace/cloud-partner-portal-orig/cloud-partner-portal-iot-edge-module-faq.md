---
title: Moduł usługi IoT Edge — Marketplace — często zadawane pytania | Dokumentacja firmy Microsoft
description: Portal Marketplace moduł usługi IoT Edge — często zadawane pytania.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 2c2e7729eb70a5dd37dc4df10605eec9429e1043
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810369"
---
# <a name="iot-edge-module-marketplace-frequently-asked-questions"></a>Moduł usługi IoT Edge w witrynie Marketplace — często zadawane pytania


## <a name="what-is-the-edge-module-marketplace"></a>Co to jest witryna Marketplace modułu Edge?


Portal Marketplace z modułu IoT Edge znajduje się lista *certyfikowane* wstępnie skompilowane moduły graniczne, które są *wykrywalny* w portalu Azure Marketplace.

![Moduły usługi IoT Edge](./media/cloud-partner-portal-iot-edge-module-faq/iot-edge-modules.png)

## <a name="where-will-edge-modules-be-visible"></a>Gdzie moduły usługi Edge będą widoczne? 


W [witryny Azure portal marketplace](https://ms.portal.azure.com/) (uwierzytelniony środowiska), w obszarze kategoria Internet of Things oznaczone jako "Moduł usługi IoT Edge".

I w [witryny sieci Web portalu Azure marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/internet-of-things?page=1) (interfejs użytkownika anonimowego) w obszarze kategoria Internet of Things oznaczone jako "Moduł usługi IoT Edge".

## <a name="is-it-open-to-partners"></a>Czy jest otwarta dla partnerów?


Jeszcze nie. Obecnie tylko moduły opracowanych przez firmę Microsoft są publikowane w sekcji usługi IoT Edge w witrynie marketplace. 

## <a name="why-should-partners-publish-their-iot-edge-modules"></a>Dlaczego partnerów publikować swoje moduły usługi IoT Edge?


-   Aby zwiększyć ich widoczności produktu, dodając ten kanał na rynek i zaprezentować swoich rozwiązań.

-   Aby uzyskać więcej potencjalnych klientów. W ramach witryny Azure Marketplace mogą uzyskać szczegółowe informacje o tym, kto jest zainteresowany swoje rozwiązanie.

-   Aby się jako pierwszy wykorzystywać więcej możliwości zysków z aplikacji.

## <a name="what-is-the-onboarding-process"></a>Co to jest proces dołączania?


Gdy nie jeszcze otwarty, proces dołączania zostanie wykonane w portalu Azure Marketplace. Szczegółowe wytyczne znajdują się w [Podręcznik publikowania w portalu Azure Marketplace i AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide). Zapoznaj się z instrukcji transact typ oferty dla kontenerów. 

Partnerzy muszą najpierw zostać wydawcą w portalu Azure Marketplace. Następnie będą mogli dołączyć ich modułów usługi Edge za pośrednictwem [portalu Cloud Partner](./cloud-partner-portal-getting-started-with-the-cloud-partner-portal.md).

## <a name="are-there-any-monetization-capabilities"></a>Czy istnieją jakichkolwiek możliwości zysków z aplikacji?


Nie gotowy do użycia teraz. Naszym pierwszym celem jest, aby otworzyć Portal marketplace z *bezpłatne* lub *model dostarczania własnej licencji* krawędzi modułów. Będziemy dodawać więcej możliwości zysków z aplikacji, takich jak użycie model rozliczeń.

## <a name="what-is-bring-your-own-license-byol"></a>Co to jest bring-your-own-license (BYOL)?


Oficjalna definicji w [Microsoft Azure Marketplace — zasady udziału](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) jest:

- *Klienci uzyskać poza portalem Azure Marketplace prawo dostępu lub korzystać z oferty i nie są naliczane opłaty portalu Azure Marketplace za korzystanie z oferty w portalu Azure Marketplace.*

To Ty partnerów do licencji oprogramowania i zbieranie przychodów dla niego.

## <a name="can-partners-publish-a-freemium-module"></a>Czy partnerzy publikować modułu "freemium"?


Tak freemium moduły, które to moduły, które są dostępne bezpłatnie, ale z pewnymi ograniczeniami jest traktowana jako inne publikacji.

## <a name="is-intellectual-property-protected"></a>Własność intelektualna jest chroniona?


Moduł usługi Edge jest zgodny kontenera platformy Docker. To Ty partnerami, aby chronić swoje prawa własności intelektualnej (IP) spakowany w dystrybuowane kontenery.

## <a name="where-will-the-modules-be-hosted"></a>Gdzie będzie hostowany modułów?


Moduły usługi IoT Edge będzie obsługiwana w rejestrze kontenerów należącymi do firmy Microsoft, który ma być anonimowo zapytania — możliwe, takich jak usługi Docker Hub.

## <a name="what-are-the-integration-plans-between-the-azure-marketplace-and-the-azure-iot-tools"></a>Jakie są plany integracji między w portalu Azure Marketplace i narzędzi usługi Azure IoT?

Będziemy tworzyć ściślejszą integrację portalu Azure Marketplace i narzędzi usługi Azure IoT. Przykłady mamy na uwadze dotyczą niektórych przeglądanie marketplace moduł usługi IoT Edge, bezpośrednio z portalu usługi IoT Hub lub bezpośrednio z poziomu programu Visual Studio Code.

## <a name="which-operating-system-or-architecture-should-my-container-support"></a>Którego systemu operacyjnego lub architektury powinien obsługiwać kontener?

Moduły usługi IoT Edge musi obsługiwać ten sam system operacyjny / macierzy architekturę jako usługi IoT Edge w środowisku produkcyjnym. Ta lista jest przechowywana w [Azure IoT Edge obsługi](https://docs.microsoft.com/azure/iot-edge/support). Na przykład moduł musi obsługiwać obecnie Linux x64 i ARM32 systemu Linux.

## <a name="are-there-any-other-certification-constraints-to-be-aware-of"></a>Czy istnieją inne ograniczenia certyfikacji wiedzieć?

Wciąż pracujemy nad dokładny zestaw ograniczeń certyfikacji. Nasze wytyczne są następujące:

-   Podwyższanie poziomu jakości przez ilość.

-   Kontenery specyficzne dla IoT Edge (nie losowych z nich).

-   Klasy korporacyjnej w środowisku produkcyjnym.

-   Wdrażanie 1 (co najmniej zestaw domyślnych wartości konfiguracji pod warunkiem).

## <a name="any-other-questions"></a>Inne pytania?


Skontaktuj się z pomocą [usługi Azure IoT krawędzi wprowadzającego](mailto:azureiotedgeonboarding@service.microsoft.com).
