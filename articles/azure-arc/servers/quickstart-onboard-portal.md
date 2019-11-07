---
title: Szybki Start — łączenie maszyn z platformą Azure przy użyciu usługi Azure ARC dla serwerów — Portal
description: W tym przewodniku szybki start dowiesz się, jak połączyć maszyny z platformą Azure przy użyciu usługi Azure ARC dla serwerów z portalu
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: Azure Automation, DSC, PowerShell, Konfiguracja żądanego stanu, zarządzanie aktualizacjami, śledzenie zmian, spis, elementy Runbook, Python, graficzne, hybrydowe i dołączane
ms.date: 08/25/2019
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 2ae7c8545286baebc83077276e356cd2e41f0dc3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73668673"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---portal"></a>Szybki Start: łączenie maszyn z platformą Azure przy użyciu usługi Azure ARC dla serwerów — Portal

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Zapoznaj się z obsługiwanymi klientami i wymaganą konfiguracją sieci w [usłudze Azure ARC dla serwerów — Omówienie](overview.md).

## <a name="generate-the-agent-install-script-using-the-azure-portal"></a>Generowanie skryptu instalacji agenta przy użyciu Azure Portal

1. [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal) uruchamiania
1. Kliknij pozycję **+ Dodaj**
1. Wykonaj kroki kreatora, aby ukończyć
1. Na ostatniej stronie jest generowany skrypt, który można skopiować (lub pobrać).

Skrypt należy uruchomić na maszynie docelowej, która ma zostać nawiązane połączenie. Pobierze agenta, zainstaluje go i nawiąże połączenie z maszyną jako pojedynczą operacją.

Na serwerach spoza platformy Azure, którymi chcesz zarządzać:

1. Logowanie do serwera (przy użyciu komunikacji zdalnej SSH, RDP lub PowerShell)
1. Uruchom powłokę: bash w systemie Linux, program PowerShell jako administrator systemu Windows
1. Wklej skrypt z portalu i wykonaj go na serwerze, aby można było połączyć się z platformą Azure.
1. Domyślne uwierzytelnianie na potrzeby dołączania indywidualnego serwera jest *interaktywne* przy użyciu Login' urządzenia platformy Azure. Po uruchomieniu skryptu zostanie wyświetlony komunikat podobny do:

  ```none
  To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B3V3NLWRF to authenticate.
  ```
  
   Otwórz przeglądarkę i wprowadź kod do uwierzytelnienia. Przeglądarka nie musi być uruchomiona na serwerze, który jest dołączany, może być na innym komputerze, na przykład na laptopie.

1. Aby uwierzytelnić się w sposób nieinteraktywny, wykonaj kroki opisane w temacie [Tworzenie nazwy głównej usługi](quickstart-onboard-powershell.md#create-a-service-principal-for-onboarding-at-scale) i modyfikowanie skryptu wygenerowanego z portalu.

> [!NOTE]
> Jeśli używasz programu Internet Explorer na serwerze podczas pierwszego logowania, wystąpi błąd. Można po prostu ponownie otworzyć przeglądarkę i wykonać tę czynność.

## <a name="execute-the-script-on-target-nodes"></a>Wykonaj skrypt w węzłach docelowych

Zaloguj się do każdego węzła i wykonaj skrypt wygenerowany w portalu. Po pomyślnym zakończeniu działania skryptu przejdź do Azure Portal Sprawdź, czy serwer został pomyślnie połączony.

![Pomyślne dołączanie](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>Czyszczenie

Aby rozłączyć maszynę z usługi Azure ARC dla serwerów, należy wykonać dwa kroki.

1. Wybierz maszynę w [portalu](https://aka.ms/hybridmachineportal), kliknij wielokropek (`...`), a następnie wybierz pozycję **Usuń**.
1. Odinstaluj agenta z komputera.

   W systemie Windows możesz odinstalować agenta za pomocą panelu sterowania "Aplikacje & funkcje".
  
  ![Aplikacje & funkcje](./media/quickstart-onboard/apps-and-features.png)

   Jeśli chcesz, aby skrypt został odinstalowany, możesz użyć poniższego przykładu, który pobiera **PackageID** i odinstalowuje agenta przy użyciu `msiexec /X`.

   Poszukaj w kluczu rejestru `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` i Znajdź **PackageID**. Następnie można odinstalować agenta za pomocą `msiexec`.

   W poniższym przykładzie przedstawiono Odinstalowywanie agenta.

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /Quiet /X "$($_.PsChildName)"}
   ```

   W systemie Linux wykonaj następujące polecenie, aby odinstalować agenta.

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przypisywanie zasad do połączonych maszyn](../../governance/policy/assign-policy-portal.md)