---
title: Dodatek programu Excel dla usług sieci Web
titleSuffix: ML Studio (classic) - Azure
description: Jak używać usługi Azure Machine Learning w sieci Web bezpośrednio w programie Excel, bez konieczności pisania jakiegokolwiek kodu.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: f7b5ca7112a6fb79586dc66b385e8015fe10e0b0
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153455"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Dodatek programu Excel dla usług sieci Web Azure Machine Learning Studio (klasyczny)
Excel ułatwia wywołują usługi sieci web bezpośrednio, bez konieczności pisania kodu.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Kroki, aby użyć usługi sieci web istniejące w skoroszycie

1. Otwórz [przykładowy plik programu Excel](https://aka.ms/amlexcel-sample-2), który zawiera dodatek programu Excel i dane dotyczące pasażerów w Titanic. 
 
    > [!NOTE]
    > Zobaczysz, że na liście usług sieci Web związane z nim do pliku i u dołu pola wyboru "Automatycznie — przewidywanie". Jeśli włączysz funkcję autoprzewidywania, prognozy **wszystkich** usług zostaną zaktualizowane po każdym wprowadzeniu zmiany w danych wejściowych. Jeśli nie jest zaznaczone trzeba będzie kliknąć "Przewidzieć All" w przypadku odświeżania. Umożliwiające automatyczne — przewidywanie na usługę poziomu przejdź do kroku 6.

2. Wybierz usługę sieci web, klikając go — "Titanic renty predykcyjne (przykład dodatku Excel) [Wynik]" w tym przykładzie.
   
    ![Wybierz usługę sieci Web](./media/excel-add-in-for-web-services/image1.png)
3. Spowoduje to przejście do sekcji **przewidywania** .  Ten skoroszyt zawiera już przykładowe dane, ale dla pustego skoroszytu możesz wybrać komórkę w programie Excel i kliknąć pozycję **Użyj przykładowych danych**.
4. Wybierz dane z nagłówkami, a następnie kliknij ikonę zakres danych wejściowych.  Upewnij się, że zaznaczono pole "Moje dane mają nagłówki".
5. W obszarze **dane wyjściowe**wprowadź numer komórki, w której mają być wyświetlane dane wyjściowe, na przykład "H1" w tym miejscu.
6. Kliknij pozycję **przewidywanie**. Jeśli zaznaczysz pole wyboru "automatycznie predict" wszelkie zmiany w wybranych obszarach (te określone jako dane wejściowe) spowoduje wyzwolenie żądanie i aktualizacji komórek danych wyjściowych, bez konieczności naciśnięcia przycisku predict.
   
    ![Przewidywanie sekcji](./media/excel-add-in-for-web-services/image1.png)

Wdrażanie usługi sieci web, lub użyj istniejącej usługi sieci Web. Aby uzyskać więcej informacji na temat wdrażania usługi sieci Web, zobacz [samouczek 3: Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md).

Uzyskaj klucz interfejsu API dla usługi sieci web. W przypadku, gdy wykonujesz tej akcji jest zależna od tego, czy opublikowana klasycznego uczenia maszynowego usługi sieci web usługi sieci web nowej usługi Machine Learning.

**Korzystanie z klasycznej usługi sieci Web** 

1. W Machine Learning Studio (klasyczny) kliknij sekcję **usługi sieci Web** w okienku po lewej stronie, a następnie wybierz usługę sieci Web.
   
    ![Wybierz Studio usługi sieci Web](./media/excel-add-in-for-web-services/image4.png)
2. Skopiuj klucz interfejsu API usługi sieci web.
   
    ![Klucz interfejsu API programu Studio](./media/excel-add-in-for-web-services/image5.png)
3. Na karcie **pulpit nawigacyjny** usługi sieci Web kliknij link **żądanie/odpowiedź** .
4. Poszukaj sekcji **Żądaj identyfikatora URI** .  Skopiuj i Zapisz adres URL.

> [!NOTE]
> Teraz można zalogować się do portalu [usług sieci web Azure Machine Learning](https://services.azureml.net) , aby uzyskać klucz interfejsu API dla klasycznej usługi sieci Web Machine Learning.
> 
> 

**Użyj nowej usługi sieci Web**

1. W portalu [usług sieci web Azure Machine Learning](https://services.azureml.net) kliknij pozycję **usługi sieci Web**, a następnie wybierz usługę sieci Web. 
2. Kliknij **pozycję**Użyj.
3. Poszukaj sekcji **podstawowe informacje o zużyciu** . Skopiuj i Zapisz **klucz podstawowy** i adres URL **odpowiedzi żądania** .

## <a name="steps-to-add-a-new-web-service"></a>Kroki, aby dodać nową usługę sieci web

1. Wdrażanie usługi sieci web, lub użyj istniejącej usługi sieci Web. Aby uzyskać więcej informacji na temat wdrażania usługi sieci Web, zobacz [samouczek 3: Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md).
2. Kliknij **pozycję**Użyj.
3. Poszukaj sekcji **podstawowe informacje o zużyciu** . Skopiuj i Zapisz **klucz podstawowy** i adres URL **odpowiedzi żądania** .
4. W programie Excel przejdź do sekcji **usługi sieci Web** (Jeśli jesteś w sekcji **przewidywania** , kliknij strzałkę wstecz, aby przejść do listy usług sieci Web).
   
    ![Przejdź do wybór usługi sieci Web](./media/excel-add-in-for-web-services/image3.png)
5. Kliknij pozycję **Dodaj usługę sieci Web**.
6. Wklej adres URL do pola tekstowego dodatku programu Excel o nazwie **URL**.
7. Wklej interfejs API/klucz podstawowy do pola tekstowego oznaczonego **kluczem interfejsu API**.
8. Kliknij pozycję **Add** (Dodaj).
   
    ![Adres URL i klucz API klasyczne usługi sieci Web.](./media/excel-add-in-for-web-services/image6.png)
9. Aby użyć usługi sieci web, wykonaj czynności podane poprzedniej, "Kroki, aby użyć sieci web istniejące usługi".

## <a name="sharing-your-workbook"></a>Udostępnianie skoroszytu
Po zapisaniu skoroszytu, klucz interfejsu API/podstawowej dla usług sieci web, które zostały dodane również jest zapisywany. Oznacza to, że skoroszyt powinien udostępniać tylko dla osób, którym ufasz.

Zadawaj pytania w poniższej sekcji komentarza lub na naszym [forum](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).
