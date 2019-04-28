---
title: Wymagania dotyczące systemu Microsoft Azure Data Box | Dokumentacja firmy Microsoft
description: Informacje na temat wymagań dotyczących oprogramowania i sieci dla urządzenia Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 01/23/2019
ms.author: alkohli
ms.openlocfilehash: 7d52af9e3948f40936795efab5b6671c3f71007a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746934"
---
# <a name="azure-data-box-system-requirements"></a>Wymagania systemowe w usłudze Azure Data Box

W tym artykule opisano wymagania systemowe dla usługi Microsoft Azure Data Box i klientom połączenie do urządzenia Data Box. Zaleca się, że starannie Przejrzyj informacje przed wdrożeniem usługi Data Box i następnie wrócić do niego zgodnie z potrzebami podczas wdrażania i kolejna operacja.

Wymagania systemowe, obejmują:

* **Wymagania programowe dotyczące hostów połączenie do urządzenia Data Box** — w tym artykule opisano obsługiwanych platform, przeglądarki dla lokalnego Interfejsu w przeglądarce, klientów protokołu SMB i wszelkie dodatkowe wymagania dla hostów, które można połączyć do urządzenia Data Box.
* **Wymagania dotyczące sieci dla urządzenia Data Box** — informacje na temat wymagań sieciowych, aby umożliwić optymalne działanie urządzenia Data Box.


## <a name="software-requirements"></a>Wymagania dotyczące oprogramowania

Wymagania dotyczące oprogramowania zawierają informacje o obsługiwane systemy operacyjne, przeglądarki obsługiwane przez lokalnego Interfejsu w przeglądarce i klienci SMB.

### <a name="supported-operating-systems-for-clients"></a>Obsługiwane systemy operacyjne dla klientów

Poniżej przedstawiono listę obsługiwanych systemów operacyjnych dla operacji kopiowania danych za pomocą klientów podłączonych do urządzenia Data Box.

| **System operacyjny** | **Wersje** | 
| --- | --- | 
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 | 
| Windows |7, 8, 10 | 
|Linux    |         |

### <a name="supported-file-systems-for-linux-clients"></a>Systemy plików obsługiwane przez klientów systemu Linux

| **Protokoły** | **Wersje** | 
| --- | --- | 
| SMB |2.X lub nowszy |
| NFS | Wszystkie wersje, w tym 4.1|

### <a name="supported-storage-accounts"></a>Konta magazynu obsługiwane

Poniżej przedstawiono listę kont magazynu obsługiwanych typów magazynów dla urządzenia Data Box. Aby uzyskać pełną listę wszystkich różnych typów kont magazynu i ich pełne możliwości, zobacz [typy kont magazynu](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

| **Konto magazynu / obsługiwane typy magazynu** | **Blokowe obiekty blob** |**Obiekt blob typu Page*** |**Usługa pliki Azure** |**Uwagi**|
| --- | --- | -- | -- | -- |
| Standardowa klasycznego | Tak | Tak | Tak |
| Standardowa ogólnego przeznaczenia w wersji 1  | Tak | Tak | Tak | Obsługiwane są zarówno gorąca i chłodna.|
| Ogólnego przeznaczenia w wersji 1 — wersja Premium  |  | Tak| | |
| Standardowa ogólnego przeznaczenia w wersji 2  | Tak | Tak | Tak | Obsługiwane są zarówno gorąca i chłodna.|
| Ogólnego przeznaczenia w wersji 2 — wersja Premium  |  |Tak | | |
| Magazyn obiektów blob Standard |Tak | | |Obsługiwane są zarówno gorąca i chłodna. |

\* *-Danych przekazanych do stronicowych obiektów blob musi być 512 bajtów wyrównane, takie jak wirtualne dyski twarde.*

>[!NOTE]
> Usługa Azure Data Lake Storage Gen 2 konta nie są obsługiwane.


### <a name="supported-storage-types"></a>Obsługiwane typy

Poniżej przedstawiono listę typów magazynu obsługiwane urządzenia Data Box.

| **Format pliku** | **Uwagi** |
| --- | --- |
| Usługa Azure blokowych obiektów blob | |
| Obiekt blob typu page platformy Azure  | Dane powinny mieć 512 bajtów wyrównane.|
| Azure Files | |


### <a name="supported-web-browsers"></a>Obsługiwane przeglądarki sieci web

Poniżej przedstawiono listę przeglądarek sieci web obsługiwane w przypadku lokalnego Interfejsu w przeglądarce.

| **Przeglądarka** | **Wersje** | **Dodatkowe wymagania dotyczące/uwagi** |
| --- | --- | --- |
| Google Chrome |Najnowsza wersja |Przetestowane za pomocą przeglądarki Chrome|
| Microsoft Edge |Najnowsza wersja | |
| FireFox | Najnowsza wersja | Przetestowane za pomocą przeglądarki FireFox|
| Internet Explorer |Najnowsza wersja |Jeśli nie możesz się zarejestrować, sprawdź, czy pliki cookie i języka Javascript są włączone. Aby włączyć dostęp do interfejsu użytkownika, należy dodać adres IP urządzenia, aby **Akcje prywatności** tak, aby urządzenia mogą uzyskiwać dostęp do plików cookie. |


## <a name="networking-requirements"></a>Wymagania dotyczące sieci

Twoje centrum danych musi mieć dostęp do szybkiej sieci. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE. Jeśli połączenie 10 GbE nie jest dostępna, łącza 1 GbE danych może służyć do skopiowania danych, ale kopiowania, które ma wpływ szybkości.

## <a name="next-step"></a>Następny krok

* [Wdrażanie usługi Azure Data Box](data-box-deploy-ordered.md)

