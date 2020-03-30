---
title: Parametry usługi sieci Web — usługa Azure Machine Learning Studio (klasyczna) | Dokumenty firmy Microsoft
description: Jak używać parametrów usługi azure machine learning sieci Web, aby zmodyfikować zachowanie modelu, gdy usługa sieci web jest dostępna.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204413"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>Korzystanie z parametrów usługi sieci Web usługi Azure Machine Learning Studio (klasycznej)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Usługa sieci web usługi Azure Machine Learning jest tworzona przez opublikowanie eksperymentu zawierającego moduły z konfigurowalnymi parametrami. W niektórych przypadkach można zmienić zachowanie modułu, gdy usługa sieci web jest uruchomiona. *Parametry usługi sieci Web* umożliwiają wykonać to zadanie. 

Typowym przykładem jest skonfigurowanie [modułu Importuj dane,][reader] aby użytkownik opublikowanej usługi sieci web mógł określić inne źródło danych podczas uzyskiwania dostępu do usługi sieci web. Lub konfigurowanie modułu [Eksportuj dane,][writer] tak aby można było określić inne miejsce docelowe. Inne przykłady obejmują zmianę liczby bitów dla modułu [Hashing funkcji][feature-hashing] lub liczby żądanych funkcji dla modułu wybór funkcji opartych na [filtrze.][filter-based-feature-selection] 

Można ustawić parametry usługi sieci Web i skojarzyć je z co najmniej jednym parametrem modułu w eksperymencie i określić, czy są one wymagane, czy opcjonalne. Użytkownik usługi sieci web może następnie podać wartości dla tych parametrów podczas wywoływania usługi sieci web. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Jak ustawić i używać parametrów usługi sieci Web
Parametr usługi sieci Web można zdefiniować, klikając ikonę obok parametru modułu i wybierając opcję "Ustaw jako parametr usługi sieci web". Spowoduje to utworzenie nowego parametru usługi sieci Web i połączenie go z tym parametrem modułu. Następnie, gdy usługa sieci web jest dostępna, użytkownik może określić wartość parametru usługi sieci Web i jest stosowany do parametru modułu.

Po zdefiniowaniu parametru usługi sieci Web jest on dostępny dla dowolnego innego parametru modułu w eksperymencie. W przypadku zdefiniowania parametru usługi sieci Web skojarzonego z parametrem dla jednego modułu można użyć tego samego parametru usługi sieci Web dla dowolnego innego modułu, o ile parametr oczekuje tego samego typu wartości. Na przykład jeśli parametr usługi sieci Web jest wartością liczbową, może być używany tylko dla parametrów modułu, które oczekują wartości liczbowej. Gdy użytkownik ustawi wartość parametru usługi sieci Web, zostanie on zastosowany do wszystkich skojarzonych parametrów modułu.

Można zdecydować, czy należy podać wartość domyślną parametru usługi sieci Web. Jeśli to zrobisz, parametr jest opcjonalny dla użytkownika usługi sieci web. Jeśli nie podasz wartości domyślnej, użytkownik musi wprowadzić wartość, gdy jest dostępny w usłudze sieci web.

Dokumentacja interfejsu API dla usługi sieci web zawiera informacje dla użytkownika usługi sieci web o tym, jak programowo określić parametr usługi sieci Web podczas uzyskiwania dostępu do usługi sieci web.

> [!NOTE]
> Dokumentacja interfejsu API dla klasycznej usługi sieci web jest dostarczana za pośrednictwem łącza **strony pomocy interfejsu API** w usłudze sieci Web **DASHBOARD** w usłudze Machine Learning Studio (klasyczny). Dokumentacja interfejsu API dla nowej usługi sieci web jest dostarczana za pośrednictwem portalu [usług Azure Machine Learning Web Services](https://services.azureml.net/Quickstart) na stronach interfejsu API **zużywają** i **swagger** dla usługi sieci web.
> 
> 

## <a name="example"></a>Przykład
Na przykład załóżmy, że mamy eksperyment z modułem [Eksportuj dane,][writer] który wysyła informacje do magazynu obiektów blob platformy Azure. Zdefiniujemy parametr usługi sieci Web o nazwie "Ścieżka obiektu Blob", który umożliwia użytkownikowi usługi sieci web zmianę ścieżki do magazynu obiektów blob, gdy usługa jest dostępna.

1. W udiocielka (klasyczny) kliknij moduł [Eksportuj dane,][writer] aby go wybrać. Jego właściwości są wyświetlane w właściwości okienka po prawej stronie kanwy eksperymentu.
2. Określ typ magazynu:
   
   * W obszarze **Proszę określić miejsce docelowe danych**wybierz "Usługa Azure Blob Storage".
   * W **obszarze Proszę określić typ uwierzytelniania**wybierz "Konto".
   * Wprowadź informacje o koncie magazynu obiektów blob platformy Azure. 

3. Kliknij ikonę po prawej stronie path to **blob począwszy od parametru kontenera**. Wygląda to w ten sposób:
   
   ![Ikona parametru usługi sieci Web](./media/web-service-parameters/icon.png)
   
   Wybierz opcję "Ustaw jako parametr usługi sieci web".
   
   Wpis jest dodawany w obszarze **Parametry usługi sieci Web** u dołu okienka Właściwości o nazwie "Ścieżka do obiektu blob począwszy od kontenera". Jest to parametr usługi sieci Web, który jest teraz skojarzony z tym [parametrem modułu Eksportuj dane.][writer]
4. Aby zmienić nazwę parametru usługi sieci Web, kliknij nazwę, wprowadź "Ścieżkę obiektu blob" i naciśnij klawisz **Enter.** 
5. Aby podać wartość domyślną parametru usługi sieci Web, kliknij ikonę po prawej stronie nazwy, wybierz "Podaj wartość domyślną", wprowadź wartość (na przykład "container1/output1.csv") i naciśnij klawisz **Enter.**
   
   ![Parametr usługi sieci Web](./media/web-service-parameters/parameter.png)
6. Kliknij **przycisk Uruchom**. 
7. Kliknij **pozycję Wdrażanie usługi sieci Web** i wybierz pozycję **Wdrażanie usługi sieci Web [Klasyczny]** lub **Wdrażanie usługi sieci Web [Nowy],** aby wdrożyć usługę sieci Web.

> [!NOTE] 
> Aby wdrożyć nową usługę sieci web, musisz mieć wystarczające uprawnienia w subskrypcji, do której wdrażasz usługę sieci web. Aby uzyskać więcej informacji, zobacz [Zarządzanie usługą sieci Web przy użyciu portalu usług azure machine learning web .](manage-new-webservice.md) 

Użytkownik usługi sieci web może teraz określić nowe miejsce docelowe dla modułu [Eksportuj dane][writer] podczas uzyskiwania dostępu do usługi sieci web.

## <a name="more-information"></a>Więcej informacji
Aby uzyskać bardziej szczegółowy przykład, zobacz wpis [Parametry usługi sieci Web](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) w blogu uczenia [maszynowego](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do usługi sieci Web usługi uczenia maszynowego, zobacz [Jak korzystać z usługi azure machine learning sieci Web](consume-web-services.md).

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

