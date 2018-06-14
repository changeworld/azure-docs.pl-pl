---
title: Zarządzanie plan usługi aplikacji na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak do wykonywania różnych zadań zarządzania plan usługi aplikacji.
keywords: usługi aplikacji, usługi azure app service, skalowania, plan usługi app service, zmienić, tworzenia, zarządzania, zarządzanie
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.openlocfilehash: 1dfe8a903e19ff524a1c4a0228e6aefcbe9ff183
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29117684"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Zarządzanie plan usługi aplikacji na platformie Azure

[Planu usługi aplikacji Azure](azure-web-sites-web-hosting-plans-in-depth-overview.md) udostępnia zasoby aplikacji usługi app Service musi być uruchamiane. W tym przewodniku pokazano, jak zarządzać plan usługi aplikacji.

## <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

> [!TIP]
> Jeśli masz środowisko usługi aplikacji, zobacz [Utwórz plan usługi aplikacji w środowisku usługi aplikacji](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Można utworzyć pusty plan usługi aplikacji lub można utworzyć planu w ramach tworzenia aplikacji.

1. W [portalu Azure](https://portal.azure.com), wybierz pozycję **nowy** > **sieci Web i mobilność**, a następnie wybierz **aplikacji sieci Web** lub inny rodzaj aplikacji usługi app Service.

2. Wybierz istniejący plan usługi aplikacji lub utworzyć plan dla nowej aplikacji.

   ![Utwórz aplikację w portalu Azure.][createWebApp]

   Aby utworzyć plan:

   a. Wybierz **[+] Tworzenie nowych**.

      ![Tworzenie planu usługi aplikacji.][createASP] 

   b. Aby uzyskać **planu usługi aplikacji**, wprowadź nazwę planu.

   c. Aby uzyskać **lokalizacji**, wybierz odpowiednią lokalizację.

   d. Aby uzyskać **warstwa cenowa**, wybierz odpowiedni poziom cenowy dla usługi. Wybierz **Wyświetl wszystkie** do widoku więcej cennik opcje, takie jak **wolne** i **Shared**. Po wybraniu warstwy cenowej kliknij **wybierz** przycisku.

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Przenieś aplikację do innego planu usługi aplikacji

Aplikację można przenieść do innego planu usług aplikacji, tak długo, jak planu źródłowego i docelowego planu znajdują się w _tej samej grupie zasobów i region geograficzny_.

1. W [portalu Azure](https://portal.azure.com), przejdź do aplikacji, którą chcesz przenieść.

2. W menu Wyszukaj **planu usługi App Service** sekcji.

3. Wybierz **planu usługi aplikacji zmiany** otworzyć **planu usługi aplikacji** selektora.

   ![Selektor planu usługi aplikacji.][change] 

4. W **planu usługi aplikacji** selektora, wybierz istniejący plan można przenieść tej aplikacji do.   

> [!IMPORTANT]
> **Planu usługi aplikacji wybierz** strony są filtrowane według następujących kryteriów: 
> - Istnieje w tej samej grupie zasobów 
> - Istnieje w tym samym regionie geograficznym 
> - Istnieje w tej samej przestrzeni sieci Web  
> 
> A _przestrzeni sieci Web_ jest konstrukcją logiczną w usłudze App Service, który definiuje grupę zasobów serwera. Region geograficzny (na przykład zachodnie stany USA) zawiera wiele webspaces Aby przydzielić klienci korzystający z usługi aplikacji. Obecnie nie można przenieść zasobów z usługi aplikacji między webspaces. 
> 

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Każdy plan ma własną warstwy cenowej. Na przykład przenoszenie lokacji z **wolne** warstwy do **standardowe** warstwy włącza wszystkie aplikacje przypisane do korzystania z funkcji i zasobów **standardowe** warstwy. Jednak przenoszenie aplikacji z planu wyższej warstwy do planu warstwowej dolna oznacza już mieć dostęp do niektórych funkcji. Jeśli aplikacja korzysta z funkcji, który nie jest dostępny w planie docelowej, występuje błąd, pokazujący, której funkcji jest używana, który nie jest dostępny. 

Na przykład jeśli jednej z aplikacji korzysta z certyfikatów SSL, można napotkać ten komunikat o błędzie:

`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

W takim przypadku przed aplikacji można przenieść do docelowego planu, należy albo:
- Skalowanie w górę warstwę cenową planu docelowego **podstawowe** lub nowszej.
- Usunięcia wszystkich połączeń SSL do aplikacji.

## <a name="move-an-app-to-a-different-region"></a>Przenieś aplikację w innym regionie

Region, w której aplikacja będzie działać to region planu usługi aplikacji, który znajduje się w. Jednak nie można zmienić regionu plan usługi aplikacji. Jeśli chcesz uruchomić aplikację w innym regionie, co alternatywa to aplikacji klonowania. Klonowanie tworzy kopię aplikacji w nowy lub istniejący plan usługi aplikacji w dowolnym regionie.

Można znaleźć **aplikacji w klonowania** w **narzędzi programistycznych** części menu.

> [!IMPORTANT]
> Klonowanie ma pewne ograniczenia. Informacje o ich w [klonowania aplikacji usługi aplikacji Azure](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Skalowanie planu usługi aplikacji

Aby skalować usługę aplikacji planu do warstwy cenowej, zobacz [skalowanie w górę aplikacji na platformie Azure](web-sites-scale.md).

Aby skalować w poziomie liczbę wystąpień aplikacji, zobacz [skalowanie liczby wystąpień ręcznie lub automatycznie](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Usuwanie planu usługi aplikacji

Aby uniknąć nieoczekiwanego opłat, po usunięciu ostatniej aplikacji w planie usługi aplikacji, usług aplikacji spowoduje również usunięcie planu domyślnie. Jeśli chcesz zachować zamiast tego planu, należy zmienić planu **wolne** warstwy, więc nie są naliczane opłaty.

> [!IMPORTANT]
> Planów usługi aplikacji, których nie ma aplikacji skojarzonych z nimi nadal spowodować naliczenie opłat, ponieważ nadal zarezerwować skonfigurowanych wystąpień maszyny Wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Skalowanie w górę aplikacji na platformie Azure](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
