---
title: Użyj Azure Machine Learning parametry usługi sieci Web | Dokumentacja firmy Microsoft
description: Jak używać parametry usługi sieci Web Azure Machine Learning, aby zmodyfikować zachowanie modelu podczas uzyskiwania dostępu do usługi sieci web.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.openlocfilehash: 3eb6f08d89daec063841eeb5fff8479c9f2f41fe
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="use-azure-machine-learning-web-service-parameters"></a>Używanie parametrów usługi sieci Web Azure Machine Learning
Usługi sieci web Azure Machine Learning jest tworzony przez publikowanie eksperymentu, która zawiera moduły można skonfigurować parametrów. W niektórych przypadkach możesz zmienić to zachowanie modułu jest uruchomiona usługa sieci web. *Parametry usługi sieci Web* umożliwiają wykonywanie tego zadania. 

Typowym przykładem jest konfigurowanie [i zaimportuj dane] [ reader] modułu, aby użytkownik usługi opublikowana w sieci web można określić innego źródła danych podczas uzyskiwania dostępu do usługi sieci web. Lub konfigurowania [eksportowanie danych] [ writer] modułu, dzięki czemu można określić inną lokalizację docelową. Inne przykłady zmiana liczby bitów dla [Tworzenie skrótu funkcji] [ feature-hashing] modułu lub liczbę potrzebne funkcje [na podstawie filtru wybór funkcji] [ filter-based-feature-selection] modułu. 

Można ustawić parametry usługi sieci Web i skojarz je z jednego lub więcej parametrów modułu w eksperymencie i czy są one wymagane lub opcjonalne. Użytkownik usługi sieci web mogą udzielić im wartości dla tych parametrów, gdy wywołują usługi sieci web. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="how-to-set-and-use-web-service-parameters"></a>Jak ustawić i skorzystać parametry usługi sieci Web
Należy zdefiniować parametr usługi sieci Web, klikając ikonę obok parametru modułu i wybierając "Ustaw jako parametr usługi sieci web". Tworzy nowy parametr usługi sieci Web i łączy go do tego parametru modułu. Następnie podczas uzyskiwania dostępu do usługi sieci web użytkownika można określić wartość dla parametru usługi sieci Web i jest stosowane do parametru modułu.

Po zdefiniowaniu parametr usługi sieci Web jest dostępne dla innych parametrów modułu w eksperymencie. Po zdefiniowaniu parametr usługi sieci Web skojarzony z parametrem dla jednego modułu można użyć tego samego parametru usługi sieci Web dla inny moduł, tak długo, jak parametr oczekuje wartości tego samego typu. Na przykład jeśli parametr usługi sieci Web jest wartość liczbowa, następnie tylko umożliwia dla modułu parametrów, które oczekują wartość liczbową. Gdy użytkownik ustawia wartość dla parametru usługi sieci Web, będą dotyczyć wszystkich parametrów skojarzonych modułu.

Można zdecydować, czy można podać wartości domyślnej dla parametru usługi sieci Web. Jeśli to zrobisz, parametr jest opcjonalny dla użytkownika usługi sieci web. Jeśli nie podasz wartość domyślną, użytkownika jest wymagane wprowadzenie wartości podczas uzyskiwania dostępu do usługi sieci web.

Dokumentacja interfejsu API usługi sieci web zawiera informacje o użytkowniku usługi sieci web na temat sposobu programowo określić parametr usługi sieci Web podczas uzyskiwania dostępu do usługi sieci web.

> [!NOTE]
> Dokumentacja interfejsu API dla usługi sieci web klasycznego jest zapewniana za pomocą **strona pomocy interfejsu API** łącze usługi sieci web **pulpitu NAWIGACYJNEGO** w usłudze Machine Learning Studio. Dokumentacja interfejsu API dla nowej usługi sieci web jest zapewniana za pomocą [usługi sieci Web systemu Azure Machine Learning](https://services.azureml.net/Quickstart) portal **Consume** i **interfejsu API programu Swagger** stron dla usługi sieci web.
> 
> 

## <a name="example"></a>Przykład
Na przykład załóżmy, że mamy doświadczenia z [eksportowanie danych] [ writer] moduł, który wysyła informacje do magazynu obiektów blob platformy Azure. Zdefiniujemy parametr usługi sieci Web o nazwie "Ścieżka obiektu Blob" umożliwiająca użytkownika usługi sieci web zmienić ścieżkę do magazynu obiektów blob podczas uzyskiwania dostępu do usługi.

1. W usłudze Machine Learning Studio, kliknij przycisk [eksportowanie danych] [ writer] modułu, aby go wybrać. Jego właściwości są wyświetlane w okienku właściwości z prawej strony obszaru roboczego eksperymentu.
2. Określ typ magazynu:
   
   * W obszarze **określ miejsce docelowe danych**, wybierz pozycję "Magazyn obiektów Blob Azure".
   * W obszarze **Określ typ uwierzytelniania**, wybierz "Konto".
   * Wprowadź informacje o koncie magazynu obiektów blob platformy Azure. 

3. Kliknij ikonę z prawej strony **ścieżki do obiektu blob, począwszy od parametru kontenera**. Wygląda następująco:
   
   ![Ikona parametr usługi sieci Web][icon]
   
   Wybierz "Ustaw jako parametr usługi sieci web".
   
   Wpis zostanie dodany w obszarze **parametry usługi sieci Web** w dolnej części okienka właściwości o nazwie "Ścieżki do obiektu blob kontenera, począwszy od". Jest to parametr usługi sieci Web, która jest teraz skojarzony z tym [eksportowanie danych] [ writer] parametru modułu.
4. Zmień nazwę parametru usługi sieci Web, kliknij nazwę, wprowadź "Ścieżka obiektu Blob" i naciśnij klawisz **Enter** klucza. 
5. Aby podać wartości domyślnej dla parametru usługi sieci Web, kliknij ikonę z prawej strony nazwy wybierz "Podać wartości domyślnej", wprowadź wartość (na przykład "container1/output1.csv") i naciśnij klawisz **Enter** klucza.
   
   ![Parametr usługi sieci Web][parameter]
6. Kliknij pozycję **Run** (Uruchom). 
7. Kliknij przycisk **wdrażanie usługi sieci Web** i wybierz **wdrażanie usługi sieci Web [klasyczny]** lub **wdrażanie usługi sieci Web [New]** wdrożenie usługi sieci web.

> [!NOTE] 
> Aby wdrożyć nową usługę sieci web musi masz wystarczające uprawnienia do subskrypcji, do którego należy wdrożyć usługę sieci web. Aby uzyskać więcej informacji, zobacz [zarządzania usługi sieci Web przy użyciu portalu usługi sieci Web systemu Azure Machine Learning](manage-new-webservice.md). 

Użytkownik usługi sieci web można teraz określić nową lokalizację docelową dla [eksportowanie danych] [ writer] modułu podczas uzyskiwania dostępu do usługi sieci web.

## <a name="more-information"></a>Więcej informacji
Aby uzyskać bardziej szczegółowy przykład zobacz [parametry usługi sieci Web](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) wpis w [Machine Learning blogu](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Aby uzyskać więcej informacji dotyczących uzyskiwania dostępu do usługi sieci web uczenie maszynowe, zobacz [jak korzystać z usługi sieci Web Azure Machine Learning](consume-web-services.md).

<!-- Images -->
[icon]: ./media/web-service-parameters/icon.png
[parameter]: ./media/web-service-parameters/parameter.png


<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

