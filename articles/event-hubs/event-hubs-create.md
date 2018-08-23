---
title: Tworzenie Centrum zdarzeń platformy Azure | Dokumentacja firmy Microsoft
description: Tworzenie przestrzeni nazw usługi Azure Event Hubs i Centrum zdarzeń za pomocą witryny Azure portal
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 5b468e1758d752cd3001c85b328d064369429499
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "42060724"
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Tworzenie przestrzeni nazw usługi Event Hubs i Centrum zdarzeń za pomocą witryny Azure portal

## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

1. Zaloguj się do [witryny Azure portal][Azure portal]i kliknij przycisk **Utwórz zasób** w lewym górnym rogu ekranu.
2. Kliknij pozycję **Internet rzeczy**, a następnie kliknij pozycję **Event Hubs**.
   
    ![](./media/event-hubs-create/create-event-hub9.png)

3. W polu **Utwórz przestrzeń nazw** podaj nazwę przestrzeni nazw. System od razu sprawdza, czy nazwa jest dostępna.  

4. Po upewnieniu się, że nazwa przestrzeni nazw jest dostępna, wybierz warstwę cenową (Podstawowa lub Standardowa). Ponadto wybierz subskrypcję platformy Azure, grupę zasobów i lokalizację, w której chcesz utworzyć zasób.
 
5. Kliknij pozycję **Utwórz**, aby utworzyć przestrzeń nazw. Może być konieczne odczekanie kilku minut, aby system przeprowadził pełną aprowizację zasobów.

    ![](./media/event-hubs-create/create-event-hub1.png)

6. Na liście przestrzeni nazw w portalu kliknij nowo utworzoną przestrzeń nazw.

7. Kliknij pozycję **Zasady dostępu współdzielonego**, a następnie kliknij pozycję **RootManageSharedAccessKey**.
    
    ![](./media/event-hubs-create/create-event-hub7.png)

8. Kliknij przycisk kopiowania, aby skopiować parametry połączenia **RootManageSharedAccessKey** do schowka. Zapisz te parametry połączenia w lokalizacji tymczasowej, takiej jak Notatnik, do użycia później.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

1. Na liście przestrzeni nazw usługi Event Hubs kliknij nowo utworzoną przestrzeń nazw.      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. W bloku przestrzeni nazw kliknij pozycję **Centra zdarzeń**.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

3. W górnej części bloku kliknij **+ Centrum zdarzeń**.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
4. Wpisz nazwę centrum zdarzeń, a następnie kliknij pozycję **Utwórz**. 

Utworzono Centrum zdarzeń i uzyskano parametry połączenia potrzebne do wysyłania i odbierania zdarzeń.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat usługi Event Hubs, skorzystaj z następujących linków:

* [Omówienie usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Omówienie interfejsu API usługi Event Hubs](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/