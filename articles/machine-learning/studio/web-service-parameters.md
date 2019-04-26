---
title: Parametry usługi — Azure Machine Learning Studio w sieci Web | Dokumentacja firmy Microsoft
description: Jak używać parametry usługi sieci Web do programu Azure Machine Learning, aby zmodyfikować zachowanie modelu podczas uzyskiwania dostępu do usługi sieci web.
services: machine-learning
documentationcenter: ''
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/12/2017
ms.openlocfilehash: a236043d5622e5a2e1ffd572c887fb5ffac2174a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60345451"
---
# <a name="use-azure-machine-learning-studio-web-service-parameters"></a>Korzystanie z parametrów usług sieci web Azure Machine Learning Studio
Usługi sieci web Azure Machine Learning jest tworzony przez opublikowanie eksperyment, który zawiera moduły z konfigurowalne parametry. W niektórych przypadkach można zmienić zachowanie modułu, gdy jest uruchomiona usługa sieci web. *Parametry usługi w sieci Web* pozwalają wykonać to zadanie. 

Typowym przykładem jest konfigurowanie [importu danych] [ reader] modułu, aby określić użytkownika opublikowanej usługi sieci web z innym źródłem danych podczas uzyskiwania dostępu do usługi sieci web. Lub konfigurowanie [Eksport danych] [ writer] modułu, aby można określić inną lokalizację docelową. Niektóre inne przykłady Zmienianie liczby bitów dla [Tworzenie skrótu funkcji] [ feature-hashing] modułu lub liczba żądane funkcje [na podstawie filtru wybór funkcji] [ filter-based-feature-selection] modułu. 

Można ustawić parametry usługi sieci Web i skojarzyć je z co najmniej jeden parametr modułu w eksperymencie. Ponadto można określić, czy są one wymagane lub opcjonalne. Użytkownik usługi sieci web może następnie udostępniać wartości dla tych parametrów, gdy będą wywoływać usługi sieci web. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Jak ustawić i korzystanie z parametrów usług sieci Web
Należy zdefiniować parametr usługi sieci Web, klikając ikonę obok parametrów dla modułu i wybierając polecenie "Ustaw jako parametr usługi sieci web". Tworzy nowy parametr usługi sieci Web i łączy ją do tego parametru modułu. Następnie podczas uzyskiwania dostępu do usługi sieci web użytkownik może określić wartość dla parametru usługi sieci Web i zostanie zastosowany do parametru modułu.

Po zdefiniowaniu parametr usługi sieci Web, jest dostępna dla każdego parametru modułu w eksperymencie. Jeśli zdefiniujesz parametr usługi sieci Web skojarzony z parametrem dla jednego modułu, można użyć tego samego parametru usługi sieci Web dla każdego modułu, tak długo, jak parametr oczekuje, że ten sam typ wartości. Na przykład jeśli parametr usługi sieci Web jest wartością liczbową, następnie go należy używać tylko dla parametrów modułu, które oczekują na wartość liczbową. Gdy użytkownik ustawia wartość dla parametru usługi sieci Web, zostaną zastosowane do wszystkich parametrów skojarzonego modułu.

Możesz zdecydować, czy można podać wartości domyślnej parametru usługi sieci Web. Jeśli to zrobisz, parametr jest opcjonalny w przypadku użytkowników usługi sieci web. Jeśli nie podano wartości domyślnej, następnie użytkownik musi wprowadzić wartość podczas uzyskiwania dostępu do usługi sieci web.

Dokumentacja interfejsu API usługi sieci web zawiera informacje o użytkownika usługi sieci web o tym, jak programowo określić parametr usługi sieci Web podczas uzyskiwania dostępu do usługi sieci web.

> [!NOTE]
> Klasyczna usługa sieci web można znaleźć w dokumentacji interfejsu API jest oferowana w ramach **strona pomocy interfejsu API** linku w usłudze web **pulpit NAWIGACYJNY** w usłudze Machine Learning Studio. Dokumentacja interfejsu API dla nowej usługi sieci web jest oferowana w ramach [usług sieci Web Azure Machine Learning](https://services.azureml.net/Quickstart) portalu na **zużywania** i **interfejsu API struktury Swagger** stron sieci Web Usługa.
> 
> 

## <a name="example"></a>Przykład
Na przykład załóżmy, że mamy eksperyment za pomocą [Eksport danych] [ writer] moduł, który wysyła informacje do usługi Azure blob storage. Zdefiniujemy parametr usługi sieci Web o nazwie "Blob: ścieżka" umożliwiająca użytkownika usługi sieci web zmienić ścieżkę do magazynu obiektów blob podczas uzyskiwania dostępu do usługi.

1. W usłudze Machine Learning Studio, kliknij przycisk [Eksport danych] [ writer] modułu, aby go zaznaczyć. Jego właściwości są wyświetlane w okienku właściwości po prawej stronie obszaru roboczego eksperymentu.
2. Określanie typu magazynu:
   
   * W obszarze **Określ docelową lokalizację danych**, wybierz pozycję "Azure Blob Storage".
   * W obszarze **Określ typ uwierzytelniania**, wybierz pozycję "Konto".
   * Wprowadź informacje o koncie dla usługi Azure blob storage. 

3. Kliknij ikonę, aby po prawej stronie **ścieżki do obiektu blob zaczynający się od parametru kontenera**. Wygląda ono następująco:
   
   ![Ikona parametr usługi sieci Web](./media/web-service-parameters/icon.png)
   
   Wybierz pozycję "Ustaw jako parametr usługi sieci web".
   
   Wpis zostanie dodany w obszarze **parametry usługi sieci Web** w dolnej części okienka właściwości o nazwie "Ścieżka do obiektu blob zaczynający się od kontenera". Jest to parametr usługi sieci Web, która jest teraz skojarzony z tym [Eksport danych] [ writer] parametru modułu.
4. Zmień nazwę parametru usługi sieci Web, kliknij nazwę, wpisz "Ścieżka obiektu Blob" i naciśnij klawisz **Enter** klucza. 
5. Podaj wartość domyślną dla parametru usługi sieci Web, kliknij ikonę z prawej strony nazwy, wybierz pozycję "Dostarczaj wartości domyślnej", wprowadź wartość (na przykład "container1/output1.csv") i naciśnij klawisz **Enter** klucza.
   
   ![Parametr usługi sieci Web](./media/web-service-parameters/parameter.png)
6. Kliknij pozycję **Run** (Uruchom). 
7. Kliknij przycisk **wdrażanie usługi sieci Web** i wybierz **wdrażanie usługi sieci Web [klasyczny]** lub **wdrażanie usługi sieci Web [New]** wdrażanie usługi sieci web.

> [!NOTE] 
> Aby wdrożyć nową usługę sieci web musi masz wystarczające uprawnienia w ramach subskrypcji, do której możesz wdrażanie usługi sieci web. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługą sieci Web przy użyciu portalu usług sieci Web Azure Machine Learning](manage-new-webservice.md). 

Użytkownik usługi sieci web może teraz określić nowe miejsce docelowe dla [Eksport danych] [ writer] modułu podczas uzyskiwania dostępu do usługi sieci web.

## <a name="more-information"></a>Więcej informacji
Aby uzyskać bardziej szczegółowym przykładem, zobacz [parametry usługi sieci Web](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) wpis [blogu dotyczącym uczenia maszynowego](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do usługi sieci web Machine Learning, zobacz [jak korzystanie z usługi Azure Machine Learning w sieci Web](consume-web-services.md).

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

