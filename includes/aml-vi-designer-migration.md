---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: xiaoharper
ms.author: zhanxia
ms.date: 10/18/2019
ms.openlocfilehash: 08833f869a7838fe893b8e941072078f89033c2b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517854"
---
1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com).

1. Uaktualnij obszar roboczy do wersji Enterprise Edition.

    Po uaktualnieniu wszystkie eksperymenty interfejsu wizualizacji zostaną przekonwertowane na wersje robocze potokowe w projektancie.
    
    > [!NOTE]
    > Nie trzeba przeprowadzać uaktualnienia do wersji Enterprise, aby przekonwertować usługi internetowe interfejsu graficznego na punkty końcowe w czasie rzeczywistym.
    
1. Przejdź do sekcji Projektant obszaru roboczego, aby wyświetlić listę wersji roboczych potoku. 
    
    Przekonwertowane usługi sieci Web można znaleźć, przechodząc do **punktów końcowych** > **punkty końcowe w czasie rzeczywistym**.

1. Wybierz wersję roboczą potoku, aby ją otworzyć.

    Jeśli wystąpił błąd podczas procesu konwersji, zostanie wyświetlony komunikat o błędzie z instrukcjami dotyczącymi rozwiązania problemu. 

### <a name="known-issues"></a>Znane problemy

 Poniżej znajdują się znane problemy dotyczące migracji, które należy rozwiązać ręcznie:

- **Importowanie danych** lub **Eksportowanie modułów danych**
        
    Jeśli w tym eksperymentie masz moduł **Importuj dane** lub **Eksportuj dane** , musisz zaktualizować źródło danych tak, aby korzystało z magazynów danych. Aby dowiedzieć się, jak utworzyć magazyn danych, zapoznaj się z tematem [jak korzystać z usługi Azure Storage](../articles/machine-learning/service/how-to-access-data.md). Informacje o koncie magazynu w chmurze zostały dodane w komentarzach modułu **Importuj dane** lub **Eksportuj dane** dla wygody użytkownika. 
      