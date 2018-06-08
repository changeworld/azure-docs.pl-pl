---
title: Tworzenie punktów końcowych usługi sieci Web usługi Machine Learning | Dokumentacja firmy Microsoft
description: Tworzenie punktów końcowych usługi sieci Web w usłudze Azure Machine Learning
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 4657fc1b-5228-4950-a29e-bc709259f728
ms.service: machine-learning
ms.component: studio
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/04/2016
ms.openlocfilehash: 8cdf8c5ac3676d8abc9084fc842484aca5b6d1c7
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833550"
---
# <a name="creating-endpoints"></a>Tworzenie punktów końcowych
> [!NOTE]
>  W tym temacie opisano techniki dotyczy **klasycznego** usługi Machine Learning w sieci Web.
> 
> 

Podczas tworzenia usług sieci Web, które sprzedaży do przodu do klientów, należy podać przeszkolone modeli do każdego klienta, nadal połączonych z doświadczenia, z którego utworzono usługę sieci Web. Ponadto wszelkie aktualizacje eksperymentu powinny być stosowane selektywnie punkt końcowy bez zastępowania dostosowania.

W tym celu uczenia maszynowego Azure umożliwia tworzenie wielu punktów końcowych dla wdrożonej usługi sieci Web. Każdy punkt końcowy usługi sieci Web jest niezależnie skierowana, ograniczenie i zarządzany. Każdy punkt końcowy jest unikatowy adres URL i klucza autoryzacji, którą można dystrybuować do klientów.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="adding-endpoints-to-a-web-service"></a>Dodawanie punktów końcowych usługi sieci Web
Istnieją dwa sposoby, aby dodać punkt końcowy usługi sieci Web.

* Programistyczne
* Za pomocą portalu usługi sieci Web systemu Azure Machine Learning

Po utworzeniu punktu końcowego, możesz pobrać go za pośrednictwem interfejsów API synchroniczne, partii interfejsów API i arkusze programu excel. Oprócz dodania punktów końcowych za pośrednictwem tego interfejsu użytkownika, umożliwia także interfejsów API Management punktu końcowego do programowego dodawania punktów końcowych.

> [!NOTE]
> Po dodaniu dodatkowe punkty końcowe z usługą sieci Web, nie można usunąć domyślnego punktu końcowego.
> 
> 

## <a name="adding-an-endpoint-programmatically"></a>Programowe Dodawanie punktu końcowego
Możesz dodać punkt końcowy do usługi sieci Web, programowo przy użyciu [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) przykładowy kod.

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>Dodawanie punktu końcowego za pomocą portalu usługi sieci Web systemu Azure Machine Learning
1. W usłudze Machine Learning Studio w kolumnie nawigacji po lewej stronie kliknij usług sieci Web.
2. W dolnej części pulpitu nawigacyjnego usługi sieci Web, kliknij przycisk **zarządzanie punktami końcowymi**. Portal usługi sieci Web systemu Azure Machine Learning zostanie otwarta strona punkty końcowe usługi sieci Web.
3. Kliknij przycisk **Nowy**.
4. Wpisz nazwę i opis dla nowego punktu końcowego. Nazwy punktu końcowego musi być 24 znaków lub mniej długości i musi składać się z małych małych liter i cyfr. Wybierz poziom rejestrowania i czy jest włączone przykładowych danych. Aby uzyskać więcej informacji, zobacz [należy włączyć rejestrowanie dla usługi Machine Learning Web](web-services-logging.md).

## <a name="next-steps"></a>Kolejne kroki
[Jak korzystać z usługi sieci Web Azure Machine Learning](consume-web-services.md).

