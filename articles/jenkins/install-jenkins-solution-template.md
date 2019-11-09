---
title: Tworzenie serwera Jenkins na platformie Azure
description: Zainstaluj usługę Jenkins na maszynie wirtualnej z systemem Linux platformy Azure za pomocą szablonu rozwiązania Jenkins i utwórz przykładową aplikację Java.
ms.service: jenkins
keywords: jenkins, azure, devops, portal, virtual machine, solution template
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 6/7/2017
ms.openlocfilehash: 531199303f7b00083bcd7139ac00ac36bde5c583
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837517"
---
# <a name="create-a-jenkins-server-on-an-azure-linux-vm-from-the-azure-portal"></a>Tworzenie serwera Jenkins na maszynie wirtualnej z systemem Linux platformy Azure przy użyciu witryny Azure Portal

W tym przewodniku Szybki start pokazano, jak zainstalować usługę [Jenkins](https://jenkins.io) z narzędziami i wtyczkami skonfigurowanymi do współdziałania z platformą Azure na maszynie wirtualnej z systemem Ubuntu Linux. Po zakończeniu uzyskasz działający na platformie Azure serwer Jenkins umożliwiający skompilowanie przykładowej aplikacji Java z usługi [GitHub](https://github.com).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure
* Dostęp do powłoki SSH w wierszu polecenia na komputerze (takiej jak powłoka Bash lub [PuTTY](https://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>Tworzenie maszyny wirtualnej z usługą Jenkins za pomocą szablonu rozwiązania
Usługa Jenkins obsługuje model, w którym serwer Jenkins deleguje pracę do jednego lub większej liczby agentów, co umożliwia pojedynczej instalacji usługi Jenkins hostowanie dużej liczby projektów lub udostępnianie różnych środowisk na potrzeby kompilacji lub testów. Kroki opisane w tej sekcji przeprowadzają użytkownika przez proces instalowania i konfigurowania serwera Jenkins na platformie Azure.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-jenkins"></a>Nawiązywanie połączenia z usługą Jenkins

Przejdź do maszyny wirtualnej (na przykład `http://jenkins2517454.eastus.cloudapp.azure.com/`) w przeglądarce sieci Web. Konsola usługi Jenkins nie jest dostępna za pośrednictwem niezabezpieczonych połączeń HTTP, więc na stronie będą podane instrukcje, aby uzyskać dostęp do tej konsoli w sposób bezpieczny z komputera przy użyciu tunelu SSH.

![Odblokowywanie usługi Jenkins](./media/install-jenkins-solution-template/jenkins-ssh-instructions.png)

Skonfiguruj tunel przy użyciu polecenia `ssh` na stronie z wiersza polecenia, zamieniając pozycję `username` na nazwę użytkownika administratora maszyny wirtualnej wybraną wcześniej podczas konfigurowania maszyny wirtualnej za pomocą szablonu rozwiązania.

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

Po uruchomieniu tunelu przejdź do adresu `http://localhost:8080/` na maszynie lokalnej. 

Uzyskaj hasło początkowe, uruchamiając następujące polecenie w wierszu polecenia przy aktywnym połączeniu z maszyną wirtualną z usługą Jenkins za pośrednictwem powłoki SSH.

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Odblokuj pulpit nawigacyjny usługi Jenkins za pomocą hasła początkowego.

![Odblokowywanie usługi Jenkins](./media/install-jenkins-solution-template/jenkins-unlock.png)

Na następnej stronie wybierz pozycję **Zainstaluj sugerowane wtyczki**, a następnie utwórz administratora usługi Jenkins, który będzie służyć do uzyskiwania dostępu do pulpitu nawigacyjnego usługi Jenkins.

![Usługa Jenkins jest gotowa do użycia.](./media/install-jenkins-solution-template/jenkins-welcome.png)

Serwer Jenkins jest już gotowy do kompilowania kodu.

## <a name="create-your-first-job"></a>Tworzenie pierwszego zadania

W konsoli usługi Jenkins wybierz pozycję **Utwórz nowe zadania**, nadaj temu elementowi nazwę **mySampleApp** i wybierz pozycję **Projekt dowolny**, a następnie wybierz przycisk **OK**.

![Tworzenie nowego zadania](./media/install-jenkins-solution-template/jenkins-new-job.png) 

Wybierz kartę **Zarządzanie kodem źródłowym**, włącz pozycję **Git** i wprowadź następujący adres URL w polu **Adres URL repozytorium**:`https://github.com/spring-guides/gs-spring-boot.git`

![Definiowanie repozytorium Git](./media/install-jenkins-solution-template/jenkins-job-git-configuration.png) 

Wybierz kartę **Kompilacja**, a następnie wybierz pozycję **Dodaj krok kompilacji**, **Wywołaj skrypt Gradle**. Wybierz pozycję **Użyj otoki Gradle**, a następnie wprowadź wartość `complete` w polu **Lokalizacja otoki** i wartość `build` w polu **Zadania**.

![Kompilowanie przy użyciu otoki Gradle](./media/install-jenkins-solution-template/jenkins-job-gradle-config.png) 

Wybierz pozycję **Zaawansowane**, a następnie wprowadź wartość `complete` w polu **Główny skrypt kompilacji**. Wybierz pozycję **Zapisz**.

![Konfigurowanie ustawień zaawansowanych w kroku dotyczącym kompilacji i otoki Gradle](./media/install-jenkins-solution-template/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>Kompilowanie kod

Wybierz pozycję **Kompiluj teraz**, aby skompilować kod i utworzyć pakiet z aplikacją przykładową. Po zakończeniu kompilacji wybierz link **Obszar roboczy** dla projektu.

![Przechodzenie do obszaru roboczego w celu pobrania pliku JAR z kompilacji](./media/install-jenkins-solution-template/jenkins-access-workspace.png) 

Przejdź do folderu `complete/build/libs` i upewnij się, że znajduje się tam plik `gs-spring-boot-0.1.0.jar`, w celu potwierdzenia, że kompilacja zakończyła się pomyślnie. Serwer Jenkins jest już gotowy i możesz za jego pomocą kompilować własne projekty na platformie Azure.

## <a name="troubleshooting-the-jenkins-solution-template"></a>Rozwiązywanie problemów z szablonem rozwiązania Jenkins

Jeśli napotkasz jakiekolwiek usterki w szablonie rozwiązania Jenkins, prześlij zgłoszenie do [repozytorium GitHub usługi Jenkins](https://github.com/azure/jenkins/issues).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie maszyn wirtualnych platformy Azure jako agentów usługi Jenkins](jenkins-azure-vm-agents.md)
