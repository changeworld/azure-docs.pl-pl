---
title: Tworzenie punktów końcowych usługi sieci Web w usłudze Machine Learning — usługi Azure Machine Learning Studio | Dokumentacja firmy Microsoft
description: Tworzenie punktów końcowych usługi sieci Web Azure Machine Learning
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
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
ms.openlocfilehash: 266970bec1e61dca808bb6358f2b4580359ee70d
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2018
ms.locfileid: "52314017"
---
# <a name="creating-endpoints"></a>Tworzenie punktów końcowych 
> [!NOTE]
>  W tym temacie opisano technikach do zastosowania **klasycznego** usługi Machine Learning w sieci Web.
> 
> 

Podczas tworzenia usługi sieci Web, które sprzedawać do przodu dla klientów, należy podać uczone modele do każdego klienta, które nadal są połączone z eksperymentu, z której została utworzona Usługa sieci Web. Ponadto wszelkie aktualizacje eksperymentu stosuje się selektywnie do punktu końcowego bez zastępowania dostosowań.

Aby to osiągnąć, Azure Machine Learning Studio umożliwia tworzenie wielu punktów końcowych dla wdrożonej usługi sieci Web. Każdy punkt końcowy usługi sieci Web niezależnie rozwiązane, ograniczenia i zarządzane. Każdy punkt końcowy jest unikatowy adres URL i klucza autoryzacji, którą można dystrybuować swoim klientom.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="adding-endpoints-to-a-web-service"></a>Dodawanie punktów końcowych usługi sieci Web
Istnieją dwa sposoby dodawania punktu końcowego usługi sieci Web.

* Programistyczne
* Za pośrednictwem portalu usług sieci Web Azure Machine Learning

Po utworzeniu punktu końcowego, możesz pobrać go za pośrednictwem synchronicznych interfejsów API, interfejsów API usługi batch i arkusze programu excel. Oprócz dodania punktów końcowych za pomocą tego interfejsu użytkownika, umożliwia także interfejsów API zarządzania punktu końcowego Aby programowo dodać punkty końcowe.

> [!NOTE]
> Po dodaniu dodatkowych punktów końcowych usługi sieci Web, nie można usunąć domyślnego punktu końcowego.
> 
> 

## <a name="adding-an-endpoint-programmatically"></a>Programowe Dodawanie punktu końcowego
Można dodać punkt końcowy usługi sieci Web, w sposób programowy za pomocą [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) przykładowego kodu.

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>Dodawanie punktu końcowego przy użyciu portalu usług sieci Web Azure Machine Learning
1. W usłudze Machine Learning Studio w kolumnie nawigacji po lewej stronie kliknij usług sieci Web.
2. W dolnej części pulpitu nawigacyjnego usług sieci Web kliknij **zarządzanie punktami końcowymi**. Portal usług sieci Web Azure Machine Learning zostanie otwarty na stronie punkty końcowe usługi sieci Web.
3. Kliknij przycisk **Nowy**.
4. Wpisz nazwę i opis dla nowego punktu końcowego. Nazwy punktów końcowych musi być 24 znaków lub mniej długości i muszą składać się z małe litery lub cyfry. Wybierz poziom rejestrowania i czy jest włączony przykładowych danych. Aby uzyskać więcej informacji na temat rejestrowania, zobacz [włączyć rejestrowanie dla usługi Machine Learning w sieci Web](web-services-logging.md).

## <a name="next-steps"></a>Kolejne kroki
[Jak używać usługi Azure Machine Learning w sieci Web](consume-web-services.md).

