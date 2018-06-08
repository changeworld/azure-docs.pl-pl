---
title: Jak zwiększyć współbieżność usługi sieci web Azure Machine Learning | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zwiększyć współbieżność usługi sieci web Azure Machine Learning, dodając dodatkowe punkty końcowe.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
keywords: Azure uczenia maszynowego, usług sieci web operationalization skalowania, punkt końcowy, współbieżności
ms.assetid: c2c51d7f-fd2d-4f03-bc51-bf47e6969296
ms.service: machine-learning
ms.component: studio
ms.devlang: NA
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
ms.openlocfilehash: 2f950d93c0d923e20451eb1622dd4b1393f343a7
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835903"
---
# <a name="scaling-an-azure-machine-learning-web-service-by-adding-additional-endpoints"></a>Skalowanie usługi sieci web Azure Machine Learning, dodając dodatkowe punkty końcowe
> [!NOTE]
> W tym temacie opisano techniki dotyczy **klasycznego** usługi Machine Learning w sieci Web. 
> 
> 

Domyślnie każdy opublikowane usługi sieci Web jest skonfigurowany do obsługi 20 równoczesnych żądań i może być możliwie jak 200 równoczesnych żądań. Usługa Azure Machine Learning automatycznie optymalizuje ustawienie, aby osiągnąć optymalną wydajność dla usługi sieci web i portalu wartość jest ignorowana. 

Jeśli planujesz do wywołania interfejsu API z obciążeniem wyższa niż wartość maksymalnej liczby równoczesnych wywołań 200 będzie obsługiwać, należy utworzyć wiele punktów końcowych na tej samej usługi sieci Web. Następnie można losowo dystrybucji obciążenia we wszystkich z nich.

Skalowanie usługi sieci Web jest typowych zadań. Niektóre przyczyny skalowania mają obsługuje więcej niż 200 równoczesnych żądań, Zwiększ dostępność za pośrednictwem wiele punktów końcowych lub podaj oddzielne punkty końcowe usługi sieci web. Skali można zwiększyć przez dodanie dodatkowych punktów końcowych usługi sieci Web za pośrednictwem [Usługa sieci Web systemu Azure Machine Learning](https://services.azureml.net/) portalu.

Aby uzyskać więcej informacji na temat dodawania nowych punktów końcowych, zobacz [tworzenie punktów końcowych](create-endpoint.md).

Należy zwrócić uwagę przy użyciu liczba wysokiej współbieżności mogą być szkodliwe, jeśli nie jest wywołanie interfejsu API z odpowiednio wysoki współczynnik. Sporadyczne przekroczeń limitu czasu i/lub nagłego mogą zobaczyć w opóźnienie umieszczenie stosunkowo niska obciążenia na interfejs API skonfigurowane dla wysokie obciążenie.

Synchroniczne interfejsy API są zazwyczaj używane w sytuacjach, w których jest potrzebne małe opóźnienia. W tym miejscu opóźnienia oznacza czas przyjmuje dla jednego żądania interfejsu API, a nie konto do wszelkich opóźnień sieci. Załóżmy, że masz interfejsu API z opóźnieniem 50 ms. Pełni korzystać z dostępnej pojemności z poziomu ograniczania wysokiej i maksymalnej liczby równoczesnych wywołań = 20, należy wywołać tego interfejsu API 20 * 1000 / 50 = 400 razy na sekundę. Dodatkowo to rozszerzenie, maksymalnej liczby równoczesnych wywołań 200 pozwala wywoływać razy 4000 interfejsu API na sekundę, przy założeniu opóźnienia 50 ms.

<!--Image references-->
[1]: ./media/scaling-webservice/machlearn-1.png
[2]: ./media/scaling-webservice/machlearn-2.png
