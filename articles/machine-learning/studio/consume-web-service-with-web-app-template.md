---
title: Korzystania z usługi internetowej Machine Learning za pomocą szablonu aplikacji sieci web | Dokumentacja firmy Microsoft
description: Użyj szablonu aplikacji sieci web w witrynie Azure Marketplace z predykcyjna usługa internetowa Azure Machine Learning.
keywords: Usługa sieci Web, operacjonalizacja, interfejs API REST usługi machine learning
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
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
ms.openlocfilehash: 39add830d44cac43e620a4c13d20e282f3d59e47
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2018
ms.locfileid: "52264221"
---
# <a name="consume-an-azure-machine-learning-web-service-by-using-a-web-app-template"></a>Korzystanie z usługi sieci web Azure Machine Learning za pomocą szablonu aplikacji sieci web

Mogą tworzyć model predykcyjny i wdrożyć go jako usługę sieci web platformy Azure przy użyciu:
- Azure Machine Learning Studio.
- Narzędzia, takie jak R lub Python. 

Po tym modelu zoperacjonalizowanej dostęp przy użyciu interfejsu API REST.

Istnieje szereg sposobów korzystania z interfejsu API REST i uzyskać dostęp do usługi sieci web. Na przykład napisać aplikację w C#, R lub Python przy użyciu przykładowego kodu automatycznie wygenerowanego podczas wdrażania usługi sieci web. (Przykładowy kod jest dostępny w [portalu usług sieci Web Machine Learning](https://services.azureml.net/quickstart) lub pulpicie nawigacyjnym usługi sieci web w usłudze Machine Learning Studio.) Możesz też przykładowy skoroszyt programu Excel utworzony w tym samym czasie.

Ale to najszybszy i najłatwiejszy sposób dostęp do usługi sieci web za pomocą szablonów aplikacji sieci web dostępnych w [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="azure-machine-learning-web-app-templates"></a>Szablony aplikacji sieci web w usłudze Azure Machine Learning
Szablony aplikacji sieci web dostępnych w witrynie Azure Marketplace można utworzyć niestandardową aplikację internetową znający usługę sieci web dane wejściowe i oczekiwanych wyników. To wszystko, co należy zrobić, uzyskania dostępu do aplikacji sieci web do usługi sieci web i danych, a szablon zajmie się resztą.

Dostępne są dwa szablony:

* [Szablon aplikacji sieci Web usługi Azure ML odpowiedź na żądanie](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Usługa Azure Batch ML — szablonu aplikacji sieci Web usługi wykonywania](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Każdy szablon służy do tworzenia przykładowej aplikacji platformy ASP.NET za pomocą identyfikatora URI interfejsu API i klucza dla usługi sieci web. Szablon następnie aplikacja jest wdrażana jako witryny sieci Web na platformie Azure. 

Szablon usługi odpowiedź na żądanie (RRS) umożliwia utworzenie aplikacji sieci web, które można użyć, aby wysłać pojedynczy wiersz danych do usługi sieci web, umożliwiającej uzyskanie pojedynczego wyniku. Szablon usługi Batch Execution Service (BES) tworzy aplikację internetową, która służy do wysyłania wiele wierszy danych, aby wyświetlić wyniki wielu.

Bez kodowania jest wymagana do używania tych szablonów. Po prostu podać klucz interfejsu API i identyfikator URI, a szablon tworzy aplikację.

Aby uzyskać klucz interfejsu API i identyfikator URI żądania usługi sieci web:

1. W [portalu usług sieci Web](https://services.azureml.net/quickstart), wybierz opcję **usług sieci Web** u góry. Lub klasyczna usługa sieci web, można wybrać **klasycznych usług sieci Web**.
2. Wybierz usługę sieci web, który chcesz uzyskać dostęp.
3. Klasyczna usługa sieci web wybierz punkt końcowy, który chcesz uzyskać dostęp.
4. Wybierz **zużywania** u góry.
5. Skopiuj klucz podstawowy lub pomocniczy i zapisz go.
6. Jeśli tworzysz szablon RRS, skopiuj **odpowiedź na żądanie** identyfikatora URI i zapisz go. Jeśli tworzysz szablon usługi BES Skopiuj **żądań wsadowych** identyfikatora URI i zapisz go.


## <a name="how-to-use-the-request-response-service-template"></a>Sposób używania szablonu usługi odpowiedź na żądanie
Wykonaj następujące kroki, aby użyć szablonu aplikacji sieci web RRS, jak pokazano na poniższym diagramie.

![Proces, aby użyć szablonu sieci web RRS][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![Request URI][image4] -->

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **nowy**, wyszukiwanie i wybieranie **usługi Azure ML odpowiedź na żądanie usługi Web App**, a następnie wybierz pozycję **Utwórz**. 
3. W **Utwórz** okienka:
   
   * Nadaj aplikacji sieci web unikatową nazwę. Adres URL aplikacji sieci web będzie to nazwa, następuje **. azurewebsites.net**. Na przykład **http://carprediction.azurewebsites.net**.
   * Wybierz subskrypcję platformy Azure i usług, w których jest uruchomiona usługa sieci web.
   * Wybierz pozycję **Utwórz**.
     
   ![Tworzenie aplikacji internetowej][image5]

4. Po zakończeniu Azure podczas wdrażania aplikacji sieci web, wybierz **adresu URL** w ustawieniach aplikacji sieci web na platformie Azure, lub strony wprowadź adres URL w przeglądarce sieci web. Na przykład, wprowadź **http://carprediction.azurewebsites.net**.
5. Po uruchomieniu pierwszych aplikacji sieci web, prosi o **adresu URL interfejsu API wpisu** i **klucz interfejsu API**. Wprowadź wartości, które zostały wcześniej zapisane (żądanie identyfikatora URI i klucz API,). Wybierz **przesłać**.
     
   ![Po wprowadź identyfikator URI i klucz API][image6]

6. Aplikacja sieci web jest wyświetlana jego **konfigurację aplikacji sieci Web** strony z bieżącymi ustawieniami usługi sieci web. W tym miejscu można wprowadzić zmiany do ustawień, których używa aplikacja sieci web.
   
   > [!NOTE]
   > Zmiana ustawień w tym miejscu zmienia tylko je dla tej aplikacji sieci web. Go nie powoduje zmiany ustawień domyślnych usługi sieci web. Na przykład w przypadku zmiany tekstu w **opis** w tym miejscu nie zmienia opis wyświetlany na pulpicie nawigacyjnym usługi sieci web w usłudze Machine Learning Studio.
   > 
   > 
   
    Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz zmiany**, a następnie wybierz pozycję **przejdź do strony głównej**.

7. Na stronie głównej można wprowadzić wartości do wysłania do usługi sieci web. Wybierz **przesyłania** gdy wszystko będzie gotowe, a wynik zostanie zwrócony.

Jeśli chcesz powrócić do **konfiguracji** strony, przejdź do **setting.aspx** stronie aplikacji sieci web. Na przykład, przejdź do **http://carprediction.azurewebsites.net/setting.aspx**. Zostanie wyświetlony monit ponownie wprowadź klucz interfejsu API. Potrzebujesz, aby uzyskać dostęp do strony Ustawienia i aktualizować.

Możesz zatrzymać, ponownie uruchomić lub usunąć aplikacji sieci web w witrynie Azure portal, jak dowolną inną aplikację sieci web. Tak długo, jak działa, możesz przejść do adresu internetowego głównego i wprowadź nowe wartości.

## <a name="how-to-use-the-batch-execution-service-template"></a>Jak używać szablonu usługę wykonywania wsadowego
Za pomocą szablonu aplikacji sieci web usługi BES w taki sam sposób jak rekordy zasobów szablonu. Różnica polega na tym, czy można użyć utworzonej aplikacji sieci web możesz przesłać wiele wierszy danych i otrzymywać wiele wyników.

Wartości wejściowe dla usługi sieci web wykonywanie wsadowe mogą pochodzić z usługi Azure Storage lub lokalny plik. Wyniki są przechowywane w kontenerze usługi Azure storage. Dlatego należy kontenera usługi Azure storage do przechowywania wyników, które zwraca aplikacji sieci web. Należy również przygotowywanie danych wejściowych.

![Proces, aby użyć szablonu sieci web usługi BES][image2]

1. Wykonaj tę samą procedurę do tworzenia aplikacji sieci web usługi BES, jak w przypadku szablonu rekordy zasobów. Ale w takim przypadku przejdź do [szablonu aplikacji sieci Web Azure ML partii wykonywania usługi](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) aby otworzyć szablon usługi BES w witrynie Azure Marketplace. Wybierz **tworzenie aplikacji sieci Web**.

2. Aby określić, w którym wyniki, przechowywane, wprowadź informacje kontener docelowy, na stronie głównej aplikacji sieci web. Również określić, gdzie w aplikacji sieci web można uzyskać wartości wejściowe: w pliku lokalnym lub w kontenerze usługi Azure storage.
   Wybierz **przesłać**.
   
   ![Informacje dotyczące magazynu][image7]

Aplikacja sieci web wyświetla stronę o stanie zadania. Po zakończeniu zadania możesz uzyskać lokalizację wyników w usłudze Azure Blob storage. Istnieje również możliwość pobierania wyniki do pliku lokalnego.

## <a name="for-more-information"></a>Więcej informacji
Aby dowiedzieć się więcej:

* Tworzenie eksperymentu usługi machine learning za pomocą usługi Machine Learning Studio, zobacz [Tworzenie pierwszego eksperymentu w usłudze Azure Machine Learning Studio](create-experiment.md).
* Jak wdrożyć eksperymentu usługi machine learning jako usługę sieci web, zobacz [wdrażanie usługi sieci web Azure Machine Learning](publish-a-machine-learning-web-service.md).
* Inne sposoby uzyskania dostępu do usługi sieci web, zobacz [sposobu korzystania z usługi sieci web Azure Machine Learning](consume-web-services.md).

[image1]: media/consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/consume-web-service-with-web-app-template/api-key.png
[image4]: media/consume-web-service-with-web-app-template/post-uri.png
[image5]: media/consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/consume-web-service-with-web-app-template/storage.png
