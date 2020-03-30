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
ms.openlocfilehash: e3cb977871af2e6cd7a59dd48505090dd29e8a76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75541805"
---
1. Zaloguj się do [studia usługi Azure Machine Learning](https://ml.azure.com).

1. Uaktualnij obszar roboczy do wersji Enterprise.

    Po uaktualnieniu wszystkie eksperymenty interfejsu wizualnego zostaną przekonwertowane na wersje robocze potoku w projektancie.
    
    > [!NOTE]
    > Nie trzeba uaktualniać do wersji Enterprise, aby przekonwertować usługi sieci web interfejsu wizualnego do punktów końcowych w czasie rzeczywistym.
    
1. Przejdź do sekcji projektanta obszaru roboczego, aby wyświetlić listę wersji roboczych potoku. 
    
    Przekonwertowane usługi internetowe można znaleźć, przechodząc do **punktów końcowych w** > **czasie rzeczywistym.**

1. Wybierz projekt potoku, aby ją otworzyć.

    Jeśli wystąpił błąd podczas procesu konwersji, pojawi się komunikat o błędzie z instrukcjami, aby rozwiązać problem. 

### <a name="known-issues"></a>Znane problemy

 Poniżej znajdują się znane problemy związane z migracją, które należy rozwiązać ręcznie:

- **Moduły Importuj dane** lub **Eksportuj dane**
        
    Jeśli masz **importu danych** lub **eksportu danych** modułu w eksperymencie, należy zaktualizować źródło danych, aby użyć magazynu danych. Aby dowiedzieć się, jak utworzyć magazyn danych, zobacz [Jak uzyskać dostęp do danych w usługach magazynu platformy Azure](../articles/machine-learning/how-to-access-data.md). Informacje o koncie magazynu w chmurze zostały dodane w komentarzach modułu **Importuj dane** lub **Eksportuj dane** dla Twojej wygody. 
      