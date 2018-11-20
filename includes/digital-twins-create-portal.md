---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 09/17/2018
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: e5e015c1e12e68f54ae7fd32082faed1abfebe74
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51626402"
---
1. Zaloguj się w witrynie [Azure Portal](http://portal.azure.com).

1. W okienku po lewej stronie wybierz pozycję **Utwórz zasób**. Wyszukaj pozycję **digital twins** i wybierz pozycję **Digital Twins (wersja zapoznawcza)**. Wybierz przycisk **Utwórz**, aby rozpocząć proces wdrażania.

   ![Opcje podczas tworzenia nowego wystąpienia usługi Digital Twins](./media/create-digital-twins-portal/create-digital-twins.png)

1. W okienku **Digital Twins** wprowadź następujące informacje:
   * **Nazwa zasobu**: utwórz unikatową nazwę dla wystąpienia usługi Digital Twins.
   * **Subskrypcja**: wybierz subskrypcję, która ma zostać użyta do utworzenia tego wystąpienia usługi Digital Twins. 
   * **Grupa zasobów**: wybierz lub utwórz [grupę zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) dla wystąpienia usługi Digital Twins.
   * **Lokalizacja**: wybierz lokalizację znajdującą się najbliżej Twoich urządzeń.

    ![Okienko usługi Digital Twins z wprowadzonymi informacjami](./media/create-digital-twins-portal/create-digital-twins-param.png)

1. Przejrzyj informacje dotyczące usługi Digital Twins, a następnie wybierz pozycję **Utwórz**. Tworzenie wystąpienia usługi Digital Twins może potrwać kilka minut. Postępy możesz monitorować w okienku **Powiadomienia**.

1. Otwórz okienko **Przegląd** wystąpienia usługi Digital Twins. Zwróć uwagę na link w obszarze **Interfejs API zarządzania**.

   Adres URL **interfejsu API zarządzania** ma następujący format: _https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger_. Ten adres URL prowadzi do dokumentacji interfejsu API REST usługi Azure Digital Twins dotyczącej Twojego wystąpienia. Przeczytaj artykuł [Jak używać struktury Swagger w usłudze Digital Twins](../articles/digital-twins/how-to-use-swagger.md), aby dowiedzieć się, jak odczytać tę dokumentację interfejsu API i korzystać z niej.

    Zmodyfikuj adres URL **interfejsu API zarządzania** do następującego formatu: _https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/_. Aplikacja uzyska dostęp do Twojego wystąpienia, używając zmodyfikowanego adresu URL jako podstawowego adresu URL. Skopiuj ten zmodyfikowany adres URL do pliku tymczasowego. Będzie potrzebny w następnej sekcji.

    ![Interfejs API zarządzania](./media/create-digital-twins-portal/digital-twins-management-api.png)