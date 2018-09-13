---
title: W tym samouczku utworzysz Maszynę wirtualną platformy Azure Stack przy użyciu szablonu | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak utworzyć Maszynę wirtualną przy użyciu szablonu predfined i szablonu niestandardowego usługi GitHub za pomocą ASDK.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 0b3ba5c3a091cf673d8b3dbc413d36cb5fb75de5
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713412"
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>Samouczek: tworzenie maszyny Wirtualnej przy użyciu szablonu społeczności
Jako operatora infrastruktury Azure Stack lub użytkownika, możesz utworzyć maszynę Wirtualną przy użyciu [niestandardowe szablony szybkiego startu usługi GitHub](https://github.com/Azure/AzureStack-QuickStart-Templates) zamiast wdrażania go ręcznie z portalu marketplace usługi Azure Stack.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Więcej informacji na temat szablonów szybkiego startu usługi Azure Stack 
> * Utwórz Maszynę wirtualną przy użyciu szablonu niestandardowego usługi GitHub
> * Rozpocznij minikube i zainstalować aplikację

## <a name="learn-about-azure-stack-quickstart-templates"></a>Więcej informacji na temat szablonów szybkiego startu usługi Azure Stack
Szablony szybkiego startu usługi Azure Stack są przechowywane w [publicznego repozytorium szablonów szybkiego startu AzureStack](https://github.com/Azure/AzureStack-QuickStart-Templates) w witrynie GitHub. To repozytorium zawiera szablony wdrażania usługi Azure Resource Manager, które zostały przetestowane za pomocą programu Microsoft Azure Stack Development Kit (ASDK). Aby ułatwić ocenę usługi Azure Stack i używanie środowiska ASDK, można użyć ich. 

Wraz z upływem czasu wielu użytkowników w usłudze GitHub przyczynić się do repozytorium, wynikiem ogromny zbiór ponad 400 szablony wdrażania. To repozytorium stanowi punkt wyjścia o wspaniałych, aby uzyskać lepsze zrozumienie, jak różne rodzaje środowisk można wdrożyć w usłudze Azure Stack. 

>[!IMPORTANT]
> Niektóre z tych szablonów są tworzone przez członków społeczności i nie przez firmę Microsoft. Każdy szablon jest licencjonowany na mocy umowy licencyjnej udzielonej przez właściciela, nie przez firmę Microsoft. Firma Microsoft nie odpowiada za te szablony i nie sprawdza ich pod kątem bezpieczeństwa, zgodności ani wydajności. Szablony społeczności nie są obsługiwane w ramach usług ani programów pomocy technicznej firmy Microsoft i są udostępniane ", w jakim są" bez gwarancji jakiegokolwiek rodzaju.

Jeśli chcesz współtworzyć szablony usługi Azure Resource Manager do usługi GitHub, należy wnoszeniem wkładu do [repozytorium azure-quickstart-templates](https://github.com/Azure/AzureStack-QuickStart-Templates).

Aby dowiedzieć się więcej na temat repozytorium serwisu GitHub i jak przyczyniają się do niego, zobacz [pliku readme](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md). 


## <a name="create-a-vm-using-a-custom-github-template"></a>Utwórz Maszynę wirtualną przy użyciu szablonu niestandardowego usługi GitHub
W tym samouczku przykład [101-vm-linux-minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) szablon szybkiego startu usługi Azure Stack umożliwia wdrażanie maszyny wirtualnej systemu Ubuntu 16.04 w systemem Minikube, aby zarządzać klastrem Kubernetes w usłudze Azure Stack.

Minikube jest narzędziem, które można łatwo uruchamiać lokalnie rozwiązania Kubernetes. Minikube uruchamia jednowęzłowy klaster Kubernetes, wewnątrz maszyny Wirtualnej, dzięki któremu można wypróbować Kubernetes lub Programowanie przy użyciu jego codziennych. Obsługuje prosty, z jednym węzłem klastra Kubernetes uruchomionego na maszynie Wirtualnej systemu Linux. Minikube to najszybszy i najprostszy sposób uzyskać pełnej funkcjonalności klastra Kubernetes uruchomionego. Umożliwia ona deweloperom tworzenia i testowania wdrożeń aplikacji opartej na usłudze Kubernetes na swoich komputerach lokalnych. Po architektonicznie Minikube uruchomiony na maszynie Wirtualnej zarówno węzła głównego, jak i składniki węzła agenta lokalnie:

- Węzeł główny składniki, takie jak serwera interfejsu API, harmonogram, a [etcd serwera](https://coreos.com/etcd/) są uruchamiane w ramach jednego procesu systemu Linux o nazwie LocalKube.
- Składniki węzła agenta są uruchamiane wewnątrz kontenerów platformy docker, dokładnie tak, jak działają w normalnym węźle agenta. Z punktu widzenia wdrażania aplikacji nie ma żadnej różnicy, gdy aplikacja jest wdrażana na Minikube lub regularnych klastra Kubernetes.

Ten szablon powoduje zainstalowanie następujących składników:

- Ubuntu 16.04 LTS z maszyny Wirtualnej
- [Docker CE](https://download.docker.com/linux/ubuntu) 
- [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
- [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
- xFCE4
- xRDP

> [!IMPORTANT]
> Obraz maszyny Wirtualnej systemu Ubuntu (Ubuntu Server 16.04 LTS w tym przykładzie) należy już zostały dodane do witryny marketplace usługi Azure Stack przed rozpoczęciem tych kroków.

1.  Kliknij przycisk **+ Utwórz zasób** > **niestandardowe** > **wdrożenie szablonu**.

    ![](media/azure-stack-create-vm-template/1.PNG) 

2. Kliknij przycisk **Edytuj szablon**.

    ![](media/azure-stack-create-vm-template/2.PNG) 

3.  Kliknij przycisk **szablon szybkiego startu**.

    ![](media/azure-stack-create-vm-template/3.PNG)

4. Wybierz **101-vm-linux-minikube** spośród dostępnych szablonów przy użyciu **wybierz szablon** listy rozwijanej listy, a następnie kliknij przycisk **OK**.  

    ![](media/azure-stack-create-vm-template/4.PNG)

5. Jeśli chcesz wprowadzić zmiany do szablonu JSON, możesz to zrobić, jeśli nie, lub po zakończeniu kliknij przycisk **Zapisz** aby zamknąć okno dialogowe Edytowanie szablonu.

    ![](media/azure-stack-create-vm-template/5.PNG) 

6.  Kliknij pozycję **parametry**Wypełnij lub zmodyfikować dostępne pola zgodnie z potrzebami, a następnie kliknij **OK**. Wybierz subskrypcję, aby używać, lub wybierz istniejącą nazwę grupy zasobów, a następnie kliknij polecenie **Utwórz** do inicjowania wdrożenia szablonu.

    ![](media/azure-stack-create-vm-template/6.PNG)

7. Wybierz subskrypcję, aby używać, lub wybierz istniejącą nazwę grupy zasobów, a następnie kliknij polecenie **Utwórz** do inicjowania wdrożenia szablonu.

    ![](media/azure-stack-create-vm-template/7.PNG)

8. Wdrożenie grupy zasobów trwa kilka minut, aby utworzyć niestandardową maszynę Wirtualną na podstawie szablonu. Możesz monitorować stan instalacji za pośrednictwem powiadomień i właściwości grupy zasobów. 

    ![](media/azure-stack-create-vm-template/8.PNG)

    >[!NOTE]
    > Maszyna wirtualna zostanie uruchomiona po ukończeniu wdrażania. 

## <a name="start-minikube-and-install-an-application"></a>Rozpocznij minikube i zainstalować aplikację
Teraz czy maszyny Wirtualnej systemu Linux została pomyślnie utworzona, możesz zalogować się do uruchomienia minikube i zainstalowania aplikacji. 

1. Po zakończeniu wdrożenia kliknij przycisk **Connect** Aby wyświetlić publiczny adres IP używany do łączenia z maszyną wirtualną systemu Linux. 

    ![](media/azure-stack-create-vm-template/9.PNG)

2. W wierszu polecenia z podwyższonym poziomem uprawnień uruchom **mstsc.exe** otworzyć usługę Podłączanie pulpitu zdalnego i nawiązać połączenie z publicznym adresem IP maszyny Wirtualnej systemu Linux, odnalezione w poprzednim kroku. Po wyświetleniu monitu zaloguj się do xRDP, należy użyć poświadczenia zostały określone podczas tworzenia maszyny Wirtualnej.

    ![](media/azure-stack-create-vm-template/10.PNG)

3. Otwórz Terminal Emulator, a następnie wprowadź następujące polecenia, aby uruchomić minikube:

    ```shell
    sudo minikube start --vm-driver=none
    sudo minikube addons enable dashboard
    sudo minikube dashboard --url
    ```

    ![](media/azure-stack-create-vm-template/11.PNG)

4. Otwórz przeglądarkę sieci web i odwiedź adres pulpitu nawigacyjnego platformy Kubernetes. Gratulacje, masz teraz w pełni pracujące instalacji usługi Kubernetes przy użyciu minikube!

    ![](media/azure-stack-create-vm-template/12.PNG)

5. Jeśli chcesz wdrożyć przykładową aplikację, odwiedź stronę oficjalną dokumentacją rozwiązania kubernetes, Pomiń w sekcji "Tworzenie klastra Minikube", ponieważ zostały już one utworzone powyżej. Po prostu przejść do sekcji "Tworzenie aplikacji Node.js" w https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Więcej informacji na temat szablonów szybkiego startu usługi Azure Stack 
> * Utwórz Maszynę wirtualną przy użyciu szablonu niestandardowego usługi GitHub
> * Rozpocznij minikube i zainstalować aplikację

