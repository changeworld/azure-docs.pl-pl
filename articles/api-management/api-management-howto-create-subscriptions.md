---
title: Jak utworzyć subskrypcje usługi Azure API Management | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 3f4e113125ec9644aac974e47996afe290e57cee
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621824"
---
# <a name="how-to-create-subscriptions-in-azure-api-management"></a>Jak utworzyć subskrypcje usługi Azure API Management

Podczas publikowania interfejsów API za pomocą usługi Azure API Management (APIM), najłatwiejszym i najbardziej powszechnym sposobem bezpiecznego dostępu do tych interfejsów API jest za pomocą kluczy w subskrypcji. Innymi słowy aplikacje klienckie, które muszą korzystać z opublikowanych interfejsów API musi zawierać prawidłowy klucz subskrypcji w żądaniach HTTP podczas wykonywania wywołań do tych interfejsów API. Aby uzyskać klucz subskrypcji do uzyskiwania dostępu do interfejsów API, wymagana jest subskrypcja. Aby uzyskać więcej informacji o subskrypcjach, zobacz [subskrypcji w usłudze Azure API Management](api-management-subscriptions.md)

W tym artykule przedstawiono procedurę tworzenia subskrypcji w witrynie Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, należy:

+ [Tworzenie wystąpienia usługi APIM](get-started-create-service-instance.md)
+ Zrozumienie [subskrypcji w APIM](api-management-subscriptions.md)

## <a name="create-a-new-subscription"></a>Utwórz nową subskrypcję

1. Kliknij pozycję **subskrypcje** w menu po lewej stronie
2. Kliknij przycisk **Dodaj subskrypcję**
3. Podaj nazwę subskrypcji, a następnie wybierz zakres
4. Kliknij pozycję **Zapisz**

![Elastyczne subskrypcji](./media/api-management-subscriptions/flexible-subscription.png)

Po utworzeniu subskrypcji parę kluczy interfejsu API (podstawowych i pomocniczych) są aprowizowane do uzyskiwania dostępu do interfejsów API.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat usługi API Management:

+ Dowiedz się, inne [pojęcia](api-management-terminology.md) w usłudze API Management
+ Postępuj zgodnie z naszym [samouczki](import-and-publish.md) Aby dowiedzieć się więcej o usłudze API Management
+ Sprawdź nasze [z często Zadawanymi pytaniami](api-management-faq.md) dla często zadawane pytania