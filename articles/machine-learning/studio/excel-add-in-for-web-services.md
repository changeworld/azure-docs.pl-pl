---
title: Dodatek programu Excel do usługi Machine Learning Web | Dokumentacja firmy Microsoft
description: Jak używać usługi Azure Machine Learning w sieci Web bezpośrednio w programie Excel bez pisania żadnego kodu.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 9618079d-502f-4974-a3e2-8f924042a23f
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 2/1/2018
ms.openlocfilehash: 68e2f72dfd8cc58d42263f4b6378d89304aaaa4d
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834196"
---
# <a name="excel-add-in-for-azure-machine-learning-web-services"></a>Dodatki programu Excel do usług sieci Web Azure Machine Learning
Excel ułatwia wywołują usługi sieci web bezpośrednio, bez konieczności pisania kodu.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Kroki, aby przy użyciu usługi sieci web istniejące w skoroszycie

1. Otwórz [przykładowy plik programu Excel](http://aka.ms/amlexcel-sample-2), który zawiera dane dotyczące osób na Titanic dodatek programu Excel i.
2. Wybierz usługę sieci web, klikając go-"Titanic predykcyjne renty (przykład dodatku Excel) [Wynik]" w tym przykładzie.
   
    ![Wybierz usługę sieci Web][01]
3. Powoduje to przejście do **Predict** sekcji.  Ten skoroszyt zawiera już dane przykładowe, ale dla pustego skoroszytu można zaznaczyć komórkę w programie Excel i kliknij przycisk **użyj przykładowych danych**.
4. Wybierz dane z nagłówkami i kliknij ikonę zakres danych wejściowych.  Upewnij się, że zaznaczono pole "Moje dane mają nagłówki".
5. W obszarze **dane wyjściowe**, wprowadź numer komórki, w której mają danych wyjściowych w postaci, na przykład "H1" w tym miejscu.
6. Kliknij przycisk **prognozowania**.
   
    ![Przewidywanie sekcji][02]

Wdrażanie usługi sieci web, lub użyj istniejącej usługi sieci Web. Aby uzyskać więcej informacji na temat wdrażania usługi sieci web, zobacz [wskazówki krok 5: Wdrażanie usługi sieci Web Azure Machine Learning](walkthrough-5-publish-web-service.md).

Pobierz klucz interfejsu API usługi sieci web. Miejsce można wykonać tej akcji zależy czy opublikowane usługi sieci web klasycznego uczenia maszynowego usługi sieci web uczenie maszynowe nowe.

**Użyj usługi sieci web klasycznego** 

1. W usłudze Machine Learning Studio, kliknij przycisk **usług sieci WEB** sekcji w okienku po lewej stronie, a następnie wybierz usługę sieci web.
   
    ![Wybierz Studio usługi sieci Web][04]
2. Skopiuj klucz interfejsu API usługi sieci web.
   
    ![Klucz interfejsu API Studio][05]
3. Na **pulpitu NAWIGACYJNEGO** usługi sieci web, kliknij pozycję **żądanie/odpowiedź** łącza.
4. Wyszukaj **przez identyfikator URI żądania** sekcji.  Skopiuj i Zapisz adres URL.

> [!NOTE]
> Obecnie istnieje możliwość logowania się do [usługi sieci Web systemu Azure Machine Learning](https://services.azureml.net) portalu, aby uzyskać klucz interfejsu API usługi sieci web uczenie maszynowe klasycznego.
> 
> 

**Użyj nowej usługi sieci web**

1. W [usługi sieci Web systemu Azure Machine Learning](https://services.azureml.net) portalu, kliknij przycisk **usług sieci Web**, wybierz opcję usługi sieci web. 
2. Kliknij przycisk **korzystać**.
3. Wyszukaj **zużycie podstawowe informacje o** sekcji. Skopiuj i Zapisz **klucza podstawowego** i **żądań i odpowiedzi** adresu URL.

## <a name="steps-to-add-a-new-web-service"></a>Kroki, aby dodać nową usługę sieci web

1. Wdrażanie usługi sieci web, lub użyj istniejącej usługi sieci Web. Aby uzyskać więcej informacji na temat wdrażania usługi sieci web, zobacz [wskazówki krok 5: Wdrażanie usługi sieci Web Azure Machine Learning](walkthrough-5-publish-web-service.md).
2. Kliknij przycisk **korzystać**.
3. Wyszukaj **zużycie podstawowe informacje o** sekcji. Skopiuj i Zapisz **klucza podstawowego** i **żądań i odpowiedzi** adresu URL.
4. W programie Excel, przejdź do **usług sieci Web** sekcji (jeśli jest **Predict** kliknij strzałkę Wstecz, aby przejść do listy usług sieci web).
   
    ![Przejdź do wybór usługi sieci Web][03]
5. Kliknij przycisk **Dodaj usługę sieci Web**.
6. Wklej adres URL do programu Excel z etykietą pola tekstowego dodatku **adres URL**.
7. Wklej klucz interfejsu API/podstawowy w pole tekstowe z etykietą **klucz interfejsu API**.
8. Kliknij pozycję **Add** (Dodaj).
   
    ![Adres URL i klucz API usługi sieci Web klasycznego.][06]
9. Aby korzystać z usługi sieci web, postępuj zgodnie z instrukcjami poprzedniego, "Czynności w celu istniejące sieci web usługi".

## <a name="sharing-your-workbook"></a>Udostępnianie skoroszytu
Czy zapisać skoroszyt, klucz interfejsu API/podstawowych usług sieci web, które zostały dodane również jest zapisywane. Oznacza to, że skoroszyt powinien udostępniać tylko dla osób zaufanych.

Wszelkie pytania z poniższej sekcji komentarza lub na naszych [forum](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).

[01]: ./media/excel-add-in-for-web-services/image1.png
[02]: ./media/excel-add-in-for-web-services/image2.png
[03]: ./media/excel-add-in-for-web-services/image3.png
[04]: ./media/excel-add-in-for-web-services/image4.png
[05]: ./media/excel-add-in-for-web-services/image5.png
[06]: ./media/excel-add-in-for-web-services/image6.png
