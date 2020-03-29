---
title: Konfigurowanie mapowania zestawu danych w udziale danych platformy Azure
description: Dowiedz się, jak skonfigurować mapowanie zestawu danych dla odebranego udziału przy użyciu usługi Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 1ff432c2073536448c8194ebe537c8bf8cf00663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964247"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Jak skonfigurować mapowanie zestawu danych dla odebranego udziału w udziale danych platformy Azure

W tym artykule wyjaśniono, jak skonfigurować mapowanie zestawu danych dla odebranego udziału przy użyciu udziału danych platformy Azure. Należy to zrobić, jeśli zaakceptowano zaproszenie do udostępniania danych, ale zdecydowałeś się na "Zaakceptuj i skonfiguruj później", lub jeśli dane są udostępniane w miejscu. Można skonfigurować mapowanie zestawu danych, jeśli trzeba zmienić miejsce docelowe dla danych udostępnianych z Tobą lub jeśli chcesz odbierać dane do programu SQL Server. 

## <a name="navigate-to-a-received-data-share"></a>Przechodzenie do odebranego udziału danych

W usłudze Azure Data Share przejdź do odebranego udziału i wybierz kartę **Szczegóły.** 

![Mapowanie zestawu danych](./media/dataset-mapping.png "Mapowanie zestawu danych") 

Zaznacz pole obok zestawu danych, do którego chcesz przypisać miejsce docelowe. Wybierz **opcję Odłóż mapowanie,** aby odmapować istniejące mapowanie. Wybierz **+ Mapa do celu,** aby wybrać nowy sklep docelowy. 

![Mapa do celu](./media/dataset-map-target.png "Mapa do celu") 

## <a name="select-a-new-target-store"></a>Wybierz nowy sklep docelowy

Wybierz docelowy typ danych, w który mają trafić dane. W przypadku udostępniania opartego na migawkach wszystkie dane, które już istnieją na wcześniej mapowanych kontach magazynu, nie zostaną automatycznie przeniesione do nowego magazynu docelowego. W przypadku udostępniania w miejscu wybierz magazyn danych w określonej lokalizacji. Lokalizacja to centrum danych platformy Azure, w którym znajduje się magazyn danych źródłowych dostawcy danych.

![Docelowe konto magazynu](./media/dataset-map-target-sql.png "Magazyn docelowy") 

## <a name="select-a-file-format-sql-sources-only"></a>Wybieranie formatu pliku (tylko źródła SQL)

Jeśli dane źródłowe pochodzą ze źródła opartego na języku SQL, można wybrać format, w którym są odbierane. 

![Wybierz format](./media/sql-file-formats.png "Formaty plików SQL")

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak rozpocząć udostępnianie danych, przejdź do [samouczka udostępniania danych.](share-your-data.md)



