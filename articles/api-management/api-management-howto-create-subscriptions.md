---
title: Tworzenie subskrypcji na platformie Azure API Management | Microsoft Docs
description: Dowiedz się, jak tworzyć subskrypcje na platformie Azure API Management.
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
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073537"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Tworzenie subskrypcji na platformie Azure API Management

Po opublikowaniu interfejsów API za pomocą usługi Azure API Management jest to łatwe i powszechne do zabezpieczenia dostępu do tych interfejsów API przy użyciu kluczy subskrypcji. Aplikacje klienckie, które muszą używać opublikowanych interfejsów API, muszą zawierać prawidłowy klucz subskrypcji w żądaniach HTTP podczas wykonywania wywołań do tych interfejsów API. Aby uzyskać klucz subskrypcji do uzyskiwania dostępu do interfejsów API, wymagana jest subskrypcja. Aby uzyskać więcej informacji o subskrypcjach, zobacz [subskrypcje na platformie Azure API Management](api-management-subscriptions.md).

W tym artykule omówiono procedurę tworzenia subskrypcji w Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, wymagania wstępne są następujące:

+ [Utwórz wystąpienie API Management](get-started-create-service-instance.md).
+ Informacje [o subskrypcjach w API Management](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Utwórz nową subskrypcję

1. Wybierz pozycję **subskrypcje** w menu po lewej stronie.
2. Wybierz pozycję **Dodaj subskrypcję**.
3. Podaj nazwę subskrypcji i wybierz zakres.
4. Opcjonalnie możesz wybrać, czy subskrypcja ma być skojarzona z użytkownikiem.
5. Wybierz pozycję **Zapisz**.

![Elastyczne subskrypcje](./media/api-management-subscriptions/flexible-subscription.png)

Po utworzeniu subskrypcji są udostępniane dwa klucze interfejsu API w celu uzyskania dostępu do interfejsów API. Jeden klucz jest podstawowy, a drugi to pomocniczy. 

## <a name="next-steps"></a>Następne kroki
Uzyskaj więcej informacji na API Management:

+ Zapoznaj [](api-management-terminology.md) się z innymi pojęciami w API Management.
+ Postępuj [](import-and-publish.md) zgodnie z naszymi samouczkami, aby dowiedzieć się więcej o API Management.
+ Zapoznaj się z naszą [stroną często](api-management-faq.md) zadawanych pytań.