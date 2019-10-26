---
title: Tworzenie aplikacji usługi Azure IoT Central | Microsoft Docs
description: Tworzenie nowej aplikacji usługi Azure IoT Central. Utwórz aplikację typu Wersja próbna lub Płatność zgodnie z rzeczywistym użyciem przy użyciu szablonu aplikacji.
author: lmasieri
ms.author: lmasieri
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 5672c1868ef33e02a900e8ada1f027c3216ca585
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957671"
---
# <a name="create-an-azure-iot-central-application-preview-features"></a>Tworzenie aplikacji IoT Central platformy Azure (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ten przewodnik Szybki Start przedstawia sposób tworzenia aplikacji IoT Central platformy Azure, która zawiera funkcje w wersji zapoznawczej, takie jak usługa IoT Plug and Play.

> [!WARNING]
> Możliwości Plug and Play IoT na platformie Azure IoT Central są obecnie dostępne w publicznej wersji zapoznawczej. Nie należy używać Plug and Play IoT włączonych IoT Central aplikacji dla obciążeń produkcyjnych. W przypadku środowisk produkcyjnych używana jest aplikacja IoT Central utworzona na podstawie bieżącego, ogólnie dostępnego szablonu aplikacji.

## <a name="create-an-application"></a>Tworzenie aplikacji

Przejdź do witryny [Azure IoT Central Build](https://aka.ms/iotcentral) . Następnie zaloguj się przy użyciu konta osobistego, służbowego lub szkolnego firmy Microsoft.

Tworzysz nową aplikację z listy IoT Central szablonów odpowiednich dla branż, aby pomóc szybko rozpocząć pracę, lub Zacznij od podstaw, korzystając z szablonu **aplikacji niestandardowej** .

![IoT Central tworzenia strony aplikacji przez platformę Azure](media/quick-deploy-iot-central-pnp/iotcentralcreate-templates-pnp.png)

Aby utworzyć nową aplikację usługi Azure IoT Central:

1. Aby utworzyć nową aplikację IoT Central platformy Azure na podstawie *szablonu branżowego*, wybierz szablon aplikacji z listy dostępnych szablonów w ramach jednej z branż. Możesz również zacząć od podstaw, wybierając pozycję *aplikacja niestandardowa*.
1. Usługa Azure IoT Central automatycznie sugeruje **nazwę aplikacji** w oparciu o wybrany szablon aplikacji. Możesz użyć tej nazwy lub wprowadzić własną przyjazną nazwę aplikacji.
1. Usługa Azure IoT Central generuje również unikatowy prefiks **adresu URL aplikacji** na podstawie nazwy aplikacji. Ten adres URL jest używany do uzyskiwania dostępu do aplikacji. Jeśli chcesz, możesz zmienić ten prefiks adresu URL na bardziej zapamiętanie.

    ![IoT Central tworzenia strony aplikacji przez platformę Azure](media/quick-deploy-iot-central-pnp/iotcentralcreate-industry-pnp.png)

    > [!NOTE]
    > Jeśli używasz szablonu aplikacji niestandardowej, zobaczysz pole listy rozwijanej **szablon aplikacji** . W tym miejscu możesz przełączać się między podglądem i ogólnie dostępnymi szablonami. Mogą również być widoczne inne szablony, które zostały udostępnione dla Twojej organizacji.

1. Wybierz, czy chcesz utworzyć tę aplikację przy użyciu 7-dniowej bezpłatnej wersji próbnej, czy użyć subskrypcji płatnej zgodnie z rzeczywistym użyciem.
    - **Wersje próbne** aplikacji są bezpłatne przez siedem dni i obsługują maksymalnie pięć urządzeń. W dowolnym momencie przed wygaśnięciem można przekonwertować te aplikacje na typ Płatność zgodnie z rzeczywistym użyciem. W przypadku tworzenia aplikacji w wersji próbnej należy wprowadzić informacje kontaktowe i zdecydować, czy otrzymywać informacje i porady od firmy Microsoft.
    - W przypadku aplikacji obsługujących **płatność zgodnie z rzeczywistym** użyciem opłaty są naliczane na podstawie poszczególnych urządzeń, przy czym dwa pierwsze urządzenia są bezpłatne. Dowiedz się więcej o [cenach IoT Central](https://aka.ms/iotcentral-pricing). W przypadku tworzenia aplikacji z opcją płatność zgodnie z rzeczywistym użyciem należy wybrać *katalog*, *subskrypcję platformy Azure*i *region*:
        - *Katalog* jest Azure Active Directory (AAD), w którym utworzysz aplikację. Usługa Azure AD zawiera tożsamości użytkowników, poświadczenia i inne informacje o organizacji. Jeśli nie masz usługi Azure AD, po utworzeniu subskrypcji platformy Azure zostanie utworzona jedna z nich.
        - *Subskrypcja platformy Azure* umożliwia tworzenie wystąpień usług platformy Azure. IoT Central udostępniają zasoby w ramach subskrypcji. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na [stronie tworzenia konta platformy Azure](https://aka.ms/createazuresubscription). Po utworzeniu subskrypcji platformy Azure przejdź z powrotem do strony **Tworzenie aplikacji** . Nowa subskrypcja zostanie wyświetlona na liście rozwijanej **subskrypcja platformy Azure** .
        - *Region* to fizyczna lokalizacja, w której będą przechowywane dane urządzeń. Zazwyczaj należy wybrać region, który jest fizycznie najbliższy urządzenia, aby uzyskać optymalną wydajność, a także zapewnić zgodność danych. Po wybraniu regionu nie można później przenieść aplikacji do innego regionu.

        > [!NOTE]
        > W publicznej wersji zapoznawczej jedyne dostępne regiony dla **aplikacji w wersji zapoznawczej** to **Europa Północna** i **środkowe stany USA**.

1. Przejrzyj warunki i postanowienia, a następnie wybierz pozycję **Utwórz** w dolnej części strony.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono aplikację usługi IoT Central. Poniżej przedstawiono następny sugerowany krok:

> [!div class="nextstepaction"]
> [Dodawanie symulowanego urządzenia do aplikacji IoT Central](./quick-create-pnp-device-pnp.md)
