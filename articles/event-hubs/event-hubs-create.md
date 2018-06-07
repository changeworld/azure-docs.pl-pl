---
title: Tworzenie Centrum zdarzeń platformy Azure | Dokumentacja firmy Microsoft
description: Tworzenie przestrzeni nazw usługi Azure Event Hubs i Centrum zdarzeń za pomocą portalu Azure
services: event-hubs
author: sethmanheim
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 05/29/2017
ms.author: sethm
ms.openlocfilehash: 9b466d4e727c1511ca2318c0da3ec2807a965a5d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625546"
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Tworzenie przestrzeni nazw usługi Event Hubs i Centrum zdarzeń za pomocą portalu Azure

## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

1. Zaloguj się do [portalu Azure][Azure portal]i kliknij przycisk **Utwórz zasób** w lewym górnym rogu ekranu.
2. Kliknij przycisk **Internetu rzeczy**, a następnie kliknij przycisk **usługi Event Hubs**.
   
    ![](./media/event-hubs-create/create-event-hub9.png)

3. W **tworzenie przestrzeni nazw**, wprowadź nazwę przestrzeni nazw. System od razu sprawdza, czy nazwa jest dostępna.  

4. Po upewnieniu się, że nazwa przestrzeni nazw jest dostępna, wybierz warstwę cenową (Podstawowa lub Standardowa). Ponadto wybierz subskrypcję platformy Azure, grupę zasobów i lokalizację, w której chcesz utworzyć zasób.
 
5. Kliknij pozycję **Utwórz**, aby utworzyć przestrzeń nazw. Może być konieczne odczekanie kilku minut, aby system przeprowadził pełną aprowizację zasobów.

    ![](./media/event-hubs-create/create-event-hub1.png)

6. W portalu listę przestrzeni nazw kliknij nowo utworzonej przestrzeni nazw.

7. Kliknij przycisk **zasady dostępu współużytkowanego**, a następnie kliknij przycisk **RootManageSharedAccessKey**.
    
    ![](./media/event-hubs-create/create-event-hub7.png)

8. Kliknij przycisk kopiowania, aby skopiować parametry połączenia **RootManageSharedAccessKey** do schowka. Zapisz te parametry połączenia w tymczasowej lokalizacji, takiego jak Notatnik, do użycia w przyszłości.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

1. Na liście przestrzeni nazw usługi Event Hubs kliknij nowo utworzonej przestrzeni nazw.      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. W bloku przestrzeni nazw kliknij pozycję **Centra zdarzeń**.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

3. W górnej części bloku, kliknij **+ Centrum zdarzeń**.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
4. Wpisz nazwę Centrum zdarzeń, a następnie kliknij przycisk **Utwórz**. 

Utworzono Centrum zdarzeń i uzyskano parametry połączenia potrzebne do wysyłania i odbierania zdarzeń.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat usługi Event Hubs, odwiedź te linki:

* [Omówienie usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Omówienie interfejsu API usługi Event Hubs](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/