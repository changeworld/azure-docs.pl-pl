---
title: 'Szybki start: Wykrywanie twarzy na obrazie przy użyciu interfejsu API REST platformy Azure i języka Python'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start użyjesz interfejsu API REST rozpoznawania twarzy platformy Azure i języka Python do wykrywania twarzy na obrazie.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/09/2018
ms.author: pafarley
ms.openlocfilehash: 606f01059620443c8c42a24f416128d0856eff50
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55213790"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-python"></a>Szybki start: Wykrywanie twarzy na obrazie przy użyciu interfejsu API REST rozpoznawania twarzy i języka Python

W tym przewodniku Szybki start użyjesz interfejsu API REST rozpoznawania twarzy platformy Azure i języka Python do wykrywania twarzy na obrazie. Skrypt narysuje ramki wokół twarzy i nałoży informacje o płci i wieku na obraz.

![Mężczyzna i kobieta, każde z prostokątem narysowanym wokół twarzy oraz informacjami o wieku i płci wyświetlonymi na obrazie](../images/labelled-faces-python.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 


## <a name="prerequisites"></a>Wymagania wstępne

- Klucz subskrypcji interfejsu API rozpoznawania twarzy. Klucz subskrypcji bezpłatnej wersji próbnej możesz uzyskać na stronie [Wypróbuj usługi Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Możesz też wykonać instrukcje z tematu [Create a Cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) (Tworzenie konta usług Cognitive Services), aby subskrybować usługę interfejsu API rozpoznawania twarzy i uzyskać klucz.

## <a name="run-the-jupyter-notebook"></a>Uruchamianie notesu Jupyter

Ten przewodnik Szybki start można uruchomić jako notes Jupyter w programie [MyBinder](https://mybinder.org). Aby uruchomić usługę Binder, wybierz poniższy przycisk. Następnie postępuj zgodnie z instrukcjami w notesie.

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=FaceAPI.ipynb)

## <a name="next-steps"></a>Następne kroki

Następnie zapoznaj się z dokumentacją referencyjną interfejsu API rozpoznawania twarzy, aby dowiedzieć się więcej na temat obsługiwanych scenariuszy.

> [!div class="nextstepaction"]
> [Interfejs API rozpoznawania twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
