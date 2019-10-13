---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: f634ebb4d8c3f443b5d843b0093c955b384a9f5f
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285593"
---
| Nazwy domen                  | Porty wychodzące | Opis                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Wymagane przez własne środowisko Integration Runtime do łączenia się z usługami przenoszenia danych w Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Wymagane przez własne środowisko Integration Runtime do nawiązywania połączenia z usługą Data Factory. |
| `download.microsoft.com`    | 443            | Wymagane przez własne środowisko Integration Runtime do pobierania aktualizacji. Jeśli funkcja autoaktualizacji została wyłączona, można to pominąć. |
| `*.core.windows.net`          | 443            | Używany przez własne środowisko Integration Runtime do nawiązywania połączenia z kontem usługi Azure Storage w przypadku korzystania z funkcji [kopiowania etapowego](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) . |
| `*.database.windows.net`      | 1433           | Obowiązkowe Wymagane podczas kopiowania z lub do Azure SQL Database lub Azure SQL Data Warehouse. Funkcja kopiowania przemieszczania służy do kopiowania danych do Azure SQL Database lub Azure SQL Data Warehouse bez otwierania portu 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Obowiązkowe Wymagane podczas kopiowania z lub do Azure Data Lake Store. |
