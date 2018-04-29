---
title: Konfigurowanie poświadczeń uwierzytelniania o nazwie | Dokumentacja firmy Microsoft
description: Informacje o podanie poświadczeń, które Visual Studio można użyć do uwierzytelniania żądań do usługi Azure, co umożliwia publikowanie aplikacji na platformie Azure w programie Visual Studio lub monitorować istniejącą usługę w chmurze.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 61570907-42a1-40e8-bcd6-952b21a55786
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/11/2017
ms.author: ghogen
ms.openlocfilehash: 3dce80f5c6611cb2a22293d1a574980db5ea8378
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="set-up-named-authentication-credentials"></a>Konfigurowanie poświadczeń uwierzytelniania o nazwie

Aby opublikować aplikację na platformie Azure lub do monitorowania istniejącą usługę w chmurze, program Visual Studio wymaga poświadczeń do uwierzytelniania żądań na platformie Azure, czyli identyfikator subskrypcji platformy Azure i X.509 v3 certyfikat z kluczem co najmniej 2048 bitów. Te poświadczenia przy użyciu jednej z następujących metod:

- W programie Visual Studio wybierz **Widok > Eksploratora serwera**, kliknij prawym przyciskiem myszy **Azure** węzła, wybierz opcję **nawiązywanie połączenia z subskrypcją platformy Microsoft Azure**i zaloguj się.
- Utwórz plik subskrypcji (`.publishsettings`), który zawiera klucz publiczny certyfikatu. Plik subskrypcji może zawierać poświadczeń dla więcej niż jedną subskrypcję, zgodnie z opisem w tym artykule.

Uwaga: te poświadczenia są inne niż poświadczenia używane do uwierzytelniania żądań do usługi Magazyn Azure.

## <a name="create-a-subscription-file"></a>Utwórz plik subskrypcji

W Eksploratorze serwera, kliknij prawym przyciskiem myszy **Azure** a następnie wybierz węzeł **zarządzanie i subskrypcje filtru**. Następnie wybierz **certyfikaty** karcie, a następnie wykonaj jedną z następujących czynności:

- Wybierz **importu** otworzyć **importu subskrypcji platformy Microsoft Azure** okno dialogowe. Wybierz **pobierania pliku subskrypcji** łącza, a w przeglądarce Zapisz pobrany plik do lokalizacji tymczasowej. W oknie dialogowym Przejdź do lokalizacji pobierania i zaimportuj go do użytku podczas uwierzytelniania.
- Wybierz subskrypcję active **Edytuj**, które otwiera okno dialogowe edycji istniejącej subskrypcji do użytku podczas uwierzytelniania.
- Wybierz **nowy** otworzyć **nową subskrypcję** okna dialogowego polu oraz wymagane informacje. Aby przekazać certyfikat do chmury usługi są wymienione w oknie dialogowym Zaloguj się do portalu Azure, przejdź do usługi w chmurze, wybierz opcję **Ustawienia > certyfikaty zarządzania**, wybierz pozycję **przekazać**, następnie Określ ścieżkę do `.cer` pliku.

Jeśli chcesz samodzielnie utworzyć certyfikat mogą odwoływać się do instrukcji w [tworzenie i przekazywanie certyfikatu zarządzania platformy Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) , a następnie ręcznie przekazać certyfikat do [portalu Azure](https://portal.azure.com/).

## <a name="next-steps"></a>Kolejne kroki

- [Ogólne omówienie aplikacji sieci Web](https://docs.microsoft.com/azure/app-service/)
- [Wdrażanie aplikacji w usłudze Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-deploy-local-git) 
- [Wdrażanie zadań WebJob za pomocą programu Visual Studio](https://docs.microsoft.com/azure/app-service/websites-dotnet-deploy-webjobs)
- [Tworzenie i wdrażanie usługi w chmurze](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy-portal)