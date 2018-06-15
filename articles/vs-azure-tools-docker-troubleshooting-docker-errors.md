---
title: Rozwiązywanie problemów z błędami klienta Docker w systemie Windows za pomocą programu Visual Studio | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów występujących podczas tworzenia i wdrażania aplikacji sieci web do Docker w systemie Windows za pomocą programu Visual Studio 2017 przy użyciu programu Visual Studio.
services: azure-container-service
documentationcenter: na
author: devinb
manager: douge
editor: ''
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 10/13/2017
ms.author: devinb
ms.openlocfilehash: 90dd5df4a607568e2f3a60791da2948af7ce4e50
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2017
ms.locfileid: "24002934"
---
# <a name="troubleshoot-visual-studio-2017-development-with-docker"></a>Rozwiązywanie problemów z programu Visual Studio 2017 programowanie z Docker

Podczas pracy z programu Visual Studio Tools for Docker, mogą występować problemy podczas kompilowania lub debugowania aplikacji. Poniżej są niektóre typowe kroki rozwiązywania problemów.

## <a name="volume-sharing-is-not-enabled-enable-volume-sharing-in-the-docker-ce-for-windows-settings--linux-containers-only"></a>Udostępnianie wolumin nie jest włączone. Włączenie woluminu udostępnianie w ustawieniach CE Docker dla systemu Windows (tylko kontenery Linux)

Aby rozwiązać ten problem:

1. Kliknij prawym przyciskiem myszy **Docker dla systemu Windows** w obszarze powiadomień, a następnie wybierz **ustawienia**.
1. Wybierz **udostępnione dyski** i udostępnianie dysku systemowym wraz z dysku, na którym znajduje się projekt.

> [!NOTE]
> Jeśli pliki są wyświetlane udostępnionego, nadal może być konieczne kliknij polecenie "poświadczenia..." Resetuj w dolnej części okna dialogowego, aby ponownie włączyć udostępnianie woluminu.

![dyski udostępnione](./media/vs-azure-tools-docker-troubleshooting-docker-errors/shareddrives.png)

## <a name="unable-to-start-debugging"></a>Nie można rozpocząć debugowania

Powodem może być związany mających starych składników debugowania w folderze profilu użytkownika. Wykonaj następujące polecenia, aby usunąć te foldery, tak aby debugowania najnowsze składniki są pobierane w następnej sesji debugowania.

- del %userprofile%\vsdbg
- del %userprofile%\onecoremsvsmon

## <a name="errors-specific-to-networking-when-debugging-your-application"></a>Błędy specyficzne dla sieci podczas debugowania aplikacji

Spróbuj wykonywania skryptu do pobrania z [oczyszczania kontenera hosta sieci](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/windows-server-container-tools/CleanupContainerHostNetworking), który zostanie odświeżony składniki związane z sieci na komputerze hosta.


## <a name="microsoftdockertools-github-repo"></a>Repozytorium GitHub Microsoft/DockerTools

W przypadku innych problemów wystąpią, zobacz [Microsoft/DockerTools](https://github.com/microsoft/dockertools/issues) problemów.