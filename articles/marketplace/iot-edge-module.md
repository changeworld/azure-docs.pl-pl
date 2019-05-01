---
title: Azure IoT Edge Modules
description: IoT Edge modułu oferty w portalu Azure Marketplace wydawcy aplikacji i usługi.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, IoT Edge module offer
author: qianw211
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 09/22/2018
ms.author: qianw211
ms.openlocfilehash: 9f4ad704de83e5971b5bc10083aefeec5d28374b
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64937856"
---
# <a name="iot-edge-modules"></a>Moduły usługi IoT Edge

[Usługi Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) platformy jest objęta najlepszą w chmurze platformy Azure.  Ta platforma pozwala wdrażać obciążenia w chmurze do uruchamiania bezpośrednio na urządzeniach IoT.  Moduł usługi IoT Edge można uruchamiać obciążenia w trybie offline i do analizy danych lokalnie. Ten typ oferty ułatwia zapisywanie przepustowości, chronić lokalne i dane wrażliwe i oferuje czas odpowiedzi o małych opóźnieniach.  Masz teraz opcje, aby móc korzystać z tych wstępnie skompilowanych obciążeń. Do tej pory tylko niewielki podzbiór rozwiązania firmy od firmy Microsoft, były dostępne.  Trzeba było inwestować czas i zasoby do tworzenia własnych niestandardowych rozwiązań IoT.

Dzięki zastosowaniu [moduły usługi IoT Edge w witrynie Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1), mamy w jednym miejscu dla wydawców, udostępnianie i sprzedawać swoje rozwiązania do odbiorców IoT. Ostatecznie znaleźć i zakupić możliwości, aby przyspieszyć opracowywanie rozwiązań, ich deweloperom IoT.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Najważniejszych korzyści zapewnianych przez moduły usługi IoT Edge w witrynie Azure Marketplace:

| **Dla wydawców**    | **Dla klientów (deweloperzy IoT)**  |
| :------------------- | :-------------------|
| Dotrzyj do milionów deweloperów chcących tworzyć i wdrażać rozwiązania usługi IoT Edge.  | Tworzenie rozwiązania usługi IoT Edge swobodne korzystanie ze składników bezpieczne i przetestowane. |
| Opublikuj raz, a w żadnego sprzętu usługi IoT Edge, która obsługuje kontenery. | Skróć czas wprowadzania produktu na rynek, wyszukując i wdrożenie 1 i 3 strony moduły usługi IoT Edge w zależności od określonych potrzeb. |
| Wprowadzanie do obiegu za pomocą elastyczne opcje rozliczeń <ul> <li> Bezpłatne i model dostarczania własnej licencji (BYOL). </li> </ul> | Dokonywanie zakupów przy użyciu wybranych przez siebie model rozliczeń. <ul> <li> Bezpłatne i model dostarczania własnej licencji (BYOL). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>Co to jest moduł usługi IoT Edge?

Usługa Azure IoT Edge umożliwia wdrażanie i zarządzanie nimi logiki biznesowej na urządzeniach brzegowych w formie modułów. Moduły platformy Azure IoT Edge to najmniejsza jednostki obliczeń zarządzane przez usługi IoT Edge i może zawierać usług firmy Microsoft (np. usługi Azure Stream Analytics), usługi innych firm 3 lub kodu specyficznego dla rozwiązania. Aby dowiedzieć się więcej na temat moduły usługi IoT Edge, zobacz [modułów Omówienie usługi Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).

**Jaka jest różnica między typem oferty kontenera i typ oferty modułu usługi IoT Edge?**

Typ oferty modułu usługi IoT Edge jest określonego typu kontenera uruchomionego w urządzenia usługi IoT Edge. Jest dostarczany z domyślnych ustawień konfiguracji do uruchamiania w kontekście usługi IoT Edge i opcjonalnie używa moduł usługi IoT Edge, zestaw SDK integracji ze środowiskiem uruchomieniowym usługi IoT Edge.

## <a name="publishing-your-iot-edge-module"></a>Publikowanie modułu usługi IoT Edge

**Wybierając odpowiednie sklepu**

IoT Edge moduły są tylko publikowane w portalu Azure Marketplace, nie ma zastosowania usługi AppSource.  Aby uzyskać więcej informacji na temat różnic i docelowymi odbiorcami różnych sklepów, zobacz [Określanie opcji publikowania rozwiązania](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type).
 
**Opcje rozliczeń**

Obecnie Portal marketplace obsługuje **bezpłatna** i **Bring Your Own License (BYOL)** rozliczeń opcje moduły usługi IoT Edge.
 
**Opcje publikowania**

We wszystkich przypadkach należy wybierać moduły usługi IoT Edge **Transact** opcji publikowania.  Zobacz [opcję publikacji](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) więcej informacji na temat opcji publikowania.  

## <a name="eligibility-criteria"></a>Kryteria kwalifikacyjne

Wszystkie warunki umów Microsoft Azure Marketplace i zasady mają zastosowanie do oferty modułu usługi IoT Edge.  Ponadto istnieją wymagania wstępne i wymagania techniczne dla modułów usługi IoT Edge.  

**Wymagania wstępne**

Aby opublikować moduł usługi IoT Edge w portalu Azure Marketplace, musisz spełnić następujące wymagania wstępne:

- Dostęp do portalu Cloud Partner (CPP). Aby uzyskać więcej informacji, zobacz [Podręcznik publikowania w portalu Azure Marketplace i AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Hosta modułu usługi IoT Edge w usługi Azure Container Registry. 
- Są gotowe, takich jak (niepełna lista) metadane modułu usługi IoT Edge: 
    - Tytuł
    - Opis (format HTML)
    - Obraz logo (PNG format i rozmiary Stały obraz, w tym 40x40px 90x90px, 115x115px, 255x115px)
    - Okres użycia i zasady zachowania poufności
    - Domyślna konfiguracja modułu (trasy, odpowiednich właściwości bliźniaka, CreateOptions, można żądań, zmienne środowiskowe)
    - Dokumentacja
    - Kontakt z pomocą techniczną

**Wymagania techniczne**

Podstawowy wymagań technicznych dotyczących moduł usługi IoT Edge, aby go uzyskać certyfikat i opublikowane w portalu Azure Marketplace są szczegółowo opisane w [przygotowanie modułu usługi IoT Edge, zasoby techniczne](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets).  

## <a name="documentation-and-resources"></a>Dokumentacja i zasoby

[Utwórz ofertę moduł usługi IoT Edge](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-offer) — — proces publikowania nowego modułu usługi IoT Edge oferty z portalu wydawców w chmurze.

## <a name="next-steps"></a>Kolejne kroki

Jeśli użytkownik jeszcze tego nie zrobiono,

- Rejestrowanie w [sieci Microsoft Partner Network](https://partner.microsoft.com/membership).
- Tworzenie [Account Microsoft](https://account.microsoft.com/account/) (wymagane dla portalu Azure Marketplace transact oferty; zalecane przez inne osoby).
- Prześlij [formularz rejestracji w portalu Marketplace](https://azuremarketplace.microsoft.com/sell/signup).

Jeśli jesteś zarejestrowanym i tworzysz nową ofertę czy działa na podstawie istniejącego

- Zaloguj się do [portalu Cloud Partner](https://cloudpartner.azure.com/) do utworzenia lub zakończyć oferty.
- Zobacz [oferty modułu usługi IoT Edge publikowania Przegląd](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts) informacji na temat opublikować ofertę moduł usługi IoT Edge.
