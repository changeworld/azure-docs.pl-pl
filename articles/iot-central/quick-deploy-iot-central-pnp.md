---
title: Tworzenie aplikacji usługi Azure IoT Central | Microsoft Docs
description: Utwórz nową aplikację usługi Azure IoT Central. Tworzenie aplikacji w wersji próbnej lub z opcją płatność zgodnie z rzeczywistym użyciem przy użyciu szablonu aplikacji.
author: viv-liu
ms.author: viviali
ms.date: 06/07/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 3d5dfe824d95506fa3f83f1fbbdca1e29e02d566
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001300"
---
# <a name="create-an-azure-iot-central-application-preview-features"></a>Tworzenie aplikacji IoT Central platformy Azure (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Ten przewodnik Szybki Start przedstawia sposób tworzenia aplikacji IoT Central platformy Azure, która korzysta z funkcji w wersji zapoznawczej, takich jak IoT Plug and Play.

> [!WARNING]
> Możliwości Plug and Play IoT na platformie Azure IoT Central są obecnie dostępne w publicznej wersji zapoznawczej. Nie należy używać Plug and Play IoT włączonych IoT Central aplikacji dla obciążeń produkcyjnych. W przypadku środowisk produkcyjnych używana jest aplikacja IoT Central utworzona na podstawie bieżącego, ogólnie dostępnego szablonu aplikacji.

## <a name="create-an-application"></a>Tworzenie aplikacji

Przejdź do witryny sieci Web programu [Azure IoT Central Application Manager](https://aka.ms/iotcentral) . Następnie zaloguj się przy użyciu konta osobistego, służbowego lub szkolnego firmy Microsoft.

Aby rozpocząć tworzenie nowej aplikacji IoT Central platformy Azure, wybierz pozycję **Nowa aplikacja**. Ten link prowadzi do strony **Tworzenie aplikacji** .

![IoT Central tworzenia strony aplikacji przez platformę Azure](media/quick-deploy-iot-central-pnp/iotcentralcreate-pnp.png)

Aby utworzyć nową aplikację usługi Azure IoT Central, która obejmuje funkcje w wersji zapoznawczej, takie jak usługa IoT Plug and Play:

1. Wybierz plan płatności:
   - **Wersje próbne** aplikacji są bezpłatne przez siedem dni przed ich wygaśnięciem. Można je przekonwertować na **płatność zgodnie z rzeczywistym** użyciem w dowolnym momencie przed wygaśnięciem. W przypadku tworzenia aplikacji w **wersji próbnej** należy wprowadzić informacje kontaktowe i zdecydować, czy otrzymywać informacje i porady od firmy Microsoft.
   - W przypadku aplikacji z **opłatą zgodnie** z rzeczywistym użyciem opłaty są naliczone za urządzenie, a pierwsze pięć urządzeń jest bezpłatnych. W przypadku tworzenia aplikacji z **opcją płatność zgodnie z rzeczywistym** użyciem należy wybrać *katalog*, *subskrypcję platformy Azure*i *region*:
        - *Katalog* to Azure Active Directory (AD), aby utworzyć aplikację. Zawiera tożsamości użytkowników, poświadczenia i inne informacje o organizacji. Jeśli nie masz usługi Azure AD, po utworzeniu subskrypcji platformy Azure zostanie utworzona jedna z nich.
        - *Subskrypcja platformy Azure* umożliwia tworzenie wystąpień usług platformy Azure. IoT Central udostępniają zasoby w ramach subskrypcji. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na [stronie rejestracji na platformie Azure](https://aka.ms/createazuresubscription). Po utworzeniu subskrypcji platformy Azure przejdź z powrotem do strony **Tworzenie aplikacji** . Twoja nowa subskrypcja zostanie wyświetlona na liście rozwijanej **subskrypcja platformy Azure** .
        - *Region* jest fizyczną lokalizacją, w której chcesz utworzyć aplikację. Zazwyczaj należy wybrać region, który znajduje się fizycznie najbliżej Twoich urządzeń, aby uzyskać optymalną wydajność. W publicznej wersji zapoznawczej jedyne dostępne regiony dla **aplikacji w wersji zapoznawczej** to **Europa Północna** i **środkowe stany USA**. Po wybraniu regionu nie można później przenieść aplikacji do innego regionu.

        Dowiedz się więcej o cenach na [stronie cennika usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

1. Wybierz szablon **aplikacji w wersji zapoznawczej** . Szablon aplikacji może zawierać wstępnie zdefiniowane elementy, takie jak szablony urządzeń i pulpity nawigacyjne ułatwiające rozpoczęcie pracy.

1. Usługa Azure IoT Central automatycznie sugeruje nazwę aplikacji w oparciu o wybrany szablon aplikacji. Możesz zaakceptować tę nazwę lub wprowadzić własną przyjazną nazwę aplikacji, taką jak **contoso IoT**. Usługa Azure IoT Central generuje również unikatowy prefiks adresu URL na podstawie nazwy aplikacji. Jeśli chcesz, możesz zmienić ten prefiks adresu URL na bardziej zapamiętanie.

1. Wypełnij dodatkowe informacje wymagane dla wybranego wcześniej planu płatności, w kroku 1.

1. Wybierz pozycję **Utwórz** w dolnej części strony.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono aplikację IoT Central, która korzysta z funkcji w wersji zapoznawczej. Oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Definiowanie nowego typu urządzenia w aplikacji IoT Central platformy Azure](./tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
