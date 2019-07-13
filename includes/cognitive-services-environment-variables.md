---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 6d6451d50a00569eb1da8f5b0a0dc10d3c6b1115
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841425"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Konfigurowanie zmiennej środowiskowej uwierzytelniania

Aplikacje muszą uwierzytelnić dostęp do usług Cognitive Services używają. Na potrzeby uwierzytelniania, zaleca się utworzenie zmiennej środowiskowej, aby klucze były przechowywane dla zasobów platformy Azure. 

Po umieszczeniu klucza, zapisz je w nowej zmiennej środowiskowej na komputerze lokalnym, na którym uruchomiona jest aplikacja. Aby ustawić zmienną środowiskową, otwórz okno konsoli i postępuj zgodnie z instrukcjami dla systemu operacyjnego. Zastąp `your-key` przy użyciu jednego z kluczy zasobu bazy danych.

* Windows

    ```console
    setx COGNITIVE_SERVICE_KEY "your-key"
    ```

    Po dodaniu zmiennej środowiskowej być może trzeba będzie ponownie uruchomić działające programy, które muszą odczytywać zmienną środowiskową, w tym okno konsoli. Na przykład jeśli używasz programu Visual Studio jako edytora, uruchom ponownie program Visual Studio przed uruchomieniem przykładu.

* Linux
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    Po dodaniu zmiennej środowiskowej uruchom polecenie `source ~/.bashrc` z okna konsoli, aby zmiany zostały uwzględnione.
    
* macOS
    
    Edytuj swój plik .bash_profile i dodaj zmienną środowiskową:
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    Po dodaniu zmiennej środowiskowej uruchom polecenie `source .bash_profile` z okna konsoli, aby zmiany zostały uwzględnione.
