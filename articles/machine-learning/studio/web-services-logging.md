---
title: Rejestrowanie usługi sieci Web — Azure Machine Learning Studio (klasyczny) | Microsoft Docs
description: Dowiedz się, jak włączyć rejestrowanie dla usług sieci Web Machine Learning Studio (klasycznych). Rejestrowanie udostępnia dodatkowe informacje ułatwiające rozwiązywanie problemów z interfejsów API.
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
ms.openlocfilehash: 0b0dfeb6a19e2f6f24568de0b4712758d2b7ad4a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427398"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Włącz rejestrowanie dla usług sieci Web Azure Machine Learning Studio (klasycznych)
Ten dokument zawiera informacje na temat możliwości rejestrowania usług sieci Web Machine Learning Studio (klasycznych). Rejestrowanie zawiera dodatkowe informacje, po których tylko numer błędu i komunikat mogą pomóc w rozwiązywaniu problemów z wywołaniami do interfejsów API Machine Learning Studio (klasycznego).  

## <a name="how-to-enable-logging-for-a-web-service"></a>Jak włączyć rejestrowanie dla usługi sieci Web

Rejestrowanie można włączyć z poziomu portalu [usług sieci Web Azure Machine Learning Studio (klasycznego)](https://services.azureml.net) . 

1. Zaloguj się do portalu usług sieci Web Azure Machine Learning Studio (klasyczny) pod [https://services.azureml.net](https://services.azureml.net). W przypadku klasycznej usługi sieci Web można również przejść do portalu, klikając pozycję **nowe usługi sieci Web** na stronie usług sieci Web Machine Learning Studio (klasycznej) w programie Studio (klasyczny).

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
> W przypadku klasycznej usługi sieci Web pulpit nawigacyjny usług sieci Web w Machine Learning Studio (klasyczny) ma również przełącznik umożliwiający włączenie rejestrowania. Jednak ponieważ rejestrowania jest teraz zarządzana za pośrednictwem portalu usług sieci Web, należy włączyć rejestrowanie za pośrednictwem portalu, zgodnie z opisem w tym artykule. Jeśli włączono już rejestrowanie w programie Studio (klasyczne), w portalu usług sieci Web Wyłącz rejestrowanie i włącz je ponownie.


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

* batch  
* Ocena/żądań  
* Ocena/init  

