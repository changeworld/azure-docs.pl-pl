---
title: Rejestrowanie dla usługi sieci web usługi Machine Learning | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć rejestrowanie dla usługi sieci web usługi Machine Learning. Rejestrowanie udostępnia dodatkowe informacje ułatwiające rozwiązywanie problemów z interfejsów API.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/15/2017
ms.openlocfilehash: 4e1545c8fd05795c683b24c029376a3d1e6d85b8
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835835"
---
# <a name="enable-logging-for-machine-learning-web-services"></a>Włączanie rejestrowania usług sieci Web Machine Learning
Ten dokument zawiera informacje o możliwości rejestrowania usług sieci web Machine Learning. Rejestrowanie udostępnia dodatkowe informacje, poza tylko numer błędu i wiadomości, które ułatwiają rozwiązywanie problemów z wywołaniami interfejsy API Machine Learning.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Jak włączyć rejestrowanie dla usługi sieci Web

Włączanie rejestrowania z [usługi sieci Web systemu Azure Machine Learning](https://services.azureml.net) portalu. 

1. Zaloguj się do portalu usługi sieci Web systemu Azure Machine Learning pod adresem [ https://services.azureml.net ](https://services.azureml.net). Usługi sieci web klasycznego, można także uzyskać portalu, klikając **nowego środowiska usług sieci Web** na stronie usługi sieci Web usługi Machine Learning w usłudze Machine Learning Studio.

   ![Nowe łącze środowisko usługi sieci Web](./media/web-services-logging/new-web-services-experience-link.png)

2. Na pasku menu u góry kliknij **usług sieci Web** dla nowej usługi sieci web, lub kliknij przycisk **usług sieci Web klasycznego** dla klasyczny usługi sieci web.

   ![Wybierz nowy lub Classic usługi sieci web](./media/web-services-logging/select-web-service.png)

3. Aby uzyskać nową usługę sieci web kliknij nazwę usługi sieci web. Usługi sieci web klasycznego kliknij nazwę usługi sieci web, a następnie w na następnej stronie kliknij odpowiednie punktu końcowego.

4. Na pasku menu u góry kliknij **Konfiguruj**.

5. Ustaw **Włącz rejestrowanie** opcji w celu *błąd* (aby rejestruje tylko błędy) lub *wszystkie* (Aby uzyskać pełne rejestrowanie).

   ![Wybierz poziom rejestrowania](./media/web-services-logging/enable-logging.png)

6. Kliknij pozycję **Zapisz**.

7. Dla usług sieci web klasycznego, należy utworzyć **diagnostyki ml** kontenera.

   Wszystkie dzienniki usługi sieci web są przechowywane w kontenerze obiektu blob o nazwie **diagnostyki ml** w ramach konta magazynu skojarzone z usługą sieci web. Dla nowej usługi sieci web ten kontener jest tworzony po raz pierwszy dostęp do usługi sieci web. Dla usług sieci web klasycznego należy utworzyć kontener, jeśli jeszcze nie istnieje. 

   1. W [portalu Azure](https://portal.azure.com), przejdź do konta magazynu skojarzone z usługą sieci web.

   2. W obszarze **usługa Blob**, kliknij przycisk **kontenery**.

   3. Jeśli kontener **diagnostyki ml** nie istnieje, kliknij przycisk **+ kontener**, nadaj nazwę "ml diagnostyki" kontenera i wybierz **dostęp typu** jako "Blob". Kliknij przycisk **OK**.

      ![Wybierz poziom rejestrowania](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Usługi sieci web klasycznego pulpitu nawigacyjnego usług sieci Web w usłudze Machine Learning Studio ma przełącznik, aby włączyć rejestrowanie. Jednak ponieważ rejestrowania będzie teraz zarządzana za pośrednictwem portalu usługi sieci Web, należy włączyć rejestrowanie za pośrednictwem portalu, zgodnie z opisem w tym artykule. Jeśli już włączone rejestrowanie w Studio, w portalu usługi sieci Web, należy wyłączyć rejestrowanie i włącz ją ponownie.


## <a name="the-effects-of-enabling-logging"></a>Efekty Włączanie rejestrowania
Po włączeniu rejestrowania diagnostyki i błędów z punktem końcowym usługi sieci web są rejestrowane w **diagnostyki ml** kontenera obiektów blob na koncie magazynu Azure połączone z obszarem roboczym użytkownika. Ten kontener zawiera wszystkie informacje diagnostyczne dla wszystkich sieci web usługi punkty końcowe dla wszystkich obszarów roboczych skojarzonych z tym kontem magazynu.

Dzienniki można wyświetlać przy użyciu dowolnego narzędzia kilka dostępne zapoznać się z konta magazynu platformy Azure. Najprostsza może być przejdź do konta magazynu w portalu Azure, kliknij przycisk **kontenery**, a następnie kliknij przycisk kontenera **diagnostyki ml**.  

## <a name="log-blob-detail-information"></a>Dziennik blob szczegółowych informacji
Każdy obiekt blob w kontenerze przechowuje informacje diagnostyczne dla dokładnie jeden z następujących czynności:

* Wykonywanie metody wykonywania wsadowego usługi  
* Wykonywanie metody żądań i odpowiedzi  
* Inicjowanie kontenera żądań i odpowiedzi

Nazwa każdego obiektu blob ma prefiks następującą postać: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Gdzie _typ dziennika_ jest jednym z następujących wartości:  

* partia  
* wynik/żądań  
* wynik/init  

