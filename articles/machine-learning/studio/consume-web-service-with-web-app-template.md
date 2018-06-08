---
title: Korzystać z usługi sieci web uczenie maszynowe przy użyciu szablonu aplikacji sieci web | Dokumentacja firmy Microsoft
description: Użyj szablonu aplikacji sieci web w portalu Azure Marketplace, aby korzystać z usługi sieci web predykcyjnej w usłudze Azure Machine Learning.
keywords: Usługa sieci Web, operationalization, interfejsu API REST, uczenia maszynowego
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: e0d71683-61b9-4675-8df5-09ddc2f0d92d
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 03729a5b94b355869367e7f356e299f9afe38f75
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835002"
---
# <a name="consume-an-azure-machine-learning-web-service-by-using-a-web-app-template"></a>Korzystać z usługi sieci web uczenie maszynowe Azure przy użyciu szablonu aplikacji sieci web

Możesz utworzyć model predykcyjny i go wdrożyć jako usługę sieci web platformy Azure przy użyciu:
- Azure Machine Learning Studio.
- Narzędzia, takie jak R lub Python. 

Po wykonaniu tej można uzyskać dostępu do modelu operationalized przy użyciu interfejsu API REST.

Istnieje wiele sposobów, aby korzystać z interfejsu API REST i uzyskania dostępu do usługi sieci web. Na przykład można napisać aplikację w języku C#, R lub Python za pomocą przykładowy kod wygenerowany przez po wdrożeniu usługi sieci web. (Przykładowy kod jest dostępny w [portal usługi sieci Web usługi Machine Learning](https://services.azureml.net/quickstart) lub na pulpicie nawigacyjnym usługi sieci web w usłudze Machine Learning Studio.) Lub może używać do przykładowego skoroszytu programu Microsoft Excel utworzony w tym samym czasie.

Ale jest najszybszym i Najprostszym sposobem uzyskania dostępu do usługi sieci web za pomocą szablonów aplikacji sieci web dostępnych w [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="azure-machine-learning-web-app-templates"></a>Szablony aplikacji sieci web w usłudze Azure Machine Learning
Szablony aplikacji sieci web dostępne w portalu Azure Marketplace można utworzyć aplikacji sieci web niestandardowego, który zna usługi sieci web dane wejściowe i oczekiwanych rezultatów. Wszystko, co należy zrobić to uzyskania dostępu do aplikacji sieci web do usługi sieci web i danych, a reszta szablonu.

Dostępne są dwa szablony:

* [Szablon aplikacji sieci Web usługi Azure ML żądań i odpowiedzi](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Szablon aplikacji partii zadań Azure ML wykonywania usługi sieci Web](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Każdy szablon tworzy przykładowej aplikacji ASP.NET przy użyciu identyfikatora URI interfejsu API i klucz dla usługi sieci web. Szablon następnie wdraża aplikację jako witryny sieci Web na platformie Azure. 

Szablon usługi żądań i odpowiedzi (RR) do tworzenia aplikacji sieci web, który służy do wysyłania pojedynczy wiersz danych z usługą sieci web, umożliwiającej uzyskanie pojedynczego wyniku. Szablon usługi wykonywania wsadowego (BES) tworzy służącego do wysyłania wiele wierszy danych, aby uzyskać wyniki wiele aplikacji sieci web.

Kodowanie nie jest wymagana do używania tych szablonów. Wystarczy podać klucz interfejsu API i identyfikator URI, a szablon tworzy aplikację.

Aby uzyskać klucz interfejsu API i adres URL żądania usługi sieci web:

1. W [usług sieci Web portalu](https://services.azureml.net/quickstart), wybierz pozycję **usług sieci Web** u góry. Lub dla usługi sieci web klasycznego, należy wybrać **klasycznym usługi sieci Web**.
2. Wybierz usługę sieci web, który chcesz uzyskać dostęp.
3. Dla usługi sieci web klasycznego należy wybrać punkt końcowy, który chcesz uzyskać dostęp.
4. Wybierz **Consume** u góry.
5. Skopiuj klucz podstawowy lub pomocniczy i zapisz go.
6. Jeśli tworzysz szablon RRS, skopiuj **żądań i odpowiedzi** identyfikatora URI i zapisz go. Jeśli tworzysz szablon usługi BES, skopiuj **żądań wsadowych** identyfikatora URI i zapisz go.


## <a name="how-to-use-the-request-response-service-template"></a>Jak przy użyciu szablonu usługi żądań i odpowiedzi
Wykonaj następujące kroki, aby użyć szablonu aplikacji sieci web rekordów zasobów, jak pokazano na poniższym diagramie.

![Proces szablon rekordy zasobów sieci web][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![Request URI][image4] -->

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **nowy**, wyszukaj i wybierz **aplikacji sieci Web usługi Azure ML żądanie-odpowiedź**, a następnie wybierz **Utwórz**. 
3. W **Utwórz** okienka:
   
   * Nadaj unikatową nazwę aplikacji sieci web. Adres URL aplikacji sieci web będzie tej nazwy, a następnie **. azurewebsites.net**. Na przykład **http://carprediction.azurewebsites.net**.
   * Wybierz subskrypcję platformy Azure i usługi, w których jest uruchomiona usługa sieci web.
   * Wybierz pozycję **Utwórz**.
     
   ![Tworzenie aplikacji internetowej][image5]

4. Po zakończeniu Azure wdrażanie aplikacji sieci web wybierz **adres URL** w ustawieniach aplikacji sieci web strony na platformie Azure, lub wprowadź adres URL w przeglądarce sieci web. Na przykład wprowadź **http://carprediction.azurewebsites.net**.
5. Podczas pierwszego uruchomienia aplikacji sieci web, pyta o **adresu URL przesyłania interfejsu API** i **klucz interfejsu API**. Wprowadź wartości, które zostały wcześniej zapisane (żądanie identyfikatora URI i klucz API,). Wybierz **przesłać**.
     
   ![Wprowadź post identyfikator URI i klucz API][image6]

6. Aplikacja sieci web jest wyświetlana jego **konfiguracji aplikacji sieci Web** strony z bieżącymi ustawieniami usługi sieci web. Tutaj można wprowadzić zmiany ustawień, które korzysta z aplikacji sieci web.
   
   > [!NOTE]
   > Zmiana ustawień w tym miejscu zmienia tylko ich tej aplikacji sieci web. Go nie powoduje zmiany ustawień domyślnych usługi sieci web. Na przykład w przypadku zmiany tekstu w **opis** w tym miejscu nie zmienia opis wyświetlane na pulpicie nawigacyjnym usługi sieci web w usłudze Machine Learning Studio.
   > 
   > 
   
    Gdy wszystko będzie gotowe, wybierz **zapisać zmiany**, a następnie wybierz **przejdź do strony głównej**.

7. Na stronie głównej można wprowadzić wartości do wysłania do usługi sieci web. Wybierz **przesyłania** gdy wszystko będzie gotowe, i zostanie zwrócony wynik.

Jeśli chcesz powrócić do **konfiguracji** strony, przejdź do **setting.aspx** strony aplikacji sieci web. Na przykład, przejdź do **http://carprediction.azurewebsites.net/setting.aspx**. Zostanie wyświetlony monit o ponowne wprowadzenie klucza interfejsu API. Należy to dostęp do strony Ustawienia i aktualizować.

Można zatrzymać, ponownie uruchomić lub usunąć aplikacji sieci web w portalu Azure, takich jak każda inna aplikacja sieci web. Tak długo, jak działa, możesz przejść na adres domowej sieci web i wprowadź nowe wartości.

## <a name="how-to-use-the-batch-execution-service-template"></a>Jak przy użyciu szablonu usługi wykonywania wsadowego
Można użyć szablonu aplikacji sieci web usługi BES w taki sam sposób jak rekordy zasobów szablonu. Różnica polega na tym, że można użyć utworzonej aplikacji sieci web do przesyłania wiele wierszy danych i odbierania wiele wyników.

Wartości wejściowe dla usługi sieci web wykonywania wsadowego mogą pochodzić z usługi Azure Storage lub pliku lokalnego. Wyniki są przechowywane w kontenerze magazynu Azure. Należy więc, kontener magazynu Azure do przechowywania wyników, które zwraca aplikacji sieci web. Należy również przygotowywanie danych wejściowych.

![Proces, aby użyć szablonu sieci web usługi BES][image2]

1. Postępuj zgodnie z tą samą procedurą, aby utworzyć aplikację sieci web usługi BES, podobnie jak w przypadku szablonu rekordy zasobów. Jednak w takim przypadku przejdź do [szablonu aplikacji sieci Web Azure ML partii wykonywania usługi](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) aby otworzyć szablon usługi BES w portalu Azure Marketplace. Wybierz **tworzenie aplikacji sieci Web**.

2. Aby określić, w którym wyniki przechowywane, wprowadź informacje o kontenerze docelowego na stronie głównej aplikacji sieci web. Również określić, gdzie aplikacja sieci web można uzyskać wartości wejściowe: w pliku lokalnym lub w kontenerze magazynu Azure.
   Wybierz **przesłać**.
   
   ![Informacje o magazynu][image7]

Aplikacja sieci web wyświetla stronę o stanie zadania. Po zakończeniu zadania można pobrać lokalizacji wyników w magazynie obiektów Blob Azure. Istnieje również możliwość pobierania wyniki do pliku lokalnego.

## <a name="for-more-information"></a>Więcej informacji
Aby dowiedzieć się więcej o:

* Tworzenie eksperymentu uczenia maszynowego o usłudze Machine Learning Studio, zobacz [Tworzenie pierwszego eksperymentu w usłudze Azure Machine Learning Studio](create-experiment.md).
* Jak wdrożyć jako usługę sieci web eksperymentu uczenia maszynowego, zobacz [wdrażanie usługi sieci web Azure Machine Learning](publish-a-machine-learning-web-service.md).
* Inne sposoby uzyskania dostępu do usługi sieci web, zobacz [jak korzystać z usługi sieci web Azure Machine Learning](consume-web-services.md).

[image1]: media/consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/consume-web-service-with-web-app-template/api-key.png
[image4]: media/consume-web-service-with-web-app-template/post-uri.png
[image5]: media/consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/consume-web-service-with-web-app-template/storage.png
