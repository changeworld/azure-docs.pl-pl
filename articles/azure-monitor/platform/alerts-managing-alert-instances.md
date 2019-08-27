---
title: Zarządzanie wystąpieniami alertów
description: Zarządzanie wystąpieniami alertów na platformie Azure
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: dbc7d37ce311d203ee051b5a23632d2bf401e27a
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034842"
---
# <a name="manage-alert-instances"></a>Zarządzanie wystąpieniami alertów
Korzystając z [funkcji ujednoliconych alertów](https://aka.ms/azure-alerts-overview) w Azure monitor, można teraz zobaczyć wszystkie różne typy alertów na platformie Azure, obejmujących wiele subskrypcji w jednym z okien szklanych. W tym artykule przedstawiono sposób wyświetlania wystąpień alertów oraz sposób głębokiego szczegółowe w portalu w celu znalezienia określonych wystąpień alertów w celu rozwiązywania problemów.

> [!NOTE]
   >  Tylko alerty wygenerowane w ciągu ostatnich 30 dni mogą być dostępne w środowisku użytkownika lub za pomocą interfejsów API REST.

1. Istnieją trzy sposoby na stronie alertów.

   + W [portalu](https://portal.azure.com/)wybierz pozycję **monitorowanie** i w sekcji Monitor wybierz pozycję **alerty**.  
     ![Monitorowanie](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
   + Możesz przejść do alertów z poziomu określonego **zasobu**. Po otwarciu zasobu przejdź do sekcji monitorowanie, a następnie wybierz pozycję **alerty**, a strona docelowa jest wstępnie filtrowana pod kątem alertów dotyczących tego konkretnego zasobu.
   
     ![Monitorowanie](media/alerts-managing-alert-instances/alert-resource.JPG)
    
   + Możesz przejść do alertów z poziomu kontekstu określonej **grupy zasobów**. Po otwarciu grupy zasobów przejdź do sekcji monitorowanie, a następnie wybierz pozycję **alerty**, a strona docelowa jest wstępnie filtrowana dla alertów dla tej konkretnej grupy zasobów.    
   
     ![Monitorowanie](media/alerts-managing-alert-instances/alert-rg.JPG)

1. Zobaczysz na stronie **Podsumowanie alertów** , która zawiera przegląd wszystkich wystąpień alertów na platformie Azure. Widok podsumowania można zmodyfikować, wybierając opcję **wiele subskrypcji** (maksymalnie 5) lub przez filtrowanie między grupami **zasobów**, konkretnymi **zasobami**lub **zakresami czasu**. Kliknij pozycję wszystkie alerty lub wszystkie przedziały ważności, aby przejść do widoku listy alertów.     
   ![Podsumowanie alertów](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
1. Zostanie wyświetlona strona **wszystkie alerty** , na której zostaną wyświetlone wszystkie wystąpienia alertów na platformie Azure. W przypadku przełączenia się do portalu z poziomu powiadomienia o alercie można użyć dostępnych filtrów, aby zawęzić w tym konkretnym wystąpieniu alertu. (**Uwaga**: Jeśli przejdziesz do strony, klikając dowolny z poziomów ważności, lista zostanie wstępnie odfiltrowana pod kątem ważności, gdy zostanie użyta). Oprócz filtrów dostępnych na poprzedniej stronie można teraz filtrować według podstaw usługi monitorowania (na przykład Platform for Metrics), warunku monitorowania (wywoływane lub rozwiązane), ważności, stanu alertu (nowy/potwierdzony/zamknięty) lub identyfikatora grupy inteligentnej.

   ![Wszystkie alerty](media/alerts-managing-alert-instances/all-alerts.jpg)

   > [!NOTE]
   >  Jeśli połączysz się ze stroną, klikając dowolny z poziomów ważności, lista zostanie wstępnie odfiltrowana pod kątem ważności, gdy zostanie użyta na tej stronie.
 
1. Kliknięcie dowolnego wystąpienia alertu otwiera stronę **szczegóły alertu** , co pozwala na szczegółowe szczegółowe informacji o tym konkretnym wystąpieniu alertu.   
   ![Szczegóły alertu](media/alerts-managing-alert-instances/alert-details.jpg)  

