---
title: Narzędzia do pracy z usługą Azure Storage | Dokumentacja firmy Microsoft
description: Lista narzędzi, które pozwalają na danych/interakcji z danymi usługi Azure Storage.
services: storage
author: dineshmurthy
ms.service: storage
ms.topic: article
ms.date: 09/06/2017
ms.author: dineshmurthy
ms.component: common
ms.openlocfilehash: 55fa63e75768662a0f909cef0e3cc04b9c2dae41
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526202"
---
# <a name="azure-storage-client-tools"></a>Azure Storage Client Tools
Użytkownicy usługi Azure Storage często chcą mieć możliwość danych/interakcji z danymi za pomocą narzędzia klienta usługi Azure Storage. W poniższych tabelach listę szereg narzędzi, które pozwalają w tym celu. Umieściliśmy "X" w każdym bloku zapewnia możliwość zarówno wyliczanie i/lub dostęp do pozyskiwania danych. W tabeli przedstawiono również, czy jest bezpłatne narzędzia, czy nie. "Próbne" wskazuje, czy istnieje bezpłatna wersja próbna, ale pełnego produktu nie jest bezpłatne. "T/N" wskazuje, że wersja jest dostępna bezpłatnie, podczas gdy inna wersja jest dostępna do zakupu.

Przedstawiliśmy tylko migawki dostępnych narzędzi klienta usługi Azure Storage. Te narzędzia mogą nadal się rozwijać i rozwój funkcji. W przypadku aktualizacji lub poprawek, pozostaw komentarz, aby dać nam znać. Jest taka sama wartość true, jeśli wiesz, narzędzi, które powinny się znajdować tutaj — będziemy wszystkiego dodać je.

**Microsoft Azure Storage Client Tools**

<table>
  <tr>
    <th rowspan="2">Narzędzie klienta usługi Azure Storage</th>
    <th rowspan="2">Blokowe obiekty Blob</th>
    <th rowspan="2">Obiekt blob typu Page</th>
    <th rowspan="2">Obiekt Blob dołączania</th>
    <th rowspan="2">Tabele</th>
    <th rowspan="2">Kolejki</th>
    <th rowspan="2">Pliki</th>
    <th rowspan="2">Bezpłatna</th>
    <th colspan="4">Platforma</th>
  </tr>
  <tr>
    <td>Sieć Web</td>
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
    <td><a href="http://storageexplorer.com/">Microsoft Azure Storage Explorer</a></td>
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
    <td><a href="https://www.visualstudio.com/features/azure-tools-vs.aspx">Eksplorator serwera programu Microsoft Visual Studio</a></td>
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
    <th rowspan="2">Narzędzie klienta usługi Azure Storage</th>
    <th rowspan="2">Blokowe obiekty Blob</th>
    <th rowspan="2">Obiekt blob typu Page</th>
    <th rowspan="2">Obiekt Blob dołączania</th>
    <th rowspan="2">Tabele</th>
    <th rowspan="2">Kolejki</th>
    <th rowspan="2">Pliki</th>
    <th rowspan="2">Bezpłatna</th>
    <th colspan="4">Platforma</th>
  </tr>
  <tr>
    <td>Sieć Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="http://www.cerebrata.com/products/azure-management-studio/introduction">Cerabrata: Azure Management Studio</a></td>
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
    <td><a href="https://www.red-gate.com/products/azure-development/azure-explorer/index">Redgate: Eksplorator usługi Azure</a></td>
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
    <td><a href="https://github.com/sebagomez/azurestorageexplorer">Eksplorator usługi Storage platformy Azure w sieci Web</a></td>
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
    <td><a href="http://www.cloudberrylab.com/explorer/microsoft-azure.aspx">Eksplorator cloudBerry</a></td>
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
    <td><a href="http://www.gapotchenko.com/cloudcombine">Łączenie z chmury</a></td>
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
    <td><a href="http://clumsyleaf.com">ClumsyLeaf: TableXplorer AzureXplorer, CloudXplorer,</a></td>
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
    <td><a href="http://www.gladinet.com/Azure-Storage/index.htm">Gladinet chmury</a></td>
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
