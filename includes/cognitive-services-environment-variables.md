---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 734fce2c01614d5dca73f171fb59f25f39d13705
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461501"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Konfigurowanie zmiennej środowiskowej uwierzytelniania

Aplikacje muszą uwierzytelnić dostęp do usług Cognitive Services używają. Na potrzeby uwierzytelniania, zaleca się utworzenie zmiennej środowiskowej, aby przechowywać klucz w zasoby platformy Azure. 

Po umieszczeniu klucza, zapisz je w nowej zmiennej środowiskowej na komputerze lokalnym, na którym uruchomiona jest aplikacja. Aby ustawić zmienną środowiskową, otwórz okno konsoli i postępuj zgodnie z instrukcjami dla systemu operacyjnego. Zastąp `your-key` swoim kluczem dostępu wykrywanie anomalii:

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
