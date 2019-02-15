---
title: Tworzenie punktów końcowych usługi sieci Web
titleSuffix: Azure Machine Learning Studio
description: Tworzenie punktów końcowych usługi sieci Web Azure Machine Learning. Każdy punkt końcowy usługi sieci Web niezależnie rozwiązane, ograniczenia i zarządzane.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/12/2019
ms.openlocfilehash: a6945ac7bfb750916e229ae04376f895f2b3d506
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267279"
---
# <a name="creating-endpoints-for-deployed-azure-machine-learning-studio-web-services"></a>Tworzenie punktów końcowych dla wdrożonej usługi sieci web Azure Machine Learning Studio

> [!NOTE]
> W tym temacie opisano technikach do zastosowania **klasycznego** usługi Machine Learning w sieci Web.

Po wdrożeniu usługi sieci Web dla tej usługi tworzony jest domyślny punkt końcowy. Domyślny punkt końcowy może być wywoływany przy użyciu właściwego dla niego klucza interfejsu API. Możesz dodać dodatkowe punkty końcowe z właściwymi dla nich kluczami z poziomu portalu usług sieci Web.
Każdy punkt końcowy usługi sieci Web niezależnie rozwiązane, ograniczenia i zarządzane. Każdy punkt końcowy jest unikatowy adres URL przy użyciu klucza autoryzacji, którą można dystrybuować swoim klientom.

## <a name="adding-endpoints-to-a-web-service"></a>Dodawanie punktów końcowych usługi sieci Web

Możesz dodać punkt końcowy usługi sieci Web przy użyciu portalu usług sieci Web Azure Machine Learning. Po utworzeniu punktu końcowego, możesz pobrać go za pośrednictwem synchronicznych interfejsów API, interfejsów API usługi batch i arkusze programu excel.

> [!NOTE]
> Po dodaniu dodatkowych punktów końcowych usługi sieci Web, nie można usunąć domyślnego punktu końcowego.

1. W usłudze Machine Learning Studio w kolumnie nawigacji po lewej stronie kliknij usług sieci Web.
2. W dolnej części pulpitu nawigacyjnego usług sieci Web kliknij **zarządzanie punktami końcowymi**. Portal usług sieci Web Azure Machine Learning zostanie otwarty na stronie punkty końcowe usługi sieci Web.
3. Kliknij przycisk **Nowy**.
4. Wpisz nazwę i opis dla nowego punktu końcowego. Nazwy punktów końcowych musi być 24 znaków lub mniej długości i muszą składać się z małe litery lub cyfry. Wybierz poziom rejestrowania i czy jest włączony przykładowych danych. Aby uzyskać więcej informacji na temat rejestrowania, zobacz [włączyć rejestrowanie dla usługi Machine Learning w sieci Web](web-services-logging.md).

## <a name="next-steps"></a>Kolejne kroki

[Jak używać usługi Azure Machine Learning w sieci Web](consume-web-services.md).
