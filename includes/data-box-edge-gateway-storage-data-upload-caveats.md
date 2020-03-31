---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183686"
---
Następujące zastrzeżenia dotyczą danych w miarę przenoszenia na platformę Azure.

- Sugerujemy, że więcej niż jedno urządzenie nie powinno zapisywać do tego samego kontenera.
- Jeśli masz istniejący obiekt platformy Azure (na przykład obiekt blob lub plik) w chmurze o takiej samej nazwie jak obiekt, który jest kopiowany, urządzenie zastąpi plik w chmurze.
- Hierarchia pustych katalogów (bez żadnych plików) utworzona w folderach udostępniania nie jest przekazywała do kontenerów obiektów blob.
- Dane można kopiować za pomocą przeciągania i upuszczania za pomocą Eksploratora plików lub za pomocą wiersza polecenia. Jeśli łączny rozmiar kopiowanych plików jest większy niż 10 GB, zalecamy użycie programu do kopiowania zbiorczego, takiego jak Robocopy lub rsync. Narzędzia kopiowania zbiorczego ponowić próbę operacji kopiowania dla błędów przerywanych i zapewniają dodatkową odporność.
- Jeśli udział skojarzony z kontenerem magazynu Platformy Azure przekaże obiekty blob, które nie pasują do typu obiektów blob zdefiniowanych dla udziału w momencie tworzenia, takie obiekty blob nie są aktualizowane. Na przykład można utworzyć blokowy udział obiektu blob na urządzeniu. Skojarz udział z istniejącym kontenerem chmury, który ma obiekty blob strony. Odśwież ten udział, aby pobrać pliki. Zmodyfikuj niektóre odświeżone pliki, które są już przechowywane jako obiekty blob stron w chmurze. Zostaną wyświetlane błędy przesyłania.
