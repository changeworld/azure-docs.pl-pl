---
title: Tworzenie subskrypcji w usłudze Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się, jak tworzyć subskrypcje w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: f8b2238eb0fab9aeeb42d11b4176c0d681b5f8e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073537"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Tworzenie subskrypcji w usłudze Azure API Management

Podczas publikowania interfejsów API za pośrednictwem usługi Azure API Management, jest to łatwe i wspólne do bezpiecznego dostępu do tych interfejsów API przy użyciu kluczy subskrypcji. Aplikacje klienckie, które muszą korzystać z opublikowanych interfejsów API, muszą zawierać prawidłowy klucz subskrypcji w żądaniach HTTP podczas wykonywania połączeń z tymi interfejsami API. Aby uzyskać klucz subskrypcji dostępu do interfejsów API, wymagana jest subskrypcja. Aby uzyskać więcej informacji na temat subskrypcji, zobacz [Subskrypcje w usłudze Azure API Management](api-management-subscriptions.md).

W tym artykule oszukuje się kroki tworzenia subskrypcji w witrynie Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, wymagania wstępne są następujące:

+ [Tworzenie wystąpienia zarządzania interfejsami API](get-started-create-service-instance.md).
+ Poznaj [subskrypcje w usłudze API Management](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Tworzenie nowej subskrypcji

1. Wybierz **pozycję Subskrypcje** w menu po lewej stronie.
2. Wybierz **pozycję Dodaj subskrypcję**.
3. Podaj nazwę subskrypcji i wybierz zakres.
4. Opcjonalnie wybierz, czy subskrypcja powinna być skojarzona z użytkownikiem.
5. Wybierz **pozycję Zapisz**.

![Elastyczne subskrypcje](./media/api-management-subscriptions/flexible-subscription.png)

Po utworzeniu subskrypcji dwa klucze interfejsu API są dostarczane w celu uzyskania dostępu do interfejsów API. Jeden klucz jest podstawowy, a jeden jest drugorzędny. 

## <a name="next-steps"></a>Następne kroki
Uzyskaj więcej informacji na temat zarządzania interfejsami API:

+ Poznaj inne pojęcia dotyczące zarządzania [interfejsami](api-management-terminology.md) API.
+ Postępuj zgodnie z naszymi [samouczkami,](import-and-publish.md) aby dowiedzieć się więcej o zarządzaniu interfejsami API.
+ Często zadawane pytania można zadać na naszej [stronie z często zadawanymi](api-management-faq.md) pytaniami.