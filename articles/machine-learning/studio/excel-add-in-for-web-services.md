---
title: Dodatek programu Excel dla usług sieci web
titleSuffix: Azure Machine Learning Studio
description: Jak używać usługi Azure Machine Learning w sieci Web bezpośrednio w programie Excel, bez konieczności pisania jakiegokolwiek kodu.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 9e801e0d7a26cd4d6c43118959aee1dec7216b1c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60750248"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-web-services"></a>Dodatek programu Excel dla usług sieci web Azure Machine Learning Studio
Excel ułatwia wywołują usługi sieci web bezpośrednio, bez konieczności pisania kodu.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Kroki, aby użyć usługi sieci web istniejące w skoroszycie

1. Otwórz [przykładowy plik programu Excel](https://aka.ms/amlexcel-sample-2), który zawiera dodatek programu Excel i danych dotyczących osób na Titanica. 
 
    > [!NOTE]
    > Zobaczysz, że na liście usług sieci Web związane z nim do pliku i u dołu pola wyboru "Automatycznie — przewidywanie". Po włączeniu automatycznego — przewidywanie przewidywań **wszystkie** usługi zostanie zaktualizowany, za każdym razem, gdy brak zmian w danych wejściowych. Jeśli nie jest zaznaczone trzeba będzie kliknąć "Przewidzieć All" w przypadku odświeżania. Umożliwiające automatyczne — przewidywanie na usługę poziomu przejdź do kroku 6.

2. Wybierz usługę sieci web, klikając go — "Titanic renty predykcyjne (przykład dodatku Excel) [Wynik]" w tym przykładzie.
   
    ![Wybierz usługę sieci Web](./media/excel-add-in-for-web-services/image1.png)
3. Spowoduje to przejście do **Predict** sekcji.  Ten skoroszyt zawiera już dane przykładowe, ale dla pustego skoroszytu można zaznaczyć komórkę w programie Excel i kliknij przycisk **użyj przykładowych danych**.
4. Wybierz dane z nagłówkami, a następnie kliknij ikonę zakres danych wejściowych.  Upewnij się, że zaznaczono pole "Moje dane mają nagłówki".
5. W obszarze **dane wyjściowe**, wprowadź liczby komórek, w której mają być, na przykład "H1" w tym miejscu danych wyjściowych.
6. Kliknij przycisk **przewidzieć**. Jeśli zaznaczysz pole wyboru "automatycznie predict" wszelkie zmiany w wybranych obszarach (te określone jako dane wejściowe) spowoduje wyzwolenie żądanie i aktualizacji komórek danych wyjściowych, bez konieczności naciśnięcia przycisku predict.
   
    ![Przewidywanie sekcji](./media/excel-add-in-for-web-services/image1.png)

Wdrażanie usługi sieci web, lub użyj istniejącej usługi sieci Web. Aby uzyskać więcej informacji na temat wdrażania usługi sieci web, zobacz [Tutorial 3: Wdrażanie modelu ryzyko kredytowe](tutorial-part3-credit-risk-deploy.md).

Uzyskaj klucz interfejsu API dla usługi sieci web. W przypadku, gdy wykonujesz tej akcji jest zależna od tego, czy opublikowana klasycznego uczenia maszynowego usługi sieci web usługi sieci web nowej usługi Machine Learning.

**Użyj klasycznej usługi sieci web** 

1. W usłudze Machine Learning Studio, kliknij przycisk **usług sieci WEB** sekcji w okienku po lewej stronie, a następnie wybierz usługę sieci web.
   
    ![Wybierz Studio usługi sieci Web](./media/excel-add-in-for-web-services/image4.png)
2. Skopiuj klucz interfejsu API usługi sieci web.
   
    ![Klucz interfejsu API programu Studio](./media/excel-add-in-for-web-services/image5.png)
3. Na **pulpit NAWIGACYJNY** usługi sieci web kliknij pozycję **ŻĄDAŃ/odpowiedzi** łącza.
4. Wyszukaj **żądanie identyfikatora URI** sekcji.  Skopiuj i Zapisz adres URL.

> [!NOTE]
> Teraz jest możliwa do logowania się do [usług sieci Web Azure Machine Learning](https://services.azureml.net) portalu, aby uzyskać klucz interfejsu API usługi sieci web klasycznej usługi Machine Learning.
> 
> 

**Użyj nowej usługi sieci web**

1. W [usług sieci Web Azure Machine Learning](https://services.azureml.net) portalu, kliknij przycisk **usług sieci Web**, następnie wybierz usługę sieci web. 
2. Kliknij przycisk **używanie**.
3. Wyszukaj **informacje podstawowe użycie** sekcji. Skopiuj i Zapisz **klucza podstawowego** i **odpowiedź na żądanie** adresu URL.

## <a name="steps-to-add-a-new-web-service"></a>Kroki, aby dodać nową usługę sieci web

1. Wdrażanie usługi sieci web, lub użyj istniejącej usługi sieci Web. Aby uzyskać więcej informacji na temat wdrażania usługi sieci web, zobacz [Tutorial 3: Wdrażanie modelu ryzyko kredytowe](tutorial-part3-credit-risk-deploy.md).
2. Kliknij przycisk **używanie**.
3. Wyszukaj **informacje podstawowe użycie** sekcji. Skopiuj i Zapisz **klucza podstawowego** i **odpowiedź na żądanie** adresu URL.
4. W programie Excel, przejdź do **usług sieci Web** sekcji (jeśli znajdują się w **Predict** kliknij strzałkę Wstecz, aby przejść do listy usług sieci web).
   
    ![Przejdź do wybór usługi sieci Web](./media/excel-add-in-for-web-services/image3.png)
5. Kliknij przycisk **Dodaj usługę sieci Web**.
6. Wklej adres URL do programu Excel z etykietą w polu tekstowym w dodatku **adresu URL**.
7. Wklej klucz interfejsu API/podstawowe pole tekstowe **klucz interfejsu API**.
8. Kliknij pozycję **Add** (Dodaj).
   
    ![Adres URL i klucz API klasyczne usługi sieci Web.](./media/excel-add-in-for-web-services/image6.png)
9. Aby użyć usługi sieci web, wykonaj czynności podane poprzedniej, "Kroki, aby użyć sieci web istniejące usługi".

## <a name="sharing-your-workbook"></a>Udostępnianie skoroszytu
Po zapisaniu skoroszytu, klucz interfejsu API/podstawowej dla usług sieci web, które zostały dodane również jest zapisywany. Oznacza to, że skoroszyt powinien udostępniać tylko dla osób, którym ufasz.

Zadawaj pytania w poniższej sekcji komentarza lub na naszej [forum](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).
