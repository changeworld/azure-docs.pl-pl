---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 367ae27a6afec803d2e3f98f54bdcf852330ddc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70274676"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Konfigurowanie zmiennej środowiskowej do uwierzytelniania

Aplikacje muszą uwierzytelniać dostęp do usług Cognitive Services, z których korzystają. Aby uwierzytelnić, zaleca się utworzenie zmiennej środowiskowej do przechowywania kluczy dla zasobów platformy Azure. 

Po naciśnięciu klawisza należy zapisać go na nowej zmiennej środowiskowej na komputerze lokalnym z uruchomieniem aplikacji. Aby ustawić zmienną środowiskową, otwórz okno konsoli i postępuj zgodnie z instrukcjami dla systemu operacyjnego. Zamień `your-key` na jeden z kluczy zasobu.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx COGNITIVE_SERVICE_KEY "your-key"
```

Po dodaniu zmiennej środowiskowej być może trzeba będzie ponownie uruchomić działające programy, które muszą odczytywać zmienną środowiskową, w tym okno konsoli. Na przykład jeśli używasz programu Visual Studio jako edytora, uruchom ponownie program Visual Studio przed uruchomieniem przykładu.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source ~/.bashrc` z okna konsoli, aby zmiany zostały uwzględnione.

#### <a name="macos"></a>[Macos](#tab/unix)

Edytuj swój plik .bash_profile i dodaj zmienną środowiskową:

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source .bash_profile` z okna konsoli, aby zmiany zostały uwzględnione.

***