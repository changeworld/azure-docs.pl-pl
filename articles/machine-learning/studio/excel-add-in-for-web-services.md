---
title: Dodatek programu Excel dla usług sieci Web
titleSuffix: ML Studio (classic) - Azure
description: Jak korzystać z usług Azure Machine Learning Web bezpośrednio w programie Excel bez pisania kodu.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: e30103589c1baf9a165839cd041ff511a119c5ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204379"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Dodatek programu Excel dla usług Azure Machine Learning Studio (klasyczne) usługi sieci Web

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Program Excel ułatwia bezpośrednie wywoływanie usług sieci web bez konieczności pisania kodu.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Kroki, aby korzystać z istniejącej usługi sieci web w skoroszycie

1. Otwórz [przykładowy plik programu Excel](https://aka.ms/amlexcel-sample-2), który zawiera dodatek Excel i dane o pasażerach na Titanicu. 
 
    > [!NOTE]
    > Zobaczysz listę usług sieci Web związanych z plikiem, a na dole pole wyboru "Automatyczne przewidywanie". Jeśli włączysz automatyczne przewidywanie prognoz **wszystkich** usług będą aktualizowane za każdym razem, gdy nastąpi zmiana danych wejściowych. Jeśli nie zaznaczone, trzeba będzie kliknąć na "Predict All" do odświeżenia. Aby włączyć automatyczne przewidywanie na poziomie usługi, przejdź do kroku 6.

2. Wybierz usługę sieci web, klikając ją — "Titanic Survivor Predictor (Excel Add-in Sample) [Score]" w tym przykładzie.
   
    ![Wybierz usługę sieci Web](./media/excel-add-in-for-web-services/image1.png)
3. Spowoduje to przejście do sekcji **Predict.**  Ten skoroszyt zawiera już przykładowe dane, ale w przypadku pustego skoroszytu można zaznaczyć komórkę w programie Excel i kliknąć pozycję **Użyj przykładowych danych**.
4. Wybierz dane z nagłówkami i kliknij ikonę zakresu danych wejściowych.  Upewnij się, że pole "Moje dane ma nagłówki" jest zaznaczone.
5. W obszarze **Dane wyjściowe**wprowadź numer komórki, w którym ma być dane wyjściowe, na przykład "H1" w tym miejscu.
6. Kliknij **pozycję Wytypuj**. Jeśli wybierzesz pole wyboru "auto-predict" wszelkie zmiany w wybranych obszarach (te określone jako dane wejściowe) spowoduje żądanie i aktualizację komórek wyjściowych bez konieczności naciskania przycisku predict.
   
    ![Przewidywanie sekcji](./media/excel-add-in-for-web-services/image1.png)

Wdrażanie usługi sieci web lub korzystanie z istniejącej usługi sieci Web. Aby uzyskać więcej informacji na temat wdrażania usługi sieci web, zobacz [Samouczek 3: Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md).

Pobierz klucz interfejsu API dla usługi sieci web. Miejsce wykonania tej akcji zależy od tego, czy opublikowano klasyczną usługę sieci web uczenia maszynowego nowej usługi sieci web usługi uczenia maszynowego.

**Korzystanie z klasycznej usługi sieci web** 

1. W usłudze Machine Learning Studio (klasyczna) kliknij sekcję **USŁUGI SIECI WEB** w lewym okienku, a następnie wybierz usługę sieci web.
   
    ![Studio wybiera usługę sieci Web](./media/excel-add-in-for-web-services/image4.png)
2. Skopiuj klucz interfejsu API dla usługi sieci web.
   
    ![Klucz interfejsu API programu Studio](./media/excel-add-in-for-web-services/image5.png)
3. Na karcie **DASHBOARD** dla usługi sieci web kliknij łącze **ŻĄDANIE/ODPOWIEDŹ.**
4. Poszukaj sekcji **Identyfikator URI żądania.**  Skopiuj i zapisz adres URL.

> [!NOTE]
> Teraz można zalogować się do portalu [usług Azure Machine Learning Web Services,](https://services.azureml.net) aby uzyskać klucz interfejsu API dla klasycznej usługi sieci web uczenia maszynowego.
> 
> 

**Korzystanie z nowej usługi sieci web**

1. W portalu [usług azure machine learning web services](https://services.azureml.net) kliknij pozycję Usługi sieci **Web**, a następnie wybierz usługę sieci web. 
2. Kliknij **pozycję Pochówotwórz**.
3. Poszukaj sekcji **Informacje o zużyciu podstawowym.** Skopiuj i zapisz **klucz podstawowy** i adres URL **żądania odpowiedzi.**

## <a name="steps-to-add-a-new-web-service"></a>Kroki, aby dodać nową usługę sieci web

1. Wdrażanie usługi sieci web lub korzystanie z istniejącej usługi sieci Web. Aby uzyskać więcej informacji na temat wdrażania usługi sieci web, zobacz [Samouczek 3: Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md).
2. Kliknij **pozycję Pochówotwórz**.
3. Poszukaj sekcji **Informacje o zużyciu podstawowym.** Skopiuj i zapisz **klucz podstawowy** i adres URL **żądania odpowiedzi.**
4. W programie Excel przejdź do sekcji **Usługi sieci Web** (jeśli znajdujesz się w sekcji **Przewidywanie,** kliknij strzałkę wstecz, aby przejść do listy usług sieci web).
   
    ![Przejdź do wyboru usługi sieci Web](./media/excel-add-in-for-web-services/image3.png)
5. Kliknij **pozycję Dodaj usługę sieci Web**.
6. Wklej adres URL do adresu URL dodatku programu Excel z etykietą **URL**.
7. Wklejenie interfejsu API/klucza podstawowego do **klucza interfejsu API**z etykietą pola tekstowego .
8. Kliknij przycisk **Dodaj**.
   
    ![Klucz adresu URL i interfejsu API klasycznej usługi sieci Web.](./media/excel-add-in-for-web-services/image6.png)
9. Aby korzystać z usługi sieci web, postępuj zgodnie z poprzednimi wskazówkami, "Kroki, aby korzystać z istniejącej usługi sieci web."

## <a name="sharing-your-workbook"></a>Udostępnianie skoroszytu
Jeśli zapiszesz skoroszyt, zostanie również zapisany klucz INTERFEJSU API/Podstawowego dla dodanych usług sieci web. Oznacza to, że skoroszyt należy udostępniać tylko zaufanym osobom.

Zadaj wszelkie pytania w poniższej sekcji komentarzy lub na naszym [forum.](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409)
