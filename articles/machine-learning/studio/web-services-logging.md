---
title: Rejestrowanie usługi - Azure Machine Learning Studio w sieci Web | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć rejestrowanie dla usług sieci web Machine Learning Studio. Rejestrowanie udostępnia dodatkowe informacje ułatwiające rozwiązywanie problemów z interfejsów API.
services: machine-learning
documentationcenter: ''
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.subservice: studio
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/15/2017
ms.openlocfilehash: 727379edb60756ca8cb3e5ebdc29cd38858945e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60345639"
---
# <a name="enable-logging-for-azure-machine-learning-studio-web-services"></a>Włączanie rejestrowania usług sieci web Azure Machine Learning Studio
Ten dokument zawiera informacje dotyczące możliwości rejestrowania usług sieci web Machine Learning Studio. Rejestrowanie udostępnia dodatkowe informacje, poza po prostu liczbą błędów i komunikatów, które mogą ułatwić rozwiązywanie problemów z wywołaniami interfejsy API usługi Machine Learning Studio.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Jak włączyć rejestrowanie dla usługi sieci Web

Włączanie rejestrowania z [usług sieci Web programu Azure Machine Learning Studio](https://services.azureml.net) portalu. 

1. Zaloguj się do portalu usług sieci Web programu Azure Machine Learning Studio na stronie [ https://services.azureml.net ](https://services.azureml.net). Dla klasycznej usługi sieci web, można także uzyskać do portalu, klikając **nowego środowiska usług sieci Web** na stronie usługi sieci Web programu Machine Learning Studio w usłudze Machine Learning Studio.

   ![Nowe łącze środowisko usług sieci Web](./media/web-services-logging/new-web-services-experience-link.png)

2. Na pasku menu u góry kliknij **usług sieci Web** dla nowej usługi sieci web, lub kliknij przycisk **klasycznych usług sieci Web** dla klasycznej usługi sieci web.

   ![Wybierz nowy lub klasyczne usługi sieci web](./media/web-services-logging/select-web-service.png)

3. Dla nowej usługi sieci web kliknij nazwę usługi sieci web. Dla klasycznej usługi sieci web kliknij nazwę usługi sieci web, a na następnej stronie kliknij pozycję odpowiednich punktów końcowych.

4. Na pasku menu u góry kliknij **Konfiguruj**.

5. Ustaw **Włącz rejestrowanie** opcję *błąd* (w celu rejestruje tylko błędy) lub *wszystkich* (Aby uzyskać pełne rejestrowanie).

   ![Wybierz poziom rejestrowania](./media/web-services-logging/enable-logging.png)

6. Kliknij pozycję **Zapisz**.

7. W przypadku klasycznych usług sieci web, utworzyć **diagnostyki ml** kontenera.

   Wszystkie dzienniki usługi sieci web są przechowywane w kontenerze obiektów blob o nazwie **diagnostyki ml** w ramach konta magazynu skojarzone z usługą sieci web. Dla nowych usług sieci web ten kontener jest tworzony po raz pierwszy możesz uzyskać dostęp do usługi sieci web. Klasyczne usługi sieci web należy utworzyć kontener, jeśli jeszcze nie istnieje. 

   1. W [witryny Azure portal](https://portal.azure.com), przejdź do konta magazynu skojarzone z usługą sieci web.

   2. W obszarze **Blob Service** kliknij pozycję **Kontenery**.

   3. Jeśli kontener **diagnostyki ml** nie istnieje, kliknij przycisk **+ kontener**, nadaj nazwę "ml — diagnostyki" kontenera i wybierz **dostęp typu** jako "Blob". Kliknij przycisk **OK**.

      ![Utwórz nowy kontener do przechowywania dzienników diagnostycznych](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Klasyczne usługi sieci web na pulpicie nawigacyjnym usługi sieci Web w usłudze Machine Learning Studio ma również przełącznika, aby włączyć rejestrowanie. Jednak ponieważ rejestrowania jest teraz zarządzana za pośrednictwem portalu usług sieci Web, należy włączyć rejestrowanie za pośrednictwem portalu, zgodnie z opisem w tym artykule. Jeśli jest jeszcze włączone rejestrowanie w Studio, w portalu usług sieci Web należy wyłączyć rejestrowanie i włącz ją ponownie.


## <a name="the-effects-of-enabling-logging"></a>Efekty Włączanie rejestrowania
Po włączeniu rejestrowania diagnostyki i błędy z punktu końcowego usługi sieci web są rejestrowane w **diagnostyki ml** kontenera obiektów blob na koncie magazynu platformy Azure połączonej z obszarem roboczym użytkownika. Ten kontener zawiera wszystkie informacje diagnostyczne dla wszystkich internetowych punktów końcowych usługi we wszystkich obszarach roboczych skojarzonych z tym kontem magazynu.

Dzienniki można wyświetlać przy użyciu dowolnego z kilku narzędzi można eksplorować konto usługi Azure Storage. Najprostsza może być przejdź do konta magazynu w witrynie Azure portal, kliknij przycisk **kontenery**, a następnie kliknij przycisk kontenera **diagnostyki ml**.  

## <a name="log-blob-detail-information"></a>Dziennik blob szczegółowe informacje
Każdy obiekt blob w kontenerze przechowuje informacje diagnostyczne dla dokładnie jeden z następujących czynności:

* Wykonywanie metody wykonywania wsadowego  
* Wykonywanie metody odpowiedzi na żądanie  
* Inicjowanie kontenera odpowiedź na żądanie

Nazwa każdego obiektu blob ma prefiksu o następującej postaci: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Gdzie _typ dziennika_ jest jednym z następujących wartości:  

* partia  
* Ocena/żądań  
* Ocena/init  

