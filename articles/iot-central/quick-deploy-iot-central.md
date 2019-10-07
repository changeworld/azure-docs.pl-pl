---
title: Tworzenie aplikacji usługi Azure IoT Central | Microsoft Docs
description: Utwórz nową aplikację usługi Azure IoT Central. Tworzenie aplikacji w wersji próbnej lub z opcją płatność zgodnie z rzeczywistym użyciem przy użyciu szablonu aplikacji.
author: viv-liu
ms.author: viviali
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 23ef0afbf3a3fd3e0d0db6e3b4130b45530916be
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001271"
---
# <a name="create-an-azure-iot-central-application"></a>Tworzenie aplikacji IoT Central platformy Azure

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Jako _Konstruktor_, UŻYJESZ interfejsu użytkownika usługi Azure IoT Central, aby zdefiniować aplikację Microsoft Azure IoT Central. Ten przewodnik Szybki Start przedstawia sposób tworzenia aplikacji IoT Central platformy Azure, która zawiera przykładowy _szablon urządzenia_.

## <a name="create-an-application"></a>Tworzenie aplikacji

Przejdź do witryny sieci Web programu [Azure IoT Central Application Manager](https://aka.ms/iotcentral) . Następnie zaloguj się przy użyciu konta osobistego, służbowego lub szkolnego firmy Microsoft.

Aby rozpocząć tworzenie nowej aplikacji IoT Central platformy Azure, wybierz pozycję **Nowa aplikacja**. Ten link prowadzi do strony **Tworzenie aplikacji** .

![IoT Central tworzenia strony aplikacji przez platformę Azure](media/quick-deploy-iot-central/iotcentralcreate.png)

Aby utworzyć nową aplikację usługi Azure IoT Central:

1. Wybierz plan płatności:
   - **Wersje próbne** aplikacji są bezpłatne przez siedem dni przed ich wygaśnięciem. Można je przekonwertować na **płatność zgodnie z rzeczywistym** użyciem w dowolnym momencie przed wygaśnięciem. W przypadku tworzenia aplikacji w **wersji próbnej** należy wprowadzić informacje kontaktowe i zdecydować, czy otrzymywać informacje i porady od firmy Microsoft.
   - W przypadku aplikacji z **opłatą zgodnie** z rzeczywistym użyciem opłaty są naliczone za urządzenie, a pierwsze pięć urządzeń jest bezpłatnych. W przypadku tworzenia aplikacji z **opcją płatność zgodnie z rzeczywistym** użyciem należy wybrać *katalog*, *subskrypcję platformy Azure*i *region*:
        - *Katalog* to Azure Active Directory (AD), aby utworzyć aplikację. Zawiera tożsamości użytkowników, poświadczenia i inne informacje o organizacji. Jeśli nie masz usługi Azure AD, po utworzeniu subskrypcji platformy Azure zostanie utworzona jedna z nich.
        - *Subskrypcja platformy Azure* umożliwia tworzenie wystąpień usług platformy Azure. IoT Central udostępniają zasoby w ramach subskrypcji. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na [stronie rejestracji na platformie Azure](https://aka.ms/createazuresubscription). Po utworzeniu subskrypcji platformy Azure przejdź z powrotem do strony **Tworzenie aplikacji** . Twoja nowa subskrypcja zostanie wyświetlona na liście rozwijanej **subskrypcja platformy Azure** .
        - *Region* jest fizyczną lokalizacją lub [geografią](https://azure.microsoft.com/global-infrastructure/geographies/) , w której chcesz utworzyć aplikację. Zazwyczaj należy wybrać region, który znajduje się fizycznie najbliżej Twoich urządzeń, aby uzyskać optymalną wydajność. Możesz zobaczyć regiony, w których usługa Azure IoT Central jest dostępna na stronie [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=iot-central) . Po wybraniu regionu nie można później przenieść aplikacji do innego regionu.

        Dowiedz się więcej o cenach na [stronie cennika usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

1. Wybierz szablon aplikacji. Szablon aplikacji może zawierać wstępnie zdefiniowane elementy, takie jak szablony urządzeń i pulpity nawigacyjne ułatwiające rozpoczęcie pracy.

    | Szablon aplikacji | Opis |
    | -------------------- | ----------- |
    | Przykład firmy Contoso       | Tworzy aplikację, która zawiera szablon urządzenia, który został już utworzony dla maszyny w stanie chłodzenia. Użyj tego szablonu, aby rozpocząć Eksplorowanie IoT Central platformy Azure. |
    | Przykład Devkits       | Tworzy aplikację z szablonami urządzeń gotowymi do połączenia urządzenia zestawu deweloperskiego lub Raspberry Pi. Użyj tego szablonu, jeśli jesteś programistą dla deweloperów urządzeń przy użyciu dowolnego z tych urządzeń. |
    | Aplikacja niestandardowa   | Tworzy pustą aplikację do wypełnienia własnymi szablonami urządzeń i urządzeniami. |

1. Usługa Azure IoT Central automatycznie sugeruje nazwę aplikacji w oparciu o wybrany szablon aplikacji. Możesz zaakceptować tę nazwę lub wprowadzić własną przyjazną nazwę aplikacji, taką jak **contoso IoT**. Usługa Azure IoT Central generuje również unikatowy prefiks adresu URL na podstawie nazwy aplikacji. Jeśli chcesz, możesz zmienić ten prefiks adresu URL na bardziej zapamiętanie.

1. Wypełnij dodatkowe informacje wymagane dla wybranego wcześniej planu płatności, w kroku 1.

1. Wybierz pozycję **Utwórz** w dolnej części strony.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono aplikację IoT Centralową. Oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Definiowanie nowego typu urządzenia w aplikacji IoT Central platformy Azure](./tutorial-define-device-type.md)
