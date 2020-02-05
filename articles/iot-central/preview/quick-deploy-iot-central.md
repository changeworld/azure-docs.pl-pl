---
title: Tworzenie aplikacji usługi Azure IoT Central | Microsoft Docs
description: Tworzenie nowej aplikacji usługi Azure IoT Central. Tworzenie aplikacji w wersji próbnej lub standardowej przy użyciu szablonu aplikacji.
author: lmasieri
ms.author: lmasieri
ms.date: 12/18/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 1b19909f005fa11b842fccc0497cb49960e32a3b
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989731"
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

    ![IoT Central tworzenia strony aplikacji przez platformę Azure](media/quick-deploy-iot-central/iotcentralcreate-billinginfo-pnp.png)
    > [!NOTE]
    > Jeśli używasz szablonu aplikacji niestandardowej, zobaczysz pole listy rozwijanej **szablon aplikacji** . W tym miejscu możesz przełączać się między podglądem i ogólnie dostępnymi szablonami. Mogą również być widoczne inne szablony, które zostały udostępnione dla Twojej organizacji.

1. Wybierz, czy chcesz utworzyć tę aplikację przy użyciu 7-dniowej bezpłatnej wersji próbnej, czy użyć subskrypcji standardowej.
    - W przypadku aplikacji tworzonych przy użyciu planu w **warstwie Standardowa** są naliczane opłaty za poszczególne urządzenia. możesz wybrać plan cenowy w **warstwie Standardowa 1** lub **standardowa 2** z dwoma bezpłatnymi urządzeniami. Dowiedz się więcej o planach cen bezpłatnych i standardowych na [stronie cennika usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). W przypadku tworzenia aplikacji standardowej należy wybrać *katalog*, *subskrypcję platformy Azure*i *lokalizację*:
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
