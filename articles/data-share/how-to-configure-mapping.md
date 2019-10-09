---
title: Konfigurowanie mapowania zestawu danych w wersji zapoznawczej usługi Azure Data Share
description: Informacje o konfigurowaniu mapowania zestawu danych dla odebranego udziału przy użyciu usługi Azure Data Share Preview.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 035235116240bdc6de3bc689c2430fee018b202d
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169137"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share-preview"></a>Jak skonfigurować mapowanie zestawu danych dla odebranego udziału w wersji zapoznawczej udziału danych platformy Azure

W tym artykule opisano sposób konfigurowania mapowania zestawu danych dla odebranego udziału przy użyciu usługi Azure Data Share Preview. Należy to zrobić, Jeśli zaakceptujesz zaproszenie do udziału danych, ale wybrano opcję "Akceptuj i skonfiguruj później". W przeciwnym razie możesz po prostu chcieć zmienić docelowe konto magazynu dla odebranych danych. 

## <a name="navigate-to-a-received-data-share"></a>Przejdź do odebranego udziału danych

W usłudze udział danych platformy Azure przejdź do otrzymanego udziału i wybierz kartę **szczegóły** . 

(./media/dataset-mapping.png "Mapowanie zestawu danych") ![mapowania zestawu danych] 

Zaznacz pole wyboru obok zestawu danych, do którego chcesz przypisać miejsce docelowe, a następnie kliknij pozycję **+ Mapuj, aby określić element docelowy**. Może być konieczne odwzorowanie najpierw, jeśli skonfigurowano już docelowe konto magazynu i chcesz zmienić mapowanie na inne konto magazynu. 

![Mapuj na mapowanie docelowe](./media/dataset-map-target.png "do") celu 

## <a name="select-a-new-storage-account"></a>Wybierz nowe konto magazynu 

Wybierz konto magazynu, w którym chcesz umieścić dane. Należy pamiętać, że wszystkie dane, które już istnieją na żadnym wcześniej mapowanym koncie magazynu, nie będą automatycznie przenoszone na nowe konto magazynu.

![Docelowy](./media/map-target.png "magazyn docelowy") konta magazynu 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zacząć udostępniać dane, przejdź do samouczka [udostępnianie danych](share-your-data.md) .



