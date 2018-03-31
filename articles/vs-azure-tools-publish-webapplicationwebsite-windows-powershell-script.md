---
title: Publikowanie WebApplicationWebSite (skrypt programu Windows PowerShell) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak opublikować projekt sieci web do witryny sieci Web platformy Azure. Ten skrypt tworzy wymaganych zasobów w Twojej subskrypcji platformy Azure, jeśli nie istnieją.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 63cfaa2d-f04d-40dc-8677-345385c278d5
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: aaa1f679b0368b0ca93305fe867a63f3971a788c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="publish-webapplicationwebsite-windows-powershell-script"></a>Publikowanie WebApplicationWebSite (skrypt programu Windows PowerShell)
## <a name="syntax"></a>Składnia
Publikuje projektu sieci web w witrynie sieci Web platformy Azure. Skrypt tworzy wymaganych zasobów w Twojej subskrypcji platformy Azure, jeśli nie istnieją.

    Publish-WebApplicationWebSite
    –Configuration <configuration>
    -SubscriptionName <subscriptionName>
    -WebDeployPackage <packageName>
    -DatabaseServerPassword @{Name = "name"; Password = "password"}
    -SendHostMessagesToOutput
    -Verbose


## <a name="configuration"></a>Konfigurowanie
Ścieżka do pliku konfiguracji JSON, który opisuje szczegóły wdrożenia.

| Parametr | Wartość domyślna |
| --- | --- |
| Aliasy |brak |
| Wymagana? |true |
| Położenie |o nazwie |
| Wartość domyślna |brak |
| Akceptowanie danych wejściowych potoku? |false |
| Akceptowanie symboli wieloznacznych? |false |

## <a name="subscriptionname"></a>Nazwa subskrypcji
Nazwa subskrypcji platformy Azure, który chcesz utworzyć witrynę sieci Web w.

| Parametr | Wartość domyślna |
| --- | --- |
| Aliasy |brak |
| Wymagana? |false |
| Położenie |o nazwie |
| Wartość domyślna |brak |
| Akceptowanie danych wejściowych potoku? |false |
| Akceptowanie symboli wieloznacznych? |false |

## <a name="webdeploypackage"></a>WebDeployPackage
Ścieżka do pakietu wdrożeniowego sieci web do publikowania witryny sieci Web. Ten pakiet można utworzyć za pomocą kreatora Publikowanie w sieci Web w programie Visual Studio. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usług Azure Cloud Services i platformy ASP.NET](http://go.microsoft.com/fwlink/p/?LinkID=623089).

| Parametr | Wartość domyślna |
| --- | --- |
| Aliasy |brak |
| Wymagana? |false |
| Położenie |o nazwie |
| Wartość domyślna |brak |
| Akceptowanie danych wejściowych potoku? |false |
| Akceptowanie symboli wieloznacznych? |false |

## <a name="databaseserverpassword"></a>DatabaseServerPassword
Nazwy użytkownika i hasło dla bazy danych SQL platformy Azure.

| Parametr | Wartość domyślna |
| --- | --- |
| Aliasy |brak |
| Wymagana? |false |
| Położenie |o nazwie |
| Wartość domyślna |brak |
| Akceptowanie danych wejściowych potoku? |false |
| Akceptowanie symboli wieloznacznych? |false |

## <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput
Jeśli PRAWDA, Drukuj komunikaty z skrypt do strumienia wyjściowego.

| Parametr | Wartość domyślna |
| --- | --- |
| Aliasy |brak |
| Wymagana? |false |
| Położenie |o nazwie |
| Wartość domyślna |false |
| Akceptowanie danych wejściowych potoku? |false |
| Akceptowanie symboli wieloznacznych? |false |

## <a name="remarks"></a>Uwagi
Pełny opis sposobów użycia skryptu do tworzenia środowisk do tworzenia i testowania, zobacz [za pomocą skryptów programu PowerShell systemu Windows do opublikowania deweloperów i środowisk testowych](vs-azure-tools-publishing-using-powershell-scripts.md).

Plik JSON konfiguracji określa szczegóły co ma zostać wdrożona. Zawiera informacje określone podczas tworzenia projektu, takie jak nazwa i nazwa użytkownika dla witryny sieci Web. Zawiera także bazy danych, aby udostępnić, jeśli istnieje. Poniższy kod przedstawia przykładowy plik konfiguracji JSON:

    {
        "environmentSettings": {
            "webSite": {
                "name": "WebApplication10554",
                "location": "West US"
            },
            "databases": [
                {
                    "connectionStringName": "DefaultConnection",
                    "databaseName": "WebApplication10554_db",
                    "serverName": "iss00brc88",
                    "user": "sqluser2",
                    "password": "",
                    "edition": "",
                    "size": "",
                    "collation": "",
                    "location": "West US"
                }
            ]
        }
    }

Można edytować plik JSON konfiguracji do zmiany, co jest wdrożona. Sekcja witryny sieci Web jest wymagana, ale w sekcji bazy danych jest opcjonalna.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, zobacz [Publish-WebApplicationVM (skrypt programu Windows PowerShell)](vs-azure-tools-publish-webapplicationvm.md)

