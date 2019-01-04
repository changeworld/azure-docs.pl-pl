---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: e2c8b4e74933df593dbc023dcb886dff80554a1a
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977370"
---
Ustawienia kontenera są hierarchiczne, a wszystkie kontenery na komputerze-hoście, użyj udostępnionego hierarchii.

Aby określić ustawienia, można użyć jednej z następujących pozycji:

* [Zmienne środowiskowe](#environment-variable-settings)
* [Argumenty wiersza polecenia](#command-line-argument-settings)

Wartości zmiennych środowiskowych zastępują wartości argumentów wiersza polecenia, które z kolei przesłonić wartości domyślne dla obrazu kontenera. Jeśli określisz różne wartości w zmiennej środowiskowej i argument wiersza polecenia dla tego samego ustawienia konfiguracji, wartość w zmiennej środowiskowej jest używany przez kontener wystąpień.

|Pierwszeństwo|Ustawianie lokalizacji|
|--|--|
|1|Zmienna środowiskowa| 
|2|Wiersz polecenia|
|3|Wartość domyślna obrazu kontenera|

### <a name="environment-variable-settings"></a>Ustawień zmiennych środowiskowych

Korzyści z używania zmiennych środowiskowych są:

* Można skonfigurować wiele ustawień.
* Wiele kontenerów, można użyć tych samych ustawień.

### <a name="command-line-argument-settings"></a>Ustawienia argument wiersza polecenia

Zaletą używania argumentów wiersza polecenia jest, że każdego kontenera za pomocą różnych ustawień.
