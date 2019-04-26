---
title: Tworzenie subskrypcji usługi Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć subskrypcje usługi Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: bc791fea1dfd184749e84cb7b7a912972c6a9f12
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657628"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Tworzenie subskrypcji usługi Azure API Management

Podczas publikowania interfejsów API za pomocą usługi Azure API Management jest prosta i wspólne dla bezpiecznego dostępu do tych interfejsów API przy użyciu kluczy subskrypcji. Aplikacje klienckie, które muszą korzystać z opublikowanych interfejsów API musi zawierać klucz ważnej subskrypcji w żądaniach HTTP, w momencie wywołania tych interfejsów API. Aby uzyskać klucz subskrypcji do uzyskiwania dostępu do interfejsów API, wymagana jest subskrypcja. Aby uzyskać więcej informacji o subskrypcjach, zobacz [subskrypcji w usłudze Azure API Management](api-management-subscriptions.md).

W tym artykule przedstawiono procedurę tworzenia subskrypcji w witrynie Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, wymagania wstępne są następujące:

+ [Tworzenie wystąpienia usługi API Management](get-started-create-service-instance.md).
+ Zrozumienie [subskrypcji w usłudze API Management](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Utwórz nową subskrypcję

1. Wybierz **subskrypcje** w menu po lewej stronie.
2. Wybierz **Dodaj subskrypcję**.
3. Podaj nazwę subskrypcji, a następnie wybierz zakres.
4. Opcjonalnie wybierz opcję, jeśli subskrypcja powinna być skojarzona z użytkownikiem.
5. Wybierz pozycję **Zapisz**.

![Elastyczne subskrypcji](./media/api-management-subscriptions/flexible-subscription.png)

Po utworzeniu subskrypcji dostęp do interfejsów API znajdują się dwa klucze interfejsu API. Jeden klucz jest kluczem podstawowym, a jeden to dodatkowa baza danych. 

## <a name="next-steps"></a>Kolejne kroki
Uzyskaj więcej informacji na temat usługi API Management:

+ Dowiedz się, inne [pojęcia](api-management-terminology.md) w usłudze API Management.
+ Postępuj zgodnie z naszym [samouczki](import-and-publish.md) Aby dowiedzieć się więcej o usłudze API Management.
+ Sprawdź nasze [z często Zadawanymi pytaniami](api-management-faq.md) dla często zadawane pytania.