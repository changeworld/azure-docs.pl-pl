---
title: Narzędzia do pracy z usługą Azure Storage
description: Lista narzędzi, które umożliwiają wyświetlanie danych usługi Azure Storage i korzystanie z nich.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/26/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 11838a50d70d1b9a0216505e9ef0958d3b18bdac
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035260"
---
# <a name="azure-storage-client-tools"></a>Azure Storage Client Tools
Użytkownicy usługi Azure Storage często chcą mieć możliwość wyświetlania danych i korzystania z nich przy użyciu narzędzia klienckiego usługi Azure Storage. W poniższych tabelach przedstawiono kilka narzędzi, które umożliwiają wykonanie tej czynności. W każdym bloku umieścimy symbol "X", jeśli umożliwia on Wyliczenie i/lub uzyskanie dostępu do abstrakcji danych. W tabeli pokazano również, czy narzędzia są wolne, czy nie. "Wersja próbna" oznacza, że jest dostępna bezpłatna wersja próbna, ale pełny produkt nie jest bezpłatny. "T/N" wskazuje, że wersja jest dostępna bezpłatnie, podczas gdy dostępna jest inna wersja do zakupu.

Podano tylko migawkę dostępnych narzędzi klienckich usługi Azure Storage. Te narzędzia mogą nadal rozwijać i zwiększać funkcjonalność. Jeśli istnieją poprawki lub aktualizacje, należy pozostawić komentarz, aby poinformować nas o tym. To samo jest prawdziwe, jeśli znasz narzędzia, które powinny być tutaj — będziemy mogli je dodać.

**Microsoft Azure Storage narzędzia klienckie**

<table>
  <tr>
    <th rowspan="2">Narzędzie klienckie usługi Azure Storage</th>
    <th rowspan="2">Blokowy obiekt blob</th>
    <th rowspan="2">Stronicowy obiekt blob</th>
    <th rowspan="2">Dołącz obiekt blob</th>
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
    <td>T</td>
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
    <td>T</td>
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
    <td>T</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>

**Narzędzia klienckie usługi Azure Storage innych firm**

Nie sprawdziłem funkcjonalności ani jakości pożądanych przez następujące narzędzia innych firm, a ich oferty nie wymagają potwierdzenia przez firmę Microsoft.

<table>
  <tr>
    <th rowspan="2">Narzędzie klienckie usługi Azure Storage</th>
    <th rowspan="2">Blokowy obiekt blob</th>
    <th rowspan="2">Stronicowy obiekt blob</th>
    <th rowspan="2">Dołącz obiekt blob</th>
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
    <td><a href="https://github.com/sebagomez/azurestorageexplorer">Azure Web Storage Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>T</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.cloudberrylab.com/explorer/microsoft-azure.aspx">Eksplorator CloudBerry</a></td>
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
    <td><a href="https://www.gapotchenko.com/cloudcombine">Połączenie z chmurą</a></td>
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
    <td><a href="https://clumsyleaf.com">ClumsyLeaf: AzureXplorer, CloudXplorer, TableXplorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>T</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.gladinet.com/Azure-Storage/index.htm">Chmura Gladinet</a></td>
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
