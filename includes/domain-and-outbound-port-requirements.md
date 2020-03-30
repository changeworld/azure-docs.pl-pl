---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 6af6795fefb41f1d8f2b56e0aa1fb367fc18cee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74559300"
---
| Nazwy domen                  | Porty wychodzące | Opis                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Wymagane przez środowisko uruchomieniowe integracji hostowanego samodzielnie, aby połączyć się z usługami przenoszenia danych w usłudze Azure Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Wymagane przez środowisko uruchomieniowe integracji hostowanego samodzielnie, aby połączyć się z usługą Data Factory. |
| `download.microsoft.com`    | 443            | Wymagane przez środowisko wykonawcze integracji hostowanego samodzielnie do pobierania aktualizacji. Jeśli została wyłączona automatyczna aktualizacja, możesz pominąć konfigurowanie tej domeny. |
| `*.core.windows.net`          | 443            | Używane przez środowisko uruchomieniowe integracji hostowanego samodzielnie do łączenia się z kontem magazynu platformy Azure podczas korzystania z funkcji [kopiowania etapowego.](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) |
| `*.database.windows.net`      | 1433           | Wymagane tylko podczas kopiowania z lub do usługi Azure SQL Database lub usługi Azure SQL Data Warehouse i opcjonalne w inny sposób. Funkcja kopiowania etapowego służy do kopiowania danych do bazy danych SQL lub magazynu danych SQL bez otwierania portu 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Wymagane tylko podczas kopiowania z lub do usługi Azure Data Lake Store i opcjonalne w inny sposób. |
