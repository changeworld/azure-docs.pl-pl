---
title: Konfigurowanie mapowania zestawu danych
description: Konfigurowanie mapowania zestawu danych
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 581e1eef5f1d64e68a6501f56ce60218281c605d
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789243"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share-preview"></a>Jak skonfigurować mapowanie zestawu danych odebranych udziału w udostępnianie danych platformy Azure w wersji zapoznawczej

W tym artykule wyjaśniono, jak skonfigurować mapowanie zestawu danych dla udziału odebranych przy użyciu udziału danych platformy Azure w wersji zapoznawczej. Należy to zrobić, jeśli zaakceptować zaproszenie udostępniania danych, ale postanowił wybrać "Zaakceptuj i skonfigurować później". W przeciwnym razie może po prostu chcesz zmienić docelowe konto magazynu dla odebranych danych. 

## <a name="navigate-to-a-received-data-share"></a>Przejdź do udziału odebranych danych

W usłudze udostępnianie danych platformy Azure, przejdź do udziału odebranych i wybierz **szczegóły** kartę. 

![Mapowanie zestawu danych](./media/dataset-mapping.png "mapowanie zestawu danych") 

Zaznacz pole obok zestawu danych, o których chcesz przypisać do miejsca docelowego, a następnie kliknij przycisk **+ mapy do obiektu docelowego**. Może być konieczne mapowania pierwsze, jeśli zostały już skonfigurowane docelowe konto magazynu i chcesz zmienić mapowanie do innego konta magazynu. 

![Mapuj do docelowych](./media/dataset-map-target.png "mapy do obiektu docelowego") 

## <a name="select-a-new-storage-account"></a>Wybierz nowe konto magazynu 

Wybierz konto magazynu, chcieliby znajdą się w danych. Należy pamiętać, że wszelkie dane, które już istnieje w żadnym wcześniej mapowane kont magazynu nie zostaną automatycznie przeniesione do nowego konta magazynu.

![Docelowe konto magazynu](./media/map-target.png "docelowy magazyn") 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak uruchomić, udostępnianie danych, w dalszym ciągu [udostępnianie danych](share-your-data.md) samouczka.



