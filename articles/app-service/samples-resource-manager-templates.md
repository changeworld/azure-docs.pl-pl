---
title: Przykłady szablonów usługi Azure Resource Manager
description: Znajdź przykłady szablonów usługi Azure Resource Manager dla niektórych typowych scenariuszy usługi App Service. Dowiedz się, jak zautomatyzować wdrażanie usługi App Service lub zadania zarządzania.
author: tfitzmac
tags: azure-service-management
ms.topic: sample
ms.date: 01/04/2019
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: b1d5f20ccd2f2c637d7db668af10ef331947d018
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74971200"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>Szablony usługi Azure Resource Manager dla usługi App Service

Poniższa tabela zawiera linki do szablonów usługi Azure Resource Manager dla usługi Azure App Service. Aby zapoznać się z zaleceniami dotyczącymi unikania typowych błędów podczas tworzenia szablonów aplikacji, zobacz [Guidance on deploying web apps with Azure Resource Manager templates (Wskazówki dotyczące wdrażania aplikacji przy użyciu szablonów usługi Azure Resource Manager)](deploy-resource-manager-template.md).

Aby poznać składnię JSON i właściwości dla zasobów usługi App Services, zobacz temat [typy zasobów Microsoft.Web](/azure/templates/microsoft.web/allversions).

| | |
|-|-|
|**Wdrażanie aplikacji**||
| [Plan usługi App Service i podstawowa aplikacja systemu Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Wdraża aplikację usługi App Service skonfigurowaną dla systemu Linux. |
| [Plan usługi App Service i podstawowa aplikacja systemu Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Wdraża aplikację usługi App Service skonfigurowaną dla systemu Windows. |
| [Aplikacja połączona z repozytorium GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Wdraża aplikację usługi App Service, która ściąga kod z usługi GitHub. |
| [Aplikacja z niestandardowymi miejscami wdrożenia](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Wdraża aplikację usługi App Service z niestandardowymi środowiskami/miejscami wdrożenia. |
|**Konfigurowanie aplikacji**||
| [Certyfikat aplikacji z magazynu Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Wdraża certyfikat aplikacji usługi App Service z wpisu tajnego magazynu Azure Key Vault i używa go na potrzeby powiązania SSL. |
| [Aplikacja z domeną niestandardową](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Wdraża aplikację usługi App Service z niestandardową nazwą hosta. |
| [Aplikacja z protokołem SSL i domeną niestandardową](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Wdraża aplikację usługi App Service z niestandardową nazwą hosta, a następnie pobiera certyfikat aplikacji z magazynu Key Vault na potrzeby powiązania SSL. |
| [Aplikacja z rozszerzeniem GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Wdraża aplikację usługi App Service z rozszerzeniem lokacji Golang. Umożliwia to uruchamianie aplikacji internetowych opracowanych przy użyciu oprogramowania Golang na platformie Azure. |
| [Aplikacja z rozwiązaniami Java 8 i Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Wdraża aplikację usługi App Service z włączoną obsługą rozwiązań Java 8 i Tomcat 8. Umożliwia to uruchamianie aplikacji w języku Java na platformie Azure. |
|**Ochrona aplikacji**||
| [Aplikacja zintegrowana z bramą aplikacji platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2)| Wdraża aplikację usługi App Service i bramę aplikacji i izoluje ruch przy użyciu punktu końcowego usługi i ograniczeń dostępu. |
|**Aplikacja systemu Linux z połączonymi zasobami**||
| [Aplikacja w systemie Linux z rozwiązaniem MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Wdraża aplikację usługi App Service w systemie Linux przy użyciu usługi Azure Database for MySQL. |
| [Aplikacja w systemie Linux z rozwiązaniem PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Wdraża aplikację usługi App Service w systemie Linux przy użyciu usługi Azure Database for PostgreSQL. |
|**Aplikacja z połączonymi zasobami**||
| [Aplikacja z rozwiązaniem MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Wdraża aplikację usługi App Service w systemie Windows przy użyciu usługi Azure Database for MySQL. |
| [Aplikacja z rozwiązaniem PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Wdraża aplikację usługi App Service w systemie Windows przy użyciu usługi Azure Database for PostgreSQL. |
| [Aplikacja z bazą danych SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Wdraża aplikację usługi App Service i bazę danych SQL Database na poziomie usługi Podstawowa. |
| [Aplikacja z połączeniem z usługą Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Wdraża aplikację usługi App Service z parametrami połączenia usługi Azure Blob Storage. Umożliwia to użycie usługi Azure Blob Storage z poziomu aplikacji. |
| [Aplikacja z pamięcią podręczną Azure Cache for Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Wdraża aplikację usługi App Service z pamięcią podręczną Azure Cache for Redis. |
|**Środowisko usługi aplikacji**||
| [Tworzenie środowiska usługi App Service w wersji 2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Tworzy środowisko usługi App Service w wersji 2 w sieci wirtualnej. |
| [Tworzenie środowiska usługi App Service w wersji 2 przy użyciu adresu wewnętrznego modułu równoważenia obciążenia](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Tworzy środowisko usługi App Service w wersji 2 w sieci wirtualnej przy użyciu prywatnego adresu wewnętrznego modułu równoważenia obciążenia. |
| [Konfigurowanie domyślnego certyfikatu SSL dla środowiska usługi App Service wewnętrznego modułu równoważenia obciążenia lub środowiska usługi App Service wewnętrznego modułu równoważenia obciążenia w wersji 2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Konfiguruje domyślny certyfikat SSL dla środowiska usługi App Service wewnętrznego modułu równoważenia obciążenia lub środowiska usługi App Service wewnętrznego modułu równoważenia obciążenia w wersji 2. |
| | |
