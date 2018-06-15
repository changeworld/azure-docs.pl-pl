---
title: Szybki start — Tworzenie pierwszego kontenera usługi Azure Container Instances w witrynie Azure Portal
description: W tym przewodniku Szybki start witryna Azure Portal zostanie użyta do wdrożenia kontenera w usłudze Azure Container Instances
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 6aa6fb27b2aa7c8b9614e5812fadc629b1e185f8
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076242"
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Szybki start: Tworzenie pierwszego kontenera w usłudze Azure Container Instances

Usługa Azure Container Instances ułatwia tworzenie kontenerów Docker na platformie Azure oraz zarządzanie nimi bez konieczności inicjowania obsługi maszyn wirtualnych czy adoptowania usługi wyższego poziomu. Podczas pracy z tym przewodnikiem Szybki start utworzysz kontener na platformie Azure przy użyciu witryny Azure Portal i uwidocznisz go w Internecie przy użyciu w pełni kwalifikowanej nazwy domeny. Po skonfigurowaniu kilku ustawień w przeglądarce pojawi się następujący ekran:

![Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances][aci-portal-07]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][azure-free-account].

## <a name="create-a-container-instance"></a>Tworzenie wystąpienia kontenera

Wybierz kolejno pozycje **Utwórz zasób** > **Kontenery** > **Container Instances**.

![Rozpoczynanie tworzenia nowego wystąpienia kontenera w witrynie Azure Portal][aci-portal-01]

Wprowadź następujące wartości w polach tekstowych **Nazwa kontenera**, **Obraz kontenera** oraz **Grupa zasobów**. Pozostaw domyślne wartości w pozostałych polach i kliknij przycisk **OK**.

* Nazwa kontenera: `mycontainer`
* Obraz kontenera: `microsoft/aci-helloworld`
* Grupa zasobów: `myResourceGroup`

![Konfigurowanie podstawowych ustawień nowego wystąpienia kontenera w witrynie Azure Portal][aci-portal-03]

Usługa Azure Container Instances umożliwia tworzenie kontenerów systemów Windows i Linux. Dla tego przewodnika Szybki start pozostaw domyślne ustawienie systemu **Linux**, aby wdrożyć obraz `microsoft/aci-helloworld` oparty na systemie Linux.

W obszarze **Konfiguracja** określ wartość w polu **Etykieta nazwy DNS** swojego kontenera. Nazwa musi być unikatowa w regionie platformy Azure, w którym tworzysz wystąpienie kontenera. Twój kontener będzie dostępny publicznie pod adresem `<dns-name-label>.<region>.azurecontainer.io`.

Pozostaw domyślne wartości pozostałych ustawień w obszarze **Konfiguracja**, a następnie kliknij przycisk **OK**, aby zweryfikować konfigurację.

![Konfigurowanie nowego wystąpienia kontenera w witrynie Azure Portal][aci-portal-04]

Po zakończeniu weryfikacji zostanie wyświetlone podsumowanie ustawień kontenera. Wybierz przycisk **OK**, aby przesłać żądanie wdrożenia kontenera.

![Podsumowanie ustawień nowego wystąpienia kontenera w witrynie Azure Portal][aci-portal-05]

Po rozpoczęciu wdrażania na pulpicie nawigacyjnym portalu pojawi się kafelek wskazujący postęp wdrażania. Po wdrożeniu kafelek wyświetla nowe wystąpienie kontenera.

![Postęp tworzenia nowego wystąpienia kontenera w witrynie Azure Portal][aci-portal-08]

Wybierz wystąpienie kontenera **mycontainer**, aby wyświetlić jego właściwości. Zwróć uwagę na wartość **FQDN** (w pełni kwalifikowana nazwa domeny) wystąpienia kontenera, a także na jego **Stan**.

![Przegląd grupy kontenerów w witrynie Azure Portal][aci-portal-06]

Po osiągnięciu przez **Stan** wartości *Uruchomiono* przejdź w przeglądarce do nazwy FQDN kontenera.

![Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances][aci-portal-07]

Gratulacje! Konfigurując zaledwie kilku ustawień, udało Ci się wdrożyć publicznie dostępną aplikację w usłudze Azure Container Instances.

## <a name="view-container-logs"></a>Wyświetlanie dzienników kontenerów

W przypadku rozwiązywania problemów z kontenerem lub uruchomioną w nim aplikacją przydatne jest przeglądanie dzienników wystąpienia kontenera.

Aby wyświetlić dzienniki kontenera, w obszarze **USTAWIENIA** wybierz pozycję **Kontenery**, a następnie **Dzienniki**. Powinno pojawić się żądanie HTTP GET generowane podczas wyświetlania aplikacji w przeglądarce.

![Dzienniki kontenera w witrynie Azure Portal][aci-portal-11]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z kontenerem wybierz opcję **Przegląd** dla wystąpienia kontenera *mycontainer*, a następnie wybierz polecenie **Usuń**.

![Usuwanie wystąpienia kontenera w witrynie Azure Portal][aci-portal-09]

Po pojawieniu się okna dialogowego potwierdzenia wybierz pozycję **Tak**.

![Potwierdzanie usunięcia wystąpienia kontenera w witrynie Azure Portal][aci-portal-10]

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym przewodnikiem Szybki start utworzono wystąpienie kontenera platformy Azure na podstawie obrazu z publicznego rejestru Docker Hub. Jeśli chcesz samodzielnie skompilować obraz kontenera i wdrożyć go w usłudze Azure Container Instances za pomocą prywatnej usługi Azure Container Registry, przejdź do samouczka dotyczącego usługi Azure Container Instances.

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