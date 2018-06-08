---
title: Rozwiązywanie problemów z ponownego trenowania usługi sieci web Azure Machine Learning klasycznego | Dokumentacja firmy Microsoft
description: Zidentyfikować i rozwiązać typowe problemy napotkano, gdy są ponownego trenowania modelu dla usługi sieci Web Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 75cac53c-185c-437d-863a-5d66d871921e
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.openlocfilehash: 989bf010320501050a37fbf2f0799f50a5a3e2ba
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835777"
---
# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-classic-web-service"></a>Rozwiązywanie problemów z ponownego trenowania usługi sieci web Azure Machine Learning klasycznego
## <a name="retraining-overview"></a>Ponownego trenowania — omówienie
Podczas wdrażania eksperyment predykcyjny jako usługę sieci web oceniania jest statyczny modelu. Wraz ze wzrostem dostępności nowych danych lub w przypadku interfejsu API klienta ma własne dane modelu musi być retrained. 

Aby uzyskać pełny przewodnik proces ponownego trenowania klasycznym usługi sieci web, zobacz [Retrain Machine Learning modeli programowo](retrain-models-programmatically.md).

## <a name="retraining-process"></a>Proces ponownego trenowania
Musisz ponownie ucz usługi sieci web, należy dodać niektóre dodatkowe elementy:

* Usługa sieci web wdrożone z eksperymentów szkolenia. Eksperyment musi mieć **wyjście usługi sieci Web** modułu dołączony do danych wyjściowych **Train Model** modułu.  
  
    ![Wyjście usługi sieci web należy dołączyć do modelu pociągu.][image1]
* Nowy punkt końcowy dodane do oceniania usługi sieci web.  Można dodać punktu końcowego, programowo przy użyciu przykładowy kod odwołuje się do modeli Retrain Machine Learning programowo tematu lub za pośrednictwem portalu usługi sieci Web systemu Azure Machine Learning.

Następnie można przykładowy kod C# z strona pomocy interfejsu API usługi sieci Web szkolenia retrain modelu. Po zostały obliczone wyniki i uzyskaniu je, należy zaktualizować trenowanego modelu oceniania usługi sieci web przy użyciu nowego punktu końcowego, który został dodany.

Wszystkie elementy w miejscu główne kroki, które należy wykonać, aby ponownie ucz modelu są w następujący sposób:

1. Wywoływanie usługi sieci Web szkolenia: wywołanie jest do wsadowe wykonywanie usługi (BES), nie żądanie odpowiedzi usługi (RR). Przykładowy kod C# na stronie pomocy interfejsu API służy do wywoływania. 
2. Znajdź wartości dla *BaseLocation*, *RelativeLocation*, i *SasBlobToken*: te wartości są zwracane w danych wyjściowych z połączenia z usługą sieci Web szkolenia. 
   ![Wyświetlanie danych wyjściowych ponownego trenowania próbki i wartości BaseLocation, RelativeLocation i SasBlobToken.][image6]
3. Zaktualizuj dodano punkt końcowy usługi sieci web oceniania za pomocą nowego trenowanego modelu: przy użyciu przykładowy kod podany w modelach Retrain Machine Learning programowo, zaktualizuj nowy punkt końcowy, dodane do oceniania modelu z nowo trenowanego modelu z Usługa sieci Web szkolenia.

## <a name="common-obstacles"></a>Typowych wąskich gardeł
### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>Sprawdź, czy masz poprawny adres URL poprawki
POPRAWKA adres URL, którego używasz, musi być skojarzony z nowego oceniania punktu końcowego, dodane do oceniania usługi sieci web. Istnieje wiele sposobów, aby uzyskać adres URL poprawki:

**Opcja 1: programowo**

Aby uzyskać poprawny adres URL poprawki:

1. Uruchom [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) przykładowy kod.
2. Z danych wyjściowych AddEndpoint znaleźć *HelpLocation* wartości i skopiuj adres URL.
   
   ![HelpLocation w danych wyjściowych próbki addEndpoint.][image2]
3. Wklej adres URL do przeglądarki, aby przejść do strony, która zawiera łącza pomocy dla usługi sieci web.
4. Kliknij przycisk **aktualizacji zasobów** łącze, aby otworzyć stronę pomocy poprawki.

**Opcja 2: Korzystanie z portalu usługi sieci Web systemu Azure Machine Learning**

1. Zaloguj się do [usługi sieci Web systemu Azure Machine Learning](https://services.azureml.net/) portalu.
2. Kliknij przycisk **usług sieci Web** lub **usług sieci Web klasycznego** u góry.
4. Kliknij przycisk oceniania korzystasz z usługi sieci web (Jeśli nie zmodyfikujesz domyślnej nazwy usługi sieci web, go będą kończyć się "[Exp Punktacja."]).
5. Kliknij przycisk **+ nowy**.
6. Po dodaniu punktu końcowego, kliknij nazwę punktu końcowego.
7. W obszarze **poprawki** adres URL, kliknij przycisk **pomocy interfejsu API** otworzyć stosowania poprawek strony pomocy.

> [!NOTE]
> Jeśli dodano punkt końcowy z usługą sieci Web szkolenia zamiast predykcyjnej usługi sieci Web, zostanie wyświetlony następujący błąd, po kliknięciu **aktualizacji zasobów** łącza: "Przepraszamy, ale ta funkcja nie jest obsługiwana lub dostępna w Ten kontekst. Ta usługa sieci Web ma nie nadaje się do aktualizacji zasobów. Firma Microsoft Przepraszamy za utrudnienia i działają na ułatwieniu ten przepływ pracy".
> 
> 

Strona pomocy poprawki zawiera adres URL poprawka, należy użyć i udostępnia przykładowy kod, którego można użyć w celu wywołania go.

![Adres URL poprawki.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Sprawdź, czy aktualizujesz właściwego punktu końcowego oceniania
* Nie poprawka usługę sieci web szkolenia: operacja poprawki musi zostać wykonana na oceniania usługi sieci web.
* Poprawka nie jest domyślny punkt końcowy usługi sieci web: operacja poprawki musi zostać wykonana na nowe oceniania końcowy usługi sieci web dodany.

Możesz sprawdzić, usługi sieci web, która znajduje się punkt końcowy na, przechodząc na stronę usługi sieci Web portalu. 

> [!NOTE]
> Upewnij się, że punkt końcowy jest dodawany do predykcyjnej usługi sieci Web nie usługę sieci Web szkolenia. Jeśli zostały poprawnie wdrożone zarówno szkolenia, jak i usługi sieci Web predykcyjnej, powinny pojawić się dwie usługi WWW, na liście. Predykcyjnej usługi sieci Web powinien kończyć się "[exp predykcyjnych.]".
> 
> 

1. Zaloguj się do [usługi sieci Web systemu Azure Machine Learning](https://services.azureml.net/) portalu.
2. Kliknij przycisk **usług sieci Web** lub **usług sieci Web klasycznego**.
3. Wybierz usługę sieci Web predykcyjnej.
4. Sprawdź, czy nowy punkt końcowy został dodany do usługi sieci web.

### <a name="check-that-your-workspace-is-in-the-same-region-as-the-web-service"></a>Sprawdź, czy obszar roboczy znajduje się w tym samym regionie co usługa sieci web
1. Zaloguj się do [Studio uczenia maszynowego](https://studio.azureml.net/).
2. U góry kliknij przycisk listy rozwijanej obszarów roboczych.

   ![Machine learning region interfejsu użytkownika.][image4]

3. Upewnij się, region, który znajduje się w obszarze roboczym.

<!-- Image Links -->

[image1]: ./media/troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/troubleshooting-retraining-a-model/check-workspace-region.png
[image5]: ./media/troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/troubleshooting-retraining-a-model/web-services-tab.png
