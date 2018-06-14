---
title: Przykłady szablonów usługi Azure Resource Manager — App Service | Microsoft Docs
description: Przykłady szablonów usługi Azure Resource Manager dla funkcji Web Apps w usłudze App Service
services: app-service
documentationcenter: app-service
author: tfitzmac
manager: timlt
editor: ggailey777
tags: azure-service-management
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 02/26/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 155b47fc4c664701ec0f21bdc5ae34f3d7f666ff
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
ms.locfileid: "29933072"
---
# <a name="azure-resource-manager-templates-for-web-apps"></a>Szablony usługi Azure Resource Manager dla funkcji Web Apps

Poniższa tabela zawiera linki do szablonów usługi Azure Resource Manager dla funkcji Web Apps w usłudze Azure App Service. Aby zapoznać się z zaleceniami dotyczącymi unikania typowych błędów podczas tworzenia szablonów aplikacji internetowych, zobacz [Guidance on deploying web apps with Azure Resource Manager templates (Wskazówki dotyczące wdrażania aplikacji internetowych przy użyciu szablonów usługi Azure Resource Manager)](web-sites-rm-template-guidance.md).

| | |
|-|-|
|**Wdrażanie aplikacji internetowej**||
| [Aplikacja internetowa połączona z repozytorium GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Wdraża aplikację internetową platformy Azure, która ściąga kod z usługi GitHub. |
| [Aplikacja internetowa z niestandardowymi miejscami wdrożenia](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Wdraża aplikację internetową platformy Azure z niestandardowymi środowiskami/miejscami wdrożenia. |
|**Konfigurowanie aplikacji internetowej**||
| [Certyfikat aplikacji internetowej z magazynu Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Wdraża certyfikat aplikacji internetowej platformy Azure z wpisu tajnego magazynu Azure Key Vault i używa go na potrzeby powiązania SSL. |
| [Aplikacja internetowa z domeną niestandardową](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Wdraża aplikację internetową platformy Azure z niestandardową nazwą hosta. |
| [Aplikacja internetowa z protokołem SSL i domeną niestandardową](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Wdraża aplikację internetową platformy Azure z niestandardową nazwą hosta, a następnie pobiera certyfikat aplikacji internetowej z magazynu Key Vault na potrzeby powiązania SSL. |
| [Aplikacja internetowa z rozszerzeniem GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Wdraża aplikację internetową platformy Azure z rozszerzeniem lokacji GoLang. Umożliwia to uruchamianie aplikacji internetowych opracowanych przy użyciu oprogramowania GoLang na platformie Azure. |
| [Aplikacja internetowa z rozwiązaniami Java 8 i Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Wdraża aplikację internetową platformy Azure z włączoną obsługą rozwiązań Java 8 i Tomcat 8. Umożliwia to uruchamianie aplikacji w języku Java na platformie Azure. |
|**Aplikacja internetowa systemu Linux**||
| [Aplikacja internetowa w systemie Linux z rozwiązaniem MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Wdraża aplikację internetową platformy Azure w systemie Linux przy użyciu usługi Azure Database for MySQL. |
| [Aplikacja internetowa w systemie Linux z rozwiązaniem PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Wdraża aplikację internetową platformy Azure w systemie Linux przy użyciu usługi Azure Database for PostgreSQL. |
|**Aplikacja internetowa z połączonymi zasobami**||
| [Aplikacja internetowa z rozwiązaniem MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Wdraża aplikację internetową platformy Azure w systemie Windows przy użyciu usługi Azure Database for MySQL. |
| [Aplikacja internetowa z rozwiązaniem PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Wdraża aplikację internetową platformy Azure w systemie Windows przy użyciu usługi Azure Database for PostgreSQL. |
| [Aplikacja internetowa z bazą danych SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Wdraża aplikację internetową i bazę danych SQL Database na poziomie usługi Podstawowa. |
| [Aplikacja internetowa z połączeniem z usługą Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Wdraża aplikację internetową platformy Azure z parametrami połączenia usługi Azure Blob Storage. Umożliwia to użycie usługi Azure Blob Storage z poziomu aplikacji internetowej. |
| [Aplikacja internetowa z pamięcią podręczną Redis Cache](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Wdraża aplikację internetową platformy Azure z pamięcią podręczną Redis Cache. |
|**Środowisko usługi App Service dla rozwiązania PowerApps**||
| [Tworzenie środowiska usługi App Service w wersji 2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Tworzy środowisko usługi App Service w wersji 2 w sieci wirtualnej. |
| [Tworzenie środowiska usługi App Service w wersji 2 przy użyciu adresu wewnętrznego modułu równoważenia obciążenia](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Tworzy środowisko usługi App Service w wersji 2 w sieci wirtualnej przy użyciu prywatnego adresu wewnętrznego modułu równoważenia obciążenia. |
| [Konfigurowanie domyślnego certyfikatu SSL dla środowiska usługi App Service wewnętrznego modułu równoważenia obciążenia lub środowiska usługi App Service wewnętrznego modułu równoważenia obciążenia w wersji 2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Konfiguruje domyślny certyfikat SSL dla środowiska usługi App Service wewnętrznego modułu równoważenia obciążenia lub środowiska usługi App Service wewnętrznego modułu równoważenia obciążenia w wersji 2. |
| | |
