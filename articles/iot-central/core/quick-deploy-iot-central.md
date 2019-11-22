---
title: Tworzenie aplikacji usługi Azure IoT Central | Microsoft Docs
description: Tworzenie nowej aplikacji usługi Azure IoT Central. Utwórz aplikację typu Wersja próbna lub Płatność zgodnie z rzeczywistym użyciem przy użyciu szablonu aplikacji.
author: viv-liu
ms.author: viviali
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: ba131002df64f972aa921a0f002a6d26c58f21ff
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280616"
---
# <a name="create-an-azure-iot-central-application"></a>Tworzenie aplikacji usługi Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Jako _konstruktor_ użyjesz interfejsu użytkownika usługi Azure IoT Central w celu zdefiniowania aplikacji usługi Microsoft Azure IoT Central. Ten przewodnik Szybki Start przedstawia sposób tworzenia aplikacji IoT Central platformy Azure, która zawiera przykładowy _szablon urządzenia_. Utworzona aplikacja nie korzysta z żadnych funkcji w wersji zapoznawczej.

## <a name="create-an-application"></a>Tworzenie aplikacji

Przejdź do witryny sieci Web [usługi Azure IoT Central Build](https://aka.ms/iotcentral) . Następnie zaloguj się przy użyciu konta osobistego, służbowego lub szkolnego firmy Microsoft.

Aby rozpocząć tworzenie aplikacji IoT Central platformy Azure bez włączonej funkcji wersji zapoznawczej, wybierz pozycję **Kompiluj**. Ten link prowadzi do strony **Kompilowanie aplikacji IoT** .

![Strona kompilacji usługi Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate.png)

Następnie wybierz pozycję **aplikacja niestandardowa**.

Aby utworzyć nową aplikację usługi Azure IoT Central:

1. Usługa Azure IoT Central automatycznie sugeruje nazwę aplikacji w oparciu o wybrany szablon aplikacji. Możesz zaakceptować tę nazwę lub wprowadzić własną przyjazną nazwę aplikacji, taką jak **contoso IoT**. Usługa Azure IoT Central generuje również unikatowy adres URL na podstawie nazwy aplikacji. Jeśli chcesz, możesz zmienić ten prefiks adresu URL na bardziej zapamiętanie.

1. Wybierz szablon aplikacji, który nie korzysta z funkcji w wersji zapoznawczej. Szablon aplikacji może zawierać wstępnie zdefiniowane elementy, takie jak pulpity nawigacyjne i szablony urządzeń, które pomogą Ci rozpocząć pracę.

    | Szablon aplikacji | Opis |
    | -------------------- | ----------- |
    | Przykład dotyczący firmy Contoso       | Tworzy aplikację, która zawiera już utworzony szablon urządzenia typu Automat do sprzedaży z chłodziarką. Użyj tego szablonu, aby rozpocząć eksplorowanie usługi Azure IoT Central. |
    | Przykład dotyczący zestawów deweloperskich       | Tworzy aplikację z gotowymi szablonami urządzeń, które umożliwiają łączenie z urządzeniem MXChip lub Raspberry Pi. Użyj tego szablonu, jeśli jesteś deweloperem urządzenia, który przeprowadza eksperymenty związane z dowolnym spośród tych urządzeń. |
    | Aplikacja niestandardowa   | Tworzy pustą aplikację, którą wypełnisz własnymi urządzeniami i szablonami urządzeń. |

1. Wybierz plan płatności:
   - **7-dniowe bezpłatne wersje próbne** są bezpłatne przez siedem dni przed ich wygaśnięciem. Można je przekonwertować na **płatność zgodnie z rzeczywistym** użyciem w dowolnym momencie przed wygaśnięciem. W przypadku tworzenia aplikacji w **wersji próbnej** należy wprowadzić informacje kontaktowe i zdecydować, czy otrzymywać informacje i porady od firmy Microsoft.
   - W przypadku aplikacji z **opłatą zgodnie** z rzeczywistym użyciem opłaty są naliczone za urządzenie, a pierwsze pięć urządzeń jest bezpłatnych. W przypadku tworzenia aplikacji z **opcją płatność zgodnie z rzeczywistym** użyciem należy wybrać *katalog*, *subskrypcję platformy Azure*i *lokalizację*:
        - *Katalog* to Azure Active Directory (AD), aby utworzyć aplikację. Zawiera on tożsamości użytkowników, poświadczenia i inne informacje o organizacji. Jeśli nie masz usługi Azure AD, po utworzeniu subskrypcji platformy Azure zostanie utworzona jedna z nich.
        - *Subskrypcja platformy Azure* umożliwia tworzenie wystąpień usług platformy Azure. IoT Central udostępniają zasoby w ramach subskrypcji. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na [stronie tworzenia konta platformy Azure](https://aka.ms/createazuresubscription). Po utworzeniu subskrypcji platformy Azure przejdź z powrotem do strony **Tworzenie aplikacji** . Nowa subskrypcja zostanie wyświetlona na liście rozwijanej **Subskrypcja platformy Azure**.
        - *Lokalizacja jest lokalizacją* geograficzną, [w której chcesz](https://azure.microsoft.com/global-infrastructure/geographies/) utworzyć aplikację. Zazwyczaj należy wybrać lokalizację, która jest fizycznie najbliżej Twoich urządzeń, aby uzyskać optymalną wydajność. Usługa Azure IoT Central jest obecnie dostępna w **Stany Zjednoczone**, **Australii**, **Azja i Pacyfik**lub w **Europie**.  Po wybraniu lokalizacji nie można przenieść aplikacji do innej lokalizacji później.

        Więcej informacji o cenach znajdziesz na [stronie cennika usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

1. Wypełnij dodatkowe informacje wymagane dla wybranego wcześniej planu płatności, w kroku 1.

1. Wybierz pozycję **Utwórz** w dolnej części strony.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono aplikację usługi IoT Central. Poniżej przedstawiono następny sugerowany krok:

> [!div class="nextstepaction"]
> [Definiowanie nowego typu urządzenia w aplikacji IoT Central platformy Azure](./tutorial-define-device-type.md)
