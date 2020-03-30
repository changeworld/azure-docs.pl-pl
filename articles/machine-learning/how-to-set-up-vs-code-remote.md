---
title: 'Interaktywne debugowanie: vs kod & wystąpienia obliczeniowe ML'
titleSuffix: Azure Machine Learning
description: Skonfiguruj zdalny program VS, aby interaktywnie debugować kod za pomocą usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 1999d29db21f820fbcdbca08f2258b657673be3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77169759"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>Debugowanie interaktywnie na wystąpieniu obliczeniowym usługi Azure Machine Learning za pomocą zdalnego kodu vs

W tym artykule dowiesz się, jak skonfigurować program Visual Studio Code Remote na wystąpieniu obliczeniowym usługi Azure Machine Learning, dzięki czemu można **interaktywnie debugować kod** z programu VS Code. 

+ [Wystąpienie obliczeniowe usługi Azure Machine Learning](concept-compute-instance.md) to w pełni zarządzana chmurowa stacja robocza dla analityków danych i zapewnia administratorom IT funkcje zarządzania i gotowości w przedsiębiorstwie. 


+ [Pilot kodu programu Visual Studio](https://code.visualstudio.com/docs/remote/remote-overview) Program rozwoju umożliwia użycie kontenera, komputera zdalnego lub podsystemu Windows dla systemu Linux (WSL) jako w pełni funkcjonalne środowisko programistyczne. 

## <a name="prerequisite"></a>Wymagania wstępne  

Na platformach Windows należy [zainstalować klienta SSH zgodnego z OpenSSH,](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) jeśli go jeszcze nie ma. 

> [!Note]
> PuTTY nie jest obsługiwany w systemie Windows, ponieważ polecenie ssh musi znajdować się w ścieżce. 

## <a name="get-ip-and-ssh-port"></a>Pobierz port IP i SSH 

1. Przejdź do studia usługi https://ml.azure.com/Azure Machine Learning pod adresem .

2. Wybierz [obszar roboczy](concept-workspace.md).
1. Kliknij kartę **Wystąpienia obliczeniowe.**
1. W kolumnie **Identyfikator URI aplikacji** kliknij łącze **SSH** wystąpienia obliczeniowego, którego chcesz użyć jako obliczenia zdalnego. 
1. W oknie dialogowym zanotuj adres IP i port SSH. 
1. Zapisz klucz prywatny w katalogu ~/.ssh/ na komputerze lokalnym; na przykład otwórz edytor dla nowego pliku i wklej klucz w: 

   **Linux**: 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Okna**: 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   Klucz prywatny będzie wyglądać nieco tak:
   ```
   -----BEGIN RSA PRIVATE KEY----- 

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD 
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw== 

   -----END RSA PRIVATE KEY----- 
   ```

1. Zmień uprawnienia do pliku, aby upewnić się, że tylko ty możesz odczytać plik.  
   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa   
   ```

## <a name="add-instance-as-a-host"></a>Dodawanie wystąpienia jako hosta 

Otwórz plik `~/.ssh/config` (Linux) `C:\Users<username>.ssh\config` lub (Windows) w edytorze i dodaj nowy wpis podobny do tego:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa   
```

Oto kilka szczegółów na temat pól: 

|Pole|Opis|
|----|---------|
|Host|Użyj skrótu, który ci się podoba dla instancji obliczeniowej |
|HostName|Jest to adres IP instancji obliczeniowej |
|Port|Jest to port pokazany w oknie dialogowym SSH powyżej |
|Użytkownik|Należy to `azureuser` |
|Plik tożsamości|Należy wskazać plik, w którym został zapisany klucz prywatny |

Teraz powinieneś być w stanie ssh do wystąpienia obliczeniowego przy `ssh azmlci1`użyciu skrótu, który użyłeś powyżej, . 

## <a name="connect-vs-code-to-the-instance"></a>Łączenie kodu VS z wystąpieniem 

1. [Zainstaluj kod programu Visual Studio](https://code.visualstudio.com/).

1. [Zainstaluj zdalne rozszerzenie SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh). 

1. Kliknij ikonę Remote-SSH po lewej stronie, aby wyświetlić konfiguracje SSH.

1. Kliknij prawym przyciskiem myszy właśnie utworzoną konfigurację hosta SSH.

1. Wybierz **pozycję Połącz z hostem w bieżącym oknie**. 

Od tego miejsca pracujesz całkowicie nad wystąpieniem obliczeniowym i możesz teraz edytować, debugować, używać git, używać rozszerzeń itp. 

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz skonfigurowany visual studio code remote, można użyć wystąpienia obliczeniowego jako zdalnego obliczeń z programu Visual Studio Code do interaktywnego debugowania kodu. 

[Samouczek: Trenuj swój pierwszy model uczenia maszynowego](tutorial-1st-experiment-sdk-train.md) pokazuje, jak używać wystąpienia obliczeniowego ze zintegrowanym notesem.