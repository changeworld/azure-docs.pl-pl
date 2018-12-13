---
title: Rozwiązywanie problemów z ponownego trenowania w usłudze Machine Learning Studio klasyczne usługi sieci web — Azure | Dokumentacja firmy Microsoft
description: Zidentyfikować i rozwiązać typowe problemy z napotkano w przypadku, gdy są ponowne trenowanie modeli usługi sieci web Azure Machine Learning Studio.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 75cac53c-185c-437d-863a-5d66d871921e
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.openlocfilehash: 0f12627e169af00f575347796d1f2e79fe1f6fa2
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2018
ms.locfileid: "53252783"
---
# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-studio-classic-web-service"></a>Rozwiązywanie problemów z ponownym szkoleniem usługi sieci web Azure Machine Learning Studio
## <a name="retraining-overview"></a>Ponowne szkolenie — omówienie
Podczas wdrażania eksperyment predykcyjny jako usługę sieci web oceniania jest model statyczny. Gdy nowe dane staje się dostępna lub konsumenta interfejsu API ma swoje własne dane, model musi być retrained. 

Aby uzyskać szczegółowy przewodnik proces ponownego trenowania klasycznej usługi sieci web, zobacz [Retrain Machine uczenia modeli programowo](retrain-models-programmatically.md).

## <a name="retraining-process"></a>Proces ponownego trenowania
Gdy konieczne jest ponowne szkolenie usługi sieci web, należy dodać pewne dodatkowe elementy:

* Wdrożone z eksperymentu szkolenia usługi sieci web. Eksperyment musi mieć **danych wyjściowych usługi sieci Web** modułu dołączony do danych wyjściowych **Train Model** modułu.  
  
    ![Dołączanie danych wyjściowych usługi sieci web do train model modelu.][image1]
* Nowy punkt końcowy dodane do oceniania usługi sieci web.  Można dodać punkt końcowy, w sposób programowy za pomocą przykładowego kodu, do którego odwołuje się modeli Retrain Machine Learning programowo tematu lub za pośrednictwem portalu usług sieci Web Azure Machine Learning.

Następnie można użyć przykładu C# kod ze strony pomocy interfejsu API szkolenia usługi sieci Web, ponowne szkolenie modelu. Gdy zostały obliczone wyniki i przebiegła pomyślnie z nimi, należy zaktualizować uczony model oceniania usługi sieci web przy użyciu nowego punktu końcowego, który został dodany.

Wszystkie elementy w miejscu główne kroki, które należy wykonać, aby ponowne szkolenie modelu są w następujący sposób:

1. Wywołanie usługi sieci Web szkolenia:  Wywołanie jest do usługi Batch Execution Service (BES), nie Request Response Service (RRS). Można korzystać z przykładu C# kodu na stronie pomocy interfejsu API, aby wykonać wywołanie. 
2. Znajdź wartości *BaseLocation*, *RelativeLocation*, i *SasBlobToken*: Te wartości są zwracane w danych wyjściowych z wywołania do usługi sieci Web szkolenia. 
   ![Wyświetlanie danych wyjściowych przykładu ponownego trenowania i BaseLocation, RelativeLocation i SasBlobToken wartości.][image6]
3. Zaktualizuj dodany punkt końcowy z usługi internetowej przyznawania ocen w nowym modelu uczonego: Przy użyciu przykładowy kod podany w modelach Retrain Machine Learning programowo, zaktualizuj nowy punkt końcowy, dodane do oceniania modelu z nowo uczonego modelu z usługi sieci Web szkolenia.

## <a name="common-obstacles"></a>Typowych przeszkód
### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>Sprawdź, czy masz poprawny adres URL poprawki
Stosowanie poprawek do adresu URL używanego musi być skojarzony z nowego oceniania punktu końcowego, dodane do usługi internetowej przyznawania ocen. Istnieje wiele sposobów, aby uzyskać adres URL poprawki:

**Opcja 1: Programowo**

Aby uzyskać poprawny adres URL poprawki:

1. Uruchom [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) przykładowego kodu.
2. Z danych wyjściowych AddEndpoint znaleźć *HelpLocation* wartości i skopiuj adres URL.
   
   ![HelpLocation w danych wyjściowych przykładu addEndpoint.][image2]
3. Wklej adres URL do przeglądarki, aby przejść do strony, który zawiera linki pomocy dla usługi sieci web.
4. Kliknij przycisk **aktualizacja zasobów** link, aby otworzyć stronę pomocy poprawki.

**Opcja 2: Korzystanie z portalu usług sieci Web Azure Machine Learning**

1. Zaloguj się do [usług sieci Web Azure Machine Learning](https://services.azureml.net/) portalu.
2. Kliknij przycisk **usług sieci Web** lub **klasycznych usług sieci Web** u góry.
4. Kliknij pracujesz z usługi internetowej przyznawania ocen (Jeśli nie zmodyfikujesz domyślną nazwę usługi sieci web, przestanie ona w "[Exp Punktacja.]").
5. Kliknij przycisk **+ nowy**.
6. Po dodaniu punktu końcowego kliknij nazwę punktu końcowego.
7. W obszarze **poprawki** adresu URL, kliknij przycisk **pomocy interfejsu API** o otwarcie strony pomocy stosowania poprawek.

> [!NOTE]
> Po dodaniu punktu końcowego z usługą sieci Web szkolenia zamiast predykcyjne usługi sieci Web, zostanie wyświetlony następujący błąd, po kliknięciu **aktualizacja zasobów** łącza: "Niestety, ale ta funkcja nie jest obsługiwana lub dostępna w tym kontekście. Ta usługa sieci Web ma nie można zaktualizować zasobów. Firma Microsoft Przepraszamy za utrudnienia i działają na poprawę tego przepływu pracy".
> 
> 

Strona pomocy poprawki zawiera adres URL poprawki, należy użyć i zawiera przykładowy kod, który można użyć w celu wywołania go.

![Adres URL poprawki.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Sprawdź, czy aktualizujesz właściwego punktu końcowego oceniania
* Nie poprawki szkolenia usługi sieci web: Operacja patch odbywa się na usługi internetowej przyznawania ocen.
* Nie poprawki domyślny punkt końcowy usługi sieci web: Operacja patch odbywa się na nowe oceniania sieci web usługi punktu końcowego, który został dodany.

Aby sprawdzić, które usługi sieci web punktu końcowego jest włączone przechodzenia do portalu usług sieci Web. 

> [!NOTE]
> Upewnij się, że chcesz dodać punkt końcowy do predykcyjnej usługi sieci Web nie szkolenia usługi sieci Web. Jeśli zostały poprawnie wdrożone szkoleniowe i predykcyjne usługi sieci Web, powinny pojawić się dwie usługi sieci web w osobnych na liście. Predykcyjne usługi sieci Web powinien kończyć się znakiem "[exp predykcyjne.]".
> 
> 

1. Zaloguj się do [usług sieci Web Azure Machine Learning](https://services.azureml.net/) portalu.
2. Kliknij przycisk **usług sieci Web** lub **klasycznych usług sieci Web**.
3. Wybierz swoje predykcyjną usługę sieci Web.
4. Sprawdź, czy nowy punkt końcowy został dodany do usługi sieci web.

### <a name="check-that-your-workspace-is-in-the-same-region-as-the-web-service"></a>Upewnij się, że Twój obszar roboczy jest w tym samym regionie co usługa sieci web
1. Zaloguj się do [usługi Machine Learning Studio](https://studio.azureml.net/).
2. U góry strony kliknij listę rozwijaną listę swoich obszarów roboczych.

   ![Machine learning region interfejsu użytkownika.][image4]

3. Sprawdź, region, który znajduje się w obszarze roboczym.

<!-- Image Links -->

[image1]: ./media/troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/troubleshooting-retraining-a-model/check-workspace-region.png
[image5]: ./media/troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/troubleshooting-retraining-a-model/web-services-tab.png
