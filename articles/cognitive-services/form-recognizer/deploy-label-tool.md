---
title: Jak wdrożyć przykładowe narzędzie do etykietowania z aparatem rozpoznawania formularzy
titleSuffix: Azure Cognitive Services
description: Poznaj różne sposoby wdrażania przykładowego narzędzia do etykietowania aparatu rozpoznawania formularzy, które ułatwia uczenie nadzorowane.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: pafarley
ms.openlocfilehash: fa419d7dd9668ac2ce8f2b0eb904117c7e22692d
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207845"
---
# <a name="deploy-the-sample-labeling-tool"></a>Wdrażanie przykładowego narzędzia do etykietowania

Przykładowe narzędzie do etykietowania z aparatem rozpoznawania formularzy to aplikacja działająca w kontenerze platformy Docker. Zapewnia przydatny interfejs użytkownika, którego można użyć do ręcznego etykietowania dokumentów w celu przeprowadzenia nadzorowanej nauki. [Szkolenie z etykietami](./quickstarts/label-tool.md) szybki start przedstawia sposób uruchamiania narzędzia na komputerze lokalnym, który jest najbardziej typowym scenariuszem. 

W tym przewodniku wyjaśniono alternatywne sposoby wdrażania i uruchamiania przykładowego narzędzia do etykietowania. 

## <a name="deploy-with-azure-container-instances"></a>Wdrażanie za pomocą Azure Container Instances

Narzędzie Label można uruchomić w kontenerze aplikacji sieci Web platformy Docker. Najpierw [Utwórz nowy zasób aplikacji sieci Web](https://ms.portal.azure.com/#create/Microsoft.WebSite) na Azure Portal. Wypełnij formularz informacjami o subskrypcji i grupie zasobów. Wprowadź następujące informacje w wymaganych polach:
* **Publikowanie**: kontener platformy Docker
* **Działanie** System: Linux

Na następnej stronie wypełnij następujące pola konfiguracji kontenera Docker:

* **Opcje**: pojedynczy kontener
* **Źródło obrazu**: Azure Container Registry
* **Typ dostępu**: publiczny
* **Obraz i tag**: MCR.Microsoft.com/Azure-Cognitive-Services/Custom-form/labeltool:Latest

Poniższe kroki są opcjonalne. Po zakończeniu wdrażania aplikacji można uruchomić ją i uzyskać dostęp do narzędzia etykiet w trybie online.

### <a name="connect-to-azure-ad-for-authorization"></a>Nawiązywanie połączenia z usługą Azure AD w celu autoryzacji

Zalecamy łączenie aplikacji sieci Web z usługą Azure Active Directory (AAD), tak aby tylko ktoś z poświadczeniami mógł się zalogować i korzystać z aplikacji. Postępuj zgodnie z instrukcjami w temacie [Konfigurowanie aplikacji App Service](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) , aby nawiązać połączenie z usługą AAD.

## <a name="next-steps"></a>Następne kroki

Wróć do przewodnika Szybki Start [z etykietami](./quickstarts/label-tool.md) , aby dowiedzieć się, jak ręcznie etykietować dane szkoleniowe i uczenia nadzorowanego za pomocą narzędzia.
