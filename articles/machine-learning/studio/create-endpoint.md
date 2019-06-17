---
title: Tworzenie punktów końcowych usługi sieci web
titleSuffix: Azure Machine Learning Studio
description: Utwórz następującą liczbę punktów końcowych usługi sieci web w usłudze Azure Machine Learning Studio. Każdy punkt końcowy usługi sieci web niezależnie rozwiązane, ograniczenia i zarządzane.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: ac434a696f6e77e5ce61b430232166e7727eda38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60751182"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-web-services"></a>Tworzenie punktów końcowych dla wdrożonej usługi sieci web Azure Machine Learning Studio

> [!NOTE]
> W tym temacie opisano technikach do zastosowania **klasycznego** usługę internetową Machine Learning.

Po wdrożeniu usługi sieci Web dla tej usługi tworzony jest domyślny punkt końcowy. Domyślny punkt końcowy może być wywoływany przy użyciu właściwego dla niego klucza interfejsu API. Możesz dodać dodatkowe punkty końcowe z właściwymi dla nich kluczami z poziomu portalu usług sieci Web.
Każdy punkt końcowy usługi sieci web niezależnie rozwiązane, ograniczenia i zarządzane. Każdy punkt końcowy jest unikatowy adres URL przy użyciu klucza autoryzacji, którą można dystrybuować swoim klientom.

## <a name="add-endpoints-to-a-web-service"></a>Dodaj punkty końcowe usługi sieci web

Możesz dodać punkt końcowy usługi sieci web przy użyciu portalu usług sieci Web Azure Machine Learning. Po utworzeniu punktu końcowego, możesz pobrać go za pośrednictwem synchronicznych interfejsów API, interfejsów API usługi batch i arkusze programu excel.

> [!NOTE]
> Po dodaniu dodatkowych punktów końcowych usługi sieci web, nie można usunąć domyślnego punktu końcowego.

1. W usłudze Machine Learning Studio w kolumnie nawigacji po lewej stronie kliknij usług sieci Web.
2. W dolnej części pulpitu nawigacyjnego usług sieci web kliknij **zarządzanie punktami końcowymi**. Portal usług sieci Web Azure Machine Learning zostanie otwarty na stronie punkty końcowe usługi sieci web.
3. Kliknij przycisk **Nowy**.
4. Wpisz nazwę i opis dla nowego punktu końcowego. Nazwy punktów końcowych musi być 24 znaków lub mniej długości i muszą składać się z małe litery lub cyfry. Wybierz poziom rejestrowania i czy jest włączony przykładowych danych. Aby uzyskać więcej informacji na temat rejestrowania, zobacz [Włączanie rejestrowania usług sieci web Machine Learning](web-services-logging.md).

## <a id="scaling"></a> Skalowanie usługi sieci web przez dodanie dodatkowych punktów końcowych

Domyślnie każdy opublikowanej usługi sieci web jest skonfigurowany do obsługi 20 równoczesnych żądań i może być możliwie jak 200 równoczesnych żądań. Usługa Azure Machine Learning Studio optymalizuje automatycznie ustawienie, aby zapewnić najlepszą wydajność za daną usługę sieci web i portalu wartość jest ignorowana.

Jeśli planujesz wywołania interfejsu API za pomocą wyższe obciążenie niż wartość maksymalna liczba współbieżnych wywołań 200 będzie obsługiwać, należy utworzyć wiele punktów końcowych w tej samej usługi sieci web. Następnie można losowo dystrybuować swoje obciążenia na wszystkich z nich.

Skalowanie usługi sieci web jest typowym zadaniem. Niektóre przyczyny skalowania to do obsługi ponad 200 równoczesnych żądań, zwiększyć dostępność za pośrednictwem wielu punktów końcowych lub podaj oddzielne punkty końcowe usługi sieci web. Można zwiększyć skalę, dodając dodatkowe punkty końcowe usługi sieci web za pośrednictwem [usługi sieci Web Azure Machine Learning](https://services.azureml.net/) portalu.

Należy zwrócić uwagę przy użyciu liczba współbieżności wysokiej mogą być szkodliwe, jeśli one nie wywołuje metody interfejsu API przy użyciu odpowiednio dużej szybkości. Sporadyczne przekroczeń limitu czasu i/lub wartości graniczne mogą zobaczyć w opóźnienie umieszczenie stosunkowo małym obciążeniu dla interfejsu API skonfigurowany dla dużym obciążeniem.

Synchroniczne interfejsy API są zwykle używane w sytuacjach, w którym pożądane jest o małych opóźnieniach. W tym miejscu opóźnienia oznacza czasu zajmuje dla interfejsu API zakończyć jedno żądanie, a nie konta dla dowolnej opóźnienia sieci. Załóżmy, że masz interfejs API z opóźnieniem 50 ms. Pełni korzystać z dostępnej pojemności z poziom przepustowości wysokiej i maksymalna liczba współbieżnych wywołań = 20, należy wywołać tego interfejsu API 20 * 1000 / 50 = 400 godzin w ciągu sekundy. Dodatkowo to rozszerzenie, maksymalna liczba współbieżnych wywołań 200 pozwala wywoływać razy 4000 interfejsu API na sekundę, zakładając, że opóźnienie 50 ms.

## <a name="next-steps"></a>Kolejne kroki

[Jak używać usługi sieci web Azure Machine Learning](consume-web-services.md).
