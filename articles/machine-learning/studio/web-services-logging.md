---
title: Rejestrowanie usługi sieci Web — usługa Azure Machine Learning Studio (klasyczna) | Dokumenty firmy Microsoft
description: Dowiedz się, jak włączyć rejestrowanie dla usług sieci Web Machine Learning Studio (klasycznych). Rejestrowanie zawiera dodatkowe informacje ułatwiające rozwiązywanie problemów z interfejsami API.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
ms.date: 06/15/2017
ms.openlocfilehash: 90e7692fe0e254074d8176d719d0ca9abad54a9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217844"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Włączanie rejestrowania dla usług sieci Web usługi Azure Machine Learning Studio (klasyczne)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Ten dokument zawiera informacje na temat możliwości rejestrowania usług sieci web Machine Learning Studio (klasyczne). Rejestrowanie zawiera dodatkowe informacje, poza tylko numer błędu i komunikat, które mogą pomóc w rozwiązywaniu problemów z wywołaniami do machine learning studio (klasyczne) interfejsy API.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Jak włączyć rejestrowanie usługi sieci Web

Rejestrowanie można włączyć z [portalu usług Azure Machine Learning Studio (klasyczny) web services.](https://services.azureml.net) 

1. Zaloguj się do portalu usług Azure Machine Learning [https://services.azureml.net](https://services.azureml.net)Studio (klasyczny) w sieci Web w . W przypadku klasycznej usługi sieci web można również uzyskać informacje o portalu, klikając pozycję **Nowe środowisko usług sieci Web** na stronie Usługi sieci Web Machine Learning Studio (klasyczne) w Studio (klasyczny).

   ![Łącze Nowe środowisko usług sieci Web](./media/web-services-logging/new-web-services-experience-link.png)

2. Na górnym pasku menu kliknij pozycję **Usługi sieci Web** dla nowej usługi sieci web lub kliknij pozycję **Klasyczne usługi sieci Web** dla klasycznej usługi sieci web.

   ![Wybieranie nowych lub klasycznych usług sieci web](./media/web-services-logging/select-web-service.png)

3. W przypadku nowej usługi sieci web kliknij nazwę usługi sieci web. W przypadku klasycznej usługi sieci web kliknij nazwę usługi sieci web, a następnie na następnej stronie kliknij odpowiedni punkt końcowy.

4. Na górnym pasku menu kliknij pozycję **Konfiguruj**.

5. Ustaw opcję **Włącz rejestrowanie** na *Błąd* (aby rejestrować tylko błędy) lub *Wszystkie* (dla pełnego rejestrowania).

   ![Wybierz poziom rejestrowania](./media/web-services-logging/enable-logging.png)

6. Kliknij przycisk **Zapisz**.

7. W przypadku klasycznych usług sieci web utwórz kontener **diagnostyki ml.**

   Wszystkie dzienniki usług sieci web są przechowywane w kontenerze obiektów blob o nazwie **diagnostyka ml** na koncie magazynu skojarzonym z usługą sieci web. W przypadku nowych usług sieci web ten kontener jest tworzony przy pierwszym dostępie do usługi sieci web. W przypadku klasycznych usług sieci web należy utworzyć kontener, jeśli jeszcze nie istnieje. 

   1. W [witrynie Azure portal](https://portal.azure.com)przejdź do konta magazynu skojarzonego z usługą sieci web.

   2. W obszarze **Blob Service** kliknij pozycję **Kontenery**.

   3. Jeśli kontener **ml-diagnostyka** nie istnieje, kliknij **+Kontener**, nadaj kontenerowi nazwę "ml-diagnostics" i wybierz **typ dostępu** jako "Obiekt blob". Kliknij przycisk **OK**.

      ![Tworzenie nowego kontenera do przechowywania dzienników diagnostycznych](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> W przypadku klasycznej usługi sieci web pulpit nawigacyjny usług sieci Web w programie Machine Learning Studio (klasyczny) ma również przełącznik umożliwiający rejestrowanie. Jednak ponieważ rejestrowanie jest teraz zarządzane za pośrednictwem portalu usług sieci Web, należy włączyć rejestrowanie za pośrednictwem portalu, jak opisano w tym artykule. Jeśli logowanie w Studio (klasyczne) jest już włączone, a następnie w portalu usług sieci Web wyłącz rejestrowanie i włącz je ponownie.


## <a name="the-effects-of-enabling-logging"></a>Skutki umożliwienia rejestrowania
Gdy rejestrowanie jest włączone, diagnostyki i błędy z punktu końcowego usługi sieci web są rejestrowane w kontenerze obiektu blob **diagnostyki ml** w koncie usługi Azure Storage połączone z obszaru roboczego użytkownika. Ten kontener zawiera wszystkie informacje diagnostyczne dla wszystkich punktów końcowych usługi sieci web dla wszystkich obszarów roboczych skojarzonych z tym kontem magazynu.

Dzienniki można przeglądać przy użyciu dowolnego z kilku narzędzi dostępnych do eksplorowania konta usługi Azure Storage. Najłatwiejsza może być przejście do konta magazynu w witrynie Azure portal, kliknięcie pozycji **Kontenery**, a następnie **kliknięcie diagnostyki ml kontenera**.  

## <a name="log-blob-detail-information"></a>Szczegółowe informacje o obiektach obiektów blob dziennika
Każdy obiekt blob w kontenerze zawiera informacje diagnostyczne dla dokładnie jednej z następujących akcji:

* Wykonanie metody wsadowego-wykonywania  
* Wykonanie metody Request-Response  
* Inicjowanie kontenera żądanie-odpowiedź

Nazwa każdego obiektu blob ma prefiks następującego formularza: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Gdzie _typ dziennika_ jest jedną z następujących wartości:  

* partia  
* wynik/wnioski  
* wynik/init  

