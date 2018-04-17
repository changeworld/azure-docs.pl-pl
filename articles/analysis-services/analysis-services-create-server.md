---
title: Tworzenie serwera usług Analysis Services na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć wystąpienie serwera usług Analysis Services na platformie Azure.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c910416524f149c785aae299d576ca5c521abc6d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="create-an-azure-analysis-services-server-in-azure-portal"></a>Tworzenie serwera usług Azure Analysis Services w portalu Azure
W tym artykule przedstawiono tworzenie zasobu serwera usług Analysis Services w ramach subskrypcji platformy Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

* **Subskrypcja platformy Azure**: odwiedź stronę [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), aby utworzyć konto.
* **Usługa Azure Active Directory**: subskrypcji musi być skojarzony z dzierżawy usługi Azure Active Directory. A, musisz być zalogowany na platformie Azure przy użyciu konta w tej usłudze Azure Active Directory. Konta Microsoft nie są obsługiwane. Aby dowiedzieć się więcej, zobacz [Authentication and user permissions (Uwierzytelnianie i uprawnienia użytkownika)](analysis-services-manage-users.md).
* **Grupa zasobów**: Użyj masz już grupę zasobów lub [Utwórz nową](../azure-resource-manager/resource-group-overview.md).

> [!NOTE]
> Utworzenie serwera może skutkować powstaniem nowej usługi płatnej. Aby dowiedzieć się więcej, zobacz [cennik usług Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).
> 
> 

## <a name="to-create-a-server-in-the-azure-portal"></a>Aby utworzyć serwer w portalu Azure
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).  
2. Kliknij przycisk **+ nowy** > **dane i analiza** > **usług Analysis Services**.
3. W **usług Analysis Services** bloku, wypełnij wymagane pola, a następnie naciśnij klawisz **Utwórz**.
   
    ![Tworzenie serwera](./media/analysis-services-create-server/aas-create-server-blade.png)
   
   * **Nazwa serwera**: wpisz unikatową nazwę używany do serwera.
   * **Subskrypcja**: Wybierz subskrypcję rachunków tego serwera do.
   * **Grupa zasobów**: zaprojektowano kontenery ułatwiające zarządzanie kolekcją zasobów systemu Azure. Aby dowiedzieć się więcej, zobacz [grup zasobów](../azure-resource-manager/resource-group-overview.md).
   * **Lokalizacja**: Lokalizacja centrum danych Azure ten obsługuje serwer. Wybierz lokalizację najbliżej największy bazy użytkowników.
   * **Warstwa cenowa**: Wybierz warstwę cenową. Maksymalnie 400 GB modele tabelaryczne są obsługiwane. Aby dowiedzieć się więcej, zobacz [cennik usług Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).
4. Kliknij przycisk **Utwórz**.

Utwórz potrzebuje zazwyczaj w obszarze chwilę. często tylko kilka sekund. W przypadku wybrania **dodać do portalu**, przejdź do portalu w taki sposób, aby wyświetlić nowy serwer. Lub przejdź do **wszystkie usługi** > **usług Analysis Services** czy serwer jest gotowy.

 ![Pulpit nawigacyjny](./media/analysis-services-create-server/aas-create-server-dashboard.png)


## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu serwera, możesz [wdrożenia modelu](analysis-services-deploy.md) do niej przy użyciu narzędzi SSDT lub SSMS.

Jeśli model wdrażania na serwerze nawiązuje połączenie z lokalnych źródeł danych, musisz zainstalować [bramy danych lokalnych](analysis-services-gateway.md) na komputerze w sieci.

