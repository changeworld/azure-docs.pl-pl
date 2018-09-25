---
title: Informacje o wersji Preivew bramy pola danych platformy Azure | Dokumentacja firmy Microsoft
description: Opisuje krytyczne nierozwiązane problemy i rozwiązania dla usługi Azure Gateway pole danych, systemem w wersji zapoznawczej.
services: databox-edge-gateway
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 2668279b33d2d8d1c0adf92c138cc6347c95bd4d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035355"
---
# <a name="azure-data-box-gateway-preview-release-notes"></a>Informacje o wersji usługi Azure Data Box — wersja zapoznawcza bramy

## <a name="overview"></a>Przegląd

W poniższych informacjach o Zidentyfikuj krytyczne nierozwiązane problemy i rozwiązane problemy w wersji zestawu pole bramy pakietu Microsoft Azure danych w wersji zapoznawczej.

Informacje o wersji są ciągle aktualizowane, a ponieważ są wszystkie wykrywane krytyczne problemy wymagające obejścia są dodawane. Przed wdrożeniem bramy pola danych, należy uważnie przeczytaj informacje zawarte w informacjach o wersji.

W wersji zapoznawczej odnosi się do wersji oprogramowania **dane pole bramy (wersja zapoznawcza) wersja 2.0**.

## <a name="issues-fixed-in-preview-release"></a>Problemy rozwiązane w wersji zapoznawczej

Poniższa tabela zawiera podsumowanie problemów rozwiązanych w tej wersji.

| Nie. | Problem |
| --- | --- |
| 1 | W tej wersji, gdy pliku, który został przekazany przez inne narzędzie (AzCopy) jest odświeżane, a następnie są aktualizowane w sposób zwiększa/rozszerzający rozmiar pliku, a następnie obserwuje się następujący błąd: *błąd 400: InvalidBlobOrBlock (do określonego obiektu blob lub bloku zawartość jest nieprawidłowa.)*|


## <a name="known-issues-in-preview-release"></a>Znane problemy w wersji zapoznawczej

Poniższa tabela zawiera podsumowanie znanych problemów występujących dla bramy pola danych, systemem w wersji zapoznawczej.

| Nie. | Cecha | Problem | Obejście/komentarzy |
| --- | --- | --- | --- |
| **1.** |Aktualizacje |Urządzenia bramy pola danych, utworzony w starszej wersji zapoznawczej, nie można zaktualizować wersji do tej wersji. |Pobieranie obrazów dysku wirtualnego z nowej wersji i konfigurowanie i wdrażanie nowych urządzeń. Aby uzyskać więcej informacji, przejdź do [przygotowanie do wdrożenia usługi Azure Gateway pole danych](data-box-gateway-deploy-prep.md). |
| **2.** |Dysk aprowizowane dane |Po aprowizowaniu dysk z danymi o określonym rozmiarze określonym i utworzyć odpowiednią bramę pole danych, należy nie zmniejszania dysk z danymi. Podjęto próbę zmniejszania wyniki dysku spowoduje utratę wszystkich danych lokalnych na urządzeniu. | |
| **3.** |Odświeżanie |W tej wersji można odświeżyć tylko jeden udział w danym momencie. | |
| **4.** |Zmiana nazwy |Zmiana nazwy obiektów nie jest obsługiwana. |Jeśli ta funkcja ma kluczowe znaczenie podczas przepływu pracy, skontaktuj się z Microsoft Support. |
| **5.** |Copy| Jeśli plik tylko do odczytu jest kopiowany do urządzenia, właściwości tylko do odczytu nie są zachowywane. | |
| **6.** |Dzienniki| Z powodu błędu w tej wersji, może zostać wyświetlony wystąpień kod błędu: 110 w *error.xml* z nazwami nierozpoznany element. | |
| **7.** |Upload | Z powodu błędu w tej wersji może zostać wyświetlony wystąpień 2003. kod błędu w trakcie przekazywania określonych plików. | |
| **8.** |Typy plików | Nie są obsługiwane następujące typy plików systemu Linux: znak plików bloku, gniazda, potoki, plików łącza symbolicznego.  |Kopiowanie tych plików wyników w plikach długość 0 tworzonych na systemu plików NFS jest udostępnianie. Te pliki pozostają w stanie błędu i są również zgłaszane w *error.xml*. |
| **9.** |Usuwanie | Z powodu błędu w tej wersji usunięcie udziału NFS następnie udział mogą nie zostać usunięte. Będzie wyświetlany stan udziału *usuwanie*.  |Dzieje się tak, tylko wtedy, gdy udział jest przy użyciu nazwy pliku nieobsługiwany. |
| **10.** |Odświeżanie | Uprawnienia i listy kontroli dostępu (ACL) nie są zachowywane w ramach operacji odświeżania.  | |
| **11.** |Pomoc online |Linki pomocy w witrynie Azure portal nie może połączyć się z dokumentacją.|Linki pomocy będzie działać w wersji ogólnodostępnej. |



## <a name="next-steps"></a>Kolejne kroki

- [Przygotowanie do wdrożenia usługi Azure Gateway pole danych](data-box-gateway-deploy-prep.md).


