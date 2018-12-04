---
title: Utwórz ofertę moduł usługi IoT Edge | Dokumentacja firmy Microsoft
description: Jak opublikować nowy moduł usługi IoT Edge w portalu Marketplace.
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
ms.openlocfilehash: bf7d639c682e443f29b31b3c6d7438e89c406fde
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52838637"
---
# <a name="how-to-publish-a-new-iot-edge-module-in-the-cloud-partner-portal"></a>Jak opublikować nowy moduł usługi IoT Edge w portalu Cloud Partner

W tym artykule opisano kroki umożliwiające publikowanie nowej oferty modułu usługi IoT Edge.

## <a name="prerequisites"></a>Wymagania wstępne

Następujące wymagania wstępne dotyczą publikowania moduł usługi IoT Edge w portalu Azure Marketplace.

-   Dostęp do [portalu dla partnerów (CPP) w chmurze](https://cloudpartner.azure.com/#alloffers). Aby uzyskać więcej informacji, zobacz [Podręcznik publikowania](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

-   Mieć modułu usługi IoT Edge hostowanej w usłudze Azure Container Registry.

-   Mieć gotowy (w tym otwarta lista) metadane modułu usługi IoT Edge:

    -   Stanowisko

    -   Opis (podstawowy format HTML)

    -   Logo w formacie obrazu png i następujących rozmiarów: 40 pikseli x 40 pikseli, 90 pikseli x 90 pikseli, 115 pikseli x 115 pikseli, 255 pikseli x 115 pikseli.

    -   Warunki użycia i zasady zachowania poufności

<a name="prepare-your-iot-edge-module-listing-in-cpp"></a>Przygotuj swoje listy Moduł usługi IoT Edge w CPP
-------------------------------------------

### <a name="create-a-new-offer-of-the-type-iot-edge-module"></a>Utwórz nową ofertę typu moduł usługi IoT Edge 

Wykonaj następujące kroki, aby przygotować swoje listy Moduł usługi IoT Edge:

-   Zaloguj się do Twojej [konta CPP](https://cloudpartner.azure.com/).

>[!Note]
>Aby uzyskać ogólne informacje o portalu Cloud Partner, można sprawdzić [Dowiedz się więcej dokumentacji](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-getting-started-with-the-cloud-partner-portal)

-   Wybierz **nowa oferta**, a następnie wybierz pozycję **moduł usługi IoT Edge**.

>[!NOTE]
>Moduł usługi IoT Edge to kontener, który jest celowo do uruchamiania w usłudze IoT Edge. Scenariusze obsługiwane przez moduł usługi IoT Edge muszą mieć sens w kontekście usługi IoT Edge. Zawiera on również domyślne ustawienia konfiguracji, aby wdrożenie usługi IoT Edge urządzenia proste. Kontener może również obejmować SDK IoT Edge modułu celu umożliwienia komunikacji z Centrum IoT i edgeHub.

### <a name="define-your-offer-settings"></a>Skonfiguruj wymagane ustawienia oferty

Na karcie Ustawienia oferty wprowadź informacje o ofercie.

![Zaoferowania tożsamości](./media/cloud-partner-portal-create-iot-edge-module-offer/offer-identity.png)


-   **Identyfikatora oferty Offer ID** unikatowo identyfikuje oferty CPP i mogą być używane w adresach URL przeznaczonych dla klientów.

-   **Nazwa** jest widoczna tylko przez Ciebie dla odwołujące się do tej oferty w CPP.

### <a name="create-one-or-more-skus"></a>Utwórz jeden lub więcej jednostek SKU

Każda jednostka SKU odnosi się do obrazu kontenera. Musi mieć co najmniej jednej jednostki SKU i można dodać więcej niż jeden. Istnieją dwie części do jednostki SKU:

-   Metadane jednostki SKU

-   Metadane kontenera

**Aby utworzyć jednostkę SKU:**

Wybierz **jednostki SKU** , a następnie wybierz pozycję **nowej jednostki SKU**.

![Nowej jednostki SKU](./media/cloud-partner-portal-create-iot-edge-module-offer/SKUs.png)

Metadane jednostki SKU zawierają następujące pola, które są wymagane:
- Identyfikator jednostki SKU — Unikatowy identyfikator.
- Tytuł — Nazwa jednostki SKU, maksymalnie 50 znaków.
- Podsumowanie — krótki opis do 100 znaków.
- Opis — długi opis.
- Ukryj tej jednostki SKU — wartość domyślna to **nie**.
   
![Szczegóły jednostki SKU](./media/cloud-partner-portal-create-iot-edge-module-offer/SKU-settings.png)

#### <a name="iot-edge-module-metadata-and-the-container-registry"></a>Metadane modułu usługi IoT Edge i container registry

Moduł usługi IoT Edge, metadane zawierają informacje obrazu, który jest przechowywany w usłudze Azure container registry (ACR). W portalu Azure Marketplace kopiuje obraz do rejestru publicznej platformy handlowej i jest dostępna dla klientów po certyfikacji. Wszystkie żądania użytkownika korzystanie z obrazu modułu usługi IoT Edge są obsługiwane z rejestru kontenerów w portalu Marketplace.

![Obrazy kontenera](./media/cloud-partner-portal-create-iot-edge-module-offer/container-images.png)

**Obrazy kontenera**

Szczegółowe informacje dotyczące obrazu repozytorium ma następujące wymagane pola:

-   **Identyfikator subskrypcji** identyfikator subskrypcji platformy Azure, gdzie występuje rejestru ACR.

-   **Nazwa grupy zasobów** — Nazwa grupy zasobów z rejestru ACR.

-   **Nazwa rejestru** — nazwa rejestru ACR.

-   **Nazwa repozytorium** — Nazwa repozytorium. Po ustawieniu tej wartości nie można zmienić później. Nazwa powinna być unikatowa, tak aby żadne inne oferty w ramach konta usługi ma taką samą nazwę.

-   **Nazwa użytkownika** — nazwę użytkownika skojarzoną z usługą ACR (nazwa użytkownika administratora).

-   **Hasło** — hasło skojarzone z rejestru Azure container Registry.

    >[!Note]
    >Nazwa użytkownika i hasło są wymagane, aby upewnić się, że partnerzy mają dostęp do usługi ACR, opisanego w procesie publikowania.

Gdy publikujesz do obrazu modułu usługi IoT Edge, możesz podać jeden lub więcej tagów obrazu. Pamiętaj dodać tag "najnowszy" (wartość domyślna) do modułu, dzięki czemu można łatwo zidentyfikować moduł podczas testowania.

Można również określić następujące szczegóły modułu usługi IoT Edge:

-   **CreateOptions, można żądań** — domyślne CreateOptions, można żądań do przekazania, więc ten moduł usługi IoT Edge mogą być uruchamiane poza pole.

-   **bliźniaczej reprezentacji:** -twin domyślne do przekazania, więc ten moduł usługi IoT Edge można uruchomić gotowych podczas korzystania z zestawu SDK modułu IoT.

-   **trasy:** -tras domyślnych do przekazania, tak aby ten moduł usługi IoT Edge mogą być uruchamiane gotowych, jeśli za pomocą zestawu SDK usługi IoT moduł.

### <a name="describe-your-iot-edge-module-for-your-customers"></a>Opis modułu usługi IoT Edge dla klientów

W portalu Marketplace na karcie Dodaj zawartość specyficzne dla obrotu. Te informacje są na tym, jakie będą publicznie widoczne i wyświetlane w portalu Azure Marketplace.

![Omówienie modułu usługi IoT Edge](./media/cloud-partner-portal-create-iot-edge-module-offer/overview.png)

-   **Tytuł** — tytuł Twojego moduł usługi IoT Edge, która jest publiczną.

-   **Podsumowanie** — podsumowanie Twojego modułu usługi IoT Edge, który jest publicznie dostępnych w większości stron, takich jak przeglądanie stron.

-   **Długie Podsumowanie** — podsumowanie Twojego moduł usługi IoT Edge, która jest publiczną gdy udostępniona przez moduł. 

-   **Opis** — opis modułu usługi IoT Edge, który jest publicznie dostępnych na stronie szczegółów produktu. (Można użyć podstawowa tagów HTML do formatowania opis.)

-   **Identyfikator Marketing** — Unikatowy identyfikator, który jest używany do tworzenia adresu URL produktu. Ten adres URL znajduje się w następującym formacie: *azuremarketplace.microsoft.com/enus/marketplace/apps/yourpublisherid.youriotedgemodulemarketingidentifier*.

-   **Identyfikator subskrypcji w wersji zapoznawczej** — użytkowników, którzy mają dostęp do tych subskrypcji będą mogli zobaczyć moduł usługi IoT Edge po wykonaniu kroku certyfikacji i przed przechodzi na żywo.

-   **Przydatne linki** — można dodać łącza do 10, które będą wyświetlane na stronie szczegółów produktu.

-   **Kategorii sugerowanych** — wybierz maksymalnie pięć kategorii. Będą one wyświetlane na stronie szczegółów produktu. Obecnie wszystkie moduły usługi IoT Edge zostaną wyświetlone w obszarze *Internet of Things \> moduł usługi IoT Edge* kategorii na stronie przeglądania.

-   **Logo** — przekazywanie obrazów logo modułu usługi IoT Edge w formacie PNG. Użyj następujących rozmiarów: dokładnie w następujących rozmiarów: 40 pikseli x 40 pikseli, 90 pikseli x 90 pikseli, 115 pikseli x 115 pikseli, 255 pikseli x 115 pikseli.

-   **Zrzuty ekranu** -zrzuty ekranu są wyświetlane na stronie szczegółów produktu. Są one dobrym sposobem na wizualne przedstawianie działanie modułu usługi IoT Edge i sposób jej działania. Można wyświetlić diagramy architektury, lub użyj ilustracje wielkości liter dla wystąpienia.

-   **Filmy wideo** — filmy wideo są wyświetlane na stronie szczegółów produktu. Są one dobrym sposobem na wizualne przedstawianie działanie modułu usługi IoT Edge i sposób jej działania.

-   **Zarządzanie potencjalnymi** — możesz wybrać system do zbierania wszystkich potencjalnych klientów, które pokazują zainteresowanie produktu.

-   **Ochrona prywatności** — musi mieć adres URL zasad ochrony prywatności.

-   **Warunki użytkowania** — musi mieć warunki użytkowania. Aby sformatować tę stronę lub wskazywały na jeden z innych stron sieci, można użyć tagów HTML.

### <a name="enter-your-support-contact-information"></a>Wprowadź informacje kontaktowe pomocy technicznej

Na karcie pomocy technicznej podaj **kontakt techniczny** i **techniczną** informacji.

![Kontakt z pomocą techniczną](./media/cloud-partner-portal-create-iot-edge-module-offer/support-contact-info.png)


## <a name="certify-your-iot-edge-module"></a>Certyfikowanie modułu usługi IoT Edge

Po podaniu wszystkich informacji, które są wymagane, zaznacz **Publikuj** wysyłać modułu usługi IoT Edge do certyfikacji. Zobaczysz, że oś czasu, aby pokazać, jego kroki w procesie certyfikacji.

**Moduł weryfikacji**

Modułu jest weryfikowany przez nasz zespół certyfikacji. Po certyfikowaniu moduł uzyskasz prywatnego linku do testowania modułu. Jeśli zachodzi potrzeba zmiany po zakończeniu testowania, edytować metadane swoją ofertę i Prześlij ponownie moduł do zespołu certyfikacji. 

## <a name="publish-your-iot-edge-module"></a>Publikowanie modułu usługi IoT Edge

Po zakończeniu testowania i są gotowe do opublikowania, wybierz **Go Live** publikowanie modułu usługi IoT Edge.

>[!Important]
>Jeśli chcesz mieć swoje moduł usługi IoT Edge, o którym poinformowano na Konferencję Ignite modułu muszą być publiczne 09/23/2018 r.
