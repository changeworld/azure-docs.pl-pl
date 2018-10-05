---
title: Kontenery | Dokumentacja firmy Microsoft
description: Procedura publikowania obrazu kontenera.
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
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5653e4b2ec9aa5e21a107611b9d4142168630d4a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810601"
---
<a name="publishing-a-container-image-in-the-cloud-partner-portal"></a>Publikowanie obrazu kontenera w portalu Cloud Partner
========================================================

W tym artykule opisano sposób publikowania nowy obraz kontenera w portalu Cloud Partner.

Wykonaj następujące kroki, aby opublikować nowy obraz kontenera.

1. Wybierz **nowa oferta** , a następnie wybierz **kontenerów**

    ![Wybierz opcję kontenerów na potrzeby nowej oferty](media/cpp-containers-guide/azure-container-offer.png)

2. Na karcie Ustawienia oferty, w ramach oferty tożsamości, podaj **identyfikatora oferty Offer ID**, **Identyfikatora wydawcy**, i **nazwa**.

    ![Zaoferowania tożsamości](media/cpp-containers-guide/containers-offer-settings.png)

3. Na karcie jednostki SKU wybierz **nowej jednostki SKU**.
    >[!NOTE]
    >Możesz dodać więcej niż jednej jednostki SKU.

    ![Nowy wiersz w jednostce SKU](media/cpp-containers-guide/containers-sku-settings.png)

4. Podaj informacje jednostki SKU i kontenera. Każda jednostka SKU odnosi się do obrazu kontenera. Istnieją dwie części do jednostki SKU:

    -   Metadane jednostki SKU
    -   Metadane kontenera

    Metadane jednostki SKU zawierają informacje o wyświetlania obrazów kontenera.

    ![Metadane jednostki SKU](media/cpp-containers-guide/containers-sku-details.png)

    Metadane kontenera ma informacje o odwołaniu swojego repozytorium obrazów w usłudze Azure container registry (ACR). Portal Azure Marketplace kopiuje ten obraz do rejestru publicznej platformy handlowej i jest udostępniana dla klientów po certyfikacji. Wszystkie żądania od użytkownika platformy Azure z obrazu kontenera są obsługiwane z rejestru kontenerów w portalu Marketplace.

   ![Metadane kontenera](media/cpp-containers-guide/containers-image-repository.png)

    Szczegóły dotyczące repozytorium obrazów na poprzednim zrzucie ekranu zawiera następujące pola:

    -   **Identyfikator subskrypcji** — identyfikator subskrypcji platformy Azure, gdzie występuje rejestru ACR.
    -   **Nazwa grupy zasobów** — Nazwa grupy zasobów z rejestru ACR.
    -   **Nazwa rejestru** — nazwa rejestru ACR.
    -   **Nazwa repozytorium** — Nazwa repozytorium. Po ustawieniu tej wartości nie można zmienić później. To należy nadać unikatową nazwę, aby żadne inne oferty w ramach konta usługi ma taką samą nazwę.
    -   **Nazwa użytkownika** — nazwę użytkownika skojarzoną z usługą ACR (nazwa użytkownika administratora).
    -   **Hasło** -hasło skojarzone z rejestru Azure container Registry.

    >[!NOTE]
    >Nazwa użytkownika i hasło są wymagane, aby upewnić się, że partnerzy mają dostęp do usługi ACR, o których wspomniano w procesie publikowania.

    Podczas publikowania obrazu kontenera, możesz podać jeden lub więcej tagów obrazu. Oprócz tag obrazu partnerzy mogą oferować skróty SHA. Należy pamiętać o dodaniu **tag testowy** obrazu tak, aby podczas testowania można zidentyfikować obrazu.

5. W portalu Marketplace na karcie Dodaj zawartość specyficzne dla obrotu.

    ![Informacje portalu Marketplace](media/cpp-containers-guide/containers-marketplace-tab.png)

6. Na karcie pomocy technicznej Wprowadź kontakt techniczny i informacje dotyczące obsługi klienta.

   ![Informacje dotyczące pomocy technicznej](media/cpp-containers-guide/containers-support-tab.png)

7. Wybierz **Publikuj** do opublikowania oferty. Po wybraniu publikowania, zobaczysz czasowego, który zawiera kroki związane publikowania obrazu kontenera.
