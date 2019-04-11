---
title: Przewodnik Szybki Start — wdrażanie kontenera platformy Docker w usłudze Azure Container Instances — Portal
description: W tym przewodniku Szybki Start użyjesz witryny Azure portal, aby szybko wdrożyć aplikacji konteneryzowanych sieci web uruchomioną w wystąpieniu kontenera platformy Azure odizolowane
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: f4d232d4d6043ede3979db67e5cd35130d931bef
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369449"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Szybki start: Wdrożenia wystąpienia kontenera na platformie Azure przy użyciu witryny Azure portal

Usługi Azure Container Instances umożliwia uruchamianie bez użycia serwera kontenerów platformy Docker na platformie Azure z prostotą i szybkością. Wdrażanie aplikacji kontenera wystąpienia na żądanie, jeśli nie potrzebujesz pełnej kontenera platformę koordynacji, takich jak usługi Azure Kubernetes Service.

Wdrażanie izolowanego kontenera platformy Docker i udostępnić swoją aplikację z w pełni kwalifikowaną nazwą domeny (FQDN), w tym przewodniku Szybki Start użyj witryny Azure portal. Po skonfigurowaniu kilku ustawień i wdrożeniu kontenera możesz przejść do uruchomionej aplikacji:

![Aplikacja wdrożona w usłudze Azure Container Instances widziana w przeglądarce][aci-portal-07]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][azure-free-account].

## <a name="create-a-container-instance"></a>Tworzenie wystąpienia kontenera

Wybierz kolejno pozycje **Utwórz zasób** > **Kontenery** > **Container Instances**.

![Rozpoczynanie tworzenia nowego wystąpienia kontenera w witrynie Azure Portal][aci-portal-01]

Wprowadź następujące wartości w polach tekstowych **Nazwa kontenera**, **Obraz kontenera** oraz **Grupa zasobów**. Pozostaw domyślne wartości w pozostałych polach i kliknij przycisk **OK**.

* Nazwa kontenera: `mycontainer`
* Obraz kontenera: `mcr.microsoft.com/azuredocs/aci-helloworld`
* Grupa zasobów: **Utwórz nowy element** > `myResourceGroup`

![Konfigurowanie podstawowych ustawień nowego wystąpienia kontenera w witrynie Azure Portal][aci-portal-03]

Na potrzeby tego przewodnika Szybki Start Pozostaw domyślne ustawienie **publicznych** do wdrażania publicznej Microsoft `aci-helloworld` obrazu. Ten obraz zawiera małą aplikację internetową napisaną w języku Node.js, która udostępnia statyczną stronę HTML.

W obszarze **Konfiguracja** określ wartość w polu **Etykieta nazwy DNS** swojego kontenera. Nazwa musi być unikatowa w obrębie regionu świadczenia usługi Azure, w którym tworzysz wystąpienie kontenera. Twój kontener będzie dostępny publicznie pod adresem `<dns-name-label>.<region>.azurecontainer.io`. Jeśli zostanie wyświetlony komunikat o błędzie „Etykieta nazwy DNS nie jest dostępna”, spróbuj użyć innej etykiety nazwy DNS.

Pozostaw domyślne wartości pozostałych ustawień w obszarze **Konfiguracja**, a następnie kliknij przycisk **OK**, aby zweryfikować konfigurację.

![Konfigurowanie nowego wystąpienia kontenera w witrynie Azure Portal][aci-portal-04]

Po zakończeniu weryfikacji zostanie wyświetlone podsumowanie ustawień kontenera. Wybierz przycisk **OK**, aby przesłać żądanie wdrożenia kontenera.

![Podsumowanie ustawień nowego wystąpienia kontenera w witrynie Azure Portal][aci-portal-05]

Po rozpoczęciu wdrażania zostanie wyświetlone powiadomienie wskazujące postęp wdrażania. Kolejne powiadomienie zostanie wyświetlone, gdy grupa kontenerów zostanie wdrożona.

![Postęp tworzenia nowego wystąpienia kontenera w witrynie Azure Portal][aci-portal-08]

Otwórz przegląd dla grupy kontenerów, przechodząc do ekranu **Grupy zasobów** > **myResourceGroup** > **mycontainer**. Zwróć uwagę na wartość **FQDN** (w pełni kwalifikowana nazwa domeny) wystąpienia kontenera, a także na jego **Stan**.

![Przegląd grupy kontenerów w witrynie Azure Portal][aci-portal-06]

Po osiągnięciu przez **Stan** wartości *Uruchomiono* przejdź w przeglądarce do nazwy FQDN kontenera.

![Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances][aci-portal-07]

Gratulacje! Konfigurując zaledwie kilku ustawień, udało Ci się wdrożyć publicznie dostępną aplikację w usłudze Azure Container Instances.

## <a name="view-container-logs"></a>Wyświetlanie dzienników kontenerów

W przypadku rozwiązywania problemów z kontenerem lub uruchomioną w nim aplikacją przydatne jest przeglądanie dzienników wystąpienia kontenera.

Aby wyświetlić dzienniki kontenera, w obszarze **Ustawienia** wybierz pozycję **Kontenery**, a następnie **Dzienniki**. Powinno pojawić się żądanie HTTP GET generowane podczas wyświetlania aplikacji w przeglądarce.

![Dzienniki kontenera w witrynie Azure Portal][aci-portal-11]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z kontenerem wybierz opcję **Przegląd** dla wystąpienia kontenera *mycontainer*, a następnie wybierz polecenie **Usuń**.

![Usuwanie wystąpienia kontenera w witrynie Azure Portal][aci-portal-09]

Po pojawieniu się okna dialogowego potwierdzenia wybierz pozycję **Tak**.

![Potwierdzanie usunięcia wystąpienia kontenera w witrynie Azure Portal][aci-portal-10]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start utworzono wystąpienie kontenera platformy Azure za pomocą publicznego obrazu firmy Microsoft. Jeśli chcesz skompilować obraz kontenera i wdrożyć go za pomocą prywatnej usługi Azure Container Registry, przejdź do samouczka dotyczącego usługi Azure Container Instances.

> [!div class="nextstepaction"]
> [Samouczek dotyczący usługi Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png
[aci-portal-09]: ./media/container-instances-quickstart-portal/qs-portal-09.png
[aci-portal-10]: ./media/container-instances-quickstart-portal/qs-portal-10.png
[aci-portal-11]: ./media/container-instances-quickstart-portal/qs-portal-11.png

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/