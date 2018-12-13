---
title: Skalowanie usługi sieci web Machine Learning Studio — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zwiększyć współbieżność usługi sieci web Azure Machine Learning Studio, dodając dodatkowe punkty końcowe.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.author: amlstudiodocs
editor: cgronlun
keywords: usługi Azure machine learning, usługi sieci web, operacjonalizacja, skalowanie, punkt końcowy współbieżności
ms.assetid: c2c51d7f-fd2d-4f03-bc51-bf47e6969296
ms.service: machine-learning
ms.component: studio
ms.devlang: NA
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
ms.openlocfilehash: 736c27f5c8ad6efb2b4e36f44998ce06d7b69248
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256353"
---
# <a name="scaling-an-azure-machine-learning-studio-web-service-by-adding-additional-endpoints"></a>Skalowanie usługi sieci web Azure Machine Learning Studio, dodając dodatkowe punkty końcowe
> [!NOTE]
> W tym temacie opisano technikach do zastosowania **klasycznego** usługę internetową Machine Learning. 
> 
> 

Domyślnie każdy opublikowanej usługi sieci Web jest skonfigurowany do obsługi 20 równoczesnych żądań i może być możliwie jak 200 równoczesnych żądań. Usługa Azure Machine Learning optymalizuje automatycznie ustawienie, aby zapewnić najlepszą wydajność za daną usługę sieci web i portalu wartość jest ignorowana. 

Jeśli planujesz wywołania interfejsu API za pomocą wyższe obciążenie niż wartość maksymalna liczba współbieżnych wywołań 200 będzie obsługiwać, należy utworzyć wiele punktów końcowych w tej samej usługi sieci Web. Następnie można losowo dystrybuować swoje obciążenia na wszystkich z nich.

Skalowanie usługi sieci Web jest typowym zadaniem. Niektóre przyczyny skalowania to do obsługi ponad 200 równoczesnych żądań, zwiększyć dostępność za pośrednictwem wielu punktów końcowych lub podaj oddzielne punkty końcowe usługi sieci web. Można zwiększyć skalę, dodając dodatkowe punkty końcowe usługi sieci Web za pośrednictwem [usługi sieci Web Azure Machine Learning](https://services.azureml.net/) portalu.

Aby uzyskać więcej informacji na temat dodawania nowych punktów końcowych, zobacz [tworzenia punktów końcowych](create-endpoint.md).

Należy zwrócić uwagę przy użyciu liczba współbieżności wysokiej mogą być szkodliwe, jeśli one nie wywołuje metody interfejsu API przy użyciu odpowiednio dużej szybkości. Sporadyczne przekroczeń limitu czasu i/lub wartości graniczne mogą zobaczyć w opóźnienie umieszczenie stosunkowo małym obciążeniu dla interfejsu API skonfigurowany dla dużym obciążeniem.

Synchroniczne interfejsy API są zwykle używane w sytuacjach, w którym pożądane jest o małych opóźnieniach. W tym miejscu opóźnienia oznacza czasu zajmuje dla interfejsu API zakończyć jedno żądanie, a nie konta dla dowolnej opóźnienia sieci. Załóżmy, że masz interfejs API z opóźnieniem 50 ms. Pełni korzystać z dostępnej pojemności z poziom przepustowości wysokiej i maksymalna liczba współbieżnych wywołań = 20, należy wywołać tego interfejsu API 20 * 1000 / 50 = 400 godzin w ciągu sekundy. Dodatkowo to rozszerzenie, maksymalna liczba współbieżnych wywołań 200 pozwala wywoływać razy 4000 interfejsu API na sekundę, zakładając, że opóźnienie 50 ms.

<!--Image references-->
[1]: ./media/scaling-webservice/machlearn-1.png
[2]: ./media/scaling-webservice/machlearn-2.png
