---
title: Jak wdrożyć bliźniaczej reprezentacji OPC zależności chmury na platformie Azure | Dokumentacja firmy Microsoft
description: Jak wdrożyć zależności platformy Azure w bliźniaczej reprezentacji OPC.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 95c2130727c0cef889771c181fec53557a2a4b0f
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603710"
---
# <a name="deploying-dependencies-for-local-development"></a>Wdrażanie zależności na potrzeby lokalnego programowania

W tym artykule opisano sposób wdrażania tylko usług platformy Azure muszą tylko lokalne programowanie i debugowanie.   Na koniec masz wdrożone do grupy zasobów, która zawiera wszystko, czego potrzebujesz do lokalnego programowania i debugowania.

## <a name="deploy-azure-platform-services"></a>Wdrażanie usług platformy Azure

1. Upewnij się, że program PowerShell został i [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-1.1.0) zainstalowanych rozszerzeń.  Otwórz wiersz polecenia lub terminalu i uruchom:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Postępuj zgodnie z monitami, aby przypisać nazwę do grupy zasobów dla danego wdrożenia.  Skrypt wdroży tylko zależności do tej grupy zasobów w subskrypcji platformy Azure, ale nie mikrousługami.  Skrypt rejestruje także aplikacji w usłudze Azure Active Directory.  Jest to niezbędne do obsługi uwierzytelniania OAUTH.  Wdrożenie może potrwać kilka minut.

3. Po ukończeniu działania skryptu, można wybrać można zapisać pliku env.  Plik środowiska ENV jest plik konfiguracji wszystkich usług i narzędzi, które chcesz uruchomić na komputerze deweloperskim.  

## <a name="troubleshooting-deployment-failures"></a>Rozwiązywanie problemów dotyczących niepowodzenia wdrożenia

### <a name="resource-group-name"></a>Nazwa grupy zasobów

Upewnij się, że używasz nazwę grupy zasobów krótka i prosta.  Nazwa jest używana również do nazwy zasobów związku z tym musi być zgodne z wymaganiami w zakresie nazewnictwa zasobów.  

### <a name="azure-active-directory-aad-registration"></a>Rejestracja w usłudze Azure Active Directory (AAD)

Skrypt wdrażania próbuje rejestrowania aplikacji usługi AAD w usłudze Azure Active Directory.  W zależności od prawa do wybranej dzierżawy usługi AAD to może zakończyć się niepowodzeniem.   Istnieją 3 opcje:

1. Jeśli została wybrana opcja dzierżawy usługi AAD z listy dzierżaw, uruchom ponownie skrypt i wybrać inny z listy.
2. Alternatywnie wdrażanie prywatnego dzierżawy usługi AAD, uruchom ponownie skrypt i wybierz z niego korzystać.
3. Kontynuuj bez uwierzytelniania.  Ponieważ używasz usługi wczesnych lokalnie, to jest dopuszczalna, ale nie imitują środowisk produkcyjnych.  

## <a name="next-steps"></a>Następne kroki

Teraz, gdy została pomyślnie wdrożona usług bliźniaczej reprezentacji OPC do istniejącego projektu, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wdrażaniu modułów bliźniaczej reprezentacji OPC](howto-opc-twin-deploy-modules.md)
