---
title: Narzędzia do pracy z usługą Azure Storage | Dokumentacja firmy Microsoft
description: Lista narzędzi, które pozwalają na danych/interakcji z danymi usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/06/2017
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: d7debbc760e103046ce9bb1a8bdf25a954d9891c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65138554"
---
# <a name="azure-storage-client-tools"></a>Azure Storage Client Tools
Użytkownicy usługi Azure Storage często chcą mieć możliwość danych/interakcji z danymi za pomocą narzędzia klienta usługi Azure Storage. W poniższych tabelach listę szereg narzędzi, które pozwalają w tym celu. Umieściliśmy "X" w każdym bloku zapewnia możliwość zarówno wyliczanie i/lub dostęp do pozyskiwania danych. W tabeli przedstawiono również, czy jest bezpłatne narzędzia, czy nie. "Próbne" wskazuje, czy istnieje bezpłatna wersja próbna, ale pełnego produktu nie jest bezpłatne. "T/N" wskazuje, że wersja jest dostępna bezpłatnie, podczas gdy inna wersja jest dostępna do zakupu.

Przedstawiliśmy tylko migawki dostępnych narzędzi klienta usługi Azure Storage. Te narzędzia mogą nadal się rozwijać i rozwój funkcji. W przypadku aktualizacji lub poprawek, pozostaw komentarz, aby dać nam znać. Jest taka sama wartość true, jeśli wiesz, narzędzi, które powinny się znajdować tutaj — będziemy wszystkiego dodać je.

**Microsoft Azure Storage Client Tools**

<table>
  <tr>
    <th rowspan="2">Azure Storage Client Tool</th>
    <th rowspan="2">Blokowe obiekty Blob</th>
    <th rowspan="2">Obiekt blob typu Page</th>
    <th rowspan="2">Obiekt Blob dołączania</th>
    <th rowspan="2">Tabele</th>
    <th rowspan="2">Kolejki</th>
    <th rowspan="2">Pliki</th>
    <th rowspan="2">Wolne</th>
    <th colspan="4">Platforma</th>
  </tr>
  <tr>
    <td>sieć Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://azure.microsoft.com/features/azure-portal/">Portal Microsoft Azure</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Tak</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://storageexplorer.com/">Microsoft Azure Storage Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Tak</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
  </tr>
  <tr>
    <td><a href="https://www.visualstudio.com/features/azure-tools-vs.aspx">Microsoft Visual Studio Server Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Tak</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>

**Innych firm, usługi Azure Storage Client Tools**

Firma Microsoft nie została zweryfikowana funkcja lub jakości przez następujące narzędzia firm, a ich lista nie oznacza, że firma Microsoft wspiera.

<table>
  <tr>
    <th rowspan="2">Azure Storage Client Tool</th>
    <th rowspan="2">Blokowe obiekty Blob</th>
    <th rowspan="2">Obiekt blob typu Page</th>
    <th rowspan="2">Obiekt Blob dołączania</th>
    <th rowspan="2">Tabele</th>
    <th rowspan="2">Kolejki</th>
    <th rowspan="2">Pliki</th>
    <th rowspan="2">Wolne</th>
    <th colspan="4">Platforma</th>
  </tr>
  <tr>
    <td>sieć Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://www.cerebrata.com/products/azure-management-studio/introduction">Cerabrata: Azure Management Studio</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Wersja próbna</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.red-gate.com/products/azure-development/azure-explorer/index">Redgate: Azure Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>Tak</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://github.com/sebagomez/azurestorageexplorer">Azure Web Storage Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Tak</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.cloudberrylab.com/explorer/microsoft-azure.aspx">Eksplorator cloudBerry</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>X</td>
    <td>T/N</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.gapotchenko.com/cloudcombine">Cloud Combine</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Wersja próbna</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://clumsyleaf.com">ClumsyLeaf: AzureXplorer, CloudXplorer, TableXplorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Tak</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.gladinet.com/Azure-Storage/index.htm">Gladinet Cloud</a></td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td>Wersja próbna</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>
