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
ms.openlocfilehash: 7bac2b291bec2ceda118c877cde997a2fa9f9f37
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2018
ms.locfileid: "49322912"
---
1. Zaloguj się w witrynie [Azure Portal](http://portal.azure.com).

1. Na panelu nawigacyjnym po lewej stronie kliknij pozycję **Utwórz zasób**. Wyszukaj pozycję *digital twins* i wybierz pozycję **Digital Twins (wersja zapoznawcza)**. Kliknij przycisk **Utwórz**, aby rozpocząć proces wdrażania.

    ![Tworzenie usługi Digital Twins](./media/create-digital-twins-portal/create-digital-twins.png)

1. W okienku **Digital Twins** wprowadź następujące informacje:
   * **Nazwa zasobu**: utwórz unikatową nazwę dla wystąpienia usługi Digital Twins.
   * **Subskrypcja**: wybierz subskrypcję, która ma zostać użyta do utworzenia tego wystąpienia usługi Digital Twins. 
   * **Grupa zasobów**: wybierz lub utwórz [grupę zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) dla wystąpienia usługi Digital Twins.
   * **Lokalizacja**: wybierz lokalizację znajdującą się najbliżej Twoich urządzeń.

    ![Tworzenie usługi Digital Twins](./media/create-digital-twins-portal/create-digital-twins-param.png)

1. Przejrzyj informacje dotyczące usługi Digital Twins, a następnie kliknij pozycję **Utwórz**. Utworzenie wystąpienia usługi Digital Twins może potrwać kilka minut. Postępy możesz monitorować w okienku **Powiadomienia**.

1. Otwórz okienko **Przegląd** wystąpienia usługi Digital Twins. Zwróć uwagę na link wyświetlany w obszarze **Interfejs API zarządzania**.

    1. Adres URL **interfejsu API zarządzania** ma następujący format: **_https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger_**. Ten adres URL prowadzi do dokumentacji interfejsu API REST usługi Azure Digital Twins dotyczącej Twojego wystąpienia. Przeczytaj artykuł [Jak używać struktury Swagger w usłudze Digital Twins](../articles/digital-twins/how-to-use-swagger.md), aby dowiedzieć się, jak odczytać tę dokumentację interfejsu API i korzystać z niej.

    1. Zmodyfikuj adres URL **interfejsu API zarządzania** do następującego formatu: **_https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/_**. Aplikacja uzyska dostęp do Twojego wystąpienia, używając zmodyfikowanego adresu URL jako podstawowego adresu URL. Skopiuj ten zmodyfikowany adres URL do pliku tymczasowego. Będzie on potrzebny w następnej sekcji.

    ![Interfejsy API zarządzania](./media/create-digital-twins-portal/digital-twins-management-api.png)