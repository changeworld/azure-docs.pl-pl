---
title: Usługa Azure Analysis Services, wysokiej dostępności | Dokumentacja firmy Microsoft
description: Zapewnienie wysokiej dostępności usług Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 51a0f560a0e4b6ff791d5ed3f9f221eb2eeb9b4d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61036048"
---
# <a name="analysis-services-high-availability"></a>Analiza usługi wysokiej dostępności

W tym artykule opisano, zapewniając wysoką dostępność dla serwerów usług Azure Analysis Services. 

## <a name="assuring-high-availability-during-a-service-disruption"></a>Zapewnienie wysokiej dostępności podczas przerw w działaniu usługi

Chociaż rzadko, w centrum danych platformy Azure może mieć awarię. Taka awaria powoduje zakłócenia działania firmy, która może trwać kilka minut lub może trwać wiele godzin. Wysoka dostępność w większości przypadków odbywa się przy użyciu nadmiarowość serwera. Za pomocą usług Azure Analysis Services można osiągnąć nadmiarowość, tworząc dodatkowe, pomocnicze serwery w jednym lub kilku regionach. Podczas tworzenia nadmiarowych serwerów, aby mieć pewność, danych i metadanych na tych serwerach są zsynchronizowane z serwerem w regionie, który przeszedł do trybu offline, możesz:

* Wdrażaj modele na serwerach nadmiarowych w innych regionach. Ta metoda wymaga przetwarzania danych na serwerze podstawowym i serwerach nadmiarowych w równoległych, zapewniając wszystkie serwery są zsynchronizowane.

* [Kopia zapasowa](analysis-services-backup.md) baz danych z serwera podstawowego i przywracania na serwerach nadmiarowych. Na przykład można zautomatyzować kopii zapasowych w nocy do usługi Azure storage i przywracania na innych serwerach nadmiarowych w innych regionach. 

W obu przypadkach Jeśli serwer podstawowy ulegnie awarii, należy zmienić parametry połączenia w raportowania klientom na łączenie się z serwerem w różnych regionalnych centrów danych. Tej zmiany należy rozważyć tylko w ostateczności i występuje tylko w przypadku poważnej awarii regionalnego centrum danych. Jest bardziej prawdopodobne, awarii centrum danych podstawowego serwera hostingu przybyły wróci do trybu online przed uaktualnieniem połączeń na wszystkich klientach. 

Aby uniknąć konieczności zmiany parametrów połączenia na klientach raportowania, należy utworzyć serwer [alias](analysis-services-server-alias.md) dla podstawowego serwera. Jeśli serwer podstawowy ulegnie awarii, możesz zmienić aliasu, aby wskazać nadmiarowe serwer w innym regionie. Można zautomatyzować alias na nazwę serwera, tworząc sprawdzenie kondycji punktu końcowego na serwerze podstawowym. W przypadku niepowodzenia sprawdzania kondycji tego samego punktu końcowego można nakazać serwerowi nadmiarowy w innym regionie. 

## <a name="related-information"></a>Informacje pokrewne

[Kopia zapasowa i przywracanie](analysis-services-backup.md)   
[Zarządzanie usług Azure Analysis Services](analysis-services-manage.md)   
[Alias serwera nazw](analysis-services-server-alias.md) 

