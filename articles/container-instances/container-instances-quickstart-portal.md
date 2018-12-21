---
title: 'Szybki start: Uruchamianie aplikacji w usłudze Azure Container Instances — Portal'
description: W tym przewodniku Szybki start wdrożysz aplikację kontenera Docker działającą w wyizolowanym kontenerze w usłudze Azure Container Instances witrynie Azure Portal.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 8f547977e544854e281e1c6be442607d55149e5e
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190292"
---
# <a name="quickstart-run-a-container-application-in-azure-container-instances-in-the-azure-portal"></a>Szybki start: Uruchamianie aplikacji kontenera w usłudze Azure Container Instances w witrynie Azure Portal

Używając usługi Azure Container Instances, możesz łatwo i szybko uruchamiać kontenery Docker na platformie Azure. Nie musisz wdrażać maszyn wirtualnych ani korzystać z pełnej platformy orkiestracji kontenerów, takiej jak Kubernetes. Podczas pracy z tym przewodnikiem Szybki start utworzysz kontener na platformie Azure przy użyciu witryny Azure Portal i udostępnisz jego aplikację za pomocą w pełni kwalifikowanej nazwy domeny (FQDN). Po skonfigurowaniu kilku ustawień i wdrożeniu kontenera możesz przejść do uruchomionej aplikacji:

![Aplikacja wdrożona w usłudze Azure Container Instances widziana w przeglądarce][aci-portal-07]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][azure-free-account].

## <a name="create-a-container-instance"></a>Tworzenie wystąpienia kontenera

Wybierz kolejno pozycje **Utwórz zasób** > **Kontenery** > **Container Instances**.

![Rozpoczynanie tworzenia nowego wystąpienia kontenera w witrynie Azure Portal][aci-portal-01]

Wprowadź następujące wartości w polach tekstowych **Nazwa kontenera**, **Obraz kontenera** oraz **Grupa zasobów**. Pozostaw domyślne wartości w pozostałych polach i kliknij przycisk **OK**.

* Nazwa kontenera: `mycontainer`
* Obraz kontenera: `microsoft/aci-helloworld`
* Grupa zasobów: **Utwórz nowy element** > `myResourceGroup`

![Konfigurowanie podstawowych ustawień nowego wystąpienia kontenera w witrynie Azure Portal][aci-portal-03]

Usługa Azure Container Instances umożliwia tworzenie kontenerów systemów Windows i Linux. Dla tego przewodnika Szybki start pozostaw domyślne ustawienie systemu **Linux**, aby wdrożyć obraz `microsoft/aci-helloworld` oparty na systemie Linux.

W obszarze **Konfiguracja** określ wartość w polu **Etykieta nazwy DNS** swojego kontenera. Nazwa musi być unikatowa w regionie platformy Azure, w którym tworzysz wystąpienie kontenera. Twój kontener będzie dostępny publicznie pod adresem `<dns-name-label>.<region>.azurecontainer.io`.

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

W przypadku rozwiązywania problemów z kontenerem lub uruchomioną aplikacją przydatne jest przeglądanie dzienników wystąpienia kontenera.

Aby wyświetlić dzienniki kontenera, w obszarze **Ustawienia** wybierz pozycję **Kontenery**, a następnie **Dzienniki**. Powinno pojawić się żądanie HTTP GET generowane podczas wyświetlania aplikacji w przeglądarce.

![Dzienniki kontenera w witrynie Azure Portal][aci-portal-11]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z kontenerem wybierz opcję **Przegląd** dla wystąpienia kontenera *mycontainer*, a następnie wybierz polecenie **Usuń**.

![Usuwanie wystąpienia kontenera w witrynie Azure Portal][aci-portal-09]

Po pojawieniu się okna dialogowego potwierdzenia wybierz pozycję **Tak**.

![Potwierdzanie usunięcia wystąpienia kontenera w witrynie Azure Portal][aci-portal-10]

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym przewodnikiem Szybki start utworzono wystąpienie kontenera platformy Azure na podstawie obrazu z publicznego rejestru Docker Hub. Jeśli chcesz skompilować obraz kontenera i wdrożyć go za pomocą prywatnej usługi Azure Container Registry, przejdź do samouczka dotyczącego usługi Azure Container Instances.

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