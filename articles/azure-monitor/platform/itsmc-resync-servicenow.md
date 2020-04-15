---
title: Jak ręcznie rozwiązać problemy z synchronizacją ServiceNow
description: Resetowanie połączenia z usługąNow, aby alerty na platformie Microsoft Azure mogły ponownie wywołać servicenow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: f09f5010c18f5ea064b02f0fbbae107bf473e1f8
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313682"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>Jak ręcznie rozwiązać problemy z synchronizacją ServiceNow

Usługa Azure Monitor może łączyć się z dostawcami usług IT (ITSM) innych firm. ServiceNow jest jednym z tych dostawców.

Ze względów bezpieczeństwa może być konieczne odświeżenie tokenu uwierzytelniania używanego do połączenia z ServiceNow.
Użyj następującego procesu synchronizacji, aby ponownie uaktywnić połączenie i odświeżyć token:


1. Wyszukaj rozwiązanie w górnym banerze wyszukiwania, a następnie wybierz odpowiednie rozwiązania

    ![Nowe połączenie](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. Na ekranie rozwiązania wybierz "Wybierz wszystko" w filtrze subskrypcji, a następnie przefiltruj według "ServiceDesk"

    ![Nowe połączenie](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Wybierz rozwiązanie połączenia ITSM.
1. Wybierz połączenie ITSM na lewym banerze.

    ![Nowe połączenie](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Wybierz każde łącznik z listy. 
    1. Kliknij nazwę łącznika, aby ją skonfigurować
    1. Usuwanie wszystkich łączników, które nie są już używane

    1. Aktualizowanie pól zgodnie z [tymi definicjami](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections) na podstawie typu partnera

    1. Kliknij na synchronizację

       ![Nowe połączenie](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Kliknij na zapisz

        ![Nowe połączenie](media/itsmc-resync-servicenow/save-8bit.png)

f.    Przejrzyj powiadomienia, aby sprawdzić, czy proces zakończył się sukcesem 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [połączeniach zarządzania usługami IT](itsmc-connections.md)
