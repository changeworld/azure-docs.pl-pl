---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 19ea9c749b58f6f81dc2087caa77573062d883b5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39485826"
---
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz kolejno pozycje **Utwórz zasób** > **Aplikacje mobilne** > **Centrum powiadomień**.
   
      ![Azure Portal — tworzenie centrum powiadomień](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
      
1. W polu **Centrum powiadomień** wpisz unikatową nazwę. Wybierz odpowiednie pozycje w polach **Region**, **Subskrypcja** i **Grupa zasobów** (jeśli już ją masz). 
   
      Jeśli nie masz jeszcze przestrzeni nazw magistrali usług, możesz użyć nazwy domyślnej, która jest tworzona na podstawie nazwy centrum (jeśli ta nazwa przestrzeni nazw jest dostępna).
    
      Jeśli masz już przestrzeń nazw magistrali usług, w której chcesz utworzyć centrum, wykonaj następujące kroki:

    a. W obszarze **Przestrzeń nazw** wybierz link **Wybierz istniejącą**. 
   
    b. Wybierz pozycję **Utwórz**.
   
      ![Azure Portal — ustawianie właściwości centrum powiadomień](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Wybierz pozycję **Powiadomienia** (ikonę dzwonka), a następnie polecenie **Przejdź do zasobu**. 

      ![Witryna Azure Portal -> Powiadomienia -> Przejdź do zasobu](./media/notification-hubs-portal-create-new-hub/notification-go-to-resource.png)    
1. Wybierz z listy pozycję **Zasady dostępu**. Zanotuj dwa parametry połączenia, które są dla Ciebie dostępne. Będą one potrzebne później do obsługi powiadomień push.

      >[!IMPORTANT]
      >**NIE UŻYWAJ** elementu DefaultFullSharedAccessSignature w aplikacji. Jest on przeznaczony tylko do użycia w zapleczu.
      >
   
      ![Azure Portal — parametry połączenia centrum powiadomień](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)

