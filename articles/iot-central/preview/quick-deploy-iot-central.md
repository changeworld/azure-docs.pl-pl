---
title: Tworzenie aplikacji usługi Azure IoT Central | Microsoft Docs
description: Tworzenie nowej aplikacji usługi Azure IoT Central. Utwórz aplikację typu Wersja próbna lub Płatność zgodnie z rzeczywistym użyciem przy użyciu szablonu aplikacji.
author: lmasieri
ms.author: lmasieri
ms.date: 12/18/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: bbb287805ac0351153778191d484bae2ef319705
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434791"
---
# <a name="create-an-azure-iot-central-application-preview-features"></a>Tworzenie aplikacji IoT Central platformy Azure (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ten przewodnik Szybki Start przedstawia sposób tworzenia aplikacji IoT Central platformy Azure, która zawiera funkcje w wersji zapoznawczej, takie jak usługa IoT Plug and Play.

> [!WARNING]
> Możliwości Plug and Play IoT na platformie Azure IoT Central są obecnie dostępne w publicznej wersji zapoznawczej. Nie należy używać Plug and Play IoT włączonych IoT Central aplikacji dla obciążeń produkcyjnych. W przypadku środowisk produkcyjnych używana jest aplikacja IoT Central utworzona na podstawie bieżącego, ogólnie dostępnego szablonu aplikacji.

## <a name="create-an-application"></a>Tworzenie aplikacji

Przejdź do witryny [Azure IoT Central Build](https://aka.ms/iotcentral) . Następnie zaloguj się przy użyciu konta osobistego, służbowego lub szkolnego firmy Microsoft.

Tworzysz nową aplikację z listy IoT Central szablonów odpowiednich dla branż, aby pomóc szybko rozpocząć pracę, lub Zacznij od podstaw, korzystając z szablonu **aplikacji niestandardowej** .

![IoT Central tworzenia strony aplikacji przez platformę Azure](media/quick-deploy-iot-central/iotcentralcreate-templates-pnp.png)

Aby utworzyć nową aplikację usługi Azure IoT Central:

1. Aby utworzyć nową aplikację IoT Central platformy Azure na podstawie *szablonu branżowego*, wybierz szablon aplikacji z listy dostępnych szablonów w ramach jednej z branż. Możesz również zacząć od podstaw, wybierając pozycję *aplikacja niestandardowa*.
1. Usługa Azure IoT Central automatycznie sugeruje **nazwę aplikacji** w oparciu o wybrany szablon aplikacji. Możesz użyć tej nazwy lub wprowadzić własną przyjazną nazwę aplikacji.
1. Usługa Azure IoT Central generuje również unikatowy prefiks **adresu URL aplikacji** na podstawie nazwy aplikacji. Ten adres URL jest używany do uzyskiwania dostępu do aplikacji. Jeśli chcesz, możesz zmienić ten prefiks adresu URL na bardziej zapamiętanie.

    ![IoT Central tworzenia strony aplikacji przez platformę Azure](media/quick-deploy-iot-central/iotcentralcreate-industry-pnp.png)

    > [!NOTE]
    > Jeśli używasz szablonu aplikacji niestandardowej, zobaczysz pole listy rozwijanej **szablon aplikacji** . W tym miejscu możesz przełączać się między podglądem i ogólnie dostępnymi szablonami. Mogą również być widoczne inne szablony, które zostały udostępnione dla Twojej organizacji.

1. Wybierz, czy chcesz utworzyć tę aplikację przy użyciu 7-dniowej bezpłatnej wersji próbnej, czy użyć subskrypcji płatnej zgodnie z rzeczywistym użyciem.
    - **Wersje próbne** aplikacji są bezpłatne przez siedem dni i obsługują maksymalnie pięć urządzeń. W dowolnym momencie przed wygaśnięciem można przekonwertować te aplikacje na typ Płatność zgodnie z rzeczywistym użyciem. W przypadku tworzenia aplikacji w wersji próbnej należy wprowadzić informacje kontaktowe i zdecydować, czy otrzymywać informacje i porady od firmy Microsoft.
    - W przypadku aplikacji obsługujących **płatność zgodnie z rzeczywistym** użyciem opłaty są naliczane na podstawie poszczególnych urządzeń, przy czym dwa pierwsze urządzenia są bezpłatne. Dowiedz się więcej o [cenach IoT Central](https://aka.ms/iotcentral-pricing). W przypadku tworzenia aplikacji z opcją płatność zgodnie z rzeczywistym użyciem należy wybrać *katalog*, *subskrypcję platformy Azure*i *lokalizację*:
        - *Katalog* jest Azure Active Directory (AAD), w którym utworzysz aplikację. Usługa Azure AD zawiera tożsamości użytkowników, poświadczenia i inne informacje o organizacji. Jeśli nie masz usługi Azure AD, po utworzeniu subskrypcji platformy Azure zostanie utworzona jedna z nich.
        - *Subskrypcja platformy Azure* umożliwia tworzenie wystąpień usług platformy Azure. IoT Central udostępniają zasoby w ramach subskrypcji. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na [stronie tworzenia konta platformy Azure](https://aka.ms/createazuresubscription). Po utworzeniu subskrypcji platformy Azure przejdź z powrotem do strony **Tworzenie aplikacji** . Nowa subskrypcja zostanie wyświetlona na liście rozwijanej **subskrypcja platformy Azure** .
        - *Lokalizacja jest lokalizacją* geograficzną, [w której chcesz](https://azure.microsoft.com/global-infrastructure/geographies/) utworzyć aplikację. Zazwyczaj należy wybrać lokalizację, która jest fizycznie najbliżej Twoich urządzeń, aby uzyskać optymalną wydajność. Usługa Azure IoT Central w publicznej wersji zapoznawczej jest obecnie dostępna w **Stany Zjednoczone**lub w **Europie**. Po wybraniu lokalizacji nie można przenieść aplikacji do innej lokalizacji później.

        > [!NOTE]
        > W publicznej wersji zapoznawczej jedyne dostępne lokalizacje dla **aplikacji w wersji zapoznawczej** to **Europa** i **Stany Zjednoczone**.

1. Przejrzyj warunki i postanowienia, a następnie wybierz pozycję **Utwórz** w dolnej części strony.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono aplikację usługi IoT Central. Poniżej przedstawiono następny sugerowany krok:

> [!div class="nextstepaction"]
> [Dodawanie symulowanego urządzenia do aplikacji IoT Central](./quick-create-pnp-device.md)
