---
title: Eksportowanie lub importowanie konfiguracji aprowizacji przy użyciu interfejs API programu Graph | Microsoft Docs
description: Dowiedz się, jak eksportować i importować konfigurację aprowizacji przy użyciu interfejs API programu Graph.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 609031bfad23a14a954a09a447e363e89a9d29d5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76711748"
---
# <a name="export-or-import-your-provisioning-configuration-by-using-graph-api"></a>Eksportowanie lub importowanie konfiguracji aprowizacji za pomocą interfejs API programu Graph

Za pomocą Microsoft Graph API i Eksploratora grafów można wyeksportować mapowania atrybutów aprowizacji użytkowników i schemat do pliku JSON, a następnie zaimportować je z powrotem do usługi Azure AD. Możesz również użyć tutaj przechwyconych kroków, aby utworzyć kopię zapasową konfiguracji aprowizacji. 

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Krok 1. Pobieranie inicjowania obsługi App Service Identyfikator podmiotu zabezpieczeń (identyfikator obiektu)

1. Uruchom [Azure Portal](https://portal.azure.com)i przejdź do sekcji właściwości aplikacji aprowizacji. Na przykład jeśli chcesz wyeksportować swój *dzień roboczy do usługi AD, mapowanie aplikacji do inicjowania obsługi administracyjnej użytkowników* , przejdź do sekcji Właściwości tej aplikacji. 
1. W sekcji właściwości aplikacji aprowizacji skopiuj wartość identyfikatora GUID skojarzoną z polem *Identyfikator obiektu* . Ta wartość jest również nazywana **ServicePrincipalId** aplikacji i będzie używana w operacjach Eksploratora grafu.

   ![Identyfikator podmiotu zabezpieczeń App Service Workday](media/export-import-provisioning-mappings/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Krok 2. Logowanie do Microsoft Graph Explorer

1. Uruchom [eksploratora Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)
1. Kliknij przycisk "Zaloguj się za pomocą firmy Microsoft" i zaloguj się przy użyciu poświadczeń administratora globalnego usługi Azure AD lub administratora aplikacji.

    ![Logowanie grafu](media/export-import-provisioning-mappings/wd_export_02.png)

1. Po pomyślnym zalogowaniu zobaczysz szczegóły konta użytkownika w okienku po lewej stronie.

## <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Krok 3. Pobieranie identyfikatora zadania aprowizacji dla aplikacji aprowizacji

W Eksploratorze Microsoft Graph Uruchom następujące polecenie GET Query zastępujące [servicePrincipalId] z **servicePrincipalId** wyodrębnionym z [kroku 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Otrzymasz odpowiedź, jak pokazano poniżej. Skopiuj "identyfikator atrybutu" obecny w odpowiedzi. Ta wartość jest **ProvisioningJobId** i zostanie użyta do pobrania metadanych bazowego schematu.

   [![identyfikator zadania aprowizacji](media/export-import-provisioning-mappings/wd_export_03.png)](media/export-import-provisioning-mappings/wd_export_03.png#lightbox)

## <a name="step-4-download-the-provisioning-schema"></a>Krok 4. Pobieranie schematu aprowizacji

W Eksploratorze Microsoft Graph Uruchom następujące zapytanie GET, zastępując [servicePrincipalId] i [ProvisioningJobId] ServicePrincipalId i ProvisioningJobId pobrane w poprzednich krokach.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Skopiuj obiekt JSON z odpowiedzi i Zapisz go w pliku, aby utworzyć kopię zapasową schematu.

## <a name="step-5-import-the-provisioning-schema"></a>Krok 5. Importowanie schematu aprowizacji

> [!CAUTION]
> Wykonaj ten krok tylko wtedy, gdy musisz zmodyfikować schemat konfiguracji, którego nie można zmienić przy użyciu Azure Portal lub jeśli musisz przywrócić konfigurację z wcześniej wykonanej kopii zapasowej, używając prawidłowego i roboczego schematu.

W Eksploratorze Microsoft Graph skonfiguruj następujące zapytanie PUT, zastępując [servicePrincipalId] i [ProvisioningJobId] ServicePrincipalId i ProvisioningJobId pobrane w poprzednich krokach.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Na karcie "treść żądania" Skopiuj zawartość pliku schematu JSON.

   [Treść żądania ![](media/export-import-provisioning-mappings/wd_export_04.png)](media/export-import-provisioning-mappings/wd_export_04.png#lightbox)

Na karcie "nagłówki żądań" Dodaj atrybut nagłówka Content-Type z wartością "Application/JSON"

   [Nagłówki żądania ![](media/export-import-provisioning-mappings/wd_export_05.png)](media/export-import-provisioning-mappings/wd_export_05.png#lightbox)

Kliknij przycisk "uruchom zapytanie", aby zaimportować nowy schemat.
