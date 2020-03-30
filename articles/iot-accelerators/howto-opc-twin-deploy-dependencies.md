---
title: Jak wdrożyć zależności chmury OPC Twin na platformie Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób wdrażania zależności platformy OPC Twin Azure potrzebne do lokalnego rozwoju i debugowania.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 231d1efa02ec80e8ad56a8895d4262d774480111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824097"
---
# <a name="deploying-dependencies-for-local-development"></a>Wdrażanie zależności dla rozwoju lokalnego

W tym artykule wyjaśniono, jak wdrożyć tylko usługi platformy Azure platform potrzebne do lokalnego rozwoju i debugowania.   Na końcu zostanie wdrożona grupa zasobów zawierająca wszystko, czego potrzebujesz do lokalnego rozwoju i debugowania.

## <a name="deploy-azure-platform-services"></a>Wdrażanie usług platformy Azure

1. Upewnij się, że masz zainstalowane rozszerzenia [programu PowerShell i AzureRM PowerShell.](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)  Otwórz wiersz polecenia lub terminal i uruchom:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Postępuj zgodnie z instrukcjami, aby przypisać nazwę do grupy zasobów dla wdrożenia.  Skrypt wdraża tylko zależności do tej grupy zasobów w subskrypcji platformy Azure, ale nie mikro usług.  Skrypt rejestruje również aplikację w usłudze Azure Active Directory.  Jest to potrzebne do obsługi uwierzytelniania opartego na architekturze OAUTH.  Wdrożenie może potrwać kilka minut.

3. Po zakończeniu skryptu można zapisać plik .env.  Plik środowiska .env jest plikiem konfiguracyjnym wszystkich usług i narzędzi, które chcesz uruchomić na komputerze deweloperskim.  

## <a name="troubleshooting-deployment-failures"></a>Rozwiązywanie problemów z błędami wdrażania

### <a name="resource-group-name"></a>Nazwa grupy zasobów

Upewnij się, że używasz krótkiej i prostej nazwy grupy zasobów.  Nazwa jest również używana do nazwania zasobów jako takich, które muszą być zgodne z wymaganiami dotyczącymi nazewnictwa zasobów.  

### <a name="azure-active-directory-aad-registration"></a>Rejestracja usługi Azure Active Directory (AAD)

Skrypt wdrażania próbuje zarejestrować aplikacje usługi AAD w usłudze Azure Active Directory.  W zależności od praw do wybranej dzierżawy usługi AAD może to zakończyć się niepowodzeniem.   Dostępne są trzy opcje:

1. Jeśli wybierzesz dzierżawę usługi AAD z listy dzierżaw, uruchom ponownie skrypt i wybierz inny z listy.
2. Alternatywnie wdrożyć prywatną dzierżawę usługi AAD, uruchom ponownie skrypt i wybierz, aby go użyć.
3. Kontynuuj bez uwierzytelniania.  Ponieważ korzystasz z mikro usług lokalnie, jest to dopuszczalne, ale nie naśladuje środowisk produkcyjnych.  

## <a name="next-steps"></a>Następne kroki

Teraz, po pomyślnym wdrożeniu usług OPC Twin do istniejącego projektu, oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Dowiedz się, jak wdrożyć moduły OPC Twin](howto-opc-twin-deploy-modules.md)
