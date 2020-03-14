---
title: Parametry usługi sieci Web — Azure Machine Learning Studio (klasyczny) | Microsoft Docs
description: Jak używać parametry usługi sieci Web do programu Azure Machine Learning, aby zmodyfikować zachowanie modelu podczas uzyskiwania dostępu do usługi sieci web.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2017
ms.openlocfilehash: d6ddd9603f22bd3820d18be020b9c620cf06aa42
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204413"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>Użyj Azure Machine Learning Studio (klasyczne) parametrów usługi sieci Web

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Usługi sieci web Azure Machine Learning jest tworzony przez opublikowanie eksperyment, który zawiera moduły z konfigurowalne parametry. W niektórych przypadkach można zmienić zachowanie modułu, gdy jest uruchomiona usługa sieci web. *Parametry usługi sieci Web* umożliwiają wykonanie tego zadania. 

Typowym przykładem jest skonfigurowanie modułu [Importuj dane][reader] , aby użytkownik opublikowanej usługi sieci Web mógł określić inne źródło danych podczas uzyskiwania dostępu do usługi sieci Web. Lub skonfiguruj moduł [eksportu danych][writer] , aby można było określić inną lokalizację docelową. Niektóre inne przykłady obejmują zmianę liczby bitów dla modułu [wyznaczania wartości skrótu funkcji][feature-hashing] lub liczby pożądanych funkcji modułu [wyboru funkcji opartych na filtrach][filter-based-feature-selection] . 

Można ustawić parametry usługi sieci Web i skojarzyć je z co najmniej jeden parametr modułu w eksperymencie. Ponadto można określić, czy są one wymagane lub opcjonalne. Użytkownik usługi sieci web może następnie udostępniać wartości dla tych parametrów, gdy będą wywoływać usługi sieci web. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Jak ustawić i korzystanie z parametrów usług sieci Web
Należy zdefiniować parametr usługi sieci Web, klikając ikonę obok parametrów dla modułu i wybierając polecenie "Ustaw jako parametr usługi sieci web". Tworzy nowy parametr usługi sieci Web i łączy ją do tego parametru modułu. Następnie podczas uzyskiwania dostępu do usługi sieci web użytkownik może określić wartość dla parametru usługi sieci Web i zostanie zastosowany do parametru modułu.

Po zdefiniowaniu parametr usługi sieci Web, jest dostępna dla każdego parametru modułu w eksperymencie. Jeśli zdefiniujesz parametr usługi sieci Web skojarzony z parametrem dla jednego modułu, można użyć tego samego parametru usługi sieci Web dla każdego modułu, tak długo, jak parametr oczekuje, że ten sam typ wartości. Na przykład jeśli parametr usługi sieci Web jest wartością liczbową, następnie go należy używać tylko dla parametrów modułu, które oczekują na wartość liczbową. Gdy użytkownik ustawia wartość dla parametru usługi sieci Web, zostaną zastosowane do wszystkich parametrów skojarzonego modułu.

Możesz zdecydować, czy można podać wartości domyślnej parametru usługi sieci Web. Jeśli to zrobisz, parametr jest opcjonalny w przypadku użytkowników usługi sieci web. Jeśli nie podano wartości domyślnej, następnie użytkownik musi wprowadzić wartość podczas uzyskiwania dostępu do usługi sieci web.

Dokumentacja interfejsu API usługi sieci web zawiera informacje o użytkownika usługi sieci web o tym, jak programowo określić parametr usługi sieci Web podczas uzyskiwania dostępu do usługi sieci web.

> [!NOTE]
> Dokumentacja interfejsu API dla klasycznej usługi sieci Web jest dostępna za pomocą linku **Strona pomocy interfejsu API** na **pulpicie nawigacyjnym** usługi sieci Web w Machine Learning Studio (klasyczny). Dokumentacja interfejsu API dla nowej usługi sieci Web jest dostępna za pomocą portalu [usług sieci web Azure Machine Learning](https://services.azureml.net/Quickstart) na stronach **użycia** i **interfejsu API struktury Swagger** dla usługi sieci Web.
> 
> 

## <a name="example"></a>Przykład
Załóżmy na przykład, że mamy eksperyment z modułem [danych eksportu][writer] , który wysyła informacje do usługi Azure Blob Storage. Zdefiniujemy parametr usługi sieci Web o nazwie "Blob: ścieżka" umożliwiająca użytkownika usługi sieci web zmienić ścieżkę do magazynu obiektów blob podczas uzyskiwania dostępu do usługi.

1. W Machine Learning Studio (klasyczny) kliknij moduł [Eksportuj dane][writer] , aby go wybrać. Jego właściwości są wyświetlane w okienku właściwości po prawej stronie obszaru roboczego eksperymentu.
2. Określanie typu magazynu:
   
   * W obszarze **określ miejsce docelowe danych**wybierz pozycję "Azure Blob Storage".
   * W obszarze **Określ typ uwierzytelniania**wybierz pozycję "konto".
   * Wprowadź informacje o koncie dla usługi Azure blob storage. 

3. Kliknij ikonę z prawej strony **ścieżki do obiektu BLOB rozpoczynającego się od parametru kontenera**. Wygląda ono następująco:
   
   ![Ikona parametr usługi sieci Web](./media/web-service-parameters/icon.png)
   
   Wybierz pozycję "Ustaw jako parametr usługi sieci web".
   
   Wpis jest dodawany w obszarze **Parametry usługi sieci Web** u dołu okienka właściwości o nazwie "ścieżka do obiektu BLOB rozpoczynającego się od kontenera". Jest to parametr usługi sieci Web, który jest teraz skojarzony z tym parametrem modułu [eksportowania danych][writer] .
4. Aby zmienić nazwę parametru usługi sieci Web, kliknij nazwę, wprowadź "Ścieżka obiektu BLOB" i naciśnij klawisz **Enter** . 
5. Aby podać wartość domyślną dla parametru usługi sieci Web, kliknij ikonę z prawej strony nazwy, wybierz opcję "Podaj wartość domyślną", wprowadź wartość (na przykład "container1/output1. csv") i naciśnij klawisz **Enter** .
   
   ![Parametr usługi sieci Web](./media/web-service-parameters/parameter.png)
6. Kliknij pozycję **Run** (Uruchom). 
7. Kliknij pozycję **Wdróż usługę sieci Web** i wybierz pozycję **Wdróż usługę sieci Web [klasyczny]** lub **Wdróż usługę sieci Web [New]** , aby wdrożyć usługę sieci Web.

> [!NOTE] 
> Aby wdrożyć nową usługę sieci web musi masz wystarczające uprawnienia w ramach subskrypcji, do której możesz wdrażanie usługi sieci web. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługą sieci Web przy użyciu portalu usług sieci web Azure Machine Learning](manage-new-webservice.md). 

Użytkownik usługi sieci Web może teraz określić nowe miejsce docelowe dla modułu [eksportu danych][writer] podczas uzyskiwania dostępu do usługi sieci Web.

## <a name="more-information"></a>Więcej informacji
Aby zapoznać się z bardziej szczegółowym przykładem, zobacz wpis [Parameters usługi sieci Web](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) w [blogu Machine Learning](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do usługi sieci Web Machine Learning, zobacz [jak korzystać z usługi sieci web Azure Machine Learning](consume-web-services.md).

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

